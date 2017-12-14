---
title: "Ändra Azure-prenumerationserbjudande | Microsoft Docs"
description: "Lär dig mer om hur du ändrar din Azure-prenumeration och växla till ett annat erbjudande med hjälp av Azure Account Center"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 48f5f4db60c104778a6c391d254e0bc0c4c95cee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Ändra din betala per användning för Azure-prenumeration till ett annat erbjudande

Som en [betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) kund, kan du växla din Azure-prenumeration till ett annat erbjudande i den [Kontocenter](https://account.windowsazure.com/Subscriptions). Du kan till exempel använda den här funktionen dra nytta av den [månadskrediter för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**Bara vill uppgradera från den kostnadsfria utvärderingsversionen?** Se [uppgradera till betala per användning](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Det här stöds:

| Från | Till |
| --- | --- |
| Betala per användning |[Betala per användning utveckling och testning](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Betala per användning |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Betala per användning |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Betala per användning |[MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Betala per användning |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Betala per användning |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> För andra erbjudande ändringar [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Switch-erbjudande för prenumerationen

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Logga in på [Azure Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Välj din Betala per användning-prenumeration.
1. Klicka på **Växla till ett annat erbjudande**. Knappen är bara tillgänglig om du använder Betala per användning och har gjort din första faktureringsperiod.

   ![Lägg märke till knappen Växla erbjudande till höger på sidan](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Välj önskat erbjudande** från listan över erbjudanden prenumerationen kan växlas till. Den här listan varierar beroende på medlemskap som ditt konto är associerad med. Om inget är tillgänglig kontrollerar du den [lista över tillgängliga erbjudanden som du kan växla till](#whats-supported) och kontrollera att du har rätt medlemskap. 

   ![Välj ett erbjudande som du vill växla till](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Beroende på det erbjudande du växlar till kan du se en anteckning om effekten av växlar. Gå igenom listan noggrant och följ instruktionerna innan du fortsätter.

   ![Granska anteckningarna](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Du kan byta namn på din prenumeration. Vi som standard den till ett nytt erbjudandenamn. Klicka på **växel erbjuder** att slutföra processen.

   ![Klicka på den gröna knappen](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Lyckades! Din prenumeration är nu växlas till nya erbjudandet.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-is-an-azure-offer"></a>Vad är en Azure-erbjudande?

En Azure-erbjudande är den *typen* av Azure-prenumerationen du har. Till exempel [betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure i Open](https://azure.microsoft.com/offers/ms-azr-0111p/), och [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) alla Azure-erbjudanden. Varje erbjudandet har olika [villkoren](https://azure.microsoft.com/support/legal/offer-details/) och några har särskilda fördelar. Erbjudande för prenumerationen kan hittas på sidan Kontocenter prenumeration. Klicka på namnet för erbjudande att få mer information.

   ![Klicka på länken erbjudandet i Account Center för att få mer information](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Varför visas inte knappen?

Du kanske inte se det **växla till ett annat erbjudande** om:

* Du inte är på [betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/). För närvarande endast betala per användning prenumerationer kan konverteras till ett annat erbjudande.
  * Om du använder [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/), Lär dig hur du [uppgradera till betala per användning](billing-upgrade-azure-subscription.md).
  * Växla erbjudande från en annan prenumeration [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Du håller fortfarande på din första faktureringsperioden; Du måste vänta tills den första faktureringsperioden avslutas innan du kan växla erbjudanden.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Varför ser jag ”det finns inga erbjudanden i ditt land just nu”?

* Du kanske inte berättigad till några erbjudande växlar. Kontrollera den [lista över tillgängliga erbjudanden som du kan växla till](#whats-supported) och se till att du har aktiverat rätt fördelarna med Visual Studio eller Bizspark.
* Vissa erbjudanden kanske inte tillgänglig i alla länder.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Vad gör växla Azure erbjuder gör att min tjänst och fakturering?

Här följer information om vad som händer när du växlar Azure erbjuder mitt konto.

#### <a name="no-service-downtime"></a>Utan avbrott i tjänsten

Det finns ingen tjänst avbrottstid för användare som är associerade med prenumerationen. Erbjudandet du växla till kan dock ha begränsningar. Vissa erbjudanden förhindrar till exempel produktion, så måste du flytta produktionsresurser till en annan prenumeration.

#### <a name="quota-increases-are-reset"></a>Kvoten ökar återställs

När du växlar erbjudanden alla [gräns eller kvoten ökar över Standardgränsen](../azure-supportability/resource-manager-core-quotas-request.md) återställs. Det finns utan avbrott i tjänsten, även om du har flera resurser utanför Standardgränsen. Till exempel du använder 200 kärnor i prenumerationen och sedan växla erbjudanden återställs din kärnor kvot till standardvärdet 20 kärnor. Virtuella datorer som använder 200 kärnor påverkas inte och kan fortsätta att köras. Om du inte gör en annan kvot öka begäran, men kan inte du etablera alla flera kärnor.

#### <a name="billing"></a>Fakturering

På dag du växla, skapas en faktura för alla utestående avgifter. Sedan debiteras prenumerationen per nya erbjudandet prisnivå villkoren. Din prenumeration fakturering årsdagar ändras till det datum då du har ändrat erbjudanden. Användnings- och fakturering data innan ändringen erbjudande inte behålls, så vi rekommenderar att du hämtar en kopia innan byte.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Kan jag migrera från betala per användning för [leverantör](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) eller [Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Om du vill migrera till CSP finns [Azure Provideradresser-som-du-Go Prenumerationsmigrering till CSP](https://docs.microsoft.com/en-us/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Om du vill migrera till EA har registrering administratören lägga till ditt konto i Enterprise-avtalet. Följ anvisningarna i e-postinbjudan ha dina prenumerationer under EA registrering flyttas. Läs mer i [koppla ett befintligt konto](https://ea.azure.com/helpdocs/associateExistingAccount) i EA-portalen.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Kan jag migrera data och tjänster till en ny prenumeration?

* Du kan migrera resurserna direkt till den nya prenumerationen, se [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).
* Om du vill överföra ägarskap för en Azure-prenumeration och allt innehåll i den till någon annan finns [överföra ägarskap för en Azure-prenumeration](billing-subscription-transfer.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
