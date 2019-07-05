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
ms.openlocfilehash: 88a4fe8b9f0b477ed851a03742a9957c08b7cbf0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455204"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator.

1. Ange en brytpunkt i funktionskoden och tryck på F5 för att starta funktionsappsprojektet om du vill testa funktionen. Utdata från Core Tools visas på panelen **Terminal**.

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen. Denna URL innehåller funktionsnyckel som skickas till den `code` frågeparameter.

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Körningen pausas när brytpunkten nås.

1. När du fortsätter körningen visas svaret på GET-begäran i webbläsaren nedan:

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Tryck på SKIFT+F5 för att stoppa felsökningen.