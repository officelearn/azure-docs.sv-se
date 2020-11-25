---
title: Återaktivera en inaktiverad Azure-prenumeration
description: Beskriver när du kanske har en Azure-prenumeration inaktiverad och hur du återaktiverar den.
keywords: Azure-prenumeration inaktiverad
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: cad3082981bcfc699bc230badf44e2ffc2e1bed3
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744433"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Återaktivera en inaktiverad Azure-prenumeration

Din Azure-prenumeration kan inaktiveras om din kredit har gått ut, om du har nått din utgiftsgräns, om du har en förfallen faktura, om din kreditkortsgräns har nåtts eller om prenumerationen har avbrutits av kontoadministratören. Se vilket problem som gäller dig och följ stegen i den här artikeln för att få din prenumeration återaktiverad.

## <a name="your-credit-is-expired"></a>Din kredit har upphört

När du registrerar dig för ett kostnadsfritt Azure-konto får du en kostnadsfri utvärderingsprenumeration som ger dig 200 USD i Azure-krediter i 30 dagar och 12 månaders kostnadsfria tjänster. Efter 30-dagarsperioden inaktiverar Azure din prenumeration. Din prenumeration inaktiveras för att skydda dig så att du inte oavsiktligt debiteras för användning utöver krediten och de kostnadsfria tjänsterna som ingår i din prenumeration. Om du vill fortsätta att använda Azure-tjänster måste du [uppgradera din prenumeration](upgrade-azure-subscription.md). Efter uppgraderingen har prenumerationen fortfarande tillgång till kostnadsfria tjänster i 12 månader. Du debiteras bara för användning utöver de kostnadsfria tjänsterna och kvantiteterna.

## <a name="you-reached-your-spending-limit"></a>Du har uppnått utgiftsgränsen

Azure-prenumerationer med kredit, till exempel kostnadsfri utvärdering och Visual Studio Enterprise, har en utgiftsgräns. Det innebär att du bara kan använda tjänster upp till den inkluderade krediten. När din användning når utgiftsgränsen inaktiveras din prenumeration under återstoden av faktureringsperioden. Din prenumeration inaktiveras för att skydda dig så att du inte oavsiktligt debiteras för användning utöver krediten som ingår i din prenumeration. Information om hur du tar bort utgiftsgränsen finns i [Ta bort utgiftsgränsen i Kontocenter](spending-limit.md#remove).

> [!NOTE]
> Om du har en kostnadsfri utvärderingsprenumeration och tar bort utgiftsgränsen så omvandlas din prenumeration till en enskild prenumeration med Betala per användning-priser i slutet av den kostnadsfria utvärderingsperioden. Du behåller din kvarvarande kredit under återstoden av de 30 dagarna från det att du skapade prenumerationen. Du har även tillgång till kostnadsfria tjänster i 12 månader.

Information om hur du övervakar och hanterar faktureringsaktivitet för Azure finns i [Planera för hantering av Azure-kostnader](../understand/plan-manage-costs.md).


## <a name="your-bill-is-past-due"></a>Din faktura är försenad

Information om hur du löser problem med ett passerat betalningsdatum finns i [Lösa problem med ett passerat betalningsdatum för en Azure-prenumeration när du har fått ett e-postmeddelande från Azure](resolve-past-due-balance.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>Fakturan överskrider kreditkortsgränsen

Lös problemet genom att [byta till ett annat kreditkort](change-credit-card.md). Eller om du representerar ett företag så kan du [byta till att betala mot faktura](pay-by-invoice.md).

## <a name="the-subscription-was-accidentally-canceled"></a>Prenumerationen har avbrutits av misstag

Om du är kontoadministratör och oavsiktligt har avbrutit en enskild prenumeration med Betala per användning-priser kan du återaktivera den i Kontocenter.

1. Logga in på [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Välj den avbrutna prenumerationen.
1. Klicka på **Återaktivera**.

    ![Skärmbild som visar återaktiveringslänkar i den högra rutan](./media/subscription-disabled/reactivate-sub.png)

För andra prenumerationstyper kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och få prenumerationen återaktiverad.

## <a name="after-reactivation"></a>Efter återaktivering

När din prenumeration har återaktiverats kan det uppstå en fördröjning i att skapa eller hantera resurser. Om fördröjningen överstiger 30 minuter kontaktar du [Azure faktureringsstöd](https://go.microsoft.com/fwlink/?linkid=2083458) för hjälp. De flesta Azure-resurser återupptas automatiskt och kräver ingen åtgärd. Vi rekommenderar dock att du kontrollerar dina Azure-tjänstresurser och startar om alla som inte återupptas automatiskt.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig att [Planera för hantering av Azure-kostnader](../understand/plan-manage-costs.md).
