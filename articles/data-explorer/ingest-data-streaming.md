---
title: Använd strömning för strömning för att mata in data i Azure Datautforskaren
description: Läs om hur du matar in data i Azure Datautforskaren att använda strömning.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: b99827ff9fd01c7179a25fafd05bfc8e4ef8ae63
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921104"
---
# <a name="streaming-ingestion-preview"></a>Strömnings inmatning (för hands version)

Strömnings inmatning är avsedd för scenarier som kräver låg latens med en inmatnings tid på mindre än 10 sekunder för varierande volym data. Den används för att optimera drift bearbetningen av många tabeller, i en eller flera databaser, där data strömmen i varje tabell är relativt liten (några poster per sekund), men den totala data inmatnings volymen är hög (tusentals poster per sekund).

Använd den klassiska inmatningen (bulk) i stället för strömnings inmatning när mängden data växer till mer än 1 MB per sekund per tabell. Läs [Översikt över data inmatning](/azure/data-explorer/ingest-data-overview) för att lära dig mer om de olika metoderna för inmatning.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Logga in på [webb gränssnittet](https://dataexplorer.azure.com/).
* Skapa [ett Azure datautforskaren-kluster och-databas](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Aktivera strömnings inmatning i klustret

> [!WARNING]
> Granska [begränsningarna](#limitations) innan du aktiverar inmatningen av ånga.

1. I Azure Portal går du till ditt Azure Datautforskaren-kluster. I **Inställningar**väljer du **konfigurationer**. 
1. I fönstret **konfigurationer** väljer du **på** för att aktivera **strömnings**inmatning.
1. Välj **Spara**.
 
    ![strömnings inmatning på](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. I [webb gränssnittet](https://dataexplorer.azure.com/)definierar du en [princip för strömnings](/azure/kusto/concepts/streamingingestionpolicy) inmatning för tabeller eller databaser som tar emot strömmande data. 

    > [!NOTE]
    > * Om principen definieras på databas nivå aktive ras alla tabeller i databasen för strömnings inmatning.
    > * Den tillämpade principen kan endast referera till nyligen inmatade data och inte andra tabeller i databasen.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Använd strömning för strömning för att mata in data till klustret

Det finns två typer av streaming-inmatningar som stöds:


* [**Händelsehubben**](/azure/data-explorer/ingest-data-event-hub) används som data Källa
* För **anpassad** inmatning måste du skriva ett program som använder en av Azure datautforskaren-klient biblioteken. Se [exempel på strömnings insamling](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) för ett exempel program.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Välj lämplig typ av strömnings inmatning

|   |Händelsehubb  |Anpassad inmatning  |
|---------|---------|---------|
|Data fördröjning mellan inmatnings initiering och data som är tillgängliga för fråga   |    längre fördröjning     |   kortare fördröjning      |
|Utvecklings kostnader    |   snabb och enkel installation, inga utvecklings kostnader    |   hög utvecklings kostnader för program för att hantera fel och säkerställa data konsekvens     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Inaktivera strömnings inmatning i klustret

> [!WARNING]
> Det kan ta några timmar att inaktivera strömnings inmatning.

1. Ta bort [princip för strömnings](/azure/kusto/concepts/streamingingestionpolicy) inmatning från alla relevanta tabeller och databaser. Borttagnings principen för strömning utlöser strömmande inmatnings data från den första lagringen till den permanenta lagringen i kolumn lagringen (omfattningar eller Shards). Data flytten kan gå mellan några sekunder till några timmar, beroende på mängden data i den första lagringen och hur CPU och minne används av klustret.
1. I Azure Portal går du till ditt Azure Datautforskaren-kluster. I **Inställningar**väljer du **konfigurationer**. 
1. I fönstret **konfigurationer** väljer du **av** för att inaktivera **strömnings**inmatning.
1. Välj **Spara**.

    ![Strömnings inmatning av](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Begränsningar

* Strömnings kapacitet och kapacitets skalning med ökad storlek på virtuella datorer och kluster. Samtidiga inmatningar är begränsade till sex inmatningar per kärna. För till exempel 16 core-SKU: er, till exempel D14 och L16, är den maximala belastningen som stöds 96 samtidiga inmatningar. För två kärn SKU: er, till exempel D11, är den maximala belastningen 12 samtidiga inmatningar.
* Data storleks begränsningen per inmatnings förfrågan är 4 MB.
* Schema uppdateringar, till exempel skapande och ändring av tabeller och inmatnings mappningar, kan ta upp till 5 minuter för strömnings tjänsten.
* Att aktivera strömnings inmatning i ett kluster, även när data inte matas in via direkt uppspelning, använder en del av den lokala SSD-disken på kluster datorerna för strömnings inmatnings data och minskar lagrings utrymmet som är tillgängliga för varmt cacheminne.
* Det går inte att ange [omfattnings etiketter](/azure/kusto/management/extents-overview#extent-tagging) för strömnings inmatnings data.

Streaming-inmatningen har inte stöd för följande funktioner:
* [Databas markörer](/azure/kusto/management/databasecursor).
* [Data mappning](/azure/kusto/management/mappings). Det finns endast stöd för data mappning som [skapats i förväg](/azure/kusto/management/create-ingestion-mapping-command) . 

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Datautforskaren](web-query-data.md)
