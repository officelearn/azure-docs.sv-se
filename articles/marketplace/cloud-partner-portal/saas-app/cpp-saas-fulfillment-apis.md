---
title: 'SaaS Techtrends API: er | Azure Marketplace'
description: 'Introducerar versionerna av uppfyllandet API: er som gör det möjligt att integrera din SaaS och erbjuder med Azure Marketplace.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pabutler
ms.openlocfilehash: e7a01af1eba865b0a088b0fa7226273527abd70e
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257408"
---
# <a name="saas-fulfillment-apis"></a>API:er för uppfyllnad av SaaS

> [!IMPORTANT] 
> SaaS erbjuder funktioner har migrerats till den [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Alla nya utgivare måste använda Partner Center för att skapa nya SaaS-erbjudanden och hantering av befintliga erbjudanden.  Aktuella utgivare med SaaS-erbjudanden migreras batchwise från partnerportalen i molnet till Partner Center.  Cloud Partner Portal visar statusmeddelanden för att ange när specifika befintliga erbjudanden har migrerats.
> Mer information finns i [skapa ett nytt SaaS-erbjudande](../../partner-center-portal/create-new-saas-offer.md).

API: er för SaaS betjäna kan oberoende programvaruleverantörer (ISV) för att integrera sina SaaS-program med Azure Marketplace. Dessa API: er kan ISV-program att delta i alla commerce aktiverat kanaler: direct partnerledd (återförsäljare) ledde till fältet.  De är ett krav för att lista transactable SaaS-erbjudanden på Azure Marketplace.

> [!WARNING]
> Den aktuella versionen av detta API är Version 2, som ska användas för alla nya SaaS erbjuder.  Version 1 av API: et är inaktuellt och underhålls för att stödja befintliga erbjudanden.


## <a name="business-model-support"></a>Företagssupport för modellen

Detta API stöder följande företag modellen funktioner; Du kan:

* Ange flera planer för ett erbjudande. De här planerna har olika funktioner och kan skilja sig på olika sätt.
* Ge ett erbjudande om en per webbplats eller en per användare fakturering modellen basis.
* Ange månatliga och årliga (betalas förskott) faktureringsalternativ.
* Ange privat priser till en kund som baseras på en förhandlade företagsavtal.


## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, registrera ditt SaaS-program i den [Azure-portalen](https://ms.portal.azure.com) som beskrivs i [registrera ett Azure AD-program](./cpp-saas-registration.md).  Därefter Använd den senaste versionen av det här gränssnittet för utveckling: [SaaS betjäna API-Version 2](./cpp-saas-fulfillment-api-v2.md).
