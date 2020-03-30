---
title: Vanliga frågor om säljares insikter
description: Vanliga frågor och svar om funktionen Säljarstatistik i Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285392"
---
<a name="seller-insights-faq"></a>Vanliga frågor om säljares insikter
===================

Den här artikeln innehåller vägledning för vanliga användarprocedurer inom och frågor om säljarinsikter.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Hitta definitioner för värdena i den hämtade transaktionsfilen
------------------------------------------------------------------

Definitionerna av måttvärdena i transaktionsfilen finns i artikeln [Seller Insights Definitions](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Se kundinformation om transaktioner som jag har fått betalt för
-------------------------------------------------------------

När du har hämtat dina transaktioner från utbetalningsmodulen letar du reda på kolumnen **utbetalningsstatus**och använder filtret för att bara visa värdet "Utbetalt". Följande kolumner visas som innehåller kundinformation: **Företagsnamn,** **Kundpost,** **Kundland,** **Kundtillstånd**och **Kundpostnummer**.


<a name="calculate-my-open-accounts-receivable"></a>Beräkna min öppna kundfordran
-------------------------------------

När du har laddat ned dina transaktioner från utbetalningsmodulen letar du reda på kolumnen **utbetalningsstatus**och använder filtret för att endast visa värdet "Kommande utbetalning" och "Inte redo för utbetalning". Summera sedan kolumnen med etiketten **utbetalningsbelopp (PC).**


<a name="calculate-revenue-by-customer-usage-period"></a>Beräkna intäkter efter kundanvändningsperiod
------------------------------------------

När du har hämtat dina transaktioner från utbetalningsmodulen letar du reda på kolumnen **transaktionsstatus**och filtrerar värdet "Betald".   För varje transaktion som anges representerar kolumnen **utbetalningsbelopp (PC)** det belopp du har betalat.  Om du vill uppskatta användningsperioden som är associerad med transaktionen använder du kolumnen **Debiteringsdatum**, som är en nära approximation av den sista användningsdagen för den period som transaktionen gäller.


<a name="calculate-your-bad-debt"></a>Beräkna din osäkra skuld
---------------------

När du har hämtat dina transaktioner från utbetalningsmodulen letar du reda på kolumnen **Slutlig samlingsstatus**och använder filtret för att bara visa värdet "Skriv av". Summera sedan kolumnen med etiketten **utbetalningsbelopp (PC).**


<a name="view-payout-or-customer-contact-information"></a>Visa utbetalnings- eller kundkontaktinformation
-------------------------------------------

Logga in som användare med rollen "ägare" och inte rollen "deltagare". Endast ägarrollen ser utbetalnings- och kundinformation. Du kan läsa mer om användarroller i artikeln [Hantera användare](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Beräkna mina förskottsutbetalningar
----------------------------

När du har hämtat dina transaktioner från utbetalningsmodulen letar du reda på kolumnen **transaktionstyp**och använder filtret för att bara visa värdet "Ladda". Leta sedan upp kolumnen **Slutlig samlingsstatus**och använd filtret för att bara visa värdet "Pågår". Slutligen, **summera kolumnen Utbetalningsbelopp (PC)** för att beräkna alla förskott som betalats till dig före insamling från kunden.


<a name="calculate-customer-refunds"></a>Beräkna återbetalningar av kunder
--------------------------

När du har hämtat dina transaktioner från utbetalningsmodulen letar du reda på kolumnen **Slutlig samlingsstatus**och använder filtret för att bara visa värdet "Återbetalning". Summera kolumnen **Debiteringsbelopp (PC)** för att beräkna alla återbetalningar som bearbetats för dina kunder.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identifiera vilka transaktioner som involverade en Microsoft Channel-partner
----------------------------------------------------------------

Alla transaktioner i kolumnen **Azure-licenstyp** som filtreras för att visa värdena "Enterprise through Reseller" och "Cloud Solution Provider" involverar en Microsoft Channel-partner. För mer information om partnern, kan du hitta deras **återförsäljare Namn** och **återförsäljare e-post** i utbetalningsmodulen nedladdning och kundmodulen nedladdning.


<a name="identify-trial-usage-and-trial-conversions"></a>Identifiera testanvändning och utvärderingsversioner
------------------------------------------

Hämtningar av order-, användnings- och utbetalningsmoduler innehåller nu **utvärderingsdatum** för att hjälpa dig att förstå när provperioden avslutades för den specifika ordern, där så är tillämpligt. Om du vill se utvärderingsanvändning och utvärderingsorder letar du reda på kolumnen **SKU Billing Type** i hämtningarna och använder filtret för att bara visa värdet "Utvärderingsversion". Om du vill se utvärderingskonverteringar letar du reda på kolumnen Slutdatum för **utvärderingsversionen** i hämtningarna och använder filtret för att endast visa order när **slutdatumet för utvärderingsversionen** är passerat dagens datum och kolumnen **Avbryt datum** är tom eller senare än **utvärderingsslutdatumet**.


<a name="when-is-my-monthly-payout-calculated"></a>När beräknas min månadsutbetalning
------------------------------------

Dina utbetalningar utfärdas till dig senast den 15:e varje månad för alla belopp som är klara för utbetalning senast den sista kalenderdagen i föregående månad. Den tredje dagen i månaden beräknar Microsoft föregående månads utbetalningsbelopp och uppdaterar alla tillämpliga avgiftstransaktioner i din nedladdning med "Kommande utbetalning" i kolumnen **Utbetalningsstatus.** Dessa transaktioner kommer att stanna i det tillståndet tills betalningsbegäran skickas till ditt bankkonto, då deras **utbetalningsstatus** kommer att uppdateras till "Betald ut", och "Utbetalningsdatum" kommer att uppdateras för att visa det datum då vi skickade betalningsbegäran till din bank.


<a name="calculate-customer-acquisition-and-loss"></a>Beräkna kundförvärv och förlust
---------------------------------------

Du kan se det datum då kunden först köpte ett av dina erbjudanden genom att hitta kolumnen **Inköpt datum** i kundhämtningen. På samma sätt kan du se det datum efter vilket de inte längre hade något erbjudande publicerat av dig genom att hitta kolumnen **Datum förlorade** i kundhämtningen.


<a name="finding-more-help"></a>Hitta mer hjälp
-----------------

- [Definitioner av säljarinsikter](./si-insights-definitions-v4.md) – Hitta definitioner för mätvärden och data

- [Komma igång med säljarens insikter](./si-getting-started.md) - Introduktion till säljaren Insights-funktionen.

