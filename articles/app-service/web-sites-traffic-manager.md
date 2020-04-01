---
title: Styra trafiken med Traffic Manager
description: Hitta metodtips för att konfigurera Azure Traffic Manager när du integrerar den med Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437899"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Styra Azure App Service-trafik med Azure Traffic Manager
> [!NOTE]
> Den här artikeln innehåller sammanfattande information för Microsoft Azure Traffic Manager när det gäller Azure App Service. Mer information om Azure Traffic Manager själv kan hittas genom att besöka länkarna i slutet av den här artikeln.
> 
> 

## <a name="introduction"></a>Introduktion
Du kan använda Azure Traffic Manager för att styra hur begäranden från webbklienter distribueras till appar i Azure App Service. När App Service-slutpunkter läggs till i en Azure Traffic Manager-profil kan Azure Traffic Manager hålla reda på status för dina App Service-appar (som de körs, stoppas eller har tagits bort) så att den kan bestämma vilka av slutpunkterna som ska ta emot trafik.

## <a name="routing-methods"></a>Routningsmetoder
Azure Traffic Manager använder fyra olika routningsmetoder. Dessa metoder beskrivs i följande lista när de gäller Azure App Service.

* ** [Prioritet](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** Använd en primär app för all trafik och tillhandahålla säkerhetskopior om de primära eller säkerhetskopieringsapparna inte är tillgängliga.
* ** [Viktad](../traffic-manager/traffic-manager-routing-methods.md#weighted):** fördela trafik över en uppsättning appar, antingen jämnt eller enligt vikter, som du definierar.
* ** [Prestanda:](../traffic-manager/traffic-manager-routing-methods.md#performance)** När du har appar på olika geografiska platser använder du appen "närmaste" när det gäller den lägsta nätverksfördröjningen.
* ** [Geografisk](../traffic-manager/traffic-manager-routing-methods.md#geographic):** dirigera användare till specifika appar baserat på vilken geografisk plats deras DNS-fråga kommer ifrån. 

Mer information finns i [Routningsmetoder för Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Apptjänst- och trafikhanterare profiler
Om du vill konfigurera kontrollen av App Service-apptrafiken skapar du en profil i Azure Traffic Manager som använder en av de fyra belastningsutjämningsmetoderna som beskrivits tidigare och sedan lägger till slutpunkter (i det här fallet Apptjänst) som du vill styra trafiken för till profilen. Din appstatus (löpning, stoppad eller borttagen) meddelas regelbundet till profilen så att Azure Traffic Manager kan dirigera trafiken därefter.

När du använder Azure Traffic Manager med Azure bör du tänka på följande:

* För appdistributioner endast inom samma region tillhandahåller App Service redan redundans- och round-robin-funktioner utan hänsyn till appläge.
* För distributioner i samma region som använder App Service tillsammans med en annan Azure-molntjänst kan du kombinera båda typerna av slutpunkter för att aktivera hybridscenarier.
* Du kan bara ange en apptjänstslutpunkt per region i en profil. När du väljer en app som slutpunkt för en region blir de återstående apparna i den regionen otillgängliga för val för den profilen.
* Slutpunkterna för App-tjänsten som du anger i en Azure Traffic Manager-profil visas under avsnittet **Domännamn** på sidan Konfigurera för appen i profilen, men kan inte konfigureras där.
* När du har lagt till en app i en profil visas appens anpassade domän-URL **för** appen om du har konfigurerat en webbplats på instrumentpanelen på appens portalsida. Annars visas profil-URL:en för Traffic `contoso.trafficmanager.net`Manager (till exempel ). Både appens direkta domännamn och Traffic Manager-URL:en visas på appens konfigurera sida under avsnittet **Domännamn.**
* Dina egna domännamn fungerar som förväntat, men förutom att lägga till dem i dina appar måste du också konfigurera DNS-kartan så att den pekar på Traffic Manager-URL:en. Information om hur du konfigurerar en anpassad domän för en App Service-app finns [i Konfigurera ett anpassat domännamn i Azure App Service med Traffic Manager-integrering](configure-domain-traffic-manager.md).
* Du kan bara lägga till appar som är i standard- eller premiumläge i en Azure Traffic Manager-profil.
* Om du lägger till en app i en Traffic Manager-profil startas appen om.

## <a name="next-steps"></a>Efterföljande moment
En begreppsmässig och teknisk översikt över Azure Traffic Manager finns i [Översikt över Traffic Manager](../traffic-manager/traffic-manager-overview.md).


