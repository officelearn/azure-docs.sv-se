---
title: 'Självstudiekurs: Azure Notebook - Personalizer'
titleSuffix: Azure Cognitive Services
description: Den här självstudien simulerar en Personalizer-loop _system i en Azure Notebook, vilket föreslår vilken typ av kaffe en kund ska beställa. Användarna och deras inställningar lagras i en användardatauppsättning. Information om kaffet finns också tillgänglig och lagras i en kaffedatauppsättning.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: 03e8b658f7edf4640d738e5ea3af84953185d0f5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986843"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Självstudiekurs: Använd Personalizer i Azure Notebook

Den här självstudien kör en Personalizer-loop i en Azure Notebook, vilket visar slutet till slutet av livscykeln för en Personalizer-loop.

Slingan föreslår vilken typ av kaffe en kund ska beställa. Användarna och deras inställningar lagras i en användardatauppsättning. Information om kaffet lagras i en kaffedatauppsättning.

## <a name="users-and-coffee"></a>Användare och kaffe

Anteckningsboken, som simulerar användarinteraktion med en webbplats, väljer en slumpmässig användare, tid på dagen och typ av väder från datauppsättningen. En sammanfattning av användarinformationen är:

|Kunder - kontextfunktioner|Tider på dagen|Typer av väder|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Dave|Morgon<br>Eftermiddag<br>Kväll|Soliga<br>Regniga<br>Snöiga|

För att hjälpa Personalizer lära sig, med tiden, _systemet_ vet också detaljer om kaffe val för varje person.

|Kaffe - actionfunktioner|Typer av temperatur|Ursprungsort|Typer av stek|Organisk|
|--|--|--|--|--|
|Cappacino (|Frekvent|Kenya|Mörk|Organisk|
|Kall brygd|Kall|Brasilien|Ljus|Organisk|
|Isknugga|Kall|Etiopien|Ljus|Inte ekologiskt|
|Latte|Frekvent|Brasilien|Mörk|Inte ekologiskt|

**Syftet med** Personalizer-loopen är att hitta den bästa matchningen mellan användarna och kaffet så mycket av tiden som möjligt.

Koden för den här självstudien är tillgänglig i [GitHub-databasen för Personalizer Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Så här fungerar simuleringen

I början av det löpande systemet är förslagen från Personalizer endast framgångsrika mellan 20% till 30%. Denna framgång indikeras av belöningen som skickas tillbaka till Personalizer's Reward API, med poängen 1. Efter några Rank och Reward samtal, systemet förbättras.

Efter de första begärandena kör du en offlineutvärdering. Detta gör det möjligt för Personalizer att granska data och föreslå en bättre inlärningspolicy. Använd den nya utbildningsprincipen och kör anteckningsboken igen med 20 % av antalet tidigare begäranden. Loopen kommer att fungera bättre med den nya inlärningspolicyn.

## <a name="rank-and-reward-calls"></a>Ranka och belöna samtal

För vart och ett av de få tusen anrop till Personalizer-tjänsten skickar Azure Notebook **rank-begäran** till REST API:

* Ett unikt ID för händelsen Rank/Request
* Kontextfunktioner - Ett slumpmässigt val av användare, väder och tid på dagen - simulera en användare på en webbplats eller mobil enhet
* Åtgärder med funktioner - _Alla_ kaffedata - från vilka Personalizer ger ett förslag

Systemet tar emot begäran och jämför sedan den förutsägelsen med användarens kända val för samma tid på dagen och vädret. Om det kända valet är detsamma som det förväntade valet skickas **belöningen** på 1 tillbaka till Personalizer. Annars belöningen skickas tillbaka är 0.

> [!Note]
> Detta är en simulering så algoritmen för belöningen är enkel. I ett verkligt scenario bör algoritmen använda affärslogik, eventuellt med vikter för olika aspekter av kundens upplevelse, för att bestämma belöningspoängen.


## <a name="prerequisites"></a>Krav

* Ett [Azure Notebook-konto.](https://notebooks.azure.com/)
* En [Azure Personalizer-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Om du redan har använt Personalizer-resursen måste du [rensa data](how-to-settings.md#clear-data-for-your-learning-loop) i Azure-portalen för resursen.
* Ladda upp alla filer för [det här exemplet](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) till ett Azure Notebook-projekt.

Filbeskrivningar:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) är Jupyter anteckningsboken för den här guiden.
* [Användardatauppsättning](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) lagras i ett JSON-objekt.
* [Kaffedatauppsättning](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) lagras i ett JSON-objekt.
* [Exempel begäran JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) är det förväntade formatet för en POST-begäran till Rank API.

## <a name="configure-personalizer-resource"></a>Konfigurera Personalizer-resurs

Konfigurera din [Personalizer-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) med **uppdateringsmodellfrekvensen** inställd på 15 sekunder och en väntetid på 15 sekunder i **Azure-portalen.** Dessa värden finns på **[sidan Konfiguration.](how-to-settings.md#configure-service-settings-in-the-azure-portal)**

|Inställning|Värde|
|--|--|
|uppdatera modellfrekvens|15 sekunder|
|belöning väntetid|15 sekunder|

Dessa värden har en mycket kort varaktighet för att visa ändringar i den här självstudien. Dessa värden bör inte användas i ett produktionsscenario utan att validera de uppnår ditt mål med din Personalizer-loop.

## <a name="set-up-the-azure-notebook"></a>Konfigurera Den Azure-anteckningsboken

1. Ändra kärnan `Python 3.6`till .
1. Öppna filen `Personalizer.ipynb`.

## <a name="run-notebook-cells"></a>Köra anteckningsboksceller

Kör varje körbar cell och vänta tills den returneras. Du vet att det görs när hakparenteserna `*`bredvid cellen visar ett nummer i stället för en . I följande avsnitt förklaras vad varje cell gör programmässigt och vad som väntar för utdata.

### <a name="include-the-python-modules"></a>Inkludera pythonmodulerna

Inkludera de nödvändiga pythonmodulerna. Cellen har inga utdata.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Ange personalizerresursnyckel och namn

Från Azure-portalen hittar du din nyckel och slutpunkt på **snabbstartssidan** för din Personalizer-resurs. Ändra värdet `<your-resource-name>` på till personalizerresursens namn. Ändra värdet `<your-resource-key>` på din Personalizer-nyckel.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Skriv ut aktuellt datum och aktuell tid
Använd den här funktionen för att notera start- och sluttiderna för den iterativa funktionen, iterationer.

Dessa celler har inga utdata. Funktionen matar ut aktuellt datum och aktuell tid när den anropas.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Hämta den senaste uppdateringstiden för modellen

När funktionen `get_last_updated`, anropas skrivs funktionen ut det senast ändrade datum och den tid då modellen uppdaterades.

Dessa celler har inga utdata. Funktionen matar ut det senaste modellutbildningsdatumet när det anropas.

Funktionen använder ett GET REST API för att [hämta modellegenskaper](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties).

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Hämta princip- och tjänstkonfiguration

Verifiera tjänstens tillstånd med dessa två REST-anrop.

Dessa celler har inga utdata. Funktionen matar ut servicevärdena när de anropas.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Konstruera webbadresser och läsa JSON-datafiler

Denna cell

* skapar webbadresserna som används i REST-anrop
* ställer in säkerhetshuvudet med hjälp av personalizer-resursnyckeln
* ställer in det slumpmässiga utsädet för rank-händelse-ID:t
* läser i JSON-datafilerna
* anropar `get_last_updated` metod - inlärningspolicyn har tagits bort i exempelutdata
* anropar `get_service_settings` metod

Cellen har utdata `get_last_updated` från `get_service_settings` anropet till och funktioner.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Kontrollera att utdata `rewardWaitTime` `modelExportFrequency` är och är båda inställda på 15 sekunder.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Felsöka det första REST-anropet

Den här föregående cellen är den första cellen som ropar till Personalizer. Kontrollera att REST-statuskoden i `<Response [200]>`utdata är . Om du får ett felmeddelande, till exempel 404, men du är säker på att resursnyckeln och namnet är korrekta, laddar du om anteckningsboken.

Se till att antalet kaffe och användare är både 4. Om du får ett felmeddelande kontrollerar du att du har laddat upp alla 3 JSON-filer.

### <a name="set-up-metric-chart-in-azure-portal"></a>Konfigurera måttdiagram i Azure Portal

Senare i den här självstudien visas den långvariga processen med 10 000 begäranden från webbläsaren med en uppdateringstextruta. Det kan vara lättare att se i ett diagram eller som en total summa när den tidskrävande processen avslutas. Om du vill visa den här informationen använder du de mått som medföljer resursen. Du kan skapa diagrammet nu när du har slutfört en begäran till tjänsten och sedan uppdatera diagrammet med jämna mellanrum medan den tidskrävande processen pågår.

1. Välj din Personalizer-resurs i Azure-portalen.
1. I resursnavigeringen väljer du **Mått** under Övervakning.
1. Välj **Lägg till mått**i diagrammet .
1. Resurs- och måttnamnområdet har redan angetts. Du behöver bara välja måttet för **lyckade anrop** och aggregering av **summa**.
1. Ändra tidsfiltret till de senaste 4 timmarna.

    ![Konfigurera måttdiagram i Azure Portal och lägga till mått för lyckade anrop för de senaste 4 timmarna.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Du bör se tre lyckade samtal i diagrammet.

### <a name="generate-a-unique-event-id"></a>Generera ett unikt händelse-ID

Den här funktionen genererar ett unikt ID för varje rankningsanrop. ID används för att identifiera rang och belöning samtalsinformation. Det här värdet kan komma från en affärsprocess, till exempel ett webbvy-ID eller transaktions-ID.

Cellen har inga utdata. Funktionen matar ut det unika ID:et när det anropas.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Få slumpmässig användare, väder och tid på dagen

Den här funktionen väljer en unik användare, väder och tid på dagen och lägger sedan till dessa objekt i JSON-objektet som ska skickas till Rank-begäran.

Cellen har inga utdata. När funktionen kallas returneras den slumpmässiga användarens namn, slumpmässigt väder och slumpmässig tid på dagen.

Listan över 4 användare och deras preferenser - endast vissa preferenser visas för korthet:

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Lägga till alla kaffedata

Den här funktionen lägger till hela listan med kaffe i JSON-objektet som ska skickas till rank-begäran.

Cellen har inga utdata. Funktionen ändrar `rankjsonobj` när den anropas.


Exemplet med en enda kaffe funktioner är:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Jämför förutsägelse med kända användarinställningar

Den här funktionen anropas efter att Rank API anropas för varje iteration.

Den här funktionen jämför användarens preferens för kaffe, baserat på väder och tid på dagen, med Personalizer förslag för användaren för dessa filter. Om förslaget matchar returneras poängen 1, annars är poängen 0. Cellen har inga utdata. Funktionen matar ut poängen när den anropas.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Loop genom samtal till Rank and Reward

Nästa cell är det _huvudsakliga_ arbetet i den bärbara datorn, få en slumpmässig användare, få kaffelistan, skicka både till Rank API. Jämföra förutsägelsen med användarens kända preferenser och sedan skicka tillbaka belöningen till Personalizer-tjänsten.

Slingan `num_requests` körs i flera gånger. Personalizer behöver några tusen samtal till Rank and Reward för att skapa en modell.

Ett exempel på JSON som skickas till Rank API följer. Listan över kaffe är inte komplett, för korthet. Du kan se hela JSON `coffee.json`för kaffe i .

JSON skickas till Rank API:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

JSON svar från Rank API:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Slutligen visar varje slinga slumpmässigt urval av användare, väder, tid på dagen och bestämd belöning. Belöningen 1 anger personalizer-resursen som valts rätt kaffetyp för den angivna användaren, vädret och tiden på dagen.

```console
1 Alice Rainy Morning Latte 1
```

Funktionen använder:

* Rank: en POST REST API för att [få rang](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Belöning: ett POST REST API för att [rapportera belöning](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Kör för 10 000 iterationer
Kör Personalizer-loopen för 10 000 iterationer. Detta är en långvarig händelse. Stäng inte webbläsaren som kör anteckningsboken. Uppdatera måttdiagrammet i Azure-portalen med jämna mellanrum för att se det totala antalet anrop till tjänsten. När du har cirka 20 000 samtal, ett rank- och belöningsanrop för varje iteration av loopen, görs iterationerna.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Diagramresultat för att se förbättring

Skapa ett diagram `count` `rewards`från och .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Kör diagram för 10 000 rankningsbegäranden

Kör `createChart` funktionen.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Läsa diagrammet

Det här diagrammet visar modellens framgång för den aktuella standardutbildningsprincipen.

![Det här diagrammet visar hur framgångsrik den aktuella inlärningsprincipen är för testets varaktighet.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Det idealiska målet att i slutet av testet, är slingan i genomsnitt en framgång som är nära 100 procent minus prospektering. Standardvärdet för prospektering är 20 %.

`100-20=80`

Det här utforskningsvärdet finns i Azure-portalen för Personalizer-resursen på **sidan Konfiguration.**

För att hitta en bättre inlärningspolicy, baserat på dina data till Rank API, kör du en [offlineutvärdering](how-to-offline-evaluation.md) i portalen för din Personalizer-loop.

## <a name="run-an-offline-evaluation"></a>Kör en offlineutvärdering

1. Öppna sidan Personalizer-resursens **utvärderingar** på Azure-portalen.
1. Välj **Skapa utvärdering**.
1. Ange nödvändiga data för utvärderingsnamn och datumintervall för looputvärderingen. Datumintervallet bör bara innehålla de dagar du fokuserar på för utvärderingen.
    ![Öppna sidan Personalizer-resursens utvärderingar på Azure-portalen. Välj Skapa utvärdering. Ange utvärderingsnamn och datumintervall.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Syftet med att köra den här offlineutvärderingen är att avgöra om det finns en bättre inlärningspolicy för de funktioner och åtgärder som används i den här loopen. Om du vill hitta den bättre inlärningsprincipen kontrollerar du att **Optimeringsidentifiering** är aktiverat.

1. Välj **OK** för att påbörja utvärderingen.
1. På sidan **Utvärderingar** visas den nya utvärderingen och dess aktuella status. Beroende på hur mycket data du har kan utvärderingen ta lite tid. Du kan komma tillbaka till den här sidan efter några minuter för att se resultatet.
1. När utvärderingen är klar väljer du utvärderingen och väljer sedan **Jämförelse av olika utbildningsprinciper**. Detta visar tillgängliga utbildningsprinciper och hur de skulle bete sig med data.
1. Välj den översta utbildningsprincipen i tabellen och välj **Använd**. Detta gäller den _bästa_ inlärningspolicyn för din modell och retrains.

## <a name="change-update-model-frequency-to-5-minutes"></a>Ändra uppdateringsmodellfrekvensen till 5 minuter

1. Välj **sidan Konfiguration** i Azure-portalen, som fortfarande finns kvar på Personalizer-resursen.
1. Ändra **modellens uppdateringsfrekvens** och **belöningsväntan till** 5 minuter och välj **Spara**.

Läs mer om [belöningsväntan och](concept-rewards.md#reward-wait-time) [uppdateringsfrekvensen](how-to-settings.md#model-update-frequency)för modellen .

```python
#Verify new learning policy and times
get_service_settings()
```

Kontrollera att utdata `rewardWaitTime` `modelExportFrequency` är och är båda inställda på 5 minuter.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Validera ny utbildningspolitik

Återgå till Azure-anteckningsboken och fortsätt genom att köra samma loop men för endast 2 000 iterationer. Uppdatera måttdiagrammet i Azure-portalen med jämna mellanrum för att se det totala antalet anrop till tjänsten. När du har cirka 4000 samtal, en rang och belöning samtal för varje iteration av slingan, iterationer görs.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Kör diagram för 2 000 rankningsbegäranden

Kör `createChart` funktionen.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Granska det andra diagrammet

Det andra diagrammet bör visa en synlig ökning av rankprognoser som justeras med användarinställningarna.

![Det andra diagrammet bör visa en synlig ökning av rankprognoser som justeras med användarinställningarna.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker fortsätta självstudieserien rensar du följande resurser:

* Ta bort ditt Azure Notebook-projekt.
* Ta bort din Personalizer-resurs.

## <a name="next-steps"></a>Nästa steg

[Den Jupyter-anteckningsbok och datafiler](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) som används i det här exemplet finns på GitHub-repoen för Personalizer.

