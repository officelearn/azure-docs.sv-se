---
title: Trafik vyn i Azure Traffic Manager | Microsoft Docs
description: Introduktion till Traffic Manager Trafikvy
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: d1800fea2212628e7647b5250efa33ebb97957f9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138080"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Trafikvy

Traffic Manager får du med DNS-nivå routning så att dina slutanvändare dirigeras till felfria slutpunkter baserat på vilken routningsmetod som angav när du skapade profilen. Traffic View ger en överblick över dina användarbaser (på en DNS-matchare filnivå) och deras trafikmönstret Traffic Manager. När du aktiverar Traffic View måste bearbetas den här informationen så att du får användbara insikter. 

Med hjälp av Traffic View, kan du:
- se var din användarbaser är belägen (till en lokal DNS på tolkarnivå).
- Visa vilken trafikvolym (observerats som DNS-frågor som hanteras av Azure Traffic Manager) från dessa regioner.
- få inblick i vad är den förväntade svarstiden genom att dessa användare.
- Dyk ner i det specifika trafikmönstret från var och en användarbas till Azure-regioner där du har slutpunkter. 

Du kan till exempel använda Trafikvyn för att förstå vilka regioner som har ett stort antal trafik men drabbas av högre latens. Därefter kan du använda den här informationen för att planera din fotavtryck utökning till nya Azure-regioner så att dessa användare kan ha en lägre latens-upplevelse.

## <a name="how-traffic-view-works"></a>Så här fungerar Traffic View

Traffic View fungerar genom att använda Traffic Manager titta på de inkommande frågor som tagits emot under de senaste sju dagarna mot en profil som har den här funktionen är aktiverad. Från den inkommande informationen som frågor extraherar Traffic View käll-IP för DNS-matchare som används som en representation av platsen för användarna. Dessa grupperas sedan samman i en DNS-på-tolkarnivå att skapa användaren grundläggande regioner med hjälp av geografisk information med IP-adresser som underhålls av Traffic Manager. Traffic Manager tittar sedan på Azure-regioner som frågan har dirigerats och skapar en karta för flödet av trafik för användare från dessa regioner.  
I nästa steg, Traffic Manager korrelerar användaren grundläggande regionen för Azure-region-mappning med nätverket intelligence svarstid tabeller som den underhållet för nätverk för olika slutanvändaren att förstå den genomsnittliga svarstiden av användare från dessa regioner när ansluter till Azure-regioner. Dessa beräkningar är sedan kombineras med en lokal DNS-matchare IP-nivå per innan den visas för dig. Du kan använda informationen på olika sätt.

>[!NOTE]
>Den svarstid som beskrivs i Trafikvyn är en representativ fördröjning mellan användaren och vilka Azure-regioner som har de ansluta till och är inte DNS-sökning svarstiden. Traffic View gör returnerade en bästa arbete uppskattning av fördröjning mellan den lokala DNS-matcharen och Azure-regionen frågan var dirigeras till, om det finns inte tillräckligt med data och sedan svarstiden är null. 

## <a name="visual-overview"></a>Visuell översikt

När du navigerar till den **Traffic View** avsnitt i din Traffic Manager-sidan visas med en geografisk karta med övertäckning Traffic View insikter. Kartan innehåller information om användarbas- och slutpunkter för Traffic Manager-profilen.

### <a name="user-base-information"></a>Grundläggande användarinformation

För dessa lokala DNS-matchare information är tillgänglig för vilken plats visas de på kartan. Färgen på DNS-matchare anger genomsnittlig svarstid erfarna av slutanvändare som har använt den DNS-matchning för deras Traffic Manager-frågor.

Om du hovrar över en DNS-matchare plats på kartan visas:
- IP-adressen för DNS-matchning
- mängden DNS-frågorna som visas av Traffic Manager från den
- slutpunkter till vilken trafik från DNS matchare har dirigerats, som en linje mellan slutpunkten och DNS-matchning 
- Genomsnittlig svarstid från den platsen till slutpunkten som färgen på raden ansluter dem

### <a name="endpoint-information"></a>Slutpunktsinformation

Azure-regioner där slutpunkterna bor. visas som blå punkter på kartan. Om slutpunkten är extern och har inte mappats till en Azure-region kan visas det överst i kartan. Klicka på valfri slutpunkt för att se de olika platser (baserat på DNS-matcharen används) från när trafik har skickas till denna slutpunkt. Anslutningar visas som en linje mellan slutpunkten och platsen för DNS-matchare och färgas enligt den förväntade svarstiden mellan paret. Dessutom kan du se namnet på slutpunkten, den Azure-region där den körs och den totala mängden begäranden som kommer till den av den här Traffic Manager-profilen.


## <a name="tabular-listing-and-raw-data-download"></a>Ladda ned listan och rådata

Du kan visa Traffic View-data i tabellformat i Azure-portalen. Det finns en post för varje DNS-matchare IP / endpoint koppla som visar IP-adressen för DNS-matchare, namn och geografiska platsen för Azure-regioner i som slutpunkten finns (om tillgängligt), mängden begäranden som är associerade med den DNS-matcharen till slutpunkten och den förväntade svarstiden som är associerade med slutanvändare med hjälp av den DNS (i förekommande fall). Du kan också hämta Traffic View-data som en CSV-fil som kan användas som en del av en analytics-arbetsflöde för ditt val.

## <a name="billing"></a>Fakturering

När du använder Traffic View, debiteras du baserat på antalet datapunkter som används för att skapa de insikter som visas. Typ av endast data används är för närvarande frågor som tagits emot mot din Traffic Manager-profil. Mer information om priserna på den [Traffic Manager-sidan med priser](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Nästa steg

- Lär dig [så här fungerar Traffic Manager](traffic-manager-overview.md)
- Läs mer om den [trafikroutningsmetoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

