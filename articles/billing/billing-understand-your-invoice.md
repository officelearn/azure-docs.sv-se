---
title: Förstå fakturan för Azure | Microsoft Docs
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897460"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Förstå villkoren på din faktura för Microsoft Azure

Fakturan innehåller en sammanfattning av dina avgifter och instruktioner för betalning. Den är tillgänglig för hämtning i PDF--Format (PDF) från den [Azure-portalen](https://portal.azure.com/) eller skickas via e-post. Mer information finns i [så här hämtar du din Azure-fakturering och daglig användningsdata](billing-download-azure-invoice-daily-usage-date.md).

Några saker att tänka på:

-   Om du använder en kostnadsfri utvärderingsprenumeration får du detaljerad användning-information i Azure-portalen, men du behöver inte en faktura.

-   Upp till 24 timmars användning i slutet av den tidigare faktureringsperioden kan visas i den aktuella fakturan.

-   Avgifter som anges på faktureringsutdragen för internationella kunder är endast uppskattade. Banker kan ha olika kostnader för valutaomvandling.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Detaljerade villkor och beskrivningar av din faktura
Följande avsnitt listar de viktiga termer som du ser på din faktura och beskrivningar av varje term.

### <a name="account-information"></a>Kontoinformation

Avsnittet med kontoinformationen för fakturan är överst på första sidan som visar information om profilen och prenumeration.

![Avsnittet med kontoinformationen av faktura](./media/billing-understand-your-invoice/1.png)

| Period | Beskrivning |
| --- | --- |
| Kundens Inköpsordernummer |Ett valfritt inköpsordernummer som tilldelats av dig för spårning |
| Fakturanr |Ett unikt, Microsoft genererade fakturanummer som används i spårningssyfte |
| Faktureringscykel |Datumintervall som omfattar den här fakturan |
| Fakturadatum |Datum då fakturan skapades, vanligtvis en dag efter slutet av faktureringscykel |
| Betalningsmetod |Typ av betalning som används för kontot (faktura eller kreditkort) |
| Fakturera till |Faktureringsadress som anges för kontot |
| Prenumerationen erbjuder (”betala per användning”) |Typ av prenumerationserbjudande som har köpts (betala per användning, BizSpark Plus, Azure-Pass osv.). Mer information finns i [Azure erbjudandetyper](https://azure.microsoft.com/support/legal/offer-details/). |
| E-postadress till kontoägaren | Den e-postadress som Microsoft Azure-kontot är registrerat under. <br /><br />Om du vill ändra den e-postadressen, se [så här ändrar du profilinformation för ditt Azure-konto, till exempel kontaktpersonens e-postadress, adress och telefonnummer](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Förstå sammanfattningen av fakturan
Den **fakturasammanfattning** avsnitt på fakturan visas total transaktionsbelopp sedan den senaste faktureringsperioden samt aktuella användningsdebiteringar.

![Sammanfattningen av fakturan](./media/billing-understand-your-invoice/2.png)

Prenumerationsnamn (”Produktionslagring”) är namnet på prenumerationen för den här fakturan.

#### <a name="understand-the-previous-charges"></a>Förstå tidigare debiteringar
Föregående saldo, betalningar och utestående saldo delen av fakturan sammanfattar transaktionerna sedan din senaste faktureringsperiod.

| Period | Beskrivning |
| --- | --- |
| Föregående saldo |Totalsumman från din senaste faktureringsperiod |
| Betalningar |Totala betalningar och krediter som tillämpats på din senaste faktureringsperiod |
| Utestående saldo (från föregående faktureringsperiod) |Alla krediter eller kvarvarande saldo i ditt konto sedan den senaste faktureringsperioden |

#### <a name="understand-the-current-charges"></a>Förstå aktuella debiteringar
I avsnittet aktuella debiteringar på fakturan visar information om dina månatliga debiteringar för den aktuella faktureringsperioden.

| Period | Beskrivning |
| --- | --- |
| Avgifter för användning |Användningsdebitering är totala månadsdebiteringar i en prenumeration för den aktuella faktureringsperioden|
| Rabatter |Tjänstrabatter på den aktuella faktureringsperioden|
| Justeringar |Diverse krediter (kostnadsfri användning, krediter osv.) eller obetalda avgifter som påförts den aktuella faktureringsperioden.<br/><br/>Till exempel om du har Visual Studio Enterprise med MSDN-erbjudandet kan se du en månatlig kredit. Om du avbryter din prenumeration kan du se månatliga användningskostnader som överstiger den månatliga krediten som är tillgängliga i ditt prenumerationserbjudande. Avgifterna debiteras i början av din aktuella faktureringsperiod tills Annulleringsdatum prenumeration. |

#### <a name="sold-to-and-payment-instructions"></a>Såldes till och betalningsanvisningar

I följande tabell beskrivs de Utsålt till och betalningsanvisningar som visas på den andra sidan i din faktura.

| Period |Beskrivning |
| --- | --- |
| Såldes till |Profil-adress som ligger på kontot. <br/><br/>Om du vill ändra adressen kan se [så här ändrar du profilinformation för ditt Azure-konto, till exempel kontaktpersonens e-postadress, adress och telefonnummer](billing-how-to-change-azure-account-profile.md).|
| Betalningsanvisningar |Anvisningar om hur du betalar beroende på betalningsmetod (till exempel med kredit kortet eller med faktura). |

#### <a name="usage-charges"></a>Användningsdebitering

Avsnittet användning avgifter på fakturan visar mätaren nivåinformation på dina kostnader.

![Användning i avsnittet debiteringar](./media/billing-understand-your-invoice/3.png)

I följande tabell beskrivs kolumnrubrikerna för användning avgifter visas på fakturan.

| Period |Beskrivning |
| --- | --- |
| Namn |Identifierar huvudtjänst för användning |
| Type |Definierar den Azure-tjänst-typ som kan påverka priset |
| Resurs |Anger måttenhet för den mätning som används |
| Region |Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret |
| Förbrukad |Hur mycket av används under faktureringsperioden |
| Ingår |Mängden mätaren som ingår utan kostnad i den aktuella faktureringsperioden |
| Faktureringsbar |Visar skillnaden mellan det antal som används och det antal som ingår. Du faktureras för den mängden. För användningsbaserad betalning där inga mängder ingår i erbjudandet, är denna summa samma som den förbrukade mängden |
| Pris |Det pris du debiteras per faktureringsbar enhet |
| Värde |Visar resultatet av gånger kolumnen pris överförbrukning kvantitet kolumnen. Om Förbrukat antal inte överstiger det antal som ingår, kostar ingenting i den här kolumnen. |
| Delsumma |Summan av alla dina kostnader före skatt för den här faktureringsperioden |
| Totalsumma |Summan av alla dina kostnader efter skatt för den här faktureringsperioden |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Hur gör jag till att kostnader på min faktura är korrekta?
Om det finns en avgift på fakturan som du vill ha mer information på, se [förstå fakturan för Microsoft Azure.](billing-understand-your-bill.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
