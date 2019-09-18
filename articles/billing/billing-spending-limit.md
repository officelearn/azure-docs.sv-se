---
title: Utgiftsgräns för Azure | Microsoft Docs
description: I den här artikeln beskrivs hur en utgiftsgräns för Azure fungerar samt hur du tar bort den.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114438"
---
# <a name="azure-spending-limit"></a>Utgiftsgräns i Azure

Utgiftsgränsen i Azure gör att du inte kan spendera mer än ditt kreditbelopp. Alla nya kunder som registrerar sig för en Azure-utvärdering eller erbjudanden som innefattar krediter i flera månader har utgiftsgränsen aktiverad som standard. Utgiftsgränsen är 0 USD och kan inte ändras. Du kan till exempel inte ändra utgiftsgränsen till 100 USD. Dock kan du när som helst ta bort utgiftsgränsen. Därmed har du antingen ingen gräns eller en gräns på noll som förhindrar de flesta typer av utgifter. Utgiftsgränsen är inte tillgänglig för prenumerationer under vissa planer, till exempel åtagandeplaner och planer med Betala per användning-priser. Mer information finns i den [fullständiga listan med Azure-erbjudanden och tillgänglighet för utgiftsgränsen](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Uppnå en utgiftsgräns

När din användning leder till avgifter som överstiger de månadsbelopp som ingår i din Azure-prenumeration inaktiveras de tjänster som du har distribuerat under resten av den faktureringsperioden.

När du till exempel spenderar all kredit som ingår i din prenumeration tas de Azure-resurser som du distribuerade bort från produktion, och dina virtuella Azure-datorer stoppas och avallokeras. Data i dina lagringskonton är tillgängliga som skrivskyddade.

Om ditt prenumerationserbjudande omfattar krediter under flera månader återaktiveras din prenumeration automatiskt i början av nästa faktureringsperiod. Sedan kan du distribuera om dina Azure-resurser och få fullständig åtkomst till dina lagringskonton och databaser.

Azure skickar e-postaviseringar när du når utgiftsgränsen för din prenumeration. Logga in på [Kontocenter](https://account.windowsazure.com/Subscriptions) för att se meddelanden om prenumerationer som har nått utgiftsgränsen.

Om du har en kostnadsfri utvärderingsprenumeration och når utgiftsgränsen kan du uppgradera till en plan med [Betala per användning-priser](billing-upgrade-azure-subscription.md) för att ta bort utgiftsgränsen och automatiskt aktivera prenumerationen.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Ta bort utgiftsgränsen i Kontocenter

Du kan ta bort utgiftsgränsen när som helst så länge det finns en giltig betalningsmetod som är associerad med din Azure-prenumeration. För erbjudanden som har krediter under flera månader kan du även aktivera utgiftsgränsen i början av nästa faktureringsperiod.

Om du vill ta bort utgiftsgränsen följer du dessa anvisningar:

1. Logga in på [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Välj en prenumeration. Om prenumerationen har inaktiverats på grund av att utgiftsgränsen har uppnåtts klickar du på meddelandet: **Prenumerationen har nått utgiftsgränsen och har inaktiverats för att förhindra att avgifter tas ut.** Annars klickar du på **Ta bort utgiftsgräns** i området **PRENUMERATIONSSTATUS**.
1. Välj ett alternativ som passar dig.

![Välja ett alternativ för att ta bort utgiftsgränsen](./media/billing-spending-limit/remove-spending-limit.PNG)

| Alternativ | Verkan |
| --- | --- |
| Ta bort utgiftsgränsen på obestämd tid | Tar bort utgiftsgränsen utan att den aktiveras automatiskt vid början av nästa faktureringsperiod. |
| Ta bort utgiftsgränsen för den aktuella faktureringsperioden | Tar bort utgiftsgränsen så att den återaktiveras automatiskt vid början av nästa faktureringsperiod. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Därför bör du kanske ta bort utgiftsgränsen

Utgiftsgränsen kan hindra dig från att distribuera eller använda vissa tjänster från tredje part och Microsoft-tjänster. Här följer några situationer där du bör ta bort utgiftsgränsen för din prenumeration.

-  Du planerar att distribuera avbildningar från första part, till exempel Oracle, och tjänster såsom Azure DevOps Services. Den här situationen gör att du överstiger utgiftsgränsen nästan omedelbart och gör att din prenumeration inaktiveras.
- Du har tjänster som du inte vill ska störas.
- Du har tjänster och resurser med inställningar, till exempel virtuella IP-adresser, som du inte vill förlora. Dessa inställningar förloras när tjänsterna och resurserna avallokeras.

## <a name="turn-on-the-spending-limit-after-removing"></a>Aktivera utgiftsgränsen efter borttagning

Den här funktionen är endast tillgänglig när utgiftsgränsen har tagits bort under obestämd tid. Ändra den så att den aktiveras automatiskt i början av nästa faktureringsperiod.

1. Logga in på [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Klicka på den gula banderollen för att ändra alternativet för utgiftsgräns.
1. Välj **Aktivera utgiftsgränsen under nästa faktureringsperiod \<startdatum för faktureringsperioden\>**

## <a name="custom-spending-limit"></a>Anpassad utgiftsgräns

Anpassade utgiftsgränser är inte tillgängliga.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>En utgiftsgräns förhindrar inte alla typer av utgifter

[Vissa externa tjänster som publicerats på Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) kan inte användas med dina prenumerationskrediter, och kan medföra separata avgifter även när din utgiftsgräns har angetts. Exempel är Visual Studio-licenser, Azure Active Directory Premium, supportavtal och de flesta varumärkestjänster från tredje part. När du etablerar en ny extern tjänst visas en varning om att tjänsterna faktureras separat:

![Marketplace-inköpsvarning](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Uppgradera till en plan med [Betala per användning-priser](billing-upgrade-azure-subscription.md).
