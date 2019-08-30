---
title: Använd strömning för strömning för att mata in data i Azure Datautforskaren
description: Läs om hur du matar in data i Azure Datautforskaren att använda strömning.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: b716cbf3efb044da68d4dd1dcb724369855d1ed1
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173656"
---
# <a name="streaming-ingestion-preview"></a>Strömnings inmatning (för hands version)

Strömnings inmatning är avsedd för scenarier som kräver låg latens med en inmatnings tid på mindre än 10 sekunder för varierande volym data. Den används för optimering av drift bearbetning av många tabeller, i en eller flera databaser där data strömmen i varje tabell är relativt liten (några poster per sekund), men den totala data inmatnings volymen är hög (tusentals poster per sekund).

Använd den klassiska inmatningen (bulk) i stället för strömnings inmatning när mängden data växer till mer än 1 MB per sekund per tabell. Läs [Översikt över data inmatning](/azure/data-explorer/ingest-data-overview) för att lära dig mer om de olika metoderna för inmatning.

> [!NOTE]
> Streaming-inmatningen har inte stöd för följande funktioner:
> * [Databas markörer](/azure/kusto/management/databasecursor).
> * [Data mappning](/azure/kusto/management/mappings). Det finns endast stöd för data mappning som [skapats i förväg](/azure/kusto/management/tables#create-ingestion-mapping) . 

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Logga in på [webb gränssnittet](https://dataexplorer.azure.com/).
* Skapa [ett Azure datautforskaren-kluster och-databas](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Aktivera strömnings inmatning i klustret

1. I Azure Portal går du till ditt Azure Datautforskaren-kluster. I **Inställningar**väljer du **konfigurationer**. 
1. I fönstret **konfigurationer** väljer du **på** för att aktivera **strömnings**inmatning.
1. Välj **Spara**.
 
    ![strömnings inmatning på](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. I [webb gränssnittet](https://dataexplorer.azure.com/)definierar du en [princip](/azure/kusto/concepts/streamingingestionpolicy) för strömnings inmatning för tabeller eller databaser som tar emot strömmande data. 

    > [!TIP]
    > Om principen definieras på databas nivå aktive ras alla tabeller i databasen för strömnings inmatning.

## <a name="supported-streaming-ingestion-types"></a>Hämtnings typer som stöds för strömning

Det finns två typer av streaming-inmatningar som stöds:

* [Händelsehubben](/azure/data-explorer/ingest-data-event-hub) används som data Källa
* För anpassad inmatning måste du skriva ett program som använder en av Azures Datautforskaren klient bibliotek. Se [exempel på strömnings insamling](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) för ett exempel program.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Välj lämplig typ av strömnings inmatning

|   |Händelsehubb  |Anpassad inmatning  |
|---------|---------|---------|
|Data fördröjning mellan inmatnings initiering och data som är tillgängliga för fråga   |    längre fördröjning     |   kortare fördröjning      |
|Utvecklings kostnader    |   snabb och enkel installation, inga utvecklings kostnader    |   hög utvecklings kostnader för program för att hantera fel och säkerställa data konsekvens     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Inaktivera strömnings inmatning i klustret

> [!WARNING]
> Det kan ta några timmar att inaktivera strömnings inmatning.

1. Ta bort [princip](/azure/kusto/concepts/streamingingestionpolicy) för strömnings inmatning från alla relevanta tabeller och databaser. Borttagnings principen för strömning utlöser strömmande inmatnings data från den första lagringen till den permanenta lagringen i kolumn lagringen (omfattningar eller Shards). Data flytten kan gå mellan några sekunder till några timmar, beroende på mängden data i den första lagringen och processor-och minnes användningen i klustret.
1. I Azure Portal går du till ditt Azure Datautforskaren-kluster. I **Inställningar**väljer du **konfigurationer**. 
1. I fönstret **konfigurationer** väljer du **av** för att inaktivera **strömnings**inmatning.
1. Välj **Spara**.

    ![Strömnings inmatning av](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Begränsningar

* Strömnings kapacitet och kapacitets skalning med ökad storlek på virtuella datorer och kluster. Den rekommenderade belastningen för en enskild D11-nod är upp till 20 begär Anden per sekund. Den rekommenderade belastningen för en enskild D14-nod är upp till 150 begär Anden per sekund.
* För närvarande stöds endast 8-och 16-SKU: er (D13, D14, L8 och L16).
* Data storleks begränsningen per inmatnings förfrågan är 4 MB.
* Schema uppdateringar, till exempel skapande och ändring av tabeller och inmatnings mappningar, kan ta upp till 5 minuter för strömnings tjänsten.
* Att aktivera strömnings inmatning i ett kluster, även när data inte matas in via direkt uppspelning, använder en del av den lokala SSD-disken på kluster datorerna för strömnings inmatnings data och minskar lagrings utrymmet som är tillgängliga för varmt cacheminne.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Datautforskaren](web-query-data.md)