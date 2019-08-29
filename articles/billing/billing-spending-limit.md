---
title: Utgifts gräns för Azure | Microsoft Docs
description: Den här artikeln beskriver hur en utgifts gräns i Azure fungerar och hur du tar bort den.
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
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114438"
---
# <a name="azure-spending-limit"></a>Utgifts gräns för Azure

Utgifts gränsen i Azure gör att du inte kan spendera på ditt kredit belopp. Alla nya kunder som registrerar sig för en utvärderings version av Azure eller erbjudanden som innehåller krediter under flera månader har utgifts gränsen aktive rad som standard. Utgifts gränsen är $0 och kan inte ändras. Du kan till exempel inte ändra utgifts gränsen till $100. Du kan dock ta bort utgifts gränsen. Så du har antingen ingen gräns eller så har du en gräns på noll som hindrar dig från de flesta typer av utgifter. Utgifts gränsen är inte tillgänglig för prenumerationer under vissa planer på sådana avtals planer och planer med priser för betala per användning. Se den [fullständiga listan med Azure-erbjudanden och tillgängligheten för utgifts gränsen](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Nått en utgifts gräns

När din användning leder till kostnader som förbrukar de månatliga beloppen som ingår i din Azure-prenumeration inaktive ras de tjänster som du har distribuerat för resten av fakturerings perioden.

När du till exempel lägger till all kredit som ingår i din prenumeration tas de Azure-resurser som du har distribuerat bort från produktionen och dina virtuella Azure-datorer stoppas och tas bort. Data i dina lagrings konton är tillgängliga som skrivskyddade.

I början av nästa fakturerings period, om ditt prenumerations erbjudande innehåller krediter under flera månader, aktive ras prenumerationen igen automatiskt. Sedan kan du distribuera om dina Azure-resurser och få full åtkomst till dina lagrings konton och databaser.

Azure skickar e-postaviseringar när du når utgifts gränsen för din prenumeration. Logga in på [konto centret](https://account.windowsazure.com/Subscriptions) för att se meddelanden om prenumerationer som har nått utgifts gränsen.

Om du har en kostnads fri utvärderings prenumeration och når utgifts gränsen kan du uppgradera till en plan med priset [betala per](billing-upgrade-azure-subscription.md) användning för att ta bort utgifts gränsen och automatiskt aktivera prenumerationen.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Ta bort utgifts gränsen i konto Center

Du kan när som helst ta bort utgifts gränsen så länge det finns en giltig betalnings metod som är kopplad till din Azure-prenumeration. För erbjudanden som har kredit över flera månader kan du också aktivera utgifts gränsen i början av nästa fakturerings period.

Om du vill ta bort utgiftsgränsen följer du dessa anvisningar:

1. Logga in på [konto Center](https://account.windowsazure.com/Subscriptions).
1. Välj en prenumeration. Om prenumerationen är inaktive rad eftersom utgifts gränsen har uppnåtts klickar du på meddelandet: **Prenumerationen nådde utgifts gränsen och har inaktiverats för att förhindra avgifter.** Annars klickar du på **ta bort utgifts gräns** i avsnittet **prenumerations status** .
1. Välj ett alternativ som passar dig.

![Välja ett alternativ för att ta bort utgifts gränsen](./media/billing-spending-limit/remove-spending-limit.PNG)

| Alternativ | Verkan |
| --- | --- |
| Ta bort utgiftsgränsen på obestämd tid | Tar bort utgiftsgränsen utan att den aktiveras automatiskt vid början av nästa debiteringsperiod. |
| Ta bort utgiftsgränsen för den aktuella debiteringsperioden | Tar bort utgiftsgränsen så att den nollställs automatiskt vid början av nästa faktureringsperiod. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Varför du kanske vill ta bort utgifts gränsen

Utgifts gränsen kan förhindra att du distribuerar eller använder vissa tredje parter och Microsoft-tjänster. Här är de situationer där du bör ta bort utgifts gränsen för din prenumeration.

-  Du planerar att distribuera avbildningar från första part, till exempel Oracle och tjänster som Azure DevOps Services. Den här situationen gör att du överskrider din utgifts gräns nästan omedelbart och gör att din prenumeration inaktive ras.
- Du har tjänster som du inte vill ska störas.
- Du har tjänster och resurser med inställningar, till exempel virtuella IP-adresser, som du inte vill förlora. De här inställningarna försvinner när tjänsterna och resurserna är avallokerade.

## <a name="turn-on-the-spending-limit-after-removing"></a>Aktivera utgifts gränsen efter borttagning

Den här funktionen är endast tillgänglig när utgifts gränsen har tagits bort under obestämd tid. Ändra det så att det aktive ras automatiskt i början av nästa fakturerings period.

1. Logga in på [konto Center](https://account.windowsazure.com/Subscriptions).
1. Klicka på den gula banderollen för att ändra alternativet för utgifts gräns.
1. Välj **Aktivera utgifts gränsen under nästa fakturerings period \<start datum för fakturerings perioden\>**

## <a name="custom-spending-limit"></a>Anpassad utgifts gräns

Anpassade utgifts gränser är inte tillgängliga.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>En utgifts gräns förhindrar inte alla avgifter

[Vissa externa tjänster som publicerats på Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) kan inte användas med dina prenumerations krediter, och kan medföra separata avgifter även om din utgifts gräns har angetts. Exempel är Visual Studio-licenser, Azure Active Directory Premium, support avtal och de flesta märkes tjänster från tredje part. När du etablerar en ny extern tjänst visas en varning som låter dig veta att tjänsterna faktureras separat:

![Marknads plats inköps varning](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Uppgradera till en plan med priset [betala per](billing-upgrade-azure-subscription.md) användning.
