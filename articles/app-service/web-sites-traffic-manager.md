---
title: Kontrollera trafik med Traffic Manager – Azure App Service
description: Den här artikeln innehåller översiktsinformation för Azure Traffic Manager då det relaterar till Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 79fe3bce558a8315f5fbf7dbc82a4979e8e24238
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831987"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Kontrollera trafik för Azure App Service med Azure Traffic Manager
> [!NOTE]
> Den här artikeln innehåller översiktsinformation för Microsoft Azure Traffic Manager då det relaterar till Azure App Service. Mer information om Azure Traffic Manager själva kan hittas genom att gå till länkarna i slutet av den här artikeln.
> 
> 

## <a name="introduction"></a>Introduktion
Du kan använda Azure Traffic Manager för att styra hur begäranden från webbklienter distribueras till appar i Azure App Service. När App Service-slutpunkter läggs till i en Azure Traffic Manager-profil kan Azure Traffic Manager hålla reda på status för dina App Service-appar (som de körs, stoppas eller har tagits bort) så att den kan bestämma vilka av slutpunkterna som ska ta emot trafik.

## <a name="routing-methods"></a>Routningsmetoder
Azure Traffic Manager använder fyra olika metoder för trafikroutning. Dessa metoder beskrivs i följande lista då de är kopplade till Azure App Service.

* **[Prioritet](../traffic-manager/traffic-manager-routing-methods.md#priority):** använder en primär app för all trafik och ge säkerhetskopiering om primärt eller appar för säkerhetskopiering är inte tillgänglig.
* **[Viktat](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuerar trafik över en uppsättning appar, jämnt eller enligt vikterna som du definierar.
* **[Prestanda](../traffic-manager/traffic-manager-routing-methods.md#performance):** när du har appar på olika geografiska platser kan använda appen ”närmaste” när det gäller den lägsta Nätverksfördröjningen.
* **[Geografisk](../traffic-manager/traffic-manager-routing-methods.md#geographic):** dirigera användarna till specifika appar baserat på vilka geografiska plats deras DNS-fråga som samlas in från. 

Mer information finns i [routningsmetoder för Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service och Traffic Manager-profiler
Du skapar en profil i Azure Traffic Manager som använder en av fyra läsa in metoder för belastningsutjämning som beskrivs ovan för att konfigurera kontroll över trafik för App Service, och sedan lägga till slutpunkter (i det här fallet App Service) som du vill styra trafik till den profil. Din appstatus (körs, stoppas eller tas bort) överförs regelbundet i profilen så att Azure Traffic Manager kan dirigera trafik i enlighet med detta.

När du använder Azure Traffic Manager med Azure, Tänk på följande saker övervägas:

* För endast appdistributioner inom samma region tillhandahåller App Service redan redundans och resursallokering funktioner utan hänsyn till appläge.
* Du kan kombinera båda typerna av slutpunkter för att aktivera hybridscenarier för distributioner i samma region som använder App Service i samband med en annan Azure-molntjänst.
* Du kan bara ange en App Service-slutpunkt per region i en profil. När du väljer en app som en slutpunkt för en region, blir återstående appar i den regionen otillgänglig för val av för profilen.
* App Service-slutpunkter som du anger i en Azure Traffic Manager-profil som visas under den **domännamn** avsnittet på konfigurationssidan för appen i profilen, men inte kan konfigureras det.
* När du lägger till en app till en profil i **webbplatsens URL** på instrumentpanelen för appens portalsidan visar anpassad domän-URL: en för appen om du har konfigurerat en. I annat fall visas URL: en för Traffic Manager-profil (till exempel `contoso.trafficmanager.net`). Båda direkt domännamnet för appen och Traffic Manager-URL: en är synliga på konfigurationssidan för appens under den **domännamn** avsnittet.
* Dina egna domännamn fungerar som förväntat, men förutom att lägga till dem i dina appar, måste du också konfigurera DNS-kartan så att den pekar till URL: en för Traffic Manager. Information om hur du ställer in en anpassad domän för en App Service-app finns i [mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md).
* Du kan bara lägga till appar som är i standard- eller premium-läge till en Azure Traffic Manager-profil.

## <a name="next-steps"></a>Nästa steg
En konceptuell och teknisk översikt över Azure Traffic Manager finns i [översikt över Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Mer information om hur du använder Traffic Manager med App Service finns i blogginläggen [med hjälp av Azure Traffic Manager med Azure Web Sites](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) och [Azure Traffic Manager nu kan du integrera med Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

