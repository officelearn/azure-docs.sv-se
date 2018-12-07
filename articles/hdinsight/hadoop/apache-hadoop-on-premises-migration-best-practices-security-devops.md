---
title: Migrera lokala Apache Hadoop-kluster till Azure HDInsight - säkerhet och bästa praxis för DevOps
description: Lär dig säkerhets- och DevOps-Metodtips för att migrera lokala Hadoop-kluster till Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 548992ac221b1b6f9a29082eb986aa42c6a2807e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994000"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight - säkerhet och bästa praxis för DevOps

Den här artikeln ger rekommendationer för säkerhets- och DevOps i Azure HDInsight-system. Det är en del i en serie som ger bästa praxis för att hjälpa migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Skydda och styr kluster med Enterprise Security Package

Enterprise Security Package (ESP) har stöd för Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll. HDInsight-kluster är ansluten till Active Directory-domänen med det valda ESP-alternativet och enterprise-administratör kan konfigurera rollbaserad åtkomstkontroll (RBAC för Apache Hive säkerhet) med hjälp av Apache Ranger. Administratören kan också granska dataåtkomst för anställda och eventuella ändringar som görs till principer för åtkomstkontroll.

ESP är tillgängligt på följande klustertyper: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka och Interactive Query (Hive-LLAP). 

Använd följande steg för att distribuera domänanslutna HDInsight-kluster:

- Distribuera Azure Active Directory (AAD) genom att ange domännamnet.
- Distribuera Azure Active Directory Domain Services (DS AAD).
- Skapa nödvändiga virtuellt nätverk och undernät.
- Distribuera en virtuell dator i det virtuella nätverket för att hantera AAD DS.
- Anslut den virtuella datorn till domänen.
- Installera AD och DNS-verktyg.
- Låt AAD-DS-administratören skapa en organisationsenhet (OU).
- Aktivera LDAPS för AAD DS.
- Skapa ett tjänstkonto i Azure Active Directory med delegerade Läs & admin skrivbehörighet till Organisationsenheten, så att den kan. Tjänstkontot kan ansluta datorer till domänen och placera datorn säkerhetsobjekt i Organisationsenheten. Det kan också skapa tjänstens huvudnamn i Organisationsenheten som du anger när klustret skapas.

    > [!Note]
    > Kontot behöver inte vara administratör för AD domänkonto.

- Distribuera HDInsight ESP-kluster genom att ange följande parametrar:
    - **Domännamn**: det domännamn som är associerat med Azure AD DS.
    - **Domänanvändarnamn**: kontot i Azure AD DS-DC-hanterad domän som du skapade i föregående avsnitt, till exempel: `hdiadmin@contoso.onmicrosoft.com`. Den här domänanvändare ska vara administratör för det här HDInsight-klustret.
    - **Domänlösenord**: lösenordet för tjänstkontot.
    - **Organisationsenhet**: det unika namnet på den Organisationsenhet som du vill använda med HDInsight-klustret, till exempel: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Om den här Organisationsenheten inte finns, försöker HDInsight-klustret skapa Organisationsenheten med privilegier för kontot.
    - **LDAPS-URL**: till exempel `ldaps://contoso.onmicrosoft.com:636`.
    - **Ha åtkomst till användargrupper**: säkerhetsgrupperna vars användare som du vill synkronisera i klustret, till exempel: `HiveUsers`. Om du vill ange flera användargrupper avgränsa dem med semikolon (;). I grupperna måste finnas i katalogen innan du skapar ESP-klustret.

Mer information finns i följande artiklar:

- [En introduktion till Apache Hadoop-säkerhet med domänanslutna HDInsight-kluster](../domain-joined/apache-domain-joined-introduction.md)
- [Planera Azure-domänanslutna Hadoop-kluster i HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurera ett domänanslutet HDInsight-kluster med hjälp av Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synkronisera Azure Active Directory-användare till ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurera Apache Hive-policyer i domänanslutna HDInsight](../domain-joined/apache-domain-joined-run-hive.md)
- [Kör Apache Oozie i domänanslutna HDInsight Hadoop-kluster](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementera heltäckande företagssäkerhet

Slutpunkt-till-slutpunktsäkerhet för enterprise kan uppnås med hjälp av följande kontroller:

- **Privata och skyddade datapipeline (nivå perimetersäkerhet)**
    - Säkerhet på i perimeternätverket kan ske via Azure-nätverk, Nätverkssäkerhetsgrupper och Gateway-tjänsten.

- **Autentisering och auktorisering för dataåtkomst**
    - Skapa domänanslutna HDInsight-kluster med Azure Active Directory Domain Services. (Enterprise Security Package).
    - Använda Ambari och ge rollbaserad åtkomst till klusterresurserna för AD-användare.
    - Använda Apache Ranger för att ange åtkomstkontrollprinciper för Hive på tabellen / kolumn / radnivån.
    - SSH-åtkomst till klustret kan vara begränsad endast till administratören.

- **Granskning**
    - Visa och rapportera all åtkomst till HDInsight-klusterresurser och data.
    - Visa och rapportera alla ändringar i principer för åtkomstkontroll.

- **Kryptering**
    - Transparent Server Side encryption med hjälp av Microsoft-hanterade nycklar eller Kundhanterade nycklar.
    - Använda Client Side encryption, https och TLS i överföringen kryptering.

Mer information finns i följande artiklar:

- [Översikt över Azure virtuella nätverk](../../virtual-network/virtual-networks-overview.md)
- [Översikt över Azure Nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md)
- [Azure Virtual Network-peering](../../virtual-network/virtual-network-peering-overview.md)
- [Säkerhetsguiden för Azure storage](../../storage/common/storage-security-guide.md)
- [Azure Storage Service-kryptering i vila](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Använd övervakning och avisering

Mer information finns i artikeln:

[Översikt över Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Uppgradera kluster

Uppgradera regelbundet till den senaste versionen för HDInsight kan utnyttja de senaste funktionerna. Följande steg kan användas för att uppgradera klustret till den senaste versionen:

1. Skapa ett nytt TEST HDInsight-kluster med hjälp av den senast tillgängliga versionen HDInsight.
1. Testa på det nya klustret för att se till att jobb och arbetsbelastningar fungerar som förväntat.
1. Ändra jobb eller program eller arbetsbelastningar som krävs.
1. Säkerhetskopiera alla tillfälliga data som lagras lokalt på klusternoderna.
1. Ta bort det befintliga klustret.
1. Skapa ett kluster med den senaste versionen av HDInsight i samma virtuella nätverk undernät, med hjälp av samma standard data och metadata lager som tidigare kluster.
1. Importera alla tillfälliga data som har säkerhetskopierats.
1. Starta jobb/fortsätta att bearbeta med hjälp av det nya klustret.

Mer information finns i artikeln: [uppgradera HDInsight-kluster till en ny version](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Korrigera klusteroperativsystemen

Som en hanterad tjänst i Hadoop hand HDInsight tar om uppdatering av Operativsystemet för de virtuella datorerna som används av HDInsight-kluster.

Mer information finns i artikeln: [OS-korrigering för HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Uppgifter efter migrering

1. **Åtgärda program** – iterativt gör nödvändiga ändringar till jobb, processer och skript.
2. **Utföra tester** – iterativt kör funktionella och prestanda tester.
3. **Optimera** – åtgärda några prestandaproblem baserat på resultaten av ovan och sedan testa för att bekräfta prestandaförbättringarna.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
