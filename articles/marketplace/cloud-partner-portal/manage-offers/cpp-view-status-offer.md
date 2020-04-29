---
title: Visa status för Marketplace-erbjudanden | Azure Marketplace
description: Visa statusen för erbjudanden på Azure-och AppSource-Marketplace med hjälp av Cloud Partner Portal
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275976"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Visa publicerings status för Azure Marketplace-och AppSource-erbjudanden

När du har skapat ett erbjudande och särskilt under publicerings processen kan du Visa status för ditt erbjudande i Cloud Partner Portal.  Övergripande publicerings status finns på sidan [**alla erbjudanden**](../portal-tour/cpp-all-offers-page.md) och [**godkännanden**](../portal-tour/cpp-approvals-page.md) i portalen.  En av följande status indikatorer ska visas för varje erbjudande.  

|            Status              |   Beskrivning                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Erbjudandet har skapats men publicerings processen har inte påbörjats.            |
| **Publicering pågår**        | Erbjudandet fungerar på väg genom stegen i publicerings processen.   |
| **Publiceringen misslyckades**             | Ett allvarligt problem upptäcktes under valideringen eller granskningen av Microsoft. |
| **Publicering avbruten**           | Utgivaren har avbrutit erbjudandet publicerings processen.  Det här läget avvisar inte ett befintligt erbjudande i Marketplace. | 
| **Väntar på utloggning av utgivare** | Erbjudandet har granskats av Microsoft och väntar nu på en slutgiltig verifiering av utgivaren. |
| **Har avvisats**                   | Ett tidigare publicerat erbjudande i Marketplace har tagits bort.      | 
|  |  |


## <a name="publishing-status-details"></a>Information om publicerings status 

Mer information om statusen för ett erbjudande när den går igenom publicerings processen finns på fliken **status** på sidan **ny erbjudande** .  Den här sidan visar alla publicerings steg för den här typen av erbjudande.  *Observera att antalet och de olika stegen ofta skiljer sig mellan olika typer av erbjudanden.*  Den här sidan indikerar också eventuella väntande problem som aktive ras av Microsofts verifierings-och gransknings steg, vilket ofta kräver en åtgärd från utgivaren innan publicerings processen kan fortsätta.  Följande bild visar till exempel fliken **status** för ett nytt erbjudande för virtuella datorer. 

![Fliken status för VM-erbjudandet](./media/vm-offer-pub-steps1.png)

Fliken nästa exempel **status** för en konsult tjänst som visar ett rapporterat fel i inställningarna för hantering av lead.  Eftersom lead-hantering krävs för konsult tjänster måste det här felet åtgärdas innan publicering kan fortsätta.

![Fliken status för konsult tjänsten visar fel](./media/consulting-service-error.png)

Den slutliga exempel statusen för ett Azure-program visar ett viktigt problem med Microsoft-granskning.  Den innehåller en snabb länk till det Azure DevOps-objekt som innehåller detaljerad information om det här gransknings problemet.  Mer information finns i [publicera Azure Application-erbjudande](cpp-publish-offer.md).

![Fliken status för Azure app som visar gransknings problem](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Nästa steg

Du måste [Uppdatera ett erbjudande](./cpp-update-offer.md)för att kunna åtgärda utestående problem eller uppdatera erbjudande inställningar. 
