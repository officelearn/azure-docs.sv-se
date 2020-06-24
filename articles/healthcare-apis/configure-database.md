---
title: Konfigurera databas inställningar i Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar databas inställningar i Azure API för FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: adc6fdf144927d10f811a00aa33f244cfdc25042
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "84871766"
---
# <a name="configure-database-settings"></a>Konfigurera databas inställningar 

Azure API för FHIR använder databasen för att lagra data. Prestanda för den underliggande databasen beror på antalet enheter för programbegäran (RU) som valts under tjänst etableringen eller i databas inställningarna när tjänsten har etablerats.

Azure API för FHIR lånar begreppet ru: er från Cosmos DB (se enheter för [programbegäran i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/request-units)) när du anger prestanda för underliggande databaser. 

Data flödet måste tillhandahållas för att säkerställa att tillräckligt med system resurser är tillgängliga för din databas hela tiden. Hur många ru: er du behöver för ditt program beror på vilka åtgärder du utför. Åtgärder kan vara från enkel läsning och skrivning till mer komplexa frågor. 

> [!NOTE]
> Eftersom olika åtgärder förbrukar olika antal RU returnerar vi det faktiska antalet ru: er som förbrukas i alla API-anrop i svars huvudet. På så sätt kan du profilera antalet ru: er som används av ditt program.

## <a name="update-throughput"></a>Uppdatera data flöde
Om du vill ändra den här inställningen i Azure Portal navigerar du till ditt Azure API för FHIR och öppnar databas bladet. Ändra sedan det tillhandahållna data flödet till önskat värde beroende på dina prestanda behov. Du kan ändra värdet upp till högst 10 000 RU/s. Kontakta Azure-supporten om du behöver ett högre värde.

> [!NOTE] 
> Högre värde innebär högre Azure API för FHIR-genomflöde och högre kostnad för tjänsten.

![Konfigurations Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du uppdaterar din ru: er för Azure API för FHIR. Distribuera sedan ett fullständigt hanterat Azure API för FHIR:
 
>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)