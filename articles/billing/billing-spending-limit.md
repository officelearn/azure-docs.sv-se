---
title: Utgiftsgränsen i Azure | Microsoft Docs
description: Den här artikeln beskrivs hur en Azure utgiftsgränsen fungerar och hur du tar bort den.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490602"
---
# <a name="azure-spending-limit"></a>Utgiftsgränsen i Azure

Utgiftsgränsen i Azure förhindrar utgifter under din kredit. Alla nya kunder som registrerar sig för en utvärderingsversion av Azure eller erbjudanden som omfattar krediter i flera månader ha utgiftsgränsen aktiverad som standard. Utgiftsgränsen är $0. Det går inte att ändra. Utgiftsgränsen är inte tillgängligt för prenumerationer under några planer sådana åtagandeprenumerationer och prenumerationer med användningsbaserad betalning. Se den [fullständig lista över Azure-erbjudanden och tillgänglighet för utgiftsgränsen](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Nå en utgiftsgräns

När din användning leder till avgifter som använt månatliga mängder som ingår i din Azure-prenumeration, inaktiveras de tjänster som du har distribuerat under resten av den aktuella faktureringsperioden.

Till exempel när du lägga alla krediten som ingår i prenumerationen, Azure-resurser som du har distribuerat tas bort från produktionen och Azure-datorer stoppas och frigörs. Data i dina lagringskonton är tillgängliga i skrivskyddat läge.

I början av nästa faktureringsperiod om prenumeration erbjudandet omfattar krediter i flera månader aktiveras prenumerationen igen automatiskt. Sedan kan du distribuera om dina Azure-resurser och har fullständig åtkomst till dina lagringskonton och databaser.

Azure skickar e-post-meddelande när du når den utgifter begränsa för din prenumeration. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions) att visa dessa meddelanden som har nått utgiftsgränsen.

Om du har en kostnadsfri utvärderingsprenumeration och når utgiftsgränsen kan du uppgradera till en plan med [användningsbaserad](billing-upgrade-azure-subscription.md) priser för att ta bort den utgifter begränsa och automatiskt aktivera prenumerationen.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Ta bort utgiftsgränsen i Kontocenter

Du kan ta bort utgiftsgränsen när som helst så länge det finns en giltig betalningsmetod som är associerade med din Azure-prenumeration. För erbjudanden som har krediter i flera månader, kan du också aktivera utgiftsgränsen i början av nästa faktureringsperiod.

Om du vill ta bort utgiftsgränsen följer du dessa anvisningar:

1. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Välj en prenumeration. Om prenumerationen har inaktiverats på grund av att utgiftsgränsen har uppnåtts, klickar du på meddelandet: **Prenumerationen har nåtts utgiftsgränsen och har inaktiverats för att förhindra avgifter.** Annars klickar du på **ta bort utgiftsgräns** i den **PRENUMERATIONSSTATUS** området.
1. Välj ett alternativ som passar dig.

![Att välja ett alternativ för ta bort utgiftsgräns](./media/billing-spending-limit/remove-spending-limit.PNG)

| Alternativ | Verkan |
| --- | --- |
| Ta bort utgiftsgränsen på obestämd tid | Tar bort utgiftsgränsen utan att den aktiveras automatiskt vid början av nästa debiteringsperiod. |
| Ta bort utgiftsgränsen för den aktuella debiteringsperioden | Tar bort utgiftsgränsen så att den nollställs automatiskt vid början av nästa faktureringsperiod. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Varför du kanske vill ta bort utgiftsgränsen

Utgiftsgränsen kan hindra dig från att distribuera eller använda vissa tredjeparts- och Microsoft-tjänster. Här följer i situationer där bör du ta bort utgiftsgränsen för din prenumeration.

-  Du planerar att distribuera avbildningar från första part exempel Oracle och tjänster som Azure DevOps-tjänsterna. Detta gör att du överstiger utgiftsgränsen nästan direkt och gör din prenumeration inaktiveras.
- Du har tjänster som du inte vill störs.
- Du har tjänster och resurser med inställningar, till exempel virtuella IP-adresser, som du inte vill förlora. Dessa inställningar förloras när tjänsterna och resurserna är frigjord.

## <a name="turn-on-the-spending-limit-after-removing"></a>Aktivera utgiftsgränsen när du tar bort

Den här funktionen finns bara när utgiftsgränsen har tagits bort på obestämd tid. Ändra det till aktiveras automatiskt i början av nästa faktureringsperiod.

1. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Klicka på gul banderollen för att ändra alternativet för utgiftsgräns.
1. Välj **aktivera utgiftsgränsen i nästa faktureringsperiod \<startdatum för faktureringsperiod\>**

## <a name="custom-spending-limit"></a>Anpassad utgiftsgräns

Anpassade utgiftsgränser är inte tillgängliga.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>En utgiftsgräns hindrar inte alla avgifter

[Vissa externa tjänster som publicerats på Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) kan inte användas med din kredit för prenumeration och kan medföra separat debitering även när din utgiftsgräns har angetts. Exempel är Visual Studio-licenser, Azure Active Directory premium, supportavtal och de flesta märkesprodukter från tredje part tjänster. När du etablerar en ny extern tjänst visas en varning så att du vet tjänsterna faktureras separat.

![Marketplace-köp varning](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Uppgradera till en plan med [användningsbaserad](billing-upgrade-azure-subscription.md) priser.
