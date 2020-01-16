---
title: Förstå din Azure-faktura | Microsoft Docs
description: Lär dig hur du läser och förstår användningen och fakturan för Azure-prenumerationen
services: ''
documentationcenter: ''
author: bandersmsft
manager: jureid
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: e6858d51eec1bbcb100f71418337d74d19d6db5b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994732"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Förstå villkoren i din faktura från Microsoft Azure

Fakturan innehåller en sammanfattning av dina avgifter och instruktioner för betalning. Du kan ladda ned den i PDF-format (.pdf) från [Azure-portalen](https://portal.azure.com/) och du kan få den skickad via e-post. Mer information finns i [Så här hämtar du information om fakturering och daglig användning från Azure](../manage/download-azure-invoice-daily-usage-date.md).

Några saker att tänka på:

-   Om du använder en kostnadsfri utvärderingsprenumeration kan du få detaljerad användningsinformation via Azure-portalen utan att ha en faktura.

-   Du kan se upp till 24 timmars användning från slutet av föregående faktureringsperiod på din aktuella faktura.

-   Avgifter som anges på faktureringsutdragen för internationella kunder är endast uppskattade. Bankerna kan ha olika kostnader för valutaomvandling.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Detaljerade villkor och beskrivningar av din faktura
I följande avsnitt går vi igenom de viktigaste termerna du ser på din faktura och beskrivningar för respektive term.

### <a name="account-information"></a>Kontoinformation

Avsnittet med kontoinformation på fakturan står överst på den första sidan och innehåller information om din profil och prenumeration.

![Avsnittet med kontoinformation på fakturan](./media/understand-invoice/account-information-section.png)

| Period | Beskrivning |
| --- | --- |
| Kundordernummer |Ett valfritt inköpsordernummer som tilldelar i spårningssyfte |
| Fakturanr |Ett unikt fakturanummer som Microsoft genererar i spårningssyfte |
| Faktureringscykel |Det datumintervall som fakturan täcker |
| Fakturadatum |Datumet då fakturan genererades, vanligtvis en dag efter faktureringscykelns slut |
| Betalningsmetod |Typ av betalning som används för kontot (faktura eller kreditkort) |
| Fakturera till |Den faktureringsadress som är angiven för kontot |
| Prenumerationserbjudande (betala per användning) |Typen av prenumerationserbjudande som har köpts (som betala per användning, BizSpark Plus eller Azure-pass). Mer information finns i [Typer av Azure-erbjudanden](https://azure.microsoft.com/support/legal/offer-details/). |
| E-postadress till kontoägaren | Den e-postadress som Microsoft Azure-kontot är registrerat under. <br /><br />Om du vill ändra e-postadressen kan du läsa artikeln [Så ändrar du informationen i din Azure-kontoprofil som e-postadress, postadress och telefonnummer](../manage/change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Förstå sammanfattningen av fakturan
I avsnittet **Fakturasammanfattning** på fakturan står de totala transaktionsbeloppen sedan din senaste faktureringsperiod och aktuella avgifter för användning.

![Avsnittet Fakturasammanfattning](./media/understand-invoice/invoice-summary-section.png)

Prenumerationens namn (”Production Storage”) är namnet på prenumerationen på den här fakturan.

#### <a name="understand-the-previous-charges"></a>Förstå tidigare avgifter
Avsnittet med tidigare saldo, betalningar och utestående saldo på fakturan sammanfattar transaktionerna sedan den senaste faktureringsperioden.

| Period | Beskrivning |
| --- | --- |
| Föregående saldo |Totalt restbelopp från den senaste faktureringsperioden |
| Betalningar |Totalsumma för betalningar och krediteringar till den senaste faktureringsperioden |
| Utestående saldo (från föregående faktureringsperiod) |Eventuella krediter eller återstående saldo på kontot sedan den senaste faktureringsperioden |

#### <a name="understand-the-current-charges"></a>Förstå aktuella debiteringar
I avsnittet med aktuella avgifter på fakturan visas information om dina månadsavgifter för den aktuella faktureringsperioden.

| Period | Beskrivning |
| --- | --- |
| Användningsdebitering |Avgifter för användning är summan av månadsavgifterna i prenumerationen under den aktuella faktureringsperioden|
| Rabatter |Rabatter för olika tjänster under den aktuella faktureringsperioden|
| Justeringar |Diverse krediter (som kostnadsfri användning eller krediter) eller utestående avgifter som påförts den aktuella faktureringsperioden.<br/><br/>Om du till exempel har erbjudandet Visual Studio Enterprise med MSDN ser du en månatlig kredit här. Om du avbryter din prenumeration ser du eventuella avgifter för månatlig användning som överstiger den månatliga krediten som du får med prenumerationserbjudandet. Avgifterna debiteras från början av den aktuella faktureringsperioden fram till datumet när prenumerationen avbröts. |

#### <a name="sold-to-and-payment-instructions"></a>Såldes till och Betalningsanvisningar

I följande tabell beskrivs termerna Såldes till och Betalningsanvisningar som du ser på fakturans andra sida.

| Period |Beskrivning |
| --- | --- |
| Såldes till |Profiladress som är registrerad för kontot. <br/><br/>Om du behöver ändra adressen kan du läsa artikeln [Så ändrar du informationen i din Azure-kontoprofil som e-postadress, postadress och telefonnummer](../manage/change-azure-account-profile.md).|
| Betalningsanvisningar |Betalningsanvisningar som beror på betalningsmetoden (till exempel kreditkort eller faktura). |

#### <a name="usage-charges"></a>Användningsdebitering

I avsnittet Avgifter för användning på fakturan ser du information på mätarnivå om dina avgifter.

![Avsnittet med avgifter per användning](./media/understand-invoice/usage-charges-section.png)

I följande tabell beskrivs kolumnrubrikerna för avgifterna för användning på fakturan.

| Period |Beskrivning |
| --- | --- |
| Namn |Anger den huvudtjänst som användningen gäller |
| Typ |Anger typen av Azure-tjänst, vilket kan påverka priset |
| Resurs |Anger måttenheten för den mätare som förbrukas |
| Region |Anger datacenterplatsen för vissa tjänster som prissätts beroende på var datacentret ligger |
| Förbrukad |Mängden av mätaren som använts under faktureringsperioden |
| Ingår |Mängden av mätaren som ingår utan kostnad under den aktuella faktureringsperioden |
| Faktureringsbar |Visar skillnaden mellan mängden under Förbrukad och under Ingår. Det här är beloppet du faktureras. För erbjudanden med betalning per användning där inget belopp ingår i erbjudandet är denna summa samma som summan under Förbrukad |
| Kurs |Priset du debiteras per faktureringsbar enhet |
| Värde |Resultatet av kolumnen Faktureringsbar gånger kolumnen Pris. Om mängden under Förbrukad inte överstiger mängden under Ingår visas inga avgifter i den här kolumnen. |
| Delsumma |Summan av alla avgifter exklusive moms för den här faktureringsperioden |
| Totalsumma |Summan av alla avgifter inklusive moms för den här faktureringsperioden |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Hur kontrollerar jag att avgifterna på fakturan stämmer?
Om du vill se mer information om någon av avgifterna på fakturan kan du läsa i [Förstå din faktura för Microsoft Azure](review-individual-bill.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
