---
title: Övervaka Azure Cosmos DB med Azure Monitor för Cosmos DB (förhandsversion)| Microsoft-dokument
description: I den här artikeln beskrivs Azure Monitor for Cosmos DB-funktionen som ger Cosmos DB-ägare en snabb förståelse för prestanda- och användningsproblem med sina CosmosDB-konton.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250678"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Utforska Azure Monitor för Azure Cosmos DB (förhandsversion)

Azure Monitor för Azure Cosmos DB (förhandsversion) ger en vy över den övergripande prestanda, fel, kapacitet och drifthälsa för alla dina Azure Cosmos DB-resurser i en enhetlig interaktiv upplevelse. Den här artikeln hjälper dig att förstå fördelarna med den här nya övervakningsupplevelsen och hur du kan ändra och anpassa upplevelsen så att den passar organisationens unika behov.   

## <a name="introduction"></a>Introduktion

Innan du dyker in i upplevelsen, bör du förstå hur den presenterar och visualiserar information. 

Det ger:

* **I skalperspektiv av** dina Azure Cosmos DB-resurser för alla dina prenumerationer på en enda plats, med möjlighet att selektivt begränsa till endast de prenumerationer och resurser som du är intresserad av att utvärdera.

* **Öka detaljnivån för analys** av en viss Azure CosmosDB-resurs för att diagnostisera problem eller utföra detaljerad analys efter kategori - användning, fel, kapacitet och åtgärder. Om du väljer något av dessa alternativ kan du få en djupgående vy över relevanta Azure Cosmos DB-mått.  

* **Anpassningsbar** – Den här upplevelsen bygger på azure monitor-arbetsboksmallar så att du kan ändra vilka mått som visas, ändra eller ange tröskelvärden som stämmer överens med dina gränser och sedan spara i en anpassad arbetsbok. Diagram i arbetsböckerna kan sedan fästas på Azure-instrumentpaneler.  

Den här funktionen kräver inte att du aktiverar eller konfigurerar något, dessa Azure Cosmos DB-mått samlas in som standard.

>[!NOTE]
>Det finns ingen avgift för att komma åt den här funktionen och du debiteras endast för de grundläggande funktioner för Azure Monitor som du konfigurerar eller aktiverar, enligt beskrivningen på [prisinformationssidan för Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Visa användnings- och prestandamått för Azure Cosmos DB

Om du vill visa användningen och prestanda för dina lagringskonton för alla dina prenumerationer utför du följande steg.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Bildskärm** och välj **Bildskärm**.

    ![Sökruta med ordet "Monitor" och en rullgardinsmeny som säger Tjänster "Monitor" med en hastighetsmätare stil bild](./media/cosmosdb-insights-overview/search-monitor.png)

3. Välj **Cosmos DB (förhandsgranskning)**.

    ![Skärmbild av Cosmos DB-översiktsarbetsbok](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Översikt

I **översikt**visar tabellen interaktiva Azure Cosmos DB-mått. Du kan filtrera resultaten baserat på de alternativ du väljer i följande listrutor:

* **Prenumerationer** – endast prenumerationer som har en Azure Cosmos DB-resurs visas.  

* **Cosmos DB** - Du kan välja alla, en delmängd eller en enda Azure Cosmos DB-resurs.

* **Tidsintervall** - som standard visar de senaste 4 timmarnas information baserat på motsvarande val som gjorts.

Räknarpanelen under listrutan rullar upp det totala antalet Azure Cosmos DB-resurser i de valda prenumerationerna. Det finns villkorlig färgkodning eller heatmaps för kolumner i arbetsboken som rapporterar transaktionsmått. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena. 

Om du väljer en nedrullningsbar pil bredvid en av Azure Cosmos DB-resurser visas en uppdelning av prestandamåtten på den enskilda databasbehållaren:

![Utökad listruta som avslöjar enskilda databasbehållare och tillhörande prestandauppdelning](./media/cosmosdb-insights-overview/container-view.png)

Om du väljer azure Cosmos DB-resursnamnet som är markerat i blått går du till **standardöversikten** för det associerade Azure Cosmos DB-kontot. 

### <a name="failures"></a>Fel

Välj **Fel** högst upp på sidan och delen Fel i **arbetsboksmallen** öppnas. Den visar totala begäranden med fördelningen av svar som utgör dessa begäranden:

![Skärmbild av fel med uppdelning efter HTTP-begäran typ](./media/cosmosdb-insights-overview/failures.png)

| Kod      |  Beskrivning       | 
|-----------|:--------------------|
| `200 OK`  | En av följande REST-åtgärder lyckades: </br>- Få på en resurs. </br> - SÄTT PÅ en resurs. </br> - POST på en resurs. </br> - POST på en lagrad procedurresurs för att utföra den lagrade proceduren.|
| `201 Created` | En POST-åtgärd för att skapa en resurs lyckas. |
| `404 Not Found` | Åtgärden försöker agera på en resurs som inte längre finns. Resursen kan till exempel redan ha tagits bort. |

En fullständig lista över statuskoder finns i [artikeln för Azure Cosmos DB HTTP-statuskod](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Kapacitet

Välj **Kapacitet** högst upp på sidan och **resursdelen Kapacitet** i arbetsboksmallen öppnas. Den visar hur många dokument du har, dokumenttillväxten över tid, dataanvändning och den totala mängden tillgängligt lagringsutrymme som du har kvar.  Detta kan användas för att identifiera potentiella lagrings- och dataanvändningsproblem.

![Arbetsbok för kapacitet](./media/cosmosdb-insights-overview/capacity.png) 

Precis som med översiktsarbetsboken visas en uppdelning av de enskilda behållare som utgör databasen om du väljer listrutan bredvid en Azure Cosmos DB-resurs i kolumnen **Prenumeration.**

### <a name="operations"></a>Åtgärder 

Välj **Operationer** högst upp på sidan och **operationsdelen** av arbetsboksmallen öppnas. Det ger dig möjlighet att se dina önskemål uppdelade efter den typ av begäranden som görs. 

Så i exemplet nedan `eastus-billingint` ser du som främst tar emot läsbegäranden, men med ett litet antal upsert och skapa begäranden. Medan `westeurope-billingint` är skrivskyddad från ett begärandeperspektiv, åtminstone under de senaste fyra timmarna som arbetsboken för närvarande är begränsad till via dess tidsintervallparameter.

![Arbetsbok för operationer](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Fästa, exportera och expandera

Du kan fästa något av måttavsnitten på en [Azure Dashboard](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) genom att välja pushpin-ikonen längst upp till höger i avsnittet.

![Exempel på måttavsnittsnål på instrumentpanelen](./media/cosmosdb-insights-overview/pin.png)

Om du vill exportera data till Excel-formatet markerar du nedpilikonen till vänster om pushpin-ikonen.

![Ikon för exportarbetsbok](./media/cosmosdb-insights-overview/export.png)

Om du vill expandera eller dölja alla listyrningar i arbetsboken markerar du expanderikonen till vänster om exportikonen:

![Ikonen Expandera arbetsbok](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Anpassa Azure Monitor för Azure Cosmos DB (förhandsversion)

Eftersom den här upplevelsen bygger på Azure Monitor-arbetsboksmallar kan du **anpassa** > **redigera** och **spara** en kopia av den ändrade versionen i en anpassad arbetsbok. 

![Anpassa stapel](./media/cosmosdb-insights-overview/customize.png)

Arbetsböcker sparas i en resursgrupp, antingen i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **Delade rapporter** som är tillgängligt för alla med åtkomst till resursgruppen. När du har sparat den anpassade arbetsboken måste du gå till arbetsboksgalleriet för att starta den.

![Starta arbetsboksgalleri från kommandofältet](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Nästa steg

* Konfigurera [måttaviseringar](../platform/alerts-metric.md) och [tjänsthälsomeddelanden](../../service-health/alerts-activity-log-service-notifications.md) för att konfigurera automatisk avisering för att åtgärda problem.

* Lär dig scenarier arbetsböcker är utformade för att stödja, hur du skapar nya och anpassa befintliga rapporter och mer genom att granska [Skapa interaktiva rapporter med Azure Monitor arbetsböcker](../app/usage-workbooks.md).
