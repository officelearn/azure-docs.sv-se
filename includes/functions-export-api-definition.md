---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534268"
---
## <a name="export-an-api-definition"></a>Exportera en API-definition
Du har en OpenAPI-definition för din funktion, från [skapa en openapi-definition för en funktion](../articles/azure-functions/functions-openapi-definition.md). Nästa steg i den här processen är att exportera API-definitionen så att PowerApps och Microsoft Flow kan använda dem i ett anpassat API.

> [!IMPORTANT]
> Kom ihåg att du måste vara inloggad på Azure med samma autentiseringsuppgifter som du använder för PowerApps och Microsoft Flow klienter. Detta gör att Azure kan skapa det anpassade API: et och göra det tillgängligt för både PowerApps och Microsoft Flow.

1. I [Azure Portal](https://portal.azure.com)klickar du på namnet på Function-appen (t. ex. **Function-demo-Energy**) > **Platform features** > **API-definition**.

    ![API-definition](media/functions-export-api-definition/api-definition.png)

1. Klicka på **Exportera till PowerApps + Flow**.

    ![API-definitionskälla](media/functions-export-api-definition/export-api-1.png)

1. Använd de inställningar som anges i tabellen i den högra rutan.

    |Inställning|Beskrivning|
    |--------|------------|
    |**Export läge**|Välj **Express** för att automatiskt generera det anpassade API: et. Om du väljer **manuell** exporteras API-definitionen, men du måste importera den till PowerApps och Microsoft Flow manuellt. Mer information finns i [Exportera till PowerApps och Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Miljö**|Välj den miljö som det anpassade API: t ska sparas i. Mer information finns i Översikt över [miljöer (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) eller [miljöer (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Anpassat API-namn**|Ange ett namn, t `Turbine Repair`. ex.|
    |**API-nyckelns namn**|Ange det namn som app-och Flow-konstruktörer ska se i det anpassade API-gränssnittet. Observera att exemplet innehåller användbar information.|
 
    ![Exportera till PowerApps och Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Klicka på **OK**. Det anpassade API: et är nu byggt och har lagts till i den miljö som du har angett.