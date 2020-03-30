---
title: Visa status för marknadsplatserbjudanden | Azure Marketplace
description: Visa status för erbjudanden på Azure- och AppSource Marketplaces med hjälp av Cloud Partner Portal
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275976"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Visa publiceringsstatus för Azure Marketplace- och AppSource-erbjudanden

När du har skapat ett erbjudande, och särskilt under publiceringsprocessen, kan du visa status för ditt erbjudande i Cloud Partner Portal.  Övergripande [**publiceringsstatus**](../portal-tour/cpp-approvals-page.md) är tillgänglig på sidorna [**Alla erbjudanden**](../portal-tour/cpp-all-offers-page.md) och godkännanden på portalen.  En av följande statusindikatorer bör visas för varje erbjudande.  

|            Status              |   Beskrivning                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Erbjudandet har skapats men publiceringsprocessen har inte påbörjats.            |
| **Publicera pågår**        | Erbjudandet arbetar sig igenom stegen i publiceringsprocessen.   |
| **Det gick inte att publicera**             | Ett kritiskt problem upptäcktes under valideringen eller granskningen av Microsoft. |
| **Publicera avbruten**           | Utgivaren har avbrutit erbjudandepubliceringsprocessen.  Det här tillståndet avlistar inte ett befintligt erbjudande på marknadsplatsen. | 
| **Väntar på att utgivaren ska logga ut** | Erbjudandet har granskats av Microsoft och väntar nu på en slutlig verifiering av utgivaren. |
| **Avlistad**                   | Ett tidigare publicerat erbjudande på marknaden har tagits bort.      | 
|  |  |


## <a name="publishing-status-details"></a>Information om publicering av status 

Mer information om statusen för ett erbjudande när publiceringsprocessen går igenom finns på fliken **Status** på sidan **Nytt erbjudande.**  På den här sidan visas alla publiceringssteg för den erbjudandetypen.  *Observera att antalet och specifika steg ofta skiljer sig åt mellan erbjudandetyper.*  Den här sidan visar också eventuella olösta problem som tas upp i Microsofts validerings- och granskningssteg, som ofta kräver åtgärder av utgivaren innan publiceringsprocessen kan fortsätta.  Följande bild visar till exempel fliken **Status** för ett nytt erbjudande om virtuella datorer. 

![Fliken Status för VM-erbjudande](./media/vm-offer-pub-steps1.png)

Nästa exempel **på fliken Status** för en konsulttjänst, som visar ett rapporterat fel i leadhanteringsinställningarna.  Eftersom leadhantering krävs för konsulttjänster måste det här felet korrigeras innan publiceringen kan fortsätta.

![Fliken Status för konsulttjänster som visar fel](./media/consulting-service-error.png)

Den slutliga exempelstatusen för ett Azure-program visar ett kritiskt Microsoft-granskningsproblem.  Den innehåller en snabb länk till Azure DevOps-objektet som innehåller detaljerad information om det här granskningsproblemet.  Mer information finns i [Publicera Azure-programerbjudande](cpp-publish-offer.md).

![Fliken Status för Azure-appen som visar granskningsproblem](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Nästa steg

Om du vill korrigera kvarstående problem eller uppdatera erbjudandeinställningar måste du [uppdatera ett erbjudande](./cpp-update-offer.md). 
