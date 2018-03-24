---
title: Visa i Azure Traffic Manager-trafik | Microsoft Docs
description: Introduktion till vyn för Traffic Manager-trafik
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 7ce51017fdee92e5589c06b398c9650930d5436d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager trafik vy

Traffic Manager ger du med DNS-nivå routning så att användarna dirigeras till Felfri slutpunkter baserat på routningsmetod angav när du skapade profilen. Trafik vyn ger en överblick över dina användarbaser (på en DNS-matchare filnivå) och deras trafik mönster Traffic Manager. När du aktiverar trafik visa bearbetas informationen för att ge dig tillämplig insikter. 

Med hjälp av vyn för trafiken, kan du:
- förstå var din användarbaser finns (upp till en lokal DNS-matchare nivån kornighet).
- Visa mängden trafik (observerats som DNS-frågor som hanteras av Azure Traffic Manager) från dessa regioner.
- få insikter om vad som är representativ svarstid som dessa användare har erfarit.
- ingående till viss trafikmönster från var och en av dessa användarbaser till Azure-regioner där du har slutpunkter. 

Du kan till exempel använda trafik vyn för att förstå vilka regioner som har ett stort antal trafik men drabbas av ökad latens. Därefter kan du använda den här informationen för att planera din storleken expansion till nya Azure-regioner så att dessa användare kan ha en lägre latens upplevelse.

## <a name="how-traffic-view-works"></a>Hur fungerar trafiken vy

Visa trafik fungerar genom att låta Traffic Manager titta på inkommande frågor som tagits emot de senaste sju dagarna mot en profil som har den här funktionen är aktiverad. I den inkommande frågor informationen extraherar trafik visa käll-IP för DNS-matchning som används som en representation av platsen där användare. Dessa sedan grupperas på en DNS-matchare nivån kornighet skapa användaren grundläggande regioner med hjälp av geografisk information av IP-adresser som underhålls av Traffic Manager. Traffic Manager ser då på Azure-regioner som frågan vidarebefordrades och skapar en trafik flödet karta för användare från dessa regioner.  
I nästa steg Traffic Manager korrelerar användaren grundläggande region för Azure-region-mappning med nätverket intelligence latens tabeller som det finns för olika slutanvändarens nätverk för att förstå genomsnittlig svarstid användare från dessa regioner upplever när ansluter till Azure-regioner. Dessa beräkningar kombineras sedan på en per lokala DNS-matchare IP-nivå innan den visas för dig. Du kan använda informationen på olika sätt.

>[!NOTE]
>Latens som beskrivs i trafik vyn är en representativ fördröjning mellan användaren och Azure-regioner som de hade anslutet till och är inte DNS-sökning svarstiden. Trafik vyn gör en bästa prestanda uppskattning av fördröjningen mellan den lokala DNS-matcharen och Azure-regionen frågan har vidarebefordrats till, om det finns tillräckligt med data och sedan svarstiden returnerade ska vara null. 

## <a name="visual-overview"></a>Visuell översikt

När du navigerar till den **trafik visa** avsnitt i Traffic Manager-sidan visas en geografisk karta med övertäckning trafik visa insikter. Kartan innehåller information om användarbas- och slutpunkter för Traffic Manager-profilen.

### <a name="user-base-information"></a>Grundläggande användarinformation

För de lokala DNS-matchare för vilken plats information är tillgänglig visas de i kartan. Färgen för DNS-matchning anger genomsnittlig svarstid erfarenhet av slutanvändare som används som DNS-matchning för sina Traffic Manager-frågor.

Om du hovrar över en DNS-matchare plats i kartan visas:
- IP-adressen för DNS-matchning
- volymen av DNS-frågorna som visas av Traffic Manager från den
- slutpunkter till vilken trafik från DNS matcharen vidarebefordrades som en linje mellan slutpunkten och DNS-matchning 
- Genomsnittlig svarstid från den platsen till slutpunkten som färgen på raden ansluter dem

### <a name="endpoint-information"></a>Slutpunkten

Azure-regioner där slutpunkterna finns visas som blå punkterna i kartan. Om slutpunkten är extern och har inte mappats till en Azure-region, visas den överst på kartan. Klicka på någon slutpunkt som finns på olika platser (baserat på DNS-matcharen används) från när trafiken har skickas till denna slutpunkt. Anslutningarna visas som en rad mellan slutpunkten och DNS-matchare plats och färgade enligt representativt fördröjningen mellan paret. Dessutom kan du se namnet på slutpunkten, Azure-regionen där den körs och det totala antalet begäranden som kommer till den av den här trafikhanterarprofilen.


## <a name="tabular-listing-and-raw-data-download"></a>Hämta Tabular lista och rådata

Du kan visa trafik visa data i tabellformat i Azure-portalen. Det finns en post för varje DNS-matchare IP / endpoint koppla som visar IP-adressen för DNS-matchning, namn och geografiska platsen för Azure-regionen i som slutpunkten finns (om tillgängligt), volymen av begäranden är kopplade till den DNS-matchning till denna slutpunkt och representativ svarstid som är kopplade till användare med hjälp av DNS (i förekommande fall). Du kan också hämta trafik visa data som en CSV-fil som kan användas som en del av ett arbetsflöde för analytics önskat.

## <a name="billing"></a>Fakturering

När du använder trafik vyn debiteras baserat på antalet datapunkter som används för att skapa insikter visas. Typ av endast data används är för närvarande frågor som tagits emot mot Traffic Manager-profilen. Mer information om prissättning, finns det [Traffic Manager sida med priser](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Nästa steg

- Läs [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Lär dig mer om den [routning av nätverkstrafik metoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

