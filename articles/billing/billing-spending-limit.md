---
title: Förstå utgiftsgränsen i Azure | Microsoft Docs
description: Beskriver hur Azure utgiftsgräns fungerar och hur du tar bort den
services: ''
documentationcenter: ''
author: genlin
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: bfc6bf8cab1358c47e66660303f3303f4e9c763a
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901011"
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Förstå utgiftsgränsen och om hur du tar bort den i Azure

Utgiftsgräns i Azure finns för att förhindra utgifter under din kredit. Alla nya kunder som registrerar sig för utvärderingsversionen eller erbjudanden som innefattar krediter i flera månader ha utgiftsgränsen aktiverad som standard. Utgiftsgränsen är $0. Det går inte att ändra. Utgiftsgränsen är inte tillgänglig för prenumerationstyper som prenumerationer och åtagandeprenumerationer enligt principen Betala per användning. Se den [fullständig lista över Azure-erbjudanden och tillgänglighet för utgiftsgränsen](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Vad händer när jag når utgiftsgränsen?

När din användning leder till avgifter som använt månatliga mängder som ingår i din prenumeration, inaktiveras de tjänster som du har distribuerat under resten av den aktuella faktureringsperioden. 

Till exempel när du lägga alla krediten som ingår i prenumerationen molntjänster som du har distribuerat tas bort från produktionen och dina virtuella Azure-datorer stoppas och frigörs. Data i dina lagringskonton är tillgängliga i skrivskyddat läge.

I början av nästa faktureringsperiod om prenumeration erbjudandet omfattar krediter i flera månader aktiveras prenumerationen igen automatiskt. Du kan sedan distribuera dina molntjänster och har fullständig åtkomst till dina lagringskonton och databaser.

Vi kan skicka e-postaviseringar när du når utgiftsgränsen för prenumerationen. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions), och du visa dessa meddelanden som har nått utgiftsgränsen.

Om du har en kostnadsfri utvärderingsversion och når utgiftsgränsen, kan du [uppgradering till betala per användning](billing-upgrade-azure-subscription.md) att ta bort utgiftsgränsen och en prenumeration som automatiskt återaktiveras.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Ta bort utgiftsgränsen i Kontocenter

Du kan ta bort utgiftsgränsen när som helst så länge det finns en giltig betalningsmetod som är kopplad till prenumerationen. För erbjudanden som har krediter i flera månader, kan du även återaktivera utgiftsgränsen i början av nästa faktureringsperiod.

Om du vill ta bort utgiftsgränsen följer du dessa anvisningar:

1. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Välj en prenumeration. Om prenumerationen har inaktiverats på grund av att utgiftsgränsen har uppnåtts, klickar du på det här meddelandet: &quot;utgiftsgränsen har nåtts för prenumerationen och har inaktiverats för att förhindra att avgifter tas ut&quot;. Annars klickar du på **ta bort utgiftsgräns** i den **PRENUMERATIONSSTATUS** området.
1. Välj ett alternativ som passar dig.

![Att välja ett alternativ för ta bort utgiftsgräns](./media/billing-spending-limit/remove-spending-limit.PNG)

|Alternativ|Verkan|
|-------|-----|
|Ta bort utgiftsgränsen på obestämd tid|Tar bort utgiftsgränsen utan att den aktiveras automatiskt vid början av nästa debiteringsperiod.|
|Ta bort utgiftsgränsen för den aktuella debiteringsperioden|Tar bort utgiftsgränsen så att den nollställs automatiskt vid början av nästa faktureringsperiod.|

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Varför skulle jag vilja ta bort utgiftsgränsen?

Utgiftsgränsen kan hindra dig från att distribuera eller använda vissa tredjeparts- och Microsoft-tjänster. Här följer några exempel på när du bör ta bort utgiftsgränsen för din prenumeration.

* Du planerar att distribuera avbildningar från första part exempel Oracle och tjänster som Azure DevOps-tjänsterna. Det här scenariot gör att du överstiger utgiftsgränsen nästan direkt och gör att din prenumeration inaktiveras.
* Du har tjänster som inte får avbrytas.
* Du har tjänster och resurser med inställningar, till exempel virtuella IP-adresser, som du inte vill förlora. Dessa inställningar förloras när tjänsterna och resurserna är frigjord.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Hur aktiverar jag utgiftsgränsen när du tar bort meddelandet?

Den här funktionen finns bara när utgiftsgränsen har tagits bort på obestämd tid. Ändra det till aktiveras automatiskt i början av nästa faktureringsperiod.

1. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Klicka på gul banderollen för att ändra alternativet för utgiftsgräns.
1. Välj **aktivera utgiftsgränsen i nästa faktureringsperiod \<startdatum för faktureringsperiod\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Hur ställer jag en anpassad utgiftsgräns?

Anpassade utgiftsgränser är inte tillgängliga.

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>Utgiftsgränsen förhindrar att alla avgifter från Azure?

[Vissa externa tjänster som publicerats på Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) kan inte användas med din kredit för prenumeration och kan medföra separat debitering även när din utgiftsgräns har angetts. Exempel är Visual Studio-licenser, Azure Active Directory premium, supportavtal och de flesta märkesprodukter från tredje part tjänster. När du etablerar en ny extern tjänst visas en varning så att du vet tjänsterna faktureras separat.

![Marketplace-köp varning](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
