in cmd :
rasa init
hit enter + timezoneboot (or any chatbot name) + yes + yes ...
talk with the chatbot
/stop
code . (to open vs code) 
in vs code, go to data > nlu.md
notice that there's a set of expressions for greeting, moods etc.
go back to cmd :
cd timezonebot
rasa shell
try entering a sentence that we know from the training data (exists in nlu file)
try similar intent sentence that doesn't exist to challenge the bot
/stop
rasa shell nlu (to load nlu model)
are you a bot?
notice that the response is json with intent and % of coonfidence about nwhat the user is talking about, and shows the confidence that the language understunding has of all the intents
ctrl + c to quit
in vs code, go to domain.yml file, notice that all the intents and the responses are listed here, it's also a place to define an actions, slots and entities
g to data folder > stories.md
stories file define potebtial conversation flows between the user and the chatbot, it uses utter_so	mething. This is what the chatbot expects conversations ti be like.

go to nlu.md, get rid of all the default intents, only keep greeting and goodbye
paste this instead:

```
## intent: find_time_zone
- can you tell the time?
- could you tell me the time?
- i need you to find the time zone
- can you find me a time zone?
- what time is it?
```
go back to stories.md, get rid of all the stories, only leave ## say goodbye
paste this instead : 

```
## ask for time zone long
* greet
  - utter_greet
* find_time_zone
  - utter_ask_location
* city_info
  - utter_finding_time_zone
* thanks
  - utter_you_are_welcome
  - utter_goodbye


## ask for time zone short
* greet
  - utter_greet
* find_time_zone_for_location
  - utter_finding_time_zone
* thanks
  - utter_you_are_welcome
  - utter_goodbye
```

go back to nlu.md to define the intents:

```
## intent: find_time_zone
- can you tell the time?
- could you tell me the time?
- i need you to find the time zone
- can you find me a time zone?
- what time is it?

## intent: find_time_zone_for_location
- what is   the time zo ne of London?
- time zo   ne of lisbon
- do you know the time zone of Berlin?
- I need to know the time zone of Mumbai

## intent:city_info
- London
- SanFrancisco
- Toronto
- New Dlhi
- Auckland
- Oslo

## intent:thanks
- thank you
- thanks
- great, thanks
```


We've done the intents, but we still need to de fine the responses :
go to domain.yml
erase the non existing intents from intents (only leave greet and goodbye)
add the added intents :
```
  - find_time_zone
  - find_time_zone_for_location
  - city_info
  - thanks
```

the same way, remove the non existing utters from responses (only leave greet and goodbye)
redefine the greeting response for example : "Hey! I am a Time Zone Bot"
add the utters :
```
  utter_goodbye:
  - text: "Bye"

  utter_ask_location:
  - text: "Which city do you need the time zone of?"
  
  utter_finding_time_zone:
  - text: "Ok, give me a second to look it up.."

  utter_you_are_welcome:
  - text: "You are welcome!"
```

in order to be able to see changes : go to the command line and type :
rasa train
rasa shell
talk to the bot and notice the different answers :
the conversation now looks like this : 
Your input ->  hi
Hi! I am a Time Zone Bot?
Your input ->  find the time Zone for London
Which city do you need the time zone of?
Your input ->  London
Ok, give me a second to look it up..
Your input ->  bye
Bye
Your input ->

in nlu.md : in #intent : find_time_zone add :
- tell me the time zone
in # intent : find_time_zone_for_location add :
- what is the time in Prague
rasa shell nlu
make conversation
for the entire mlu.md file :
[name of city](city)
add 
entities:
	- city

retry :
rasa train
rasa shell nlu
what is th =e time zone of London

in domain.yml, add the entity :
slots:
  city:
    type: text
    auto_fill: True
    
and replace "Ok, give me a second to look it up" by "Ok, give me a second to look up the time zone of {city}"
rasa shell
now conversation looks like :
Your input ->  find the time zone
Which city do you need the time zone of?
Your input ->  London
Ok, give me a second to look up the time zone of London
Your input ->  /stop

go to stories.md > ## ask for time zone long > * city_info :
add - action_show_time_zone
go to stories.md > ## ask for time zone short > * find_time_zone_for_location :
add - action_show_time_zone

go to domain.yml 

go to actions.py
use this code instead :


```python
from typing import Any, Text, Dict, List
from rasa_sdk import Action, Tracker
from rasa_sdk.executor import CollectingDispatcher

timezones = {
    "London": "UTC+1:00",
    "Sofia": "UTC+3:00",
    "Lisbon": "UTC+1:00",
    "Mumbai":  "UTC+5:30"
}

class ActionShowTimeZone(Action):
    def name(self) -> Text:
        return "action_show_time_zone"

    def run(self, dispatcher: CollectingDispatcher,
            tracker: Tracker,
            domain: Dict[Text, Any]) -> List[Dict[Text, Any]]:

        city = tacker.get_slot("city")
        timezone = timezones.get(city)

        if timezone is None:
            output = "Could not find the time zone of {}".format(city)
        else:
            output = "Time zone of {} is {}".format(city, timezone)

        dispatcher.utter_message(text=output)

        return []
```

go to endpoints.yml and uncomment 
action_endpoint:
  url: "http://localhost:5055/webhook"

create another terminal wondow inside timezonebot
rasa run actions
