---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: dcfa65c655d1508510282fe66e90e4076278b29b
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949972"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Nedanstående kommando startar funktionsappen. Appen körs med samma Azure Functions-körtid som finns i Azure. Start kommandot varierar beroende på ditt projekt språk.

### <a name="c"></a>C\#

```command
func start --build
```

### <a name="javascript"></a>JavaScript

```command
func start
```

### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

När Functions-värden startar skriver den något som följande utdata, som har trunkerats för läsbarhet:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Kopiera URL:en till din funktion `HttpTrigger` från körtidutdatan och klistra in den i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Nedan visas svaret på GET-begäran som returnerades av den lokala funktionen i webbläsaren:

![Testa lokalt i webbläsaren](./media/functions-run-function-test-local/functions-test-local-browser.png)

Nu när du har kört funktionen lokalt kan du skapa funktionsappen och andra nödvändiga resurser i Azure.