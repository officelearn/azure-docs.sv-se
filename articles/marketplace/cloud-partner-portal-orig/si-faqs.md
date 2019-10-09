---
title: Vanliga frågor och svar om säljar insikter
description: Vanliga frågor om säljar insikter-funktionen i Cloud Partner Portal.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 059ec175a48cdcdec6214a6581452ec0536bf566
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030448"
---
<a name="seller-insights-faq"></a>Vanliga frågor och svar om säljar insikter
===================

Den här artikeln innehåller rikt linjer för vanliga användar procedurer i och frågor om säljar insikter.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Hitta definitioner för värdena i den hämtade transaktions filen
------------------------------------------------------------------

Definitionerna för Metric-värden i transaktions filen finns i artikeln [säljare insikter definitioner](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Se kund information om transaktioner för vilka jag har betalat
-------------------------------------------------------------

När du har laddat ned transaktionerna från modulen utbetalning letar du upp kolumnen med namnet **utbetalnings status**och använder filtret för att endast visa värdet "betald". Följande kolumner kommer att visas med kund information: **Företagets namn**, **kundens e-post**, **kund land**, **kund stat**och **kund post nummer**.


<a name="calculate-my-open-accounts-receivable"></a>Beräkna mina öppna kund fordringar
-------------------------------------

När du har laddat ned dina transaktioner från modulen betalning letar du upp kolumnen med namnet **utbetalnings status**och använder filtret för att endast visa värdet "kommande utbetalning" och "inte redo för utbetalning". Summera sedan kolumnen med namnet **utbetalnings mängd (PC)** .


<a name="calculate-revenue-by-customer-usage-period"></a>Beräkna intäkter per kund användnings period
------------------------------------------

När du har laddat ned dina transaktioner från modulen betalning letar du upp kolumnen med namnet **transaktions status**och filtrerar värdet "betald".   Kolumnen med namnet **utbetalnings belopp (PC)** representerar den mängd som du har betalat för varje transaktion i listan.  Om du vill beräkna den användnings period som är kopplad till transaktionen använder du kolumnens **avgifts datum**, som är en nära approximation av den sista förbruknings dagen för den period som transaktionen gäller.


<a name="calculate-your-bad-debt"></a>Beräkna din dåliga skuld
---------------------

När du har laddat ned dina transaktioner från modulen betalning letar du upp kolumnen med namnet **slutlig samling**och använder filtret för att endast visa värdet "skriva av". Summera sedan kolumnen med namnet **utbetalnings mängd (PC)** .


<a name="view-payout-or-customer-contact-information"></a>Visa utbetalnings-eller kund kontakt information
-------------------------------------------

Logga in som en användare med rollen "ägare" och inte rollen "bidrag". Endast ägar rollen kommer att se utbetalnings-och kund information. Du kan läsa mer om användar roller i artikeln [Hantera användare](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Beräkna mina förskotts utbetalningar
----------------------------

När du har laddat ned transaktionerna från modulen betalning letar du upp kolumnen med namnet **transaktions typ**och använder filtret för att bara visa värdet "avgift". Leta sedan upp kolumnen med etikettens **slutliga samlings status**och använd filtret för att endast visa värdet "pågår". Slutligen summera kolumnen **utbetalnings belopp (dator)** för att beräkna alla förskott som betalats till dig innan du insamlingen från kunden.


<a name="calculate-customer-refunds"></a>Beräkna kund åter betalningar
--------------------------

När du har laddat ned dina transaktioner från modulen utbetalning, letar du upp kolumnen med etikettens **slutliga samlings status**och använder filtret för att endast visa värdet "åter betalning". Sammanfatta kolumnen **avgifts belopp (PC)** för att beräkna alla åter betalningar som har bearbetats för dina kunder.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identifiera vilka transaktioner som ingår i en Microsoft Channel-partner
----------------------------------------------------------------

Alla transaktioner i kolumnen **Azure-licensserver** som filtreras för att visa värdena "Enterprise genom åter försäljare" och "Cloud Solution Provider" omfattar en Microsoft Channel-partner. Om du vill ha mer information om partnern kan du hitta **åter försäljarens namn** och **åter försäljarens e-post** i hämtnings modulen och kund-modulen.


<a name="identify-trial-usage-and-trial-conversions"></a>Identifiera utvärderings användning och utvärderings versioner
------------------------------------------

Hämtnings bara filer för order, användning och utbetalning innehåller nu **slutdatum för utvärdering** som hjälper dig att förstå när utvärderings perioden är slut för den specifika ordningen, i förekommande fall. Om du vill se utvärderings användning och-beställningar letar du upp kolumnen **SKU-fakturerings typ** i nedladdningarna och använder filtret för att bara visa värdet "utvärderings version". Om du vill se utvärderings versioner, letar du upp kolumnen **utvärderings datum för utvärderings** version i nedladdningarna och använder filtret för att bara visa order när **slutdatumet för utvärderings versionen** har passerat datum och kolumnen **annullera datum** är tom eller senare än **slutdatum för utvärderings versionen**.


<a name="when-is-my-monthly-payout-calculated"></a>När beräknas min månads betalning
------------------------------------

Dina utbetalningar utfärdas till dig den 15: e i varje månad för alla belopp som är klara för utbetalning den senaste kalender dagen i föregående månad. På den tredje dagen i månaden beräknar Microsoft föregående månads utbetalnings belopp och uppdaterar alla tillämpliga avgifts transaktioner i nedladdningen med "kommande utbetalning" i kolumnen **utbetalnings status** . Transaktionerna förblir i det tillståndet tills betalnings förfrågan skickas till ditt bank konto, då deras **utbetalnings status** uppdateras till "betald" och "utbetalnings datum" uppdateras för att visa det datum då vi skickade in betalnings förfrågan till din banken.


<a name="calculate-customer-acquisition-and-loss"></a>Beräkna kund förvärv och-förlust
---------------------------------------

Du kan se datumet då kunden först köpte ett av dina erbjudanden genom att leta reda på kolumnen inhämtat **datum** i kund hämtningen. På samma sätt kan du se det datum efter vilket de inte längre hade ett erbjudande som publicerats av dig, genom att leta upp kolumnen **datum förlorat** i kund hämtningen.


<a name="finding-more-help"></a>Hitta mer hjälp
-----------------

- [Säljar insikter-definitioner](./si-insights-definitions-v4.md) – hitta definitioner för mått och data

- [Kom igång med säljare insikter](./si-getting-started.md) – introduktion till säljar insikter-funktionen.

