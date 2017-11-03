---
title: "Hantera kostnaden för datalagring i Azure Log Analytics | Microsoft Docs"
description: "Lär dig hur du ändrar den prisnivå planerings- och bevarandeprincipen för logganalys-arbetsytan i Azure-portalen."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 35064c792b72222d59b1d3f0913a92a4a2b34612
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>Hantera kostnaden för datalagring med logganalys-arbetsytan
När du registrerar dig för Log Analytics, beroende på den plan som du väljer finns det en gräns för hur länge data som genereras av dina anslutna källor lagras på din arbetsyta.  Den här artikeln visar information som kan påverka kostnader för att behålla den här informationen för olika perioder tid och hur du konfigurerar gränsen.   

Log Analytics kan förbruka stora mängder data från källor på plats, moln och hybrid-miljöer, kostnaden för lagring av data för en viss tidsperiod kan avsevärt beroende på följande faktorer:

* Antal system, infrastruktur, molnresurser, etc. som du samlar in från
* Typ av data som skapats av källa, till exempel meddelandeköer, loggar, händelser, säkerhetsrelaterade data eller prestandamått
* Mängden data som genereras av dessa källor 
* Antal hanteringslösning aktiverad, datakällan och frekvens för samlingen

> [!NOTE]
> Finns i dokumentationen för varje lösning eftersom det ger dig en uppfattning av hur mycket data som samlas in.   

Om du är på den *lediga* planen data är begränsad till sju dagar kvarhållning.  För den *fristående* eller *betald* nivån insamlade finns de senaste 31 dagarna.  

När du använder den *lediga* planera, om du hittar du konsekvent överskrider de belopp som tillåts, kan du ändra din arbetsyta till en betald plan för att samla in data utöver den här gränsen. 

> [!NOTE]
> Avgifter kan tillkomma om du vill välja en längre period för betald skiktet. Du kan ändra Plantyp av när som helst och mer information om priser, se [prisinformationen](https://azure.microsoft.com/pricing/details/log-analytics/). 

## <a name="change-the-data-retention-period"></a>Ändra Datalagringsperiod 

1. Logga in på [Azure-portalen](http://portal.azure.com). 
2. Klicka på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.
3. Välj din arbetsyta för att ändra i listan i rutan logganalys prenumerationer.
4. På sidan arbetsytan **kvarhållning** i den vänstra rutan.
5. Flytta skjutreglaget att öka eller minska antalet dagar och klicka på fönstret med arbetsytans kvarhållning **spara**.  Om du är på den *ledigt* nivån du kommer inte att kunna ändra kvarhållningsperioden för data och du måste uppgradera till betald nivån för att styra den här inställningen.<br><br> ![Ändra inställningen för kvarhållning av arbetsytan data](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>Nästa steg  

Information om hur mycket data som samlas in, vilka källor skickar och olika typer av data som skickas för att hantera förbrukning och kostnaden finns [analysera dataanvändning av i logganalys](log-analytics-usage.md)