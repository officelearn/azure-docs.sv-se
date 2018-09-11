---
title: 'Snabbstart: Node.js för QnA Maker API (V4)'
titleSuffix: Azure Cognitive Services
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med Microsoft Translator Text API i Microsoft Cognitive Services på Azure.
services: cognitive-services
author: nitinme
manager: cgronlun
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jaswel
ms.openlocfilehash: 1930315cae62081dae364d63e6b26ec26a69c654
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44349015"
---
# <a name="quickstart-for-microsoft-qna-maker-api-with-nodejs"></a>Snabbstart för Microsoft QnA Maker API med Node.js 
<a name="HOLTop"></a>

Den här artikeln visar hur du använder den [Microsoft QnA Maker API](../Overview/overview.md) med Node.js för att göra följande.

- [Skapa en ny kunskapsbas.](#Create)
- [Uppdatera en befintlig kunskapsbas.](#Update)
- [Hämta status för en begäran om att skapa eller uppdatera en kunskapsbas.](#Status)
- [Publicera en befintlig kunskapsbas.](#Publish)
- [Ersätt innehållet i en befintlig kunskapsbas.](#Replace)
- [Ladda ned innehållet i en kunskapsbas.](#GetQnA)
- [Få svar på en fråga med en kunskapsbas.](#GetAnswers)
- [Få information om en kunskapsbas.](#GetKB)
- [Få information om alla kunskapsbaser som hör till den angivna användaren.](#GetKBsByUser)
- [Ta bort en kunskapsbas.](#Delete)
- [Hämta de aktuella endpoint-nycklarna.](#GetKeys)
- [Återskapa de aktuella endpoint-nycklarna.](#PutKeys)
- [Hämta den aktuella uppsättningen word förändras.](#GetAlterations)
- [Ersätt den aktuella uppsättningen word förändras.](#PutAlterations)

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Node.js 6](https://nodejs.org/en/download/) att köra den här koden.

Du måste ha en [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **Microsoft QnA Maker API**. Du behöver en betald prenumerationsnyckel från din [Azure-instrumentpanelen](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Skapa kunskapsbas

Följande kod skapar en ny knowledge base med hjälp av den [skapa](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/create';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases/create POST method.
let create_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the POST request.
    post (path, req, function (response) {
// Extract the data we want from the POST response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

// callback is the function to call when we have the response from the GET request to check the status.
let check_status = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ wait : response.headers['retry-after'], response : response.body });
    });
}

let req = {
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
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
};

var path = service + method;
// Convert the request to a string.
let content = JSON.stringify(req);
create_kb (path, content, function (result) {
// Write out the response from the /knowledgebases/create method.
    console.log (pretty_print(result.response));
// Loop until the operation is complete.
    let loop = function () {
        path = service + result.operation;
// Check the status of the operation.
        check_status (path, function (status) {
// Write out the status.
            console.log (pretty_print(status.response));
// Convert the status into an object and get the value of the operationState field.
            var state = (JSON.parse(status.response)).operationState;
// If the operation isn't complete, wait and query again.
            if (state == 'Running' || state == 'NotStarted') {
                console.log ('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    }
// Begin the loop.
    loop();
});
```

**Skapa kunskapsbas svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

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

Följande kod uppdaterar en grundläggande, med hjälp av befintliga kunskaper i [uppdatering](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the PATCH request.
let patch = function (path, content, callback) {
    let request_params = {
        method : 'PATCH',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases PATCH method.
let update_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the PATCH request.
    patch (path, req, function (response) {
// Extract the data we want from the PATCH response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

// callback is the function to call when we have the response from the GET request to check the status.
let check_status = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ wait : response.headers['retry-after'], response : response.body });
    });
}

let req = {
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
      'https://docs.microsoft.com/en-us/azure/cognitive-services/Emotion/FAQ'
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
};

var path = service + method + kb;
// Convert the request to a string.
let content = JSON.stringify(req);
update_kb (path, content, function (result) {
    console.log (pretty_print(result.response));
// Loop until the operation is complete.
    let loop = function () {
        path = service + result.operation;
// Check the status of the operation.
        check_status (path, function (status) {
// Write out the status.
            console.log (pretty_print(status.response));
// Convert the status into an object and get the value of the operationState field.
            var state = (JSON.parse(status.response)).operationState;
// If the operation isn't complete, wait and query again.
            if (state == 'Running' || state == 'NotStarted') {
                console.log ('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    }
// Begin the loop.
    loop();
});
```

**Uppdatera kunskapsbas svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

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

Du kan anropa den [åtgärden](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) metoden för att kontrollera status för en begäran om att skapa eller uppdatera en kunskapsbas. Om du vill se hur den här metoden används, se exempelkod för den [skapa](#Create) eller [uppdatering](#Update) metod.

[Överst på sidan](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Publicera kunskapsbas

Följande kod publicerar en grundläggande, med hjälp av befintliga kunskaper i [publicera](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases POST method.
let publish_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the POST request.
    post (path, req, function (response) {
// Extract the data we want from the POST response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

var path = service + method + kb;
publish_kb (path, '', function (result) {
    console.log (pretty_print(result));
});
```

**Publicera kunskapsbas svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
  "result": "Success."
}
```

[Överst på sidan](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Ersätt kunskapsbas

Följande kod ersätter innehållet i den angivna kunskapen kodbas, med hjälp av den [Ersätt](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_publish) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the PUT request.
let put = function (path, content, callback) {
    let request_params = {
        method : 'PUT',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases PUT method.
let replace_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the PUT request.
    put (path, req, function (response) {
// Extract the data we want from the PUT response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

let req = {
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
};

var path = service + method + kb;
// Convert the request to a string.
let content = JSON.stringify(req);
replace_kb (path, content, function (result) {
    console.log (pretty_print(result));
});
```

**Ersätt kunskapsbas svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
    "result": "Success."
}
```

[Överst på sidan](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Ladda ned innehållet i en kunskapsbas

Följande kod laddar ned innehållet i den angivna kunskapen kodbas, med hjälp av den [hämta kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_download) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

// NOTE: Replace this with "test" or "prod".
let env = "test";

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = `/knowledgebases/${kb}/${env}/qna/`;

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_qna = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method;
get_qna (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Ladda ned kunskapsbas svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

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
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
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

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Få svar på en fråga med en kunskapsbas

Följande kod hämtar svar på en fråga med hjälp av angivna kunskapsbasen, med den **Generera svar** metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
1. Lägg till koden nedan.
1. Ersätt den `host` värdet med namnet på webbplatsen för QnA Maker-prenumeration. Mer information finns i [skapa QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md).
1. Ersätt den `endpoint_key` värde med en giltig slutpunkt-nyckel för din prenumeration. Observera att detta inte är samma som din prenumerationsnyckel. Du kan hämta din slutpunkt-nycklar med hjälp av den [hämta slutpunkten nycklar](#GetKeys) metod.
1. Ersätt den `kb` värdet med ID: T för kunskapsbas som du vill fråga efter svar. Observera att den här kunskapsbas måste redan har publicerats med hjälp av den [publicera](#Publish) metod.
1. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// NOTE: Replace this with a valid host name.
let host = "ENTER HOST HERE";

// NOTE: Replace this with a valid endpoint key.
// This is not your subscription key.
// To get your endpoint keys, call the GET /endpointkeys method.
let endpoint_key = "ENTER KEY HERE";

// NOTE: Replace this with a valid knowledge base ID.
// Make sure you have published the knowledge base with the
// POST /knowledgebases/{knowledge base ID} method.
let kb = "ENTER KB ID HERE";

let method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer";

let question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
};

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Authorization' : 'EndpointKey ' + endpoint_key,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases POST method.
let get_answers = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the POST request.
    post (path, req, function (response) {
        callback (response.body);
    });
}

// Convert the request to a string.
let content = JSON.stringify(question);
get_answers (method, content, function (result) {
// Write out the response from the /knowledgebases/create method.
    console.log (pretty_print(result));
});
```

**Få svar-svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

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
      "source": "https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Överst på sidan](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Få information om en kunskapsbas

Följande kod hämtar information om den angivna knowledge base med hjälp av den [kunskapsbas information](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_kb = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method + kb;
get_kb (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Hämta kunskapsbas information svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Överst på sidan](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Hämta alla kunskapsbaser för en användare

Följande kod hämtar information om alla kunskapsbaser för en viss användare med hjälp av den [hämta kunskapsbaser för användaren](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasesforuser) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_kbs = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method;
get_kbs (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Hämta kunskapsbaser för användarsvar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

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
        "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
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

Följande kod tar du bort den angivna kunskapen kodbas, med hjälp av den [ta bort kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_delete) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the DELETE request.
let http_delete = function (path, content, callback) {
    let request_params = {
        method : 'DELETE',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases DELETE method.
let delete_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the DELETE request.
    http_delete (path, req, function (response) {
// Extract the data we want from the DELETE response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

var path = service + method + kb;
delete_kb (path, '', function (result) {
    console.log (pretty_print(result));
});
```

**Ta bort kunskapsbas svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
  "result": "Success."
}
```

[Överst på sidan](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Hämta slutpunkten nycklar

Följande kod hämtar de aktuella endpoint-nycklar med den [hämta slutpunkten nycklar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/endpointkeys/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_keys = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method;
get_keys (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Hämta slutpunkten nycklar svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Överst på sidan](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Uppdatera slutpunkten nycklar

Följande kod återskapar de aktuella endpoint-nycklar med den [uppdatera slutpunkten nycklar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_refreshendpointkeys) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with "PrimaryKey" or "SecondaryKey."
let key_type = "PrimaryKey";

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/endpointkeys/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the PATCH request.
let patch = function (path, content, callback) {
    let request_params = {
        method : 'PATCH',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases PATCH method.
let refresh_keys = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the PATCH request.
    patch (path, req, function (response) {
// Extract the data we want from the PATCH response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

let req = {
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
};

var path = service + method + key_type;
// Convert the request to a string.
let content = JSON.stringify(req);
refresh_keys (path, content, function (result) {
    console.log (pretty_print(result));
});
```

**Uppdatera slutpunkten nycklar svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Överst på sidan](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Hämta word ändringar

Följande kod hämtar de aktuella word-ändringar, med hjälp av den [hämta förändras](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/alterations/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_alterations = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method;
get_alterations (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Hämta word förändras svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

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

## <a name="replace-word-alterations"></a>Ersätt word ändringar

Följande kod ersätter de aktuella word-ändringar, med hjälp av den [Ersätt förändras](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) metod.

1. Skapa ett nytt Node.js-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/alterations/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the PUT request.
let put = function (path, content, callback) {
    let request_params = {
        method : 'PUT',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases PUT method.
let put_alterations = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the PUT request.
    put (path, req, function (response) {
// Extract the data we want from the PUT response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

let req = {
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
};

var path = service + method;
// Convert the request to a string.
let content = JSON.stringify(req);
put_alterations (path, content, function (result) {
    console.log (pretty_print(result));
});
```

**Ersätt word förändras svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
  "result": "Success."
}
```

[Överst på sidan](#HOLTop)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

## <a name="see-also"></a>Se också 

[Översikt över QnA Maker](../Overview/overview.md)
