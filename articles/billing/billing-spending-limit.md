---
title: Förstå Azure utgiftsgräns | Microsoft Docs
description: Beskriver hur Azure utgiftsgräns fungerar och hur du tar bort det.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/22/2017
ms.author: genli
ms.openlocfilehash: fa7ed4ff1cb7d686dd4459a0be89a8dd9f513e70
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Förstå Azure utgifter gränsen och borttagning

Utgiftsgräns i Azure finns för att förhindra utgifter över din kredit. Alla nya kunder som registrerar dig för en testversion eller erbjudanden som innehåller krediter över flera månader har utgiftsgränsen aktiverad som standard. Utgiftsgränsen är 0. Det går inte att ändra. Utgiftsgränsen är inte tillgänglig för prenumerationstyper som prenumerationer och åtagandeprenumerationer enligt principen Betala per användning. Finns det [fullständig lista över Azure erbjudanden och tillgängligheten för utgiftsgränsen](https://azure.microsoft.com/support/legal/offer-details/).

**Letar du efter fakturering aviseringar?** Se [skapat fakturerings- eller aviseringar för Azure-prenumerationer](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Vad händer när jag når utgiftsgränsen?

När resultatet användning i tillägg som få slut på månatliga mängder som ingår i din prenumeration, inaktiveras de tjänster som du har distribuerat för resten av den faktureringsperioden. 

Till exempel när du lägger ned den kredit som ingår i din prenumeration molntjänster som du har distribuerat tas bort från produktionen och dina virtuella Azure-datorer stoppas och Frigör allokerade. Data i dina lagringskonton och databaser är tillgängliga på ett sätt som skrivskyddad.

I början av nästa faktureringsperiod om prenumerationen erbjudandet omfattar krediter över flera månader skulle prenumerationen återaktiveras automatiskt. Du kan distribuera dina molntjänster och ha fullständig åtkomst till dina lagringskonton och databaser.

Vi skickar e-postmeddelanden när du har nått utgiftsgränsen för prenumerationen. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions), och du ser meddelanden om prenumerationer som har nått utgiftsgränsen.

Om du har en kostnadsfri utvärderingsversion och nå utgiftsgränsen, kan du [uppgradera till betala per användning](billing-upgrade-azure-subscription.md) att ta bort utgiftsgränsen och har prenumerationen aktiveras automatiskt igen.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Ta bort utgiftsgränsen i Account Center

Du kan ta bort utgiftsgränsen när som helst så länge det finns en giltig betalningsmetod som är kopplad till prenumerationen. För erbjudanden som har kredit över flera månader, kan du också återaktivera utgiftsgränsen i början av din nästa faktureringsperiod.

Om du vill ta bort utgiftsgränsen följer du dessa anvisningar:

1. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Välj en prenumeration.
. Om prenumerationen har inaktiverats på grund av utgiftsgränsen nås, klickar du på det här meddelandet: ”prenumerationen uppnåtts utgiftsgräns och har inaktiverats för att förhindra avgifter”. Annars klickar du på **ta bort utgiftsgränsen** i den **PRENUMERATIONSSTATUS** område.
1. Välj ett alternativ som passar dig.

![Att välja ett alternativ för att ta bort utgiftsgränsen](./media/billing-spending-limit/remove-spending-limit.PNG)

|Alternativ|Verkan|
|-------|-----|
|Ta bort utgiftsgränsen på obestämd tid|Tar bort utgiftsgränsen utan att den aktiveras automatiskt vid början av nästa debiteringsperiod.|
|Ta bort utgiftsgränsen för den aktuella debiteringsperioden|Tar bort utgiftsgränsen så att den nollställs automatiskt vid början av nästa faktureringsperiod.|

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Varför skulle jag vilja ta bort utgiftsgränsen?

Utgiftsgränsen kan hindra dig från att distribuera eller använda vissa tredjeparts- och Microsoft-tjänster. Här följer några exempel på när du bör ta bort utgiftsgränsen för din prenumeration.

* Du planerar att distribuera avbildningar från första part, till exempel Oracle och tjänster som Visual Studio Team Services. Det här scenariot medför att överskrida din utgiftsgräns nästan omedelbart och gör att din prenumeration är inaktiverad.
* Du har tjänster som inte får avbrytas.
* Du har tjänster och resurser med inställningar, till exempel virtuella IP-adresser, som du inte vill förlora. Dessa inställningar går förlorade när de tjänster och resurser är frigöra.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Hur aktiverar jag utgiftsgränsen när du tar bort det?

Den här funktionen är tillgänglig endast när har tagits bort utgiftsgränsen på obestämd tid. Ändra den aktiveras automatiskt i början av nästa faktureringsperiod.

1. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Klicka på gul banderoll om du vill ändra alternativet för utgiftsgräns.
1. Välj **aktivera utgiftsgränsen för nästa faktureringsperiod \<startdatum för fakturering period\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Hur ställer jag in anpassade utgiftsgränsen?

Vi har inte anpassade tillbringar gränser idag. Dock kan du välja att [använder fakturaaviseringar för att kontrollera din tillbringar](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>Hindrar utgiftsgränsen alla kostnader från Azure?

[Vissa externa tjänster publiceras i Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) kan inte användas med din prenumerationskrediter och kan debiteras separat även när din utgiftsgräns har angetts. Exempel inkluderar Visual Studio licenser, Azure Active Directory premium, supportplaner och de flesta från tredje part märkta tjänster. När du etablerar en ny extern tjänst, visas en varning så att du vet tjänsterna faktureras separat:

![Marketplace köpa varning](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
