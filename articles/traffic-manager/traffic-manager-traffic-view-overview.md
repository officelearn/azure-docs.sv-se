---
title: Visa i Azure Traffic Manager-trafik | Microsoft Docs
description: "Introduktion till vyn för Traffic Manager-trafik"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: 0a07ff578c6afeedc6f3806d52bfe5aef6945c04
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager trafik vy

>[!NOTE]
>Funktionen trafik vyn i Traffic Manager finns i Public Preview och kanske inte har samma nivå av tillgänglighet och tillförlitlighet som viktiga funktioner som är i allmänhet tillgänglighet. Funktionen stöds inte, kan ha begränsad kapacitet och kanske inte tillgänglig på alla platser i Azure. Den senaste meddelanden på tillgänglighet och status för den här funktionen, kontrollera den [Azure Traffic Manager uppdaterar](https://azure.microsoft.com/updates/?product=traffic-manager) sidan.

Traffic Manager ger DNS-nivå routning så att användarna dirigeras till Felfri slutpunkter baserat på routningsmetoden som du har skapat när profilen skapas. Detta ger en överblick över dina användarbaser (på en DNS-matchare filnivå) och deras trafik mönster Traffic Manager. När du aktiverar trafik visa bearbetas informationen för att ge dig tillämplig insikter. 

Med hjälp av vyn för trafiken, kan du:
- förstå var din användarbaser finns (upp till en lokal DNS-matchare nivån kornighet).
- Visa mängden trafik (observerats som DNS-frågor som hanteras av Azure Traffic Manager) från dessa regioner.
-  få insikter om vad som är representativ svarstid som dessa användare har erfarit.
- ingående till viss trafikmönster från var och en av dessa användarbaser till Azure-regioner där du har slutpunkter. 

Du kan till exempel använda trafik vyn för att förstå vilka regioner som har ett stort antal trafik men drabbas av ökad latens. Därefter kan du använda informationen du planerar din storleken expansion till nya Azure-regioner så att dessa användare kan ha en lägre latens upplevelse.

## <a name="how-traffic-view-works"></a>Hur fungerar trafiken vy

Visa trafik fungerar genom att låta Traffic Manager titta på inkommande frågor som tagits emot de senaste sju dagarna mot en profil som har den här funktionen är aktiverad. Käll-IP för DNS-matchning används sedan som en representation av platsen där användarna extraheras från den här informationen. Dessa sedan grupperas på en DNS-matchare nivån kornighet skapa användaren grundläggande regioner med hjälp av geografisk information av IP-adresser som underhålls av Traffic Manager. Traffic Manager ser då på Azure-regioner som frågan vidarebefordrades och skapar en trafik flödet karta för användare från dessa regioner.
I nästa steg Traffic Manager korrelerar användaren grundläggande region för Azure-region-mappning med nätverket intelligence latens tabeller som det finns för olika slutanvändaren nätverk för att förstå genomsnittlig svarstid användare från dessa regioner upplever när ansluter till Azure-regioner. Dessa beräkningar visas sedan som en tabell på en per lokala DNS-matchare IP-nivå innan den visas för dig. Du kan bearbeta den här informationen i ett arbetsflöde för analytics önskat, kan du också hämta den här informationen som en CSV-fil.
När du använder trafik vyn debiteras baserat på antalet datapunkter som används för att skapa insikter visas. Typ av endast data används är för närvarande frågor som tagits emot mot Traffic Manager-profilen. Mer information om prissättning, finns det [Traffic Manager sida med priser](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Nästa steg

- Läs [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Lär dig mer om den [routning av nätverkstrafik metoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

