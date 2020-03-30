---
title: 'SaaS uppfyllelse API: er - FAQ | Azure Marketplace'
description: Identifierings- och inköpsupplevelser av kunderna för ett SaaS-erbjudande på Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275704"
---
# <a name="saas-fulfillment-apis---faq"></a>API:er för uppfyllnad av SaaS – vanliga frågor och svar

Integrationskraven med Azure Marketplace för att göra det möjligt för Azure-kunder att prenumerera på SaaS-erbjudandet visas.

## <a name="discovery-experience"></a>Upplevelse av upptäckt

När erbjudandet har publicerats kan Azure-användare upptäcka SaaS-erbjudandet på Azure Marketplace. Dina kunder kommer att kunna filtrera erbjudanden baserat på produkttyp (SaaS) och upptäcka de SaaS-tjänster de är intresserade av.

## <a name="purchase-experience"></a>Köpupplevelse

När en användare är intresserad av en specifik SaaS-tjänst kan användaren prenumerera på den från Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Vad innebär det för en Azure-användare att prenumerera på ett SaaS-erbjudande på Azure Marketplace?

Det innebär att en användare kan visa användarvillkoren och sekretesspolicyn som är associerade med SaaS-tjänsten och gå med på att betala för den enligt de faktureringsvillkor som du, utgivaren av SaaS-erbjudandet, har angett på Microsofts faktura. Användare kan använda sin befintliga betalningsprofil i Azure för att betala för SaaS-tjänstförbrukningen.

Detta är fördelaktigt av många skäl. Kunder kan nu upptäcka och prenumerera på ett ställe med Microsoft Cloud Platform som en betrodd källa, utan att behöva granska alla ISV-program som den avser att använda. Dessutom kan kunder använda sin befintliga betalningsprofil utan att uttryckligen behöva betala varje ISV-programvara oberoende av varandra.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Debiteras användaren automatiskt när erbjudandet prenumererar?

När användaren prenumererar på SaaS-erbjudandet har han gått med på att betala för konsumtion av SaaS-tjänsten via Microsoft-plattformen. Avgifterna startar dock först när erbjudandet förbrukas. Användaren måste gå till ditt SaaS-erbjudande och bekräfta att kontot skapas för att börja konsumera erbjudandet. Du meddelar sedan Microsoft att börja fakturera för den här kundens SaaS-prenumeration.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Hur meddelas du när en användare prenumererar på ditt SaaS-erbjudande?

När du prenumererar på ett erbjudande kan Azure-användaren upptäcka och hantera alla sina erbjudanden i Azure. Som standard är tillståndet för ett nyligen tecknat SaaS-erbjudande **"Etablering, uppfyllelse väntande".** I det här tillståndet kommer Azure-användaren att uppmanas med en åtgärd för att **konfigurera konto**, för att bläddra till deras SaaS-prenumerationshanteringsupplevelse i Azure-portalen.

När användaren klickar på **"Konfigurera konto"** omdirigeras de till SaaS-tjänstens webbplats. Webbadressen där de navigeras till tillhandahålls av utgivaren vid tidpunkten för publiceringen av erbjudandet. Den här sidan kallas utgivarens målsida. Azure-användare bör kunna logga in på SaaS-målsidan baserat på deras befintliga AAD-autentiseringsuppgifter i Azure.

När Azure-användaren omdirigeras till målsidan läggs en token till i fråge-URL:en. Denna token är kortlivad och giltig under en tid på 24 timmar. Du kan sedan identifiera förekomsten av den här token och anropa Microsofts API för att få mer kontext associerad med token.

![Kundabonnemangsflöde](media/saas-metering-service-integration-flow-a.png)

Mer information om API-kontraktet för hantering av transact-scenarier i livscykeln för ett SaaS-erbjudande finns i API-dokumentet för [SaaS-uppfyllelse.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Hur vet du det SaaS-erbjudande som användaren prenumererar på i Azure?

Svaret på `Resolve` API:et innehåller erbjudande- och abonnemangsinformation som är associerad med SaaS-prenumerationen.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Hur kan Azure-användaren ändra planen som är associerad med den här Azure-prenumerationen?

* Azure-användaren kan ändra planen som är associerad med SaaS-prenumerationen direkt i SaaS-upplevelsen eller via Microsoft-plattformen.

* Konverteringar kan göras när som helst i faktureringsperioden. Du måste bekräfta en konvertering, som kommer att träda i kraft när de har bekräftats.

* Förbetalda plan **(månads-** eller **årspriser)** är proportionella. Eventuell överkörning som släpps ut fram till tidpunkten för konverteringen debiteras i nästa faktura. Nya överutfärder kommer att släppas ut baserat på den nya planen.

>[!Note]
>Du kan blockera nedgraderingar om du inte vill stödja specifika konverteringsvägar.

Sekvensen nedan fångar flödet när en Azure-kund ändrar plan i SaaS-upplevelsen:

![Förändringsflöde för kundplan](media/saas-metering-service-integration-flow-b.png)

Sekvensen nedan fångar flödet när en Azure-kund ändrar plan i Microsofts skyltfönster

![Ändra flöde för kundbutiksplan](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Hur kan Azure-användaren avsluta prenumerationen på planen som är associerad med Azure-prenumeration?

En Azure-användare kan avsluta prenumerationen på ett köpt SaaS-erbjudande antingen direkt i SaaS-upplevelsen eller via Microsoft-plattformen. När användaren har avslutat prenumerationen debiteras de inte längre från och med nästa faktureringsperiod.

Sekvensen nedan fångar flödet när en Azure-kund avslutar prenumerationer på SaaS-erbjudandet i SaaS-upplevelsen:

![Avprenumerering av kunder i SaaS-upplevelsen](media/saas-metering-service-integration-flow-d.png)

Sekvensen nedan fångar flödet när Azure-användare avslutar prenumerationer i Microsofts skyltfönster:

![Avbetald kund i Microsofts skyltfönster](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Nästa steg

- Mer information [finns i API:er](./marketplace-metering-service-apis.md) för tjänsten Marketplace för mer information.
