---
title: Styr Azure-webbapptrafik med Azure Traffic Manager
description: "Den här artikeln innehåller översiktsinformation för Azure Traffic Manager när det gäller Azure-webbappar."
services: app-service\web
documentationcenter: 
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
ms.openlocfilehash: 5bf687afa8f42292a3b21b19a572c76926fef1cd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Styr Azure-webbapptrafik med Azure Traffic Manager
> [!NOTE]
> Den här artikeln innehåller översiktsinformation för Microsoft Azure Traffic Manager när det gäller Azure Web Apps. Mer information om Azure Traffic Manager själva hittar genom att besöka länkarna i slutet av den här artikeln.
> 
> 

## <a name="introduction"></a>Introduktion
Du kan använda Azure Traffic Manager för att styra hur begäranden från webbklienter distribueras till web apps i Azure App Service. Om web app slutpunkter som läggs till i en Azure Traffic Manager-profil, Azure Traffic Manager håller reda på status för dina webbappar (körs, stoppas eller borttagna) så att den kan avgöra vilken av dessa slutpunkter ska ta emot trafik.

## <a name="routing-methods"></a>routningsmetoder
Azure Traffic Manager använder tre olika metoder för routning. Dessa metoder beskrivs i följande lista som de gäller för Azure-webbappar.

* **[Prioritet](#priority):** använda en primär webbapp för all trafik och ange säkerhetskopieringar ifall primärt eller säkerhetskopiering webbprogram är inte tillgänglig.
* **[Viktade](#weighted):** distribuerar trafik över en uppsättning web apps jämnt eller baserat på vikt, som du definierar.
* **[Prestanda](#performance):** när du har webbprogram i olika geografiska platser kan använda ”närmaste” webbprogrammet vad gäller den lägsta Nätverksfördröjningen.
* **[Geografisk](#geographic):** dirigera användarna till specifika webbprogram baserat på vilka geografiska plats deras DNS-fråga som kommer från. 

Mer information finns i [routningsmetoder för Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="web-apps-and-traffic-manager-profiles"></a>Webbprogram och Traffic Manager-profiler
Om du vill konfigurera kontroll av webbtrafik för appen och skapa en profil i Azure Traffic Manager som använder någon av tre läsa in belastningsutjämning metoder som beskrivs ovan och sedan lägga till slutpunkter (i det här fallet webbprogram) som du vill styra trafik till profilen. Din web appstatus (körs, stoppas eller tas bort) regelbundet som överförs till profilen så att Azure Traffic Manager kan dirigera trafik i enlighet med detta.

När du använder Azure Traffic Manager med Azure, Tänk på följande saker övervägas:

* För web appdistributioner för endast inom samma region ger Web Apps redan redundans och resursallokering funktioner utan hänsyn till läget för webb-app.
* Du kan kombinera båda typer av slutpunkter för att aktivera hybridscenarier med för distributioner i samma region som använder Web Apps tillsammans med en annan Azure-molntjänst.
* Du kan bara ange en web app slutpunkt per region i en profil. När du väljer en webbapp som en slutpunkt för en region återstående webbprogram i regionen att vara tillgänglig för val för den här profilen.
* Web app slutpunkter som du anger i en Azure Traffic Manager-profilen visas under den **domännamn** avsnitt på sidan Konfigurera för webbappen i profilen, men inte kan konfigureras det.
* När du lägger till en webbapp till en profil av **Webbadress** på instrumentpanelen av appens Portalsida visar anpassad domän-URL: en för webbappen om du har skapat en. I annat fall visas Traffic Manager-profilens URL (till exempel `contoso.trafficmanager.net`). Båda direkt domännamnet för webbappen och Traffic Manager-URL är synliga på webbappens Konfigurera sidan under den **domännamn** avsnitt.
* Dina egna domännamn fungerar som förväntat, men förutom att lägga till dem i ditt webbprogram, måste du också konfigurera DNS-kartan för att peka på Traffic Manager-URL. Mer information om hur du konfigurerar en anpassad domän för ett Azure-webbapp finns [konfigurera ett anpassat domännamn för en Azure-webbplats](app-service-web-tutorial-custom-domain.md).
* Du kan bara lägga till webbprogram som är standard eller premium läge till en Azure Traffic Manager-profil.

## <a name="next-steps"></a>Nästa steg
En konceptuell och teknisk översikt av Azure Traffic Manager finns i [Traffic Manager-översikt](../traffic-manager/traffic-manager-overview.md).

Mer information om hur du använder Traffic Manager med Web Apps finns i blogginläggen [med hjälp av Azure Traffic Manager med Azure webbplatser](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) och [Azure Traffic Manager kan nu integrera med Azure webbplatser](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

