---
title: Använda direktuppspelning för att använda data i Azure Data Explorer
description: Lär dig mer om hur du intänder (läser in) data i Azure Data Explorer med hjälp av direktuppspelning.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297092"
---
# <a name="streaming-ingestion-preview"></a>Inmatning av direktuppspelning (förhandsgranskning)

Använd direktuppspelningsinmatning när du behöver låg latens med en inmatningstid på mindre än 10 sekunder för olika volymdata. Den används för att optimera den operativa bearbetningen av många tabeller, i en eller flera databaser, där dataflödet i varje tabell är relativt liten (få poster per sekund) men den totala datainmatningsvolymen är hög (tusentals poster per sekund). 

Använd massinmatning i stället för direktuppspelning när mängden data växer till mer än 1 MB per sekund per tabell. Läs [översikt över datainmatning](/azure/data-explorer/ingest-data-overview) om du vill veta mer om de olika metoderna för intag.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Logga in på [webbgränssnittet](https://dataexplorer.azure.com/).
* Skapa [ett Azure Data Explorer-kluster och -databas](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Aktivera direktuppspelning i klustret

> [!WARNING]
> Läs igenom [begränsningarna](#limitations) innan du aktiverar ångintag.

1. Gå till Azure Data Explorer-klustret i Azure-portalen. Välj **Konfigurationer**i **Inställningar**. 
1. Välj **På** i fönstret **Konfigurationer** om du vill aktivera **inmatning av direktuppspelning**.
1. Välj **Spara**.
 
    ![intag av strömning på](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. I [webbgränssnittet](https://dataexplorer.azure.com/)definierar du [principen för direktinmatning i](/azure/kusto/management/streamingingestionpolicy) tabeller eller databaser som tar emot strömmande data. 

    > [!NOTE]
    > * Om principen har definierats på databasnivå aktiveras alla tabeller i databasen för direktuppspelning.
    > * Den tillämpade principen kan endast referera till nyligen intjesterade data och inte andra tabeller i databasen.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Använda direktuppspelning för att få tillgång till data i klustret

Det finns två typer av direktuppspelningsintag:


* [**Event Hub**](/azure/data-explorer/ingest-data-event-hub), som används som datakälla
* **Med anpassat inmatningsär** duskriva ett program som använder ett av Azure Data Explorer-klientbiblioteken. Se [exempel på direktströmsintag](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) för ett exempelprogram.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Välj lämplig direktuppspelningsmatningstyp

|   |Händelsehubb  |Anpassat inmatningsintag  |
|---------|---------|---------|
|Datafördröjning mellan inmatningsinitiering och tillgängliga data för fråga   |    Längre fördröjning     |   Kortare fördröjning      |
|Utveckling omkostnader    |   Snabb och enkel installation, ingen utveckling overhead    |   Höga utvecklingskostnader för program för att hantera fel och säkerställa datakonsekvens     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Inaktivera direktuppspelning i klustret

> [!WARNING]
> Det kan ta några timmar att inaktivera intag av direktuppspelning.

1. Släpp [direktuppspelningsbematningsprincipen](/azure/kusto/management/streamingingestionpolicy) från alla relevanta tabeller och databaser. Borttagning av borttagning av borttagning av borttagning av direktuppspelning utlöser dataförflyttning av strömmande inmatning från den ursprungliga lagringen till permanent lagring i kolumnarkivet (omfattningar eller shards). Dataförflyttningen kan pågå mellan några sekunder till några timmar, beroende på mängden data i den ursprungliga lagringen och hur processorn och minnet används av klustret.
1. Gå till Azure Data Explorer-klustret i Azure-portalen. Välj **Konfigurationer**i **Inställningar**. 
1. I fönstret **Konfigurationer** väljer du **Av** för att inaktivera **inmatning av direktuppspelning**.
1. Välj **Spara**.

    ![Inmatning av strömning](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Begränsningar

* Inmatning av direktuppspelning stöder inte [databasmarkörer](/azure/kusto/management/databasecursor) eller [datamappning](/azure/kusto/management/mappings). Endast [förskapad](/azure/kusto/management/create-ingestion-mapping-command) datamappning stöds. 
* Strömningsinmatningsprestanda och kapacitetsskalor med ökad virtuell dator och klusterstorlekar. Samtidiga intag är begränsade till sex intag per kärna. För 16 kärnsenheter, till exempel D14 och L16, är den maximala belastningen som stöds 96 samtidiga intag. För två kärnsenheter, till exempel D11, är den maximala belastningen som stöds 12 samtidiga intag.
* Datastorleksbegränsningen per inmatningsbegäran är 4 MB.
* Schemauppdateringar, till exempel skapande och ändring av tabeller och inmatningsmappningar, kan ta upp till fem minuter för tjänsten för direktuppspelningsintag.
* Aktivera direktuppspelningsinmatning i ett kluster, även när data inte används via direktuppspelning, använder en del av den lokala SSD-disken på klusterdatorerna för strömmande inmatningsdata och minskar den tillgängliga lagringen för frekvent cachelagring.
* [Utsträckningtaggar](/azure/kusto/management/extents-overview#extent-tagging) kan inte ställas in på data för direktuppspelningsinmatning.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Data Explorer](web-query-data.md)
