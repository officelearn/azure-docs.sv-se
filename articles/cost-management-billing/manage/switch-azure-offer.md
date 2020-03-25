---
title: Byta Azure-prenumerationserbjudande
description: Lär dig hur du ändrar din Azure-prenumeration och byter till ett annat erbjudande med hjälp av Azure-kontocenter.
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 4aa02dae521844f2654a50f77be9b2ee3c46a74f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199525"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Byt erbjudande för din Azure-prenumeration

Som kund med en [enskild prenumeration med användningsbaserad betalning](https://azure.microsoft.com/offers/ms-azr-0003p/) kan du byta erbjudande för din Azure-prenumeration i [Kontocenter](https://account.windowsazure.com/Subscriptions). Du kan till exempel använda den här funktionen för att dra nytta av [de månatliga krediterna för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Vill du bara uppgradera från den kostnadsfria utvärderingsversionen?** Läs mer i [Uppgradera din prenumeration](upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Följande stöds:

Du kan växla från en individuell prenumeration med användningsbaserade priser till:

- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för andra erbjudandeändringar.
>
>

## <a name="switch-subscription-offer"></a>Byt prenumerationserbjudande

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Logga in på [Azure-kontocenter](https://account.windowsazure.com/Subscriptions).
1. Välj din enskilda prenumeration med användningsbaserade priser.
1. Klicka på **Växla till ett annat erbjudande**. Alternativet är endast tillgängligt om du har en enskild prenumeration med användningsbaserade priser och har slutfört din första faktureringsperiod.

   ![Lägg märke till knappen Byt erbjudande till höger på sidan](./media/switch-azure-offer/switchbutton.png)
1. **Välj önskat erbjudande** i listan över erbjudanden som din prenumeration kan bytas till. Den här listan varierar beroende på vilka medlemskap ditt konto är associerat med. Om inget är tillgängligt kontrollerar du [listan över tillgängliga erbjudanden som du kan byta till](#whats-supported) och kontrollerar att du har rätt medlemskap.

   ![Välj ett erbjudande som du vill byta till](./media/switch-azure-offer/selectoffer.png)
1. Beroende på vilket erbjudande du byter till kan en anmärkning visas som beskriver effekten av bytet. Gå igenom listan noga och följ anvisningarna innan du fortsätter.

   ![Läs anmärkningarna](./media/switch-azure-offer/thingstonote.png)
1. Du kan byta namn på din prenumeration. Standardnamnet är namnet på det nya erbjudandet. Slutför processen genom att klicka på **Byt erbjudande**.

   ![Klicka på den gröna knappen](./media/switch-azure-offer/confirmpage.png)
1. Lyckades! Nu är din prenumeration kopplad till det nya erbjudandet.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
I följande avsnitt får du svar på vanliga frågor.

### <a name="what-is-an-azure-offer"></a>Vad är ett Azure-erbjudande?

Ett Azure-erbjudande är den *typ* av Azure-prenumeration som du har. Exempel på Azure-erbjudanden är [en prenumeration med prismodellen Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure i Open-licensiering](https://azure.microsoft.com/offers/ms-azr-0111p/) och [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/). Varje erbjudande har olika [villkor](https://azure.microsoft.com/support/legal/offer-details/) och vissa har särskilda förmåner. Du hittar erbjudandet för din prenumeration på prenumerationssidan i Kontocenter. Klicka på namnet på erbjudandet om du vill ha mer information.

   ![Klicka på länken Erbjudande i Kontocenter om du vill ha mer information](./media/switch-azure-offer/offerlink01.png)

### <a name="why-dont-i-see-the-button"></a>Varför visas inte knappen?

Du kanske inte ser alternativet **Byt till ett annat erbjudande** om:

* Du inte har en [prenumeration med prismodellen Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/). För närvarande kan endast prenumerationer med användningsbaserad betalning omvandlas till ett annat erbjudande.
  * Om du har en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) läser du hur du kan [uppgradera till Betala per användning](upgrade-azure-subscription.md).
  * [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill byta erbjudande från en annan prenumeration.
* Du är fortfarande på din första faktureringsperiod. Du måste vänta tills din första faktureringsperiod avslutats innan du kan byta erbjudande.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Varför får jag ett meddelande om att det inte finns några tillgängliga erbjudanden i min region eller mitt land?

* Du kanske inte har rätt att byta erbjudande. Kontrollera [listan över tillgängliga erbjudanden som du kan byta till](#whats-supported) och kontrollera att du har aktiverat rätt förmåner med Visual Studio eller BizSpark.
* Vissa erbjudanden är inte tillgängliga i alla länder/regioner.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Hur påverkas min tjänst och faktureringen om jag byter Azure-erbjudande?

Här följer information om vad som händer om du byter Azure-erbjudande i Kontocenter.

#### <a name="no-service-downtime"></a>Inget avbrott i tjänsten

Det förekommer inga tjänstavbrott för användare som är kopplade till prenumerationen. Det kan dock finnas begränsningar för det erbjudande som du byter till. Till exempel tillåts inte användning i produktionsmiljö med vissa erbjudanden, vilket betyder att produktionsresurser måste flyttas till en annan prenumeration.

#### <a name="quota-increases-are-reset"></a>Kvotökningar återställs

När du byter erbjudande återställs [gräns- eller kvotökningar som överskrider standardgränsen](../../azure-portal/supportability/resource-manager-core-quotas-request.md). Det förekommer inga tjänstavbrott, även om du har fler resurser än standardgränsen. Om du exempelvis har 200 kärnor i din prenumeration och sedan byter erbjudande, återställs din kvot till standardkvoten på 20 kärnor. De virtuella datorer som använder 200 kärnor påverkas inte och fortsätter att köras. Du måste dock begära en ny kvotökning för att kunna etablera fler kärnor.

#### <a name="billing"></a>Fakturering

Den dag du byter erbjudande genereras en faktura för alla utestående debiteringar. Därefter faktureras din prenumeration enligt prisvillkoren för det nya erbjudandet. Årsdagen för prenumerationens fakturering ändras till det datum då du bytte erbjudande. Användnings- och faktureringsdata före bytet av erbjudande sparas inte. Därför rekommenderar vi att du laddar ned en kopia innan du byter.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>Kan jag migrera från en prenumeration med prismodellen Betala per användning till Leverantör av molnlösningar (CSP) eller Enterprise-avtal (EA)?

* Information om hur du migrerar till CSP finns i [Migrera en Azure-prenumeration med prismodellen Betala per användning till CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Om du vill migrera till EA ber du din registreringsadministratör att lägga till ditt konto i EA. Följ anvisningarna i e-postinbjudan för att flytta dina prenumerationer i EA-registreringen. Mer information finns i [Associera ett befintligt konto](https://ea.azure.com/helpdocs/associateExistingAccount) på EA-portalen.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Kan jag migrera data och tjänster till en ny prenumeration?

* Du kan migrera resurserna direkt till den nya prenumerationen. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Om du vill överföra ägarskapet för en Azure-prenumeration och allt innehåll i den till någon annan läser du [Överföra ägarskapet för en Azure-prenumeration](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](../costs/quick-acm-cost-analysis.md)
