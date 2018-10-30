---
title: 'Snabbstart: Skapa kunskapsbas – REST, Python – QnA Maker'
titlesuffix: Azure Cognitive Services
description: I den här REST-baserade snabbstarten går vi igenom hur du skapar ett exempel på QnA Maker-kunskapsbas, programmatiskt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: d8802f1d5188966d08ac6a5fc74d38a704925faf
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646338"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Snabbstart: Skapa en kunskapsbas i QnA Maker med hjälp av Python

Den här snabbstarten går igenom skapandet av ett exempel på QnA Maker-kunskapsbas, programmässigt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

Två exempel på URL:er för vanliga frågor och svar (i **req**-katalogobjektet ”urls”). QnA Maker extraherar automatiskt frågor och svar från delvis strukturerat innehåll, som vanliga frågor och svar, enligt den utförligare beskrivningen i det här dokumentet om [datakällor](../Concepts/data-sources-supported.md). Du kan också använda egna URL:er för vanliga frågor och svar i den här snabbstarten.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Python 3.x](https://www.python.org/downloads/) för att köra koden.

Du måste ha ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **QnA Maker** som vald resurs. Du behöver nyckeln till en betalprenumeration från din [Azure-instrumentpanel](https://ms.portal.azure.com/). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på instrumentpanelen. Båda nycklarna fungerar för den här snabbstarten.

![Tjänstnyckeln för Azure-instrumentpanelen](../media/sub-key.png)

Mer hjälp med Visual Studio och Python: [Work with Python in Visual Studio on Windows](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio) (Arbeta med Python i Visual Studio i Windows).

## <a name="create-knowledge-base"></a>Skapa kunskapsbas

Följande kod skapar en ny kunskapsbas med hjälp av metoden [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Skapa).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `subscriptionKey` med en giltig prenumerationsnyckel.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'YOUR SUBSCRIPTION KEY HERE'

# Represents the various elements used to create HTTP request path
# for QnA Maker operations.
host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'

'''
Formats and indents JSON for display.
:param content: The JSON to format and indent.
:type: string
:return: A string containing formatted and indented JSON.
:rtype: string
'''
def pretty_print(content):
  # Note: We convert content to and from an object so we can pretty-print it.
  return json.dumps(json.loads(content), indent=4)

'''
Sends the POST request to create the knowledge base.
:param path: The URL path being called.
:type: string
:param content: The contents of your POST.
:type: string
:return: A header that creates the knowledge base, the JSON response
:rtype: string, string
'''
def create_kb(path, content):
  print('Calling ' + host + path + '.')
  headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json',
    'Content-Length': len (content)
  }
  conn = http.client.HTTPSConnection(host)
  conn.request ("POST", path, content, headers)
  response = conn.getresponse ()
  # /knowledgebases/create returns an HTTP header named Location that contains a URL
  # to check the status of the operation in creating the knowledge base.
  return response.getheader('Location'), response.read ()

'''
Checks the status of the request to create the knowledge base.
:param path: The URL path being checked
:type: string
:return: The header Retry-After if request is not finished, the JSON response
:rtype: string, string
'''
def check_status(path):
  print('Calling ' + host + path + '.')
  headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
  conn = http.client.HTTPSConnection(host)
  conn.request("GET", path, None, headers)
  response = conn.getresponse ()
  # If the operation is not finished, /operations returns an HTTP header named Retry-After
  # that contains the number of seconds to wait before we query the operation again.
  return response.getheader('Retry-After'), response.read ()

'''
Dictionary that holds the knowledge base.
The data source includes a QnA pair with metadata, the URL for the
QnA Maker FAQ article, and the URL for the Azure Bot Service FAQ article.
'''
req = {
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    }
  ],
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
}

# Builds the path URL.
path = service + method
# Convert the request to a string.
content = json.dumps(req)
# Retrieve the operation ID to check status, and JSON result
operation, result = create_kb(path, content)
# Print request response in JSON with presentable formatting
print(pretty_print(result))

'''
Iteratively gets the operation state, creating the knowledge base.
Once state is no longer "Running" or "NotStarted", the loop ends.
'''
done = False
while False == done:
  path = service + operation
  # Gets the status of the operation.
  wait, status = check_status(path)
  # Print status checks in JSON with presentable formatting
  print(pretty_print(status))

  # Convert the JSON response into an object and get the value of the operationState field.
  state = json.loads(status)['operationState']
  # If the operation isn't finished, wait and query again.
  if state == 'Running' or state == 'NotStarted':
    print('Waiting ' + wait + ' seconds...')
    time.sleep(int(wait))
  else:
    done = True # request has been processed, if successful, knowledge base is created
```

## <a name="understand-what-qna-maker-returns"></a>Förstå vad QnA Maker returnerar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel. Dina resultat kan skilja sig något. Om det sista anropet returnerar statusen ”Lyckades” har kunskapsbasen skapats. Om du vill felsöka går du till [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (Hämta åtgärdsinformation) i API för QnA Maker.

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bt4ebha1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bt4ebfa1aae40fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Press any key to continue.
```

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser). Välj namnet på kunskapsbasen, till exempel QnA Maker Vanliga frågor och svar, för att visa.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)