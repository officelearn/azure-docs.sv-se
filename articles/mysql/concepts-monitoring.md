---
title: Övervakning – Azure Database for MySQL
description: Den här artikeln beskriver måtten för övervakning och aviseringar för Azure Database for MySQL, inklusive CPU, lagring och anslutnings statistik.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.custom: references_regions
ms.date: 8/13/2020
ms.openlocfilehash: 9e1bd3f555873503aa1f6ed9c804aced3620fb9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627524"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Övervakning i Azure Database for MySQL
Genom att övervaka data om dina servrar kan du felsöka och optimera för din arbets belastning. Azure Database for MySQL tillhandahåller olika mått som ger inblick i serverns beteende.

## <a name="metrics"></a>Mått
Alla Azure-mått har en frekvens på en minut och varje mått ger 30 dagars historik. Du kan konfigurera aviseringar för måtten. Steg för steg-anvisningar finns i [så här konfigurerar du aviseringar](howto-alert-on-metric.md). Andra uppgifter är att ställa in automatiserade åtgärder, utföra avancerad analys och lagrings historik. Mer information finns i [Översikt över Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for MySQL:

|Mått|Mått visnings namn|Enhet|Beskrivning|
|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procent andelen CPU som används.|
|memory_percent|Minnes procent|Procent|Procent andelen minne som används.|
|io_consumption_percent|I/o procent|Procent|Procent andelen av IO som används. (Gäller inte för Basic-nivå servrar)|
|storage_percent|Lagrings procent|Procent|Procent andelen lagring som används av serverns högsta värde.|
|storage_used|Använt lagrings utrymme|Byte|Mängden lagring som används. Lagrings utrymmet som används av tjänsten kan omfatta databasfilerna, transaktions loggarna och Server loggarna.|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Procent andelen Server logg lagring som används av serverns maximala Server logg lagring.|
|serverlog_storage_usage|Server logg lagring används|Byte|Mängden Server logg lagring som används.|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Den maximala Server logg lagringen för den här servern.|
|storage_limit|Lagrings gräns|Byte|Det maximala lagrings utrymmet för den här servern.|
|active_connections|Aktiva anslutningar|Antal|Antalet aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Antal|Antalet misslyckade anslutningar till servern.|
|seconds_behind_master|Fördröjning för replikering på några sekunder|Antal|Antalet sekunder som replik servern avgörs mot käll servern. (Gäller inte för Basic-nivå servrar)|
|network_bytes_egress|Nätverk – utgående|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk – inkommande|Byte|Nätverk i över aktiva anslutningar.|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Mängden lagring av säkerhets kopior som används. Det här måttet representerar summan av lagrings utrymme som förbrukas av alla fullständiga säkerhets kopior av databasen, differentiella säkerhets kopior och logg säkerhets kopior som bevaras baserat på den kvarhållna säkerhets kopie perioden som angetts för servern. Säkerhets kopierings frekvensen är service som hanteras och beskrivs i [artikeln begrepp](concepts-backup.md). För Geo-redundant lagring är lagrings utrymmet för säkerhets kopiering två gånger för det lokalt redundanta lagrings utrymmet.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera långsam fråga och gransknings loggning på servern. Dessa loggar är också tillgängliga via Azure-diagnostikloggar i Azure Monitor loggar, Event Hubs och lagrings konto. Mer information om loggning finns i artiklarna [gransknings loggar](concepts-audit-logs.md) och [långsam frågekörning](concepts-server-logs.md) .

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) är en funktion som håller reda på frågans prestanda över tid, inklusive statistik för körning av frågor och vänta-händelser. Funktionen fortsätter att köra prestanda information om körningen i **MySQL** -schemat. Du kan styra insamling och lagring av data via olika konfigurations rattar.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) arbetar tillsammans med Query Store för att tillhandahålla visualiseringar som är tillgängliga från Azure Portal. Med de här diagrammen kan du identifiera viktiga frågor som påverkar prestanda. Query Performance Insight är tillgängligt i avsnittet **intelligent prestanda** på Azure Database for MySQL servers Portal sida.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [prestanda rekommendationer](concepts-performance-recommendations.md) identifierar möjligheter att förbättra arbets Belastningens prestanda. Prestanda rekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra prestandan för dina arbets belastningar. För att skapa index rekommendationer tar funktionen hänsyn till olika databas egenskaper, inklusive schema och arbets belastningen som rapporteras av Frågearkivet. När du har implementerat en prestanda rekommendation bör kunderna testa prestanda för att utvärdera effekten av dessa ändringar.

## <a name="planned-maintenance-notification"></a>Meddelande om planerat underhåll

Med **planerade underhålls aviseringar** kan du få aviseringar om kommande planerat underhåll till din Azure Database for MySQL. Dessa meddelanden är integrerade med [service Health](../service-health/overview.md) planerat underhåll och gör att du kan visa alla schemalagda underhåll för dina prenumerationer på ett och samma ställe. Den hjälper också till att skala meddelandet till rätt mål grupper för olika resurs grupper, eftersom du kan ha olika kontakter som är ansvariga för olika resurser. Du får ett meddelande om kommande underhåll 72 timmar före händelsen.

Under planerat underhåll kan du vänta på att servern ska startas om och att [tillfälliga fel](concepts-connectivity.md#transient-errors) kan uppstå. De flesta av dessa händelser begränsas automatiskt av systemet på mindre än 60 sekunder.

> [!IMPORTANT]
> Meddelanden om planerade underhåll är för närvarande tillgängliga i offentlig för hands version i alla regioner **utom** västra centrala USA

### <a name="to-receive-planned-maintenance-notification"></a>För att få ett meddelande om planerat underhåll

1. I [portalen](https://portal.azure.com)väljer du **service Health**.
2. I avsnittet **aviseringar** väljer du **hälso aviseringar**.
3. Välj **+ Lägg till tjänstens hälso tillstånds avisering** och fyll i fälten.
4. Fyll i de obligatoriska fälten. 
5. Välj **händelse typ**, Välj **planerat underhåll** eller **Välj alla**
6. I **Åtgärds grupper** definierar du hur du vill få aviseringen (få ett e-postmeddelande, utlösa en Logic app osv.)  
7. Se till att aktivera regel vid skapande har angetts till Ja.
8. Välj **skapa aviserings regel** för att slutföra aviseringen

Detaljerade anvisningar om hur du skapar **tjänstens hälso aviseringar**finns i [skapa aktivitets logg aviseringar för tjänst meddelanden](../service-health/alerts-activity-log-service-notifications.md).

> [!Note]
> Vi kommer att göra varje försök att tillhandahålla **planerat underhåll meddelande** 72 timmar meddelande om alla händelser. I fall av kritiska uppdateringar eller säkerhets korrigeringar kan du dock skicka meddelanden närmare händelsen eller utelämnas.

## <a name="next-steps"></a>Nästa steg
- Se [hur du ställer in aviseringar](howto-alert-on-metric.md) för vägledning om hur du skapar en avisering på ett mått.
- Mer information om hur du får åtkomst till och exporterar mått med hjälp av Azure Portal, REST API eller CLI finns i [Översikt över Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Läs vår blogg om [bästa praxis för att övervaka servern](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
