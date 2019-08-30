---
title: ta med fil
description: ta med fil
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d47caaf9dbca14364771e0d8ba1e7f6ce16ef2d6
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174664"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator.

1. Ange en brytpunkt i funktionskoden och tryck på F5 för att starta funktionsappsprojektet om du vill testa funktionen. Utdata från Core Tools visas på panelen **Terminal**.

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen. 

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Körningen pausas när brytpunkten nås.

1. När du fortsätter körningen visas svaret på GET-begäran i webbläsaren nedan:

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Tryck på SKIFT+F5 för att stoppa felsökningen.