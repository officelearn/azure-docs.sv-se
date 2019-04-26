---
title: Säljaren insikter vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor om funktionen försäljning insikter i partnerportalen i molnet.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: ec7fc3a7877cf0bffac0043a74c34d6f0f04826b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362381"
---
<a name="seller-insights-faq"></a>Säljaren insikter vanliga frågor och svar
===================

Den här artikeln innehåller anvisningar för vanliga användarprocedurer i och frågor om försäljning insikter.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Hitta definitioner för värdena i filen hämtade transaktioner
------------------------------------------------------------------

Definitioner av mått värden i filen transaktion finns i artikeln [försäljning Insights definitioner](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Se kundens information om transaktioner som jag har betalats
-------------------------------------------------------------

Leta upp kolumnen efter nedladdningen dina transaktioner från modulen Payout **Payout Status**, och Använd filter för att bara visa värdet ”betald ut”. Följande kolumner visas som innehåller kundinformation: **Företagsnamn**, **kund e-post**, **kundens land**, **kunden tillstånd**, och **kunden postnummer**.


<a name="calculate-my-open-accounts-receivable"></a>Beräkna min öppna kundreskontra
-------------------------------------

Leta upp kolumnen efter nedladdningen dina transaktioner från modulen Payout **Payout Status**, och Använd filter för att bara visa värdet ”kommande Payout” och ”ej redo för Payout”. Summera kolumnen **Payout belopp (PC)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Beräkna intäkter genom att kunden användningsperiod
------------------------------------------

Leta upp kolumnen efter nedladdningen dina transaktioner från modulen Payout **transaktionsstatus**, och filtrera värdet ”betald”.   För varje transaktion som anges, kolumnen **Payout belopp (PC)** representerar det du har betalat.  Om du vill beräkna användningsperiod som är associerad med transaktionen måste använda kolumnen **Belastningsdatum**, vilket är en Stäng uppskattning av den sista dagen i användningen för perioden som gäller för transaktionen.


<a name="calculate-your-bad-debt"></a>Beräkna din detta
---------------------

Leta upp kolumnen efter nedladdningen dina transaktioner från modulen Payout **slutlig Status för samlingen**, och tillämpa filtret för att endast visa värdet för ”skriva ut”. Summera kolumnen **Payout belopp (PC)**.


<a name="view-payout-or-customer-contact-information"></a>Visa payout eller kundens kontaktinformation
-------------------------------------------

Logga in som en användare med rollen ”ägare” och inte rollen ”deltagare”. Endast ägarrollen visas betalnings-och kundinformation. Du hittar mer information om användarroller i artikeln [hantera användare](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Beräkna min avancerade payouts
----------------------------

Leta upp kolumnen efter nedladdningen dina transaktioner från modulen Payout **transaktionstyp**, och Använd filter för att bara visa värdet ”kostnad”. Leta sedan upp kolumnen **slutlig Status för samlingen**, och Använd filter för att bara visa värdet ”pågår”. Slutligen summera det **Payout belopp (PC)** kolumnen för att beräkna utvecklingen av alla betalas till dig innan du samlingen från kunden.


<a name="calculate-customer-refunds"></a>Beräkna kunden återbetalningar
--------------------------

Leta upp kolumnen efter nedladdningen dina transaktioner från modulen Payout **slutlig Status för samlingen**, och Använd filter för att bara visa värdet ”bidrag”. Summan i **kostnad belopp (PC)** kolumnen för att beräkna alla återbetalningar bearbetas för dina kunder.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identifiera vilka transaktioner som ingår en kanal Microsoft-Partner
----------------------------------------------------------------

Alla transaktioner i kolumnen **Azure licenstyp** som har filtrerats för att visa värdena ”Enterprise via återförsäljare” och ”Cloud Solution Provider” omfattar en kanal Microsoft-Partner. Du hittar mer information om partnern sina **återförsäljarens namn** och **återförsäljarens e-post** i betalnings modulen nedladdningen och nedladdning av kund-modulen.


<a name="identify-trial-usage-and-trial-conversions"></a>Identifiera utvärderingsversion användnings- och utvärderingsversion konverteringar
------------------------------------------

Ordning, användning och betalnings nedladdningar för modulen innehåller nu **utvärderingsversion slutdatum** för att förstå när utvärderingsperioden är slut slut för den specifika ordningen, om tillämpligt. Om du vill se utvärderingsversion användnings- och beställningar, letar du upp den **SKU fakturering typ** kolumn i nedladdningar, och Använd filter för att bara visa värdet ”utvärdering”. Om du vill se utvärderingsversion konverteringar letar du upp den **utvärderingsversion slutdatum** kolumn i nedladdningar, och tillämpa filtret för att bara visa order när den **utvärderingsversion slutdatum** har passerat dagens datum och **Avbryt datum** kolumnen är tom eller senare än den **utvärderingsversion slutdatum**.


<a name="when-is-my-monthly-payout-calculated"></a>När beräknas min månatliga payout
------------------------------------

Din payouts utfärdas till dig av 15: e i månaden för alla mängder som är redo för payout av den sista kalender dagen i föregående månad. Den tredje dagen i månaden, kommer Microsoft beräkna den föregående månaden betalnings beloppet och uppdatera alla tillämpliga kostnad transaktioner i din nedladdning med ”kommande Payout” i den **Payout Status** kolumn. De här transaktionerna ska vara kvar i det aktuella tillståndet tills betalning-begäran skickas till din bank-konto, då deras **Payout Status** kommer att uppdateras till ”betalas ut” och ”Payout datum” kommer att uppdateras för att visa det datum då vi har skickat in den begäran om betalning din bank.


<a name="calculate-customer-acquisition-and-loss"></a>Beräkna kunden anskaffning och förlust
---------------------------------------

Du kan se det datum när kunden först köpte en av dina erbjudanden genom att leta upp den **Date förvärvat** kolumn i användaren hämta. På samma sätt du kan se det datum då de inte längre hade ett erbjudande som publicerats av dig genom att leta upp den **datum förlorat** kolumn i nedladdning av kunden.


<a name="finding-more-help"></a>Hitta mer hjälp
-----------------

- [Definitioner av försäljning Insights](./si-insights-definitions-v4.md) -hitta definitioner för mått och data

- [Komma igång med försäljning Insights](./si-getting-started.md) – introduktion till funktionen försäljning insikter.

