---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61025994"
---
## <a name="export-an-api-definition"></a>Exportera en API-definition
Du har en OpenAPI-definition för din funktion från [skapa en OpenAPI-definition för en funktion](../articles/azure-functions/functions-openapi-definition.md). Nästa steg i den här processen är att exportera API-definition så att PowerApps och Microsoft Flow kan använda ett anpassat API.

> [!IMPORTANT]
> Kom ihåg att du måste vara inloggad på Azure med samma autentiseringsuppgifter som du använder för dina PowerApps och Microsoft Flow-innehavare. Detta gör att Azure för att skapa anpassade API och göra den tillgänglig för både PowerApps och Microsoft Flow.

1. I den [Azure-portalen](https://portal.azure.com), klickar du på din funktionsappens namn (t.ex. **function-demo-energy**) > **plattformsfunktioner** > **API-definition** .

    ![API-definition](media/functions-export-api-definition/api-definition.png)

1. Klicka på **exportera till PowerApps + Flow**.

    ![API-definitionskälla](media/functions-export-api-definition/export-api-1.png)

1. Använd inställningarna som anges i tabellen i den högra rutan.

    |Inställning|Beskrivning|
    |--------|------------|
    |**Exportläge**|Välj **Express** att automatiskt generera din anpassade API. Att välja **manuell** export av API-definition, men du måste importera den till PowerApps och Microsoft Flow manuellt. Mer information finns i [exportera till PowerApps och Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Miljö**|Välj den miljö som anpassade API ska sparas. Mer information finns i [översikt över miljöer (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) eller [översikt över miljöer (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Namn på anpassat API**|Ange ett namn som `Turbine Repair`.|
    |**API-nyckelnamn**|Ange det namn som appar och flöden bör finns i anpassat API-gränssnitt. Observera att exemplet innehåller användbar information.|
 
    ![Exportera till PowerApps och Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Klicka på **OK**. Anpassade API är nu skapats och läggs till i den miljö som du har angett.