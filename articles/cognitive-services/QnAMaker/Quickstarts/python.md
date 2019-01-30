---
title: REST-API (V4) – Python – QnA Maker
titleSuffix: Azure Cognitive Services
description: Hämta Python information och exempel på kod som hjälper dig att snabbt komma igång med Microsoft Translator Text API i Microsoft Cognitive Services på Azure.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: ca0d8bcbf2d4b050560d1575ef690205eacca830
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222175"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-python"></a>Hur du använder REST-API för QnA Maker med Python
<a name="HOLTop"></a>

Den här artikeln visar hur du använder den [Microsoft QnA Maker API](../Overview/overview.md) med Python för att göra följande.

- [Skapa en ny kunskapsbas.](#Create)
- [Uppdatera en befintlig kunskapsbas.](#Update)
- [Hämta statusen på en begäran för att skapa eller uppdatera en kunskapsbas.](#Status)
- [Publicera en befintlig kunskapsbas.](#Publish)
- [Ersätta innehållet i en befintlig kunskapsbas.](#Replace)
- [Ladda ned innehållet i en kunskapsbas.](#GetQnA)
- [Få svar på en fråga med hjälp av en kunskapsbas.](#GetAnswers)
- [Få information om en kunskapsbas.](#GetKB)
- [Få information om alla kunskapsbaser som hör till den angivna användaren.](#GetKBsByUser)
- [Ta bort en kunskapsbas.](#Delete)
- [Hämta de aktuella slutpunktsnycklarna.](#GetKeys)
- [Återskapa de aktuella slutpunktsnycklarna.](#PutKeys)
- [Hämta den aktuella uppsättningen med ordvariationer.](#GetAlterations)
- [Ersätta den aktuella uppsättningen med ordvariationer.](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Python 3.x](https://www.python.org/downloads/) för att köra koden.

Du behöver ett [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **Microsoft QnA Maker API**. Du behöver prenumerationsnyckeln för en betalprenumeration från din [Azure-instrumentpanel](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Skapa en kunskapsbas

Följande kod skapar en ny kunskapsbas med hjälp av metoden [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Skapa).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def create_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()
# /knowledgebases/create returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read ()

def check_status (path):
    print ('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read ()

req = {
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
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

path = service + method
# Convert the request to a string.
content = json.dumps(req)
operation, result = create_kb (path, content)
print (pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status (path)
    print (pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print ('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True
```

**Svar från Skapa kunskapsbas**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Överst på sidan](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Uppdatera kunskapsbas

Följande kod uppdaterar en befintlig kunskapsbas med hjälp av metoden [Update](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) (Uppdatera).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def update_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()
# PATCH /knowledgebases returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read ()

def check_status (path):
    print ('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read ()

req = {
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': [
      'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
    ]
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
operation, result = update_kb (path, content)
print (pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status (path)
    print (pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print ('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True
```

**Svar från Uppdatera kunskapsbas**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Överst på sidan](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Hämta status för begäran

Du kan anropa den metoden [Operation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (Åtgärd) för att kontrollera status för en begäran om att skapa eller uppdatera en kunskapsbas. Om du vill se hur den här metoden används kan du titta på exempelkoden för metoden [Create](#Create) (Skapa) eller [Update](#Update) (Uppdatera).

[Överst på sidan](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Publicera kunskapsbas

Följande kod publicerar en befintlig kunskapsbas med hjälp av metoden [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) (Publicera).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def publish_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = publish_kb (path, '')
print (pretty_print(result))
```

**Svar från Publicera kunskapsbas**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "result": "Success."
}
```

[Överst på sidan](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Ersätt kunskapsbas

Följande kod ersätter innehållet i den angivna kunskapsbasen med hjälp av metoden [Ersätt](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_publish).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def replace_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PUT", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

req = {
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my Knowledge Base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ]
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
result = replace_kb (path, content)
print (pretty_print(result))
```

**Svar från Ersätt kunskapsbas**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
    "result": "Success."
}
```

[Överst på sidan](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Ladda ned innehållet i en kunskapsbas

Följande kod laddar ned innehållet i den angivna kunskapsbasen med hjälp av metoden [Ladda ned kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_download).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

# Replace this with "test" or "prod".
env = 'test';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/{0}/{1}/qna/'.format(kb, env);

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_qna (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_qna (path)
print (pretty_print(result))
```

**Svar från Ladda ned kunskapsbas**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
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
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Överst på sidan](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Få svar på en fråga med hjälp av en kunskapsbas

Följande kod hämtar svaret på en fråga med hjälp av dne angivna kunskapsbasen, med hjälp av metoden **Generera svar**.

1. Skapa ett nytt Python-projekt i valfri IDE.
1. Lägg till koden nedan.
1. Ersätt värdet `host` med webbplatsnamnet för din QnA Maker-prenumeration. Mer information finns i [Skapa en QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md).
1. Ersätt `endpoint_key`-värdet med en giltig slutpunktsnyckel för din prenumeration. Observera att detta inte är samma som din prenumerationsnyckel. Du kan hämta dina slutpunktsnycklar med hjälp av metoden [Hämta slutpunktsnycklar](#GetKeys).
1. Ersätt `kb`-värdet med ID:t för den kunskapsbas som du vill få svar från. Observera att den här kunskapsbasen redan måste ha publicerats med hjälp av metoden [Publicera](#Publish).
1. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# NOTE: Replace this with a valid host name.
host = "ENTER HOST HERE"

# NOTE: Replace this with a valid endpoint key.
# This is not your subscription key.
# To get your endpoint keys, call the GET /endpointkeys method.
endpoint_key = "ENTER KEY HERE"

# NOTE: Replace this with a valid knowledge base ID.
# Make sure you have published the knowledge base with the
# POST /knowledgebases/{knowledge base ID} method.
kb = "ENTER KB ID HERE"

method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer"

question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_answers (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Authorization': 'EndpointKey ' + endpoint_key,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()
    return response.read ()

# Convert the request to a string.
content = json.dumps(question)
result = get_answers (method, content)
print (pretty_print(result))
```

**Svar från Hämta svar**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Överst på sidan](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Få information om en kunskapsbas

Följande kod hämtar information om den angivna kunskapsbasen med hjälp av metoden [Hämta information om kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_kb (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method + kb
result = get_kb (path)
print (pretty_print(result))
```

**Svar från Hämta information om kunskapsbas**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Överst på sidan](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Hämta alla kunskapsbaser för en användare

Följande kod hämtar information om alla kunskapsbaser för en viss användare med hjälp av metoden [Hämta kunskapsbaser för användare](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasesforuser).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_kbs (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_kbs (path)
print (pretty_print(result))
```

**Svar från Hämta kunskapsbaser för användare**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Överst på sidan](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Följande kod tar bort den angivna kunskapsbasen med hjälp av metoden [Ta bort kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_delete).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def delete_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("DELETE", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = delete_kb (path, '')
print (pretty_print(result))
```

**Svar från Ta bort kunskapsbas**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "result": "Success."
}
```

[Överst på sidan](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Hämta slutpunktsnycklar

Följande kod hämtar de aktuella slutpunktsnycklarna med hjälp av metoden [Hämta slutpunktsnycklar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_keys (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_keys (path)
print (pretty_print(result))
```

**Svar från Hämta slutpunktsnycklar**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Överst på sidan](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Uppdatera slutpunktsnycklar

Följande kod återskapar de aktuella slutpunktsnycklarna med hjälp av metoden [Uppdatera slutpunktsnycklar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_refreshendpointkeys).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with "PrimaryKey" or "SecondaryKey."
key_type = "PrimaryKey";

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def refresh_keys (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + key_type
result = refresh_keys (path, '')
print (pretty_print(result))
```

**Svar från Uppdatera slutpunktsnycklar**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Överst på sidan](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Hämta ordvariationer

Följande kod hämtar de aktuella ordvariationerna med hjälp av metoden [Ladda ned variationer](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_alterations (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_alterations (path)
print (pretty_print(result))
```

**Svar från Hämta ordvariationer**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Överst på sidan](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Ersätt ordvariationer

Följande kod ersätter de aktuella ordvariationerna med hjälp av metoden [Ersätt variationer](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd).

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def put_alterations (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PUT", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

req = {
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
result = put_alterations (path, content)
print (pretty_print(result))
```

**Svar från Ersätt ordvariationer**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "result": "Success."
}
```

[Överst på sidan](#HOLTop)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

## <a name="see-also"></a>Se också 

[Översikt över QnA Maker](../Overview/overview.md)
