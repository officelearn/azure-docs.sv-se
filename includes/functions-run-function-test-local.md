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
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988013"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Nedanstående kommando startar funktionsappen. Appen körs med samma Azure Functions-körtid som finns i Azure.

```bash
func host start --build
```

Alternativet `--build` krävs för att kompilera C#-projekt. Du behöver inte det här alternativet för ett JavaScript-projekt.

När funktionsvärden startar skriver den något i stil med följande utdata, som har trunkerats för läsbarhetens skull:

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