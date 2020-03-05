---
title: Översikt över företags säkerhet i Azure HDInsight
description: Lär dig olika metoder för att säkerställa företags säkerhet i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.date: 03/03/2020
ms.openlocfilehash: 95bfe7d7788133d8548598cb30c8084bf64a977f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267707"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Översikt över företags säkerhet i Azure HDInsight

Azure HDInsight erbjuder ett antal metoder för att lösa företagets säkerhets behov. De flesta av dessa lösningar är inte aktiverade som standard. Med den här flexibiliteten kan du välja de säkerhetsfunktioner som är viktigast för dig och hjälpa dig att undvika att betala för funktioner som du inte vill ha. Det innebär också att det är ditt ansvar att se till att rätt lösningar är aktiverade för din installation och miljö.

Den här artikeln beskriver säkerhetslösningar genom att dela upp säkerhetslösningar utmed linjerna i fyra traditionella säkerhets pelare: perimeter säkerhet, autentisering, auktorisering och kryptering.

Den här artikeln beskriver också **Azure HDInsight Enterprise Security Package (ESP)** , som ger Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomst kontroll för HDInsight-kluster.

## <a name="enterprise-security-pillars"></a>Företags säkerhets pelare

Ett sätt att titta på företags säkerhet delar säkerhetslösningar i fyra huvud grupper baserat på typen av kontroll. Dessa grupper kallas även säkerhets pelare och är följande: perimeter-säkerhet, autentisering, auktorisering och kryptering.

### <a name="perimeter-security"></a>Perimeter-säkerhet

Perimeter-säkerhet i HDInsight uppnås via [virtuella nätverk](../hdinsight-plan-virtual-network-deployment.md). En företags administratör kan skapa ett kluster i ett virtuellt nätverk (VNET) och använda nätverks säkerhets grupper (NSG) för att begränsa åtkomsten till det virtuella nätverket. Endast de tillåtna IP-adresserna i reglerna för inkommande NSG kommer att kunna kommunicera med HDInsight-klustret. Den här konfigurationen tillhandahåller perimeter-säkerhet.

Alla kluster som distribueras i ett VNET har också en privat slut punkt som matchar en privat IP-adress i VNET för privat HTTP-åtkomst till kluster-gatewayerna.

### <a name="authentication"></a>Autentisering

[Enterprise Security Package](apache-domain-joined-architecture.md) från HDInsight tillhandahåller Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomst kontroll. Active Directory-integreringen uppnås genom användning av [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Med dessa funktioner kan du skapa ett HDInsight-kluster som är anslutet till en hanterad Active Directory-domän. Sedan kan du konfigurera en lista över anställda från företaget som kan autentisera och logga in i klustret.

Med den här inställningen kan företags anställda logga in på klusternoderna genom att använda sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera med andra godkända slut punkter som Apache Ambari views, ODBC, JDBC, PowerShell och REST-API: er för att samverka med klustret.

### <a name="authorization"></a>Auktorisering

En bästa praxis som de flesta företag följer är att se till att inte alla anställda har till gång till alla företags resurser. På samma sätt kan administratören definiera rollbaserade principer för åtkomst kontroll för kluster resurserna. Detta är endast tillgängligt i ESP-klustren.

Hadoop-administratören kan konfigurera rollbaserad åtkomst kontroll (RBAC) för att skydda Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)och [Kafka](apache-domain-joined-run-kafka.md) med hjälp av dessa plugin-program i Apache Ranger. Genom att konfigurera RBAC-principer kan du associera behörigheter med en roll i organisationen. Detta skikt gör det lättare att se till att människor bara har de behörigheter som krävs för att utföra sina uppgifter. Med Ranger kan du också granska data åtkomsten för anställda och eventuella ändringar som gjorts i principer för åtkomst kontroll.

Administratören kan till exempel konfigurera [Apache Ranger](https://ranger.apache.org/) för att ange åtkomstkontrollprinciper för Hive. Den här funktionen garanterar filtrering på radnivå och på kolumn nivå (data maskning) och filtrerar känsliga data från obehöriga användare.

### <a name="auditing"></a>Granskning

Granskning av all åtkomst till kluster resurserna och data är nödvändig för att spåra obehörig eller oavsiktlig åtkomst till resurserna. Det är lika viktigt som att skydda HDInsight-klustrets resurser från obehöriga användare och skydda data.

Administratören kan visa och rapportera all åtkomst till HDInsight-kluster resurser och data. Administratören kan också visa och rapportera alla ändringar i åtkomst kontroll principerna som har skapats i Apache Ranger-stödda slut punkter.

För att komma åt Apache Ranger och Ambari gransknings loggar och SSH-åtkomst loggar [aktiverar Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) och visar de tabeller som innehåller gransknings poster.

### <a name="encryption"></a>Kryptering

Det är viktigt att skydda data för att uppfylla organisationens krav på säkerhet och efterlevnad. Förutom att begränsa åtkomsten till data från obehöriga anställda bör du kryptera den.

Både data lager för HDInsight-kluster, Azure Blob Storage och Azure Data Lake Storage Gen1/Gen2, stöder transparent [kryptering på Server sidan av data](../../storage/common/storage-service-encryption.md) i vila. Säkra HDInsight-kluster fungerar sömlöst med den här funktionen för kryptering på Server sidan av data i vila.

### <a name="compliance"></a>Efterlevnad

Azure Compliance-erbjudanden baseras på olika typer av garantier, inklusive formella certifieringar, attesteringar, godkännanden och bedömningar som produceras av oberoende gransknings företag från tredje part, avtals ändringar, själv bedömningar och kund väglednings dokument som skapats av Microsoft. Information om efterlevnad av HDInsight finns i [Microsoft Trust Center](https://www.microsoft.com/trust-center) och [Översikt över Microsoft Azure efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Delad ansvars modell

Följande bild sammanfattar de större säkerhets områdena för systemet och de säkerhetslösningar som är tillgängliga för dig i var och en. Den fokuserar också på vilka säkerhets områden som är ditt ansvar som en kund och vilka områden som är ansvariga för HDInsight som tjänst leverantör.

![Schema för delat HDInsight-ansvar](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Följande tabell innehåller länkar till resurser för varje typ av säkerhets lösning.

| Säkerhets områden | Tillgängliga lösningar | Ansvarig part |
|---|---|---|
| Säkerhet för data åtkomst | Konfigurera [åtkomst kontrol listor med ACL: er](../../storage/blobs/data-lake-storage-access-control.md) för Azure Data Lake Storage gen1 och Gen2  | Kund |
|  | Aktivera egenskapen ["säker överföring krävs"](../../storage/common/storage-require-secure-transfer.md) för lagrings konton. | Kund |
|  | Konfigurera [Azure Storage brand väggar](../../storage/common/storage-network-security.md) och virtuella nätverk | Kund |
|  | Konfigurera [tjänst slut punkter för Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) för Cosmos DB och [Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Kund |
|  | Se till att [TLS-kryptering](../../storage/common/storage-security-tls.md) har Aktiver ATS för data under överföring. | Kund |
|  | Konfigurera [Kundhanterade nycklar](../../storage/common/storage-encryption-keys-portal.md) för Azure Storage kryptering | Kund |
| Program-och mellanprogram säkerhet | Integrera med AAD-DS och [Konfigurera autentisering](apache-domain-joined-configure-using-azure-adds.md) | Kund |
|  | Konfigurera [Auktoriseringsprinciper för Apache Ranger](apache-domain-joined-run-hive.md) | Kund |
|  | Använda [Azure Monitor loggar](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Kund |
| Operativ systemets säkerhet | Skapa kluster med den senaste säkra bas avbildningen | Kund |
|  | Se till att [OS-uppdatering](../hdinsight-os-patching.md) sker med jämna mellanrum | Kund |
| Nätverkssäkerhet | Konfigurera ett [virtuellt nätverk](../hdinsight-plan-virtual-network-deployment.md) |
|  | Konfigurera [regler för inkommande nätverks säkerhets grupp (NSG)](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Kund |
|  | Konfigurera [begränsning av utgående trafik](../hdinsight-restrict-outbound-traffic.md) med brand vägg | Kund |
| Virtualiserad infrastruktur | Ej tillämpligt | HDInsight (Cloud Provider) |
| Säkerhet för fysisk infrastruktur | Ej tillämpligt | HDInsight (Cloud Provider) |

## <a name="next-steps"></a>Nästa steg

* [Planera för HDInsight-kluster med ESP](apache-domain-joined-architecture.md)
* [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md)
* [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md)
