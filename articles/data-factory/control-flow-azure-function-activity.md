---
title: Azure-funktion-aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder Azure Function-aktiviteten för att köra en Azure-funktion i Data Factory-pipeline
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: douglasl
ms.openlocfilehash: ef93c62a2e2084a43eeda578c889a568d04db4f1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856906"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Function-aktivitet i Azure Data Factory

Azure Function-aktiviteten kan du köra [Azure Functions](../azure-functions/functions-overview.md) i Data Factory-pipeline. Om du vill köra en Azure-funktion, måste du skapa en länkad tjänst-anslutning och en aktivitet som anger den Azure-funktion som du planerar att köra.

## <a name="azure-function-linked-service"></a>Azure funktion länkad tjänst

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| typ   | Type-egenskapen måste anges till: **AzureFunction** | ja |
| funktionen app-url | URL för Azure Functions-App. Formatet är `https://<accountname>.azurewebsites.net`. Denna URL är värdet under **URL** avsnittet när du visar Funktionsappen i Azure portal  | ja |
| Funktionsnyckel | Åtkomstnyckel för Azure-funktion. Klicka på den **hantera** för respektive funktion och kopiera antingen den **funktionsnyckel** eller **värdnyckeln**. Ta reda på mer här: [Azure Functions HTTP-utlösare och bindningar](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | ja |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Function-aktivitet

| **Egenskap**  | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| namn  | Namnet på aktiviteten i pipelinen  | Sträng | ja |
| typ  | Aktiviteten är 'AzureFunctionActivity' | Sträng | ja |
| Länkad tjänst | Azure-funktion som är länkad tjänst för motsvarande Azure Function-App  | Länkade tjänstreferensen | ja |
| Funktionsnamn  | Namnet på funktionen i Azure Function-App som anropar den här aktiviteten | Sträng | ja |
| metod  | REST API-metoden för funktionsanropet | Sträng typer som stöds: ”GET”, ”efter”, ”PUT”   | ja |
| sidhuvud  | Rubriker som skickas till begäran. Till exempel vill ange språk och typ för en begäran: ”headers”: {”Accept-språk” ”: en-us”, ”Content-Type”: ”application/json”} | Sträng (eller ett uttryck med resultType av sträng) | Nej |
| brödtext  | texten som skickas tillsammans med begäran till funktionen api-metoden  | Sträng (eller uttryck med resultType av sträng).   | Krävs för PUT/POST-metoder |
|   |   |   | |

Se schemat för nyttolasten i begäran i [begäran nyttolast schemat](control-flow-web-activity.md#request-payload-schema) avsnittet.

## <a name="next-steps"></a>Nästa steg

Mer information om aktiviteter i Data Factory i [Pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md).