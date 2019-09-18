---
title: Förstå din Azure-faktura | Microsoft Docs
description: Lär dig hur du läser och förstår användningen och fakturan för Azure-prenumerationen
services: ''
documentationcenter: ''
author: bandersmsft
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 77c1a85136b2117af7396b8eec2d8b92b335d61d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "60369962"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Förstå villkoren i din faktura från Microsoft Azure

Fakturan innehåller en sammanfattning av dina avgifter och instruktioner för betalning. Du kan ladda ned den i PDF-format (.pdf) från [Azure-portalen](https://portal.azure.com/) och du kan få den skickad via e-post. Mer information finns i [Så här hämtar du information om fakturering och daglig användning från Azure](billing-download-azure-invoice-daily-usage-date.md).

Några saker att tänka på:

-   Om du använder en kostnadsfri utvärderingsprenumeration kan du få detaljerad användningsinformation via Azure-portalen utan att ha en faktura.

-   Du kan se upp till 24 timmars användning från slutet av föregående faktureringsperiod på din aktuella faktura.

-   Avgifter som anges på faktureringsutdragen för internationella kunder är endast uppskattade. Bankerna kan ha olika kostnader för valutaomvandling.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Detaljerade villkor och beskrivningar av din faktura
I följande avsnitt går vi igenom de viktigaste termerna du ser på din faktura och beskrivningar för respektive term.

### <a name="account-information"></a>Kontoinformation

Avsnittet med kontoinformation på fakturan står överst på den första sidan och innehåller information om din profil och prenumeration.

![Avsnittet med kontoinformation på fakturan](./media/billing-understand-your-invoice/1.png)

| Period | Beskrivning |
| --- | --- |
| Kundordernummer |Ett valfritt inköpsordernummer som tilldelar i spårningssyfte |
| Fakturanr |Ett unikt fakturanummer som Microsoft genererar i spårningssyfte |
| Faktureringscykel |Det datumintervall som fakturan täcker |
| Fakturadatum |Datumet då fakturan genererades, vanligtvis en dag efter faktureringscykelns slut |
| Betalningsmetod |Typ av betalning som används för kontot (faktura eller kreditkort) |
| Fakturera till |Den faktureringsadress som är angiven för kontot |
| Prenumerationserbjudande (betala per användning) |Typen av prenumerationserbjudande som har köpts (som betala per användning, BizSpark Plus eller Azure-pass). Mer information finns i [Typer av Azure-erbjudanden](https://azure.microsoft.com/support/legal/offer-details/). |
| E-postadress till kontoägaren | Den e-postadress som är registrerad för Microsoft Azure-kontot. <br /><br />Om du vill ändra e-postadressen kan du läsa artikeln [Så ändrar du informationen i din Azure-kontoprofil som e-postadress, postadress och telefonnummer](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Förstå sammanfattningen av fakturan
I avsnittet **Fakturasammanfattning** på fakturan står de totala transaktionsbeloppen sedan din senaste faktureringsperiod och aktuella avgifter för användning.

![Avsnittet Fakturasammanfattning](./media/billing-understand-your-invoice/2.png)

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
| Avgifter för användning |Avgifter för användning är summan av månadsavgifterna i prenumerationen under den aktuella faktureringsperioden|
| Rabatter |Rabatter för olika tjänster under den aktuella faktureringsperioden|
| Justeringar |Diverse krediter (som kostnadsfri användning eller krediter) eller utestående avgifter som påförts den aktuella faktureringsperioden.<br/><br/>Om du till exempel har erbjudandet Visual Studio Enterprise med MSDN ser du en månatlig kredit här. Om du avbryter din prenumeration ser du eventuella avgifter för månatlig användning som överstiger den månatliga krediten som du får med prenumerationserbjudandet. Avgifterna debiteras från början av den aktuella faktureringsperioden fram till datumet när prenumerationen avbröts. |

#### <a name="sold-to-and-payment-instructions"></a>Såldes till och Betalningsanvisningar

I följande tabell beskrivs termerna Såldes till och Betalningsanvisningar som du ser på fakturans andra sida.

| Period |Beskrivning |
| --- | --- |
| Såldes till |Profiladress som är registrerad för kontot. <br/><br/>Om du behöver ändra adressen kan du läsa artikeln [Så ändrar du informationen i din Azure-kontoprofil som e-postadress, postadress och telefonnummer](billing-how-to-change-azure-account-profile.md).|
| Betalningsanvisningar |Betalningsanvisningar som beror på betalningsmetoden (till exempel kreditkort eller faktura). |

#### <a name="usage-charges"></a>Avgifter för användning

I avsnittet Avgifter för användning på fakturan ser du information på mätarnivå om dina avgifter.

![Avsnittet med avgifter per användning](./media/billing-understand-your-invoice/3.png)

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
| Pris |Priset du debiteras per faktureringsbar enhet |
| Värde |Resultatet av kolumnen Faktureringsbar gånger kolumnen Pris. Om mängden under Förbrukad inte överstiger mängden under Ingår visas inga avgifter i den här kolumnen. |
| Delsumma |Summan av alla avgifter exklusive moms för den här faktureringsperioden |
| Totalsumma |Summan av alla avgifter inklusive moms för den här faktureringsperioden |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Hur kontrollerar jag att avgifterna på fakturan stämmer?
Om du vill se mer information om någon av avgifterna på fakturan kan du läsa i [Förstå din faktura för Microsoft Azure](billing-understand-your-bill.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
