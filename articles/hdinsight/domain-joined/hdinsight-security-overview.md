---
title: Översikt över företagssäkerhet i Azure HDInsight
description: Lär dig de olika metoderna för att säkerställa företagets säkerhet i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 797767e0c463161f29e486aef7db0ccaf459e299
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733559"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Översikt över företagssäkerhet i Azure HDInsight

Azure HDInsight erbjuder ett antal metoder för att tillgodose företagets säkerhetsbehov. De flesta av dessa lösningar är inte aktiverade som standard. Med den här flexibiliteten kan du välja vilka säkerhetsfunktioner som är viktigast för dig. Och hjälper dig att undvika att betala för funktioner som du inte vill ha. Den här flexibiliteten innebär också att det är ditt ansvar att se till att rätt lösningar är aktiverade för din installation och miljö.

I den här artikeln undersöks säkerhetslösningar genom att dela upp säkerhetslösningar i fyra traditionella säkerhetspelare: perimetersäkerhet, autentisering, auktorisering och kryptering.

Den här artikeln introducerar också **ESP (Azure HDInsight Enterprise Security Package),** som tillhandahåller Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll för HDInsight-kluster.

## <a name="enterprise-security-pillars"></a>Säkerhet pelare för företag

Ett sätt att se på företagssäkerhet delar upp säkerhetslösningar i fyra huvudgrupper baserat på typen av kontroll. Dessa grupper kallas också säkerhetspelare och är följande typer: perimetersäkerhet, autentisering, auktorisering och kryptering.

### <a name="perimeter-security"></a>Säkerhetsskydd för perimeter

Perimetersäkerhet i HDInsight uppnås genom [virtuella nätverk](../hdinsight-plan-virtual-network-deployment.md). En företagsadministratör kan skapa ett kluster i ett virtuellt nätverk (VNET) och använda nätverkssäkerhetsgrupper (NSG) för att begränsa åtkomsten till det virtuella nätverket. Endast tillåtna IP-adresser i de inkommande NSG-reglerna kan kommunicera med HDInsight-klustret. Den här konfigurationen ger perimetersäkerhet.

Alla kluster som distribueras i ett VNET har också en privat slutpunkt. Slutpunkten matchas till en privat IP inuti det virtuella nätverket för privat HTTP-åtkomst till klustergateways.

### <a name="authentication"></a>Autentisering

[Enterprise Security Package](apache-domain-joined-architecture.md) från HDInsight tillhandahåller Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll. Active Directory-integreringen uppnås med hjälp av [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Med dessa funktioner kan du skapa ett HDInsight-kluster som är anslutet till en Active Directory-domän. Konfigurera sedan en lista över medarbetare från företaget som kan autentisera till klustret.

Med den här inställningen kan företagsanställda logga in på klusternoderna med hjälp av sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera med andra godkända slutpunkter. Precis som Apache Ambari Views, ODBC, JDBC, PowerShell och REST API:er för att interagera med klustret.

### <a name="authorization"></a>Auktorisering

En bästa praxis de flesta företag följer är att se till att inte alla anställda har full tillgång till alla företagsresurser. På samma sätt kan administratören definiera rollbaserade åtkomstkontrollprinciper för klusterresurserna. Den här åtgärden är endast tillgänglig i ESP-kluster.

Hadoop-administratören kan konfigurera rollbaserad åtkomstkontroll (RBAC). Konfigurationerna säkra Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)och [Kafka](apache-domain-joined-run-kafka.md) med Apache Range plugins. Genom att konfigurera RBAC-principer kan du associera behörigheter med en roll i organisationen. Detta lager av abstraktion gör det lättare att se till att människor bara har de behörigheter som behövs för att göra sitt arbete ansvar. Ranger kan du också granska dataåtkomsten för anställda och eventuella ändringar som gjorts för att komma åt kontrollprinciper.

Administratören kan till exempel konfigurera [Apache Ranger](https://ranger.apache.org/) för att ange åtkomstkontrollprinciper för Hive. Den här funktionen säkerställer filtrering på radnivå och kolumnnivå (datamaskering). Och filtrerar känsliga data från obehöriga användare.

### <a name="auditing"></a>Granskning

Granskning av klusterresursåtkomst är nödvändig för att spåra obehörig eller oavsiktlig åtkomst av resurserna. Det är lika viktigt som att skydda klusterresurserna från obehörig åtkomst.

Administratören kan visa och rapportera all åtkomst till HDInsight-klusterresurser och -data. Administratören kan visa och rapportera ändringar i åtkomstkontrollprinciperna.

Aktivera Azure Monitor om du vill komma åt Granskningsloggar för [Apache](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing)Ranger och Ambari och ssh-åtkomstloggar . Och visa tabellerna som tillhandahåller granskningsposter.

### <a name="encryption"></a>Kryptering

Att skydda data är viktigt för att uppfylla organisationens säkerhets- och efterlevnadskrav. Tillsammans med att begränsa åtkomsten till data från obehöriga medarbetare bör du kryptera den.

Azure storage and Data Lake Storage Gen1/Gen2, stöder transparent [serverbaserad kryptering av data](../../storage/common/storage-service-encryption.md) i vila. Secure HDInsight-kluster fungerar sömlöst med kryptering på serversidan av data i vila.

### <a name="compliance"></a>Efterlevnad

Azure-efterlevnadserbjudanden baseras på olika typer av försäkringar, inklusive formella certifieringar. Även intyg, valideringar och auktoriseringar. Bedömningar som tagits fram av oberoende revisionsföretag från tredje part. Avtalsändringar, självbedömningar och kundvägledningsdokument som tagits fram av Microsoft. Information om HDInsight-efterlevnad finns i [Microsoft Trust Center](https://www.microsoft.com/trust-center) och [översikten över Microsoft Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Modell med delat ansvar

Följande bild sammanfattar de viktigaste systemsäkerhetsområdena och de säkerhetslösningar som är tillgängliga för dig i varje. Det belyser också vilka säkerhetsområden som är ditt ansvar som kund. Och vilka områden är HDInsights ansvar som tjänsteleverantör.

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
