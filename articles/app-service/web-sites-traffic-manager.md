---
title: Kontrollera trafik för Azure App Service med Azure Traffic Manager
description: Den här artikeln innehåller översiktsinformation för Azure Traffic Manager relateras till Azure App Service.
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
ms.openlocfilehash: 92ab7bf64445ff772f33a18e7f7946a7e0be333a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824048"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Kontrollera trafik för Azure App Service med Azure Traffic Manager
> [!NOTE]
> Den här artikeln innehåller översiktsinformation för Microsoft Azure Traffic Manager relateras till Azure App Service. Mer information om Azure Traffic Manager själva hittar genom att besöka länkarna i slutet av den här artikeln.
> 
> 

## <a name="introduction"></a>Introduktion
Du kan använda Azure Traffic Manager för att styra hur begäranden från webbklienter distribueras till appar i Azure App Service. Om Apptjänst slutpunkter som läggs till i en Azure Traffic Manager-profil, Azure Traffic Manager håller reda på status för din Apptjänst-appar (körs, stoppas eller borttagna) så att den kan avgöra vilken av dessa slutpunkter ska ta emot trafik.

## <a name="routing-methods"></a>Routningsmetoder
Azure Traffic Manager använder fyra olika metoder. Dessa metoder beskrivs i följande lista som de gäller för Azure App Service.

* **[Prioritet](../traffic-manager/traffic-manager-routing-methods.md#priority):** använda en primär app för all trafik och ange säkerhetskopieringar ifall primärt eller appar för säkerhetskopiering är inte tillgänglig.
* **[Viktade](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuerar trafik över en uppsättning appar, jämnt eller baserat på vikt, som du definierar.
* **[Prestanda](../traffic-manager/traffic-manager-routing-methods.md#performance):** när du har appar i olika geografiska platser kan använda appen ”närmaste” vad gäller den lägsta Nätverksfördröjningen.
* **[Geografisk](../traffic-manager/traffic-manager-routing-methods.md#geographic):** dirigera användarna till specifika appar baserat på vilka geografiska plats deras DNS-fråga som kommer från. 

Mer information finns i [routningsmetoder för Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service och Traffic Manager-profiler
Om du vill konfigurera kontrollen av trafik för Apptjänst-app, skapa en profil i Azure Traffic Manager som använder någon av tre läsa in belastningsutjämning metoder som beskrivs ovan och sedan lägga till slutpunkter (i det här fallet App Service) som du vill styra trafik till den profil. Din appstatus (körs, stoppas eller tas bort) regelbundet som överförs till profilen så att Azure Traffic Manager kan dirigera trafik i enlighet med detta.

När du använder Azure Traffic Manager med Azure, Tänk på följande saker övervägas:

* För appen distributioner för endast inom samma region ger Apptjänst redan redundans och resursallokering funktioner utan hänsyn till app-läge.
* Du kan kombinera båda typer av slutpunkter för att aktivera hybridscenarier med för distributioner i samma region som använder Apptjänst tillsammans med en annan Azure-molntjänst.
* Du kan bara ange en Apptjänst-slutpunkt per region i en profil. När du väljer en app som en slutpunkt för en region återstående appar i den regionen att vara tillgänglig för val för den här profilen.
* Apptjänst-slutpunkter som du anger i en Azure Traffic Manager-profilen visas under den **domännamn** avsnitt på sidan Konfigurera för appen i profilen, men inte kan konfigureras det.
* När du lägger till en app till en profil av **Webbadress** på instrumentpanelen i appens Portalsida visar domänen Webbadressen till appen om du har skapat en. I annat fall visas Traffic Manager-profilens URL (till exempel `contoso.trafficmanager.net`). Båda direkt domännamnet för appen och Traffic Manager-URL är synliga på appens Konfigurera sidan under den **domännamn** avsnitt.
* Dina egna domännamn fungerar som förväntat, men förutom att lägga till dem till dina appar, måste du också konfigurera DNS-kartan för att peka på Traffic Manager-URL. Information om hur du konfigurerar en anpassad domän för en App Service-appen finns [mappa ett befintligt anpassade DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md).
* Du kan bara lägga till appar som är standard eller premium läge till en Azure Traffic Manager-profil.

## <a name="next-steps"></a>Nästa steg
En konceptuell och teknisk översikt av Azure Traffic Manager finns i [Traffic Manager-översikt](../traffic-manager/traffic-manager-overview.md).

Mer information om hur du använder Traffic Manager med App Service finns i blogginläggen [med hjälp av Azure Traffic Manager med Azure webbplatser](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) och [Azure Traffic Manager kan nu integrera med Azure webbplatser](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

