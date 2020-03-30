---
title: Översikt över företagssäkerhet i Azure HDInsight
description: Lär dig de olika metoderna för att säkerställa företagets säkerhet i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.date: 03/03/2020
ms.openlocfilehash: 95bfe7d7788133d8548598cb30c8084bf64a977f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78267707"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Översikt över företagssäkerhet i Azure HDInsight

Azure HDInsight erbjuder ett antal metoder för att tillgodose företagets säkerhetsbehov. De flesta av dessa lösningar är inte aktiverade som standard. Med den här flexibiliteten kan du välja de säkerhetsfunktioner som är viktigast för dig och hjälper dig att undvika att betala för funktioner som du inte vill ha. Det innebär också att det är ditt ansvar att se till att rätt lösningar är aktiverade för din installation och miljö.

I den här artikeln undersöks säkerhetslösningar genom att dela säkerhetslösningar i linje med fyra traditionella säkerhetspelare: perimetersäkerhet, autentisering, auktorisering och kryptering.

Den här artikeln introducerar också **ESP (Azure HDInsight Enterprise Security Package),** som tillhandahåller Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll för HDInsight-kluster.

## <a name="enterprise-security-pillars"></a>Säkerhet pelare för företag

Ett sätt att se på företagssäkerhet delar upp säkerhetslösningar i fyra huvudgrupper baserat på typen av kontroll. Dessa grupper kallas också säkerhetspelare och är följande: perimetersäkerhet, autentisering, auktorisering och kryptering.

### <a name="perimeter-security"></a>Säkerhetsskydd för perimeter

Perimetersäkerhet i HDInsight uppnås genom [virtuella nätverk](../hdinsight-plan-virtual-network-deployment.md). En företagsadministratör kan skapa ett kluster i ett virtuellt nätverk (VNET) och använda nätverkssäkerhetsgrupper (NSG) för att begränsa åtkomsten till det virtuella nätverket. Endast tillåtna IP-adresser i de inkommande NSG-reglerna kan kommunicera med HDInsight-klustret. Den här konfigurationen ger perimetersäkerhet.

Alla kluster som distribueras i ett VNET har också en privat slutpunkt som löser till en privat IP inuti VNET för privat HTTP-åtkomst till klustergateways.

### <a name="authentication"></a>Autentisering

[Enterprise Security Package](apache-domain-joined-architecture.md) från HDInsight tillhandahåller Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll. Active Directory-integreringen uppnås med hjälp av [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Med dessa funktioner kan du skapa ett HDInsight-kluster som är anslutet till en hanterad Active Directory-domän. Du kan sedan konfigurera en lista över medarbetare från företaget som kan autentisera och logga in i klustret.

Med den här inställningen kan företagsanställda logga in på klusternoderna med hjälp av sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera med andra godkända slutpunkter som Apache Ambari Views, ODBC, JDBC, PowerShell och REST API:er för att interagera med klustret.

### <a name="authorization"></a>Auktorisering

En bästa praxis som de flesta företag följer är att se till att inte alla anställda har tillgång till alla företagsresurser. På samma sätt kan administratören definiera rollbaserade åtkomstkontrollprinciper för klusterresurserna. Detta är endast tillgängligt i ESP-kluster.

Hadoop admin kan konfigurera rollbaserad åtkomstkontroll (RBAC) för att säkra Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)och [Kafka](apache-domain-joined-run-kafka.md) med hjälp av dessa plugins i Apache Ranger. Genom att konfigurera RBAC-principer kan du associera behörigheter med en roll i organisationen. Detta lager av abstraktion gör det lättare att se till att människor bara har de behörigheter som behövs för att utföra sina arbetsuppgifter. Ranger kan du också granska dataåtkomsten för anställda och eventuella ändringar som gjorts för att komma åt kontrollprinciper.

Administratören kan till exempel konfigurera [Apache Ranger](https://ranger.apache.org/) för att ange åtkomstkontrollprinciper för Hive. Den här funktionen säkerställer filtrering på radnivå och kolumnnivå (datamaskering) och filtrerar känsliga data från obehöriga användare.

### <a name="auditing"></a>Granskning

Granskning av all åtkomst till klusterresurserna och data är nödvändig för att spåra obehörig eller oavsiktlig åtkomst av resurserna. Det är lika viktigt som att skydda HDInsight-klusterresurserna från obehöriga användare och skydda data.

Administratören kan visa och rapportera all åtkomst till HDInsight-klusterresurser och -data. Administratören kan också visa och rapportera alla ändringar i åtkomstkontrollprinciperna som skapats i slutpunkter som stöds av Apache Ranger.

Om du vill komma åt Apache Ranger- och Ambari-granskningsloggar och ssh-åtkomstloggar [aktiverar du Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) och visar tabellerna som tillhandahåller granskningsposter.

### <a name="encryption"></a>Kryptering

Att skydda data är viktigt för att uppfylla organisationens säkerhets- och efterlevnadskrav. Tillsammans med att begränsa åtkomsten till data från obehöriga medarbetare bör du kryptera den.

Båda datalager för HDInsight-kluster, Azure Blob storage och Azure Data Lake Storage Gen1/Gen2, stöder transparent kryptering på serversidan [av data](../../storage/common/storage-service-encryption.md) i vila. Secure HDInsight-kluster fungerar sömlöst med den här funktionen för kryptering på serversidan av data i vila.

### <a name="compliance"></a>Efterlevnad

Azure-efterlevnadserbjudanden baseras på olika typer av försäkringar, inklusive formella certifieringar, intyg, valideringar, auktoriseringar och bedömningar som produceras av oberoende granskningsföretag från tredje part, avtalsändringar, självutvärderingar och kundvägledningsdokument som tagits fram av Microsoft. Information om HDInsight-efterlevnad finns i [Microsoft Trust Center](https://www.microsoft.com/trust-center) och [översikten över Microsoft Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Modell med delat ansvar

Följande bild sammanfattar de viktigaste systemsäkerhetsområdena och de säkerhetslösningar som är tillgängliga för dig i varje. Det belyser också vilka säkerhetsområden som är ditt ansvar som kund och vilka områden som är ansvaret för HDInsight som tjänsteleverantör.

![Diagram över delat ansvar i HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

I följande tabell finns länkar till resurser för varje typ av säkerhetslösning.

| Säkerhetsområde | Tillgängliga lösningar | Ansvarig part |
|---|---|---|
| Säkerhet för dataåtkomst | Konfigurera [åtkomstkontrollistor ACL:er](../../storage/blobs/data-lake-storage-access-control.md) för Azure Data Lake Storage Gen1 och Gen2  | Kund |
|  | Aktivera egenskapen ["Säker överföring krävs"](../../storage/common/storage-require-secure-transfer.md) på lagringskonton. | Kund |
|  | Konfigurera [Azure Storage-brandväggar](../../storage/common/storage-network-security.md) och virtuella nätverk | Kund |
|  | Konfigurera Slutpunkter för [virtuella Azure-nätverkstjänst för](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) Cosmos DB och Azure SQL [DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Kund |
|  | Kontrollera att [TLS-kryptering](../../storage/common/storage-security-tls.md) är aktiverad för data under överföring. | Kund |
|  | Konfigurera [kundhanterade nycklar](../../storage/common/storage-encryption-keys-portal.md) för Azure Storage-kryptering | Kund |
| Säkerhet för program och mellanprogram | Integrera med AAD-DS och [konfigurera autentisering](apache-domain-joined-configure-using-azure-adds.md) | Kund |
|  | Konfigurera [principer för Apache Ranger-auktorisering](apache-domain-joined-run-hive.md) | Kund |
|  | Använda [Azure Monitor-loggar](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Kund |
| Säkerhet i operativsystemet | Skapa kluster med den senaste säkra basavbildningen | Kund |
|  | Se till att [OS-korrigering](../hdinsight-os-patching.md) med jämna mellanrum | Kund |
| Nätverkssäkerhet | Konfigurera ett [virtuellt nätverk](../hdinsight-plan-virtual-network-deployment.md) |
|  | Konfigurera [NSG-regler (Inbound Network Security Group)](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Kund |
|  | Konfigurera [begränsning av utgående trafik](../hdinsight-restrict-outbound-traffic.md) med brandvägg | Kund |
| Virtualiserad infrastruktur | Ej tillämpligt | HDInsight (molnleverantör) |
| Säkerhet för fysisk infrastruktur | Ej tillämpligt | HDInsight (molnleverantör) |

## <a name="next-steps"></a>Nästa steg

* [Planera för HDInsight-kluster med ESP](apache-domain-joined-architecture.md)
* [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md)
* [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md)
