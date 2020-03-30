---
title: Trafikvy i Azure Traffic Manager
description: I den här introduktionen får du lära dig hur Traffic manager Traffic view fungerar.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: d5a03fde564b14baee97f50fa63fd58bf83694b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938345"
---
# <a name="traffic-manager-traffic-view"></a>Trafikhanterare Trafikvy

Traffic Manager ger dig DNS-nivåroutning så att slutanvändarna dirigeras till felfria slutpunkter baserat på den routningsmetod som angavs när du skapade profilen. Traffic View ger Traffic Manager en vy över dina användarbaser (på en DNS-lösningsgranularitetsnivå) och deras trafikmönster. När du aktiverar Traffic View bearbetas den här informationen för att ge dig användbara insikter. 

Genom att använda Traffic View kan du:
- förstå var dina användarbaser finns (upp till en lokal DNS-lösningsnivå detaljerad).
- visa trafikvolymen (observeras som DNS-frågor som hanteras av Azure Traffic Manager) som kommer från dessa regioner.
- få insikter om vad som är den representativa latens upplevs av dessa användare.
- djupdykning i de specifika trafikmönstren från var och en av dessa användarbaser till Azure-regioner där du har slutpunkter. 

Du kan till exempel använda Trafikvyn för att förstå vilka regioner som har ett stort antal trafik men lider av högre fördröjningar. Därefter kan du använda den här informationen för att planera din fotavtrycksexpansion till nya Azure-regioner så att dessa användare kan få en lägre latensupplevelse.

## <a name="how-traffic-view-works"></a>Så här fungerar Traffic View

Traffic View fungerar genom att låta Traffic Manager titta på inkommande frågor som tagits emot under de senaste sju dagarna mot en profil som har den här funktionen aktiverad. Från informationen om inkommande frågor extraherar Traffic View käll-IP för DNS-matcharen som används som representation av användarnas plats. Dessa grupperas sedan tillsammans på en DNS-matchningsnivå för att skapa användarbasregioner med hjälp av geografisk information om IP-adresser som underhålls av Traffic Manager. Traffic Manager tittar sedan på Azure-regioner som frågan dirigerades till och konstruerar en trafikflödeskarta för användare från dessa regioner.  
I nästa steg korrelerar Traffic Manager användarbasregionen till Azure-regionmappning med de register över svarstid för nätverksinformation som den upprätthåller för olika slutanvändarnätverk för att förstå den genomsnittliga svarstiden som användare från dessa regioner upplever när ansluta till Azure-regioner. Alla dessa beräkningar kombineras sedan på en IP-nivå per lokal DNS-lösnings-LÖSNING innan den presenteras för dig. Du kan använda informationen på olika sätt.

Frekvensen för trafikvydatauppdatering beror på flera interna tjänstvariabler. Data uppdateras dock vanligtvis en gång var 24:e timme.

>[!NOTE]
>Svarstiden som beskrivs i Trafikvyn är en representativ svarstid mellan slutanvändaren och Azure-regionerna som de hade anslutit till och är inte SVARstiden för DNS-sökning. Traffic View gör en uppskattning av svarstiden mellan den lokala DNS-matcharen och Azure-regionen som frågan dirigerades till, om det inte finns tillräckliga data tillgängliga kommer svarstiden som returneras att vara null. 

## <a name="visual-overview"></a>Visuell översikt

När du navigerar till avsnittet **Trafikvyn** på sidan Traffic Manager visas en geografisk karta med ett överlägg av traffic view-insikter. Kartan innehåller information om användarbasen och slutpunkterna för din Traffic Manager-profil.

![Geografisk vy för Trafikhanteraren][1]

### <a name="user-base-information"></a>Information om användarbas

För de lokala DNS-resolvers för vilka platsinformation är tillgänglig visas de på kartan. Färgen på DNS-matcharen anger den genomsnittliga svarstiden som upplevs av slutanvändare som använde DNS-matcharen för sina Traffic Manager-frågor.

Om du hovrar över en DNS-matchningsplats på kartan visas:
- IP-adressen för DNS-matcharen
- volymen av DNS-frågetrafik som traffic manager ser från den
- slutpunkter som dirigeras trafik från DNS-matcharen, som en linje mellan slutpunkten och DNS-matcharen 
- den genomsnittliga svarstiden från den platsen till slutpunkten, representerad som färgen på den linje som förbinder dem

### <a name="endpoint-information"></a>Information om slutpunkt

De Azure-regioner där slutpunkterna finns visas som blå punkter i kartan. Om slutpunkten är extern och inte har en Azure-region mappad till den visas den högst upp på kartan. Klicka på en slutpunkt för att se de olika platserna (baserat på dns-matcharen som användes) från vilken trafiken dirigerades till den slutpunkten. Anslutningarna visas som en linje mellan slutpunkten och DNS-matcharens plats och färgas enligt den representativa svarstiden mellan det paret. Dessutom kan du se namnet på slutpunkten, Azure-regionen där den körs och den totala volymen begäranden som dirigerades till den av den här Traffic Manager-profilen.


## <a name="tabular-listing-and-raw-data-download"></a>Tabelllista och hämtning av rådata

Du kan visa trafikvydata i tabellformat i Azure-portalen. Det finns en post för varje DNS-matchnings-IP/slutpunktspar som visar IP-adressen för DNS-matcharen, namn och geografisk plats för Azure-regionen där slutpunkten finns (om tillgänglig), mängden begäranden som är associerad med den DNS-matcharen till slutpunkten och den representativa svarstiden som är associerad med slutanvändare med den DNS-funktionen (om sådan finns). Du kan också hämta Traffic View-data som en CSV-fil som kan användas som en del av ett analysarbetsflöde som du väljer.

## <a name="billing"></a>Fakturering

När du använder Traffic View faktureras du baserat på antalet datapunkter som används för att skapa de insikter som presenteras. Den enda datapunktstyp som används är för närvarande de frågor som tas emot mot traffic manager-profilen. Mer information om prissättningen finns på [traffic manager-prissidan](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Vanliga frågor och svar

* [Vad gör Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Hur kan jag dra nytta av att använda Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Hur skiljer sig Traffic View från Traffic Manager-måtten tillgängliga via Azure-övervakaren?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Använder Traffic View EDNS-klientundernätsinformation?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Hur många dagar med data använder Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Hur hanterar Traffic View externa slutpunkter?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Måste jag aktivera Traffic View för varje profil i min prenumeration?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Hur stänger jag av Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Hur fungerar Traffic View-fakturering?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Nästa steg

- Läs om [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Läs mer om [de trafikroutningsmetoder](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Läs om hur du [skapar en Traffic Manager-profil](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png