---
title: Ändra Azure-prenumerationserbjudande
description: Läs mer om hur du ändrar din Azure-prenumeration och växla till ett annat erbjudande med hjälp av Azure Kontocenter.
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8e87bcb9f7d05fd5041623693c96f8c737758c32
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490988"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Ändra din Azure-prenumeration till ett annat erbjudande

Som en kund med en [enskild prenumeration med användningsbaserad betalning](https://azure.microsoft.com/offers/ms-azr-0003p/), kan du växla din Azure-prenumeration till ett annat erbjudande i den [Kontocenter](https://account.windowsazure.com/Subscriptions). Du kan till exempel använda den här funktionen för att dra nytta av den [månadskrediter för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Bara vill uppgradera från den kostnadsfria utvärderingsversionen?** Se [uppgradera din prenumeration](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Vad som stöds:

Du kan växla från en enskild prenumeration med användningsbaserad betalning till:

- [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> För andra ändringar i erbjudandet, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Byt prenumerationserbjudande

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Logga in på [Azure Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Välj din enskilda prenumeration med användningsbaserad betalning.
1. Klicka på **Växla till ett annat erbjudande**. Alternativet är endast tillgängligt om du har en enskild prenumeration med betala per användning och har slutfört din första faktureringsperiod.

   ![Lägg märke till knappen Växla erbjudande till höger på sidan](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Välj önskat erbjudande** från listan över erbjudanden prenumerationen kan växlas till. Den här listan varierar beroende på vilka medlemskap ditt konto är associerat med. Om inget är tillgängligt kontrollerar du den [lista över tillgängliga erbjudanden som du kan växla till](#whats-supported) och kontrollera att du har rätt medlemskap.

   ![Välj ett erbjudande som du vill växla till](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Beroende på erbjudandet du växlar till, kan du se om effekten av att växla. Gå igenom listan noggrant och följ instruktionerna innan du fortsätter.

   ![Läser du igenom anteckningarna](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Du kan byta namn på din prenumeration. Som standard är den inställd till ett nytt erbjudandenamn. Klicka på **växel erbjuder** att slutföra processen.

   ![Klicka på den gröna knappen](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Lyckades! Din prenumeration nu växlas till det nya erbjudandet.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
I följande avsnitt besvara vanliga frågor.

### <a name="what-is-an-azure-offer"></a>Vad är en Azure-erbjudande?

Ett Azure-erbjudande är den *typ* för Azure-prenumerationen du har. Till exempel [en prenumeration med användningsbaserad betalning](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure i Open](https://azure.microsoft.com/offers/ms-azr-0111p/), och [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) är alla Azure-erbjudanden. Varje erbjudande har olika [villkoren](https://azure.microsoft.com/support/legal/offer-details/) och några har särskilda förmåner. Erbjudandet om din prenumeration finns på sidan Kontocenter prenumeration. Klicka på namnet för erbjudandet att få mer information.

   ![Klicka på länken erbjudandet i Kontocentret för att få mer information](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Varför visas inte knappen?

Du kanske inte ser den **växla till ett annat erbjudande** om:

* Du behöver inte en [prenumeration med användningsbaserad betalning](https://azure.microsoft.com/offers/ms-azr-0003p/). För närvarande bara prenumerationer med användningsbaserad betalning kan konverteras till ett annat erbjudande.
  * Om du har en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/), Lär dig hur du [uppgradering till betala per användning](billing-upgrade-azure-subscription.md).
  * Att byta erbjudande från en annan prenumeration [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Du är fortfarande i din första faktureringsperiod; Du måste vänta tills din första faktureringsperiod avslutas innan du kan växla erbjudanden.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Varför ser jag ”det finns inga erbjudanden tillgängliga i ditt land just nu”?

* Du kanske inte berättigade till några erbjudandet växlar. Kontrollera den [lista över tillgängliga erbjudanden som du kan växla till](#whats-supported) och se till att du har aktiverat rätt fördelarna med Visual Studio eller Bizspark.
* Vissa erbjudanden som inte är tillgängliga i alla länder/regioner.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Vad kostar växla Azure-erbjudanden gör till min tjänst och fakturering?

Här följer information om vad som händer när du växlar Azure erbjuder i Kontocenter.

#### <a name="no-service-downtime"></a>Inga driftstopp för tjänsten

Det finns inga driftstopp för tjänsten för alla användare som är associerade med prenumerationen. Erbjudandet du växla till kanske begränsningar. Exempelvis förhindrar vissa erbjudanden användning i produktion, så skulle du behöva flytta produktionsresurser till en annan prenumeration.

#### <a name="quota-increases-are-reset"></a>Ökade kvoter återställs

När du växlar betalning, eventuella [gräns eller kvot ökar ovan Standardgränsen](../azure-supportability/resource-manager-core-quotas-request.md) återställs. Det finns inga driftstopp för tjänsten, även om du har fler resurser utöver Standardgränsen. Till exempel 200 kärnor använder på din prenumeration och sedan växla erbjudanden återställer din kärnkvot till standardvärdet 20 kärnor. De virtuella datorer som använder 200 kärnor påverkas inte och kan fortsätta att köras. Om du inte gör en annan kvot öka begäran, men kan inte du etablera några fler kärnor.

#### <a name="billing"></a>Fakturering

På den dag du växla, genereras en faktura för alla utestående debiteringar. Sedan debiteras din prenumeration per prissättning villkoren för det nya erbjudandet. Din prenumeration förfallodag ändras till det datum som du har ändrat erbjudanden. Användning och fakturering data innan ändringen erbjudandet inte bevaras, så vi rekommenderar att du hämtar en kopia innan du växlar.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Kan jag migrera från en prenumeration med användningsbaserad betalning till [Molnlösningsleverantör](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) eller [Företagsavtal](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Om du vill migrera till CSP: N, se [Azure betala per användning-Prenumerationsmigrering till CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Om du vill migrera till EA, har du din lägga till ditt konto i EA-Registreringsadministratör. Följ anvisningarna i e-postinbjudan för att ha dina prenumerationer under EA-avtal flyttas. Mer information finns i [koppla ett befintligt konto](https://ea.azure.com/helpdocs/associateExistingAccount) i EA-portalen.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Kan jag migrera data och tjänster till en ny prenumeration?

* Du kan migrera resurserna direkt till den nya prenumerationen, se [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).
* Om du vill överföra ägarskap för en Azure-prenumeration och allt innehåll i den till någon annan, se [överföra ägarskapet för en Azure-prenumeration](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](../cost-management/quick-acm-cost-analysis.md)
