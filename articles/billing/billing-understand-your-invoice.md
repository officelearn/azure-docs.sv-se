---
title: Förstå fakturan Azure
description: Lär dig hur du läser och förstår användningen och fakturan för Azure-prenumerationen
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 38126e4539719ba56e6e5eac5e860cea9b49d446
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205966"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Förstå villkor på fakturan Microsoft Azure

Fakturan innehåller en sammanfattning av dina debiteringar och innehåller instruktioner för betalning. Den är tillgänglig för nedladdning i Portable Document-Format (PDF) från den [Azure-portalen](https://portal.azure.com/) eller kan skickas via e-post. Mer information finns i [få dina Azure billing faktura och dagligen användningsdata](billing-download-azure-invoice-daily-usage-date.md).

Några saker att Observera:

-   Om du använder en kostnadsfri utvärderingsprenumeration får du information om din detaljerad användning i Azure-portalen, men du har inte en faktura.

-   Upp till 24 timmar för användning i slutet av faktureringsperioden tidigare kan visas i din aktuella fakturan.

-   Avgifterna som visas på fakturering instruktioner för internationella kunder är endast för beräkning av. Banker kan ha olika kostnader för konvertering priser.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Detaljerade villkor och beskrivningar av fakturan
Följande avsnitt listar de viktiga termer som du ser på din faktura och beskrivningar av varje term.

### <a name="account-information"></a>Kontoinformation

Avsnittet kontoinformation på fakturan är överst på första sidan som visar information om din profil och prenumeration.

![Kontot i avsnittet information i faktura](./media/billing-understand-your-invoice/1.png)

| Period | Beskrivning |
| --- | --- |
| Kundens Inköpsordernummer |Ett valfritt inköpsordernummer som tilldelats av du för spårning |
| Fakturanr |Ett unikt, Microsoft genererade fakturan tal som används i spårningssyfte |
| Faktureringscykel |Datumintervall som omfattar den här fakturan |
| Fakturadatum |Datum då fakturan har skapats, vanligtvis en dag efter slutet av fakturerings-cykel |
| Betalningsmetod |Typ av betalning som används för kontot (faktura eller kreditkort) |
| Fakturera till |Faktureringsadress som anges för kontot |
| Prenumerationen erbjuder (”betala per användning”) |Typ av prenumerationserbjudande som har köpts (betala per användning, BizSpark Plus, Azure-Pass, etc.). Mer information finns i [Azure erbjudandetyper](https://azure.microsoft.com/support/legal/offer-details/). |
| E-postadress till kontoägaren | Den e-postadress som Microsoft Azure-kontot är registrerat under. <br /><br />Om du vill ändra e-postadressen [ändra profilinformation i ditt Azure-konto som kontaktens e-post, adress och telefonnummer](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Förstå sammanfattningen av fakturan
Den **fakturasammanfattning** avsnittet på fakturan visas total transaktionsbelopp sedan den senaste faktureringsperioden och dina aktuella användningsavgifter.

![Fakturan sammanfattningen](./media/billing-understand-your-invoice/2.png)

Prenumerationsnamnet (”produktion lagring”) är namnet på prenumerationen för den här fakturan.

#### <a name="understand-the-previous-charges"></a>Förstå tidigare avgifterna
Den tidigare, betalningar och utestående saldo avsnittet på fakturan sammanfattas transaktioner sedan den senaste faktureringsperioden.

| Period | Beskrivning |
| --- | --- |
| Föregående saldo |Totalsumman från din senaste faktureringsperioden |
| Betalningar |Totalt antal betalningar och krediter som tillämpas på din senaste faktureringsperioden |
| Utestående saldo (från föregående faktureringsperiod) |Alla krediter eller återstoden i ditt konto sedan den senaste faktureringsperioden |

#### <a name="understand-the-current-charges"></a>Förstå aktuella debiteringar
Avsnittet aktuella avgifter för fakturan visar information om dina månatliga avgifter för den aktuella faktureringsperioden.

| Period | Beskrivning |
| --- | --- |
| Avgifter för användning |Avgifter för användning är den totala månatliga debiteringen för en prenumeration för den aktuella faktureringsperioden|
| Rabatter |Tjänsten rabatter för din aktuella faktureringsperioden|
| Justeringar |Diverse krediter (ledigt användning, krediter osv.) eller utestående avgifter som tillämpas på din aktuella faktureringsperioden.<br/><br/>Om du har Visual Studio Enterprise med MSDN erbjudande finns till exempel en månadskredit. Om du avbryter prenumerationen finns månatliga användningskostnader som överskrider månadskredit som du får med erbjudandet prenumeration. Avgifterna innebära i början av den aktuella faktureringsperioden tills Annulleringsdatum prenumeration. |

#### <a name="sold-to-and-payment-instructions"></a>Säljs till och betalningsinstruktioner

I följande tabell beskrivs de säljs till och betalning anvisningarna som visas på den andra sidan på fakturan.

| Period |Beskrivning |
| --- | --- |
| Såldes till |Profil-adress som är på kontot. <br/><br/>Om du behöver ändra adressen finns [ändra profilinformation i ditt Azure-konto som kontaktens e-post, adress och telefonnummer](billing-how-to-change-azure-account-profile.md).|
| Betalningsanvisningar |Instruktioner om hur du betalar beroende på betalningsmetod (exempelvis med kredit kort eller av en faktura). |

#### <a name="usage-charges"></a>Användningsdebitering

Avsnittet användning avgifter för fakturan visar mätaren nivåinformation på dina debiteringar.

![Användning avgifter avsnitt](./media/billing-understand-your-invoice/3.png)

I följande tabell beskrivs användning avgifter kolumnrubriker visas på fakturan.

| Period |Beskrivning |
| --- | --- |
| Namn |Identifierar tjänsten på den översta nivån för användning |
| Typ |Definierar den Azure-tjänst-typ som kan påverka hastigheten |
| Resurs |Identifierar måttenheten för mätaren används |
| Region |Identifierar platsen för datacenter för vissa tjänster som är mest baserat på plats för datacenter |
| Förbrukad |Mängden mätaren används under faktureringsperioden |
| Ingår |Mängden mätaren som ingår kostnadsfritt i din aktuella faktureringsperioden |
| Faktureringsbar |Visar skillnaden mellan antalet används och det antal som ingår. Det är debiteras för den här mängden. Betala per användning erbjudanden utan belopp som ingår i erbjudandet, till är den samma som antalet används |
| Pris |Hastigheten med vilken du är debiteras per fakturerbar enhet |
| Värde |Visar resultatet av att multiplicera kolumnen överförbrukning antal efter kolumnen hastighet. Om antalet används inte överstiger antalet ingår, är gratis i den här kolumnen. |
| Delsumma |Summan av alla dina kostnader före skatt för denna faktureringsperiod |
| Totalsumma |Summan av alla kostnader efter skatt för denna faktureringsperiod |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Hur gör jag till att tillägg i min faktura är korrekta?
Om det finns en avgift på fakturan som du vill ha mer information på, se [förstå fakturan för Microsoft Azure.](billing-understand-your-bill.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
