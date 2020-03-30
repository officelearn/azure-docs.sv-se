---
title: 'Säkerhet: Migrera lokala Apache Hadoop till Azure HDInsight'
description: Lär dig om bästa metoder för säkerhet och DevOps för att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 4ceefcbbbb53e3ae13f8ced930ae8417fb00965f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974408"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – bästa praxis för säkerhet och DevOps

Den här artikeln innehåller rekommendationer för säkerhet och DevOps i Azure HDInsight-system. Det är en del av en serie som innehåller metodtips för att hjälpa till med att migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Skydda och styra kluster med Enterprise Security Package

Enterprise Security Package (ESP) stöder Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll. Med esp-alternativet valt ansluts HDInsight-klustret till Active Directory-domänen och företagsadministratören kan konfigurera rollbaserad åtkomstkontroll (RBAC) för Apache Hive-säkerhet med hjälp av Apache Ranger. Administratören kan också granska dataåtkomsten för anställda och eventuella ändringar som gjorts för att komma åt kontrollprinciper.

ESP är tillgängligt på följande klustertyper: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka och Interactive Query (Hive LLAP).

Så här distribuerar du det domänanslutna HDInsight-klustret:

- Distribuera Azure Active Directory (AAD) genom att skicka domännamnet.
- Distribuera Azure Active Directory Domain Services (AAD DS).
- Skapa det virtuella nätverk och det nödvändiga virtuella nätverket och undernätet.
- Distribuera en virtuell dator i det virtuella nätverket för att hantera AAD DS.
- Gå med i den virtuella datorn till domänen.
- Installera AD- och DNS-verktyg.
- Låt AAD DS-administratören skapa en organisationsenhet (OU).
- Aktivera LDAPS för AAD DS.
- Skapa ett tjänstkonto i Azure Active Directory med delegerad läs & skrivadministratörsbehörighet till organisationsenheten, så att det kan. Det här tjänstkontot kan sedan ansluta datorer till domänen och placera datorns huvudnamn inom organisationsenheten. Det kan också skapa tjänsthuvudnamn inom organisationsenheten som du anger när klustret skapas.

    > [!Note]
    > Tjänstkontot behöver inte vara AD-domänadministratörskontot.

- Distribuera HDInsight ESP-kluster genom att ange följande parametrar:

    |Parameter |Beskrivning |
    |---|---|
    |Domännamn|Domännamnet som är associerat med Azure AD DS.|
    |Domännamn för domän|Tjänstkontot i den DC-hanterade Azure AD DS-domänen som `hdiadmin@contoso.onmicrosoft.com`du skapade i föregående avsnitt, till exempel: . Den här domänanvändaren kommer att vara administratör för det här HDInsight-klustret.|
    |Domänlösenord|Lösenordet för tjänstkontot.|
    |Organisationsenhet|Det unika namnet på organisationsenheten som du vill använda med HDInsight-klustret, till exempel: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Om den här organisationsenheten inte finns försöker HDInsight-klustret skapa organisationsenheten med hjälp av behörigheterna för tjänstkontot.|
    |LDAPS-URL|till exempel `ldaps://contoso.onmicrosoft.com:636`.|
    |Användargrupp för Åtkomst|De säkerhetsgrupper vars användare du vill synkronisera `HiveUsers`med klustret, till exempel: . Om du vill ange flera användargrupper avgränsar du dem efter semikolon ';'. Gruppen/grupperna måste finnas i katalogen innan ESP-klustret skapas.|

Mer information finns i följande artiklar:

- [En introduktion till Apache Hadoop-säkerhet med domänanslutna HDInsight-kluster](../domain-joined/hdinsight-security-overview.md)
- [Planera Azure-domänanstjada Apache Hadoop-kluster i HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurera ett domänanslutet HDInsight-kluster med hjälp av Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synkronisera Azure Active Directory-användare med ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurera Apache Hive-principer i domänanslutna HDInsight](../domain-joined/apache-domain-joined-run-hive.md)
- [Kör Apache Oozie i domänanslutna HDInsight Hadoop-kluster](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementera på företagssäkerhet

på på företagssäkerhet kan uppnås med hjälp av följande kontroller:

**Privat och skyddad datapipeline (säkerhetssäkerhet på perimeternivå)**
    - Perimeternivåsäkerhet kan uppnås via Azure Virtual Networks, Network Security Groups och Gateway service.

**Autentisering och auktorisering för dataåtkomst**
    - Skapa domänanslutna HDInsight-kluster med Azure Active Directory Domain Services. (Enterprise Security Package).
    - Använd Ambari för att ge rollbaserad åtkomst till klusterresurser för AD-användare.
    - Använd Apache Ranger för att ställa in åtkomstkontrollprinciper för Hive vid tabell-/kolumn-/radnivå.
    - SSH-åtkomst till klustret kan begränsas endast till administratören.

**Granskning**
    - Visa och rapportera all åtkomst till HDInsight-klusterresurser och -data.
    - Visa och rapportera alla ändringar i åtkomstkontrollprinciperna.

**Kryptering**
    - Transparent server-side-kryptering med hjälp av Microsoft-hanterade nycklar eller kundhanterade nycklar.
    - I Transitkryptering med kryptering på klientsidan, https och TLS.

Mer information finns i följande artiklar:

- [Översikt över Virtuella Azure-nätverk](../../virtual-network/virtual-networks-overview.md)
- [Översikt över Azure Network Security Groups](../../virtual-network/security-overview.md)
- [Azure Virtual Network-peering](../../virtual-network/virtual-network-peering-overview.md)
- [Säkerhetsguide för Azure Storage](../../storage/blobs/security-recommendations.md)
- [Kryptering av Azure Storage Service i vila](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Använd övervakning & aviseringar

Mer information finns i artikeln:

[Översikt över Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Uppgradera kluster

Uppgradera regelbundet till den senaste HDInsight-versionen för att dra nytta av de senaste funktionerna. Följande steg kan användas för att uppgradera klustret till den senaste versionen:

1. Skapa ett nytt TEST HDInsight-kluster med den senaste tillgängliga HDInsight-versionen.
1. Testa på det nya klustret för att se till att jobben och arbetsbelastningarna fungerar som förväntat.
1. Ändra jobb eller program eller arbetsbelastningar efter behov.
1. Säkerhetskopiera alla tillfälliga data som lagras lokalt på klusternoderna.
1. Ta bort det befintliga klustret.
1. Skapa ett kluster av den senaste HDInsight-versionen i samma virtuella nätverksundernät med samma standarddata och metalagringslager som det tidigare klustret.
1. Importera alla tillfälliga data som säkerhetskopierats.
1. Starta jobb/fortsätt bearbeta med det nya klustret.

Mer information finns i artikeln: [Uppgradera HDInsight-klustret till en ny version](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Operativsystem för korrigeringskluster

Som en hanterad Hadoop-tjänst tar HDInsight hand om att korrigera operativsystemet för de virtuella datorer som används av HDInsight-kluster.

Mer information finns i artikeln: [OS-korrigering för HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Efter migreringen

1. **Åtgärda program** - Iterativt göra nödvändiga ändringar i jobb, processer och skript.
2. **Utför tester** - Iterativt köra funktionella och prestandatester.
3. **Optimera** - Åtgärda eventuella prestandaproblem baserat på ovanstående testresultat och testa sedan för att bekräfta prestandaförbättringarna.

## <a name="next-steps"></a>Nästa steg

Läs mer om [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
