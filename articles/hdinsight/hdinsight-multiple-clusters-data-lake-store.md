---
title: Flera HDInsight-kluster & ett Azure Data Lake Storage-konto
description: Lär dig hur du använder mer än ett HDInsight-kluster med ett enda DataSjölagringskonto
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: cc67acca11e7e0f24dc0597dcd19672a38a7bf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495758"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Använda flera HDInsight-kluster med ett Azure Data Lake Storage-konto

Från och med HDInsight version 3.5 kan du skapa HDInsight-kluster med Azure Data Lake Storage-konton som standardfilsystem.
Data Lake Storage stöder obegränsad lagring som gör den idealisk inte bara för att vara värd för stora mängder data; men också för att vara värd för flera HDInsight-kluster som delar ett enda datasjölagringskonto. Instruktioner om hur du skapar ett HDInsight-kluster med DataSjölagring som lagring finns [i Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Den här artikeln innehåller rekommendationer till datasjölagringsadministratören för att konfigurera ett enda och delat datasjölagringskonto som kan användas i flera **aktiva** HDInsight-kluster. Dessa rekommendationer gäller för värd för flera säkra och icke-säkra Apache Hadoop-kluster på ett delat Data Lake Storage-konto.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>ACL:er för datasjölagringsfil och mappnivå

Resten av den här artikeln förutsätter att du har goda kunskaper om ACL:er på fil- och mappnivå i Azure Data Lake Storage, som beskrivs i detalj vid [åtkomstkontrollen i Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Inställningar för lagring av datasjölagring för flera HDInsight-kluster

Låt oss ta en mapphierarki på två nivåer för att förklara rekommendationerna för att använda flera HDInsight-kluster med ett Data Lake Storage-konto. Tänk på att du har ett Data Lake Storage-konto med mappstrukturen **/kluster/ekonomi**. Med den här strukturen kan alla kluster som krävs av finance-organisationen använda /clusters/finance som lagringsplats. I framtiden, om en annan organisation, säger Marknadsföring, vill skapa HDInsight-kluster med samma Data Lake Storage-konto, kan de skapa /kluster/marknadsföring. För nu, låt oss bara använda **/ kluster / ekonomi**.

Om du vill att den här mappstrukturen ska kunna användas effektivt av HDInsight-kluster måste datasjölagringsadministratören tilldela lämpliga behörigheter enligt beskrivningen i tabellen. Behörigheterna som visas i tabellen motsvarar Åtkomst-ACL:er och inte standard-ACL:er.

|Mapp  |Behörigheter  |Ägande användare  |Ägande grupp  | Namngiven användare | Namngivna användarbehörigheter | Namngiven grupp | Namngivna gruppbehörigheter |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Tjänstens huvudnamn |--x  |FINGRP (FINGRP)   |r-x         |
|/kluster | rwxr-x--x |admin |admin |Tjänstens huvudnamn |--x  |FINGRP (FINGRP) |r-x         |
|/kluster/ekonomi | rwxr-x--t |admin |FINGRP (FINGRP)  |Tjänstens huvudnamn |Rwx  |-  |-     |

I tabellen,

- **administratören** är skaparen och administratören av datasjölagringskontot.
- **Tjänsthuvudnamn** är Azure Active Directory (AAD) tjänsthuvudnamn som är associerade med kontot.
- **FINGRP** är en användargrupp som skapats i AAD och som innehåller användare från organisationen Ekonomi.

Instruktioner om hur du skapar ett AAD-program (som också skapar ett tjänsthuvudnamn) finns i [Skapa ett AAD-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Instruktioner om hur du skapar en användargrupp i AAD finns [i Hantera grupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Några viktiga punkter att tänka på.

- Mappstrukturen på två nivåer (**/clusters/finance/**) måste skapas och etableras med lämpliga behörigheter av datasjölagringsadministratören **innan** lagringskontot för kluster. Den här strukturen skapas inte automatiskt när kluster skapas.
- I exemplet ovan rekommenderas att du ställer in den ägande gruppen **/kluster/ekonomi** som **FINGRP** och tillåter **rx-åtkomst** till FINGRP till hela mapphierarkin från roten. Detta säkerställer att medlemmarna i FINGRP kan navigera i mappstrukturen från roten.
- I det fall då olika AAD-tjänsthuvudnamn kan skapa kluster under **/clusters/finance,** säkerställer den klibbiga biten (när den är inställd på **ekonomimappen)** att mappar som skapats av ett tjänsthuvudnamn inte kan tas bort av den andra.
- När mappstrukturen och behörigheterna är på plats skapar HDInsight-klusterprocessen en klusterspecifik lagringsplats under **/clusters/finance/**. Lagringen för ett kluster med namnet fincluster01 kan till exempel vara **/clusters/finance/fincluster01**. Ägarskapet och behörigheterna för mapparna som skapas av HDInsight-klustret visas i tabellen här.

    |Mapp  |Behörigheter  |Ägande användare  |Ägande grupp  | Namngiven användare | Namngivna användarbehörigheter | Namngiven grupp | Namngivna gruppbehörigheter |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/kluster/finanace/ fincluster01 | rwxr-x---  |Tjänstens huvudnamn |FINGRP (FINGRP)  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Rekommendationer för jobbinmatning och utdata

Vi rekommenderar att indata till ett jobb och utdata från ett jobb lagras i en mapp utanför **/kluster**. Detta säkerställer att även om den klusterspecifika mappen tas bort för att frigöra visst lagringsutrymme, är jobbindata och utdata fortfarande tillgängliga för framtida användning. I så fall bör du se till att mapphierarkin för lagring av jobbindata och utdata ger lämplig åtkomstnivå för huvudmannen för Tjänsten.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Begränsa kluster som delar ett enda lagringskonto

Gränsen för antalet kluster som kan dela ett enda Data Lake Storage-konto beror på vilken arbetsbelastning som körs på dessa kluster. Om du har för många kluster eller mycket tunga arbetsbelastningar i kluster som delar ett lagringskonto kan lagringskontot gå in för att begränsa sig.

## <a name="support-for-default-acls"></a>Stöd för standard-ACL:er

När du skapar ett tjänsthuvudnamn med namngiven användaråtkomst (som visas i tabellen ovan) rekommenderar vi att **du inte** lägger till den namngivna användaren med en standard-åtkomstkontrollista. Etablering av namngiven användaråtkomst med standard-ACL:er resulterar i tilldelning av 770 behörigheter för att äga användare, äga-grupp och andra. Även om det här standardvärdet på 770 inte tar bort behörigheter från att äga användare (7) eller äga-grupp (7), tar det bort alla behörigheter för andra (0). Detta resulterar i ett känt problem med ett visst användningsfall som beskrivs i detalj i avsnittet [Kända problem och lösningar.](#known-issues-and-workarounds)

## <a name="known-issues-and-workarounds"></a>Kända problem och lösningar

I det här avsnittet visas kända problem med att använda HDInsight med Data Lake Storage och deras lösningar.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Offentligt synliga lokaliserade Apache Hadoop YARN-resurser

När ett nytt Azure Data Lake Storage-konto skapas etableras rotkatalogen automatiskt med Access-ACL-behörighetsbitar inställda på 770. Rotmappens ägandeanvändare är inställd på den användare som skapade kontot (datasjölagringsadministratören) och den ägande gruppen är inställd på den primära gruppen för användaren som skapade kontot. Ingen åtkomst ges för "andra".

Dessa inställningar är kända för att påverka en specifik HDInsight användningsfall fångas i [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Jobbinlämningar kan misslyckas med ett felmeddelande som liknar detta:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Som anges i YARN JIRA länkade tidigare, medan lokalisera offentliga resurser, validerar localizer att alla begärda resurser verkligen är offentliga genom att kontrollera deras behörigheter på fjärrfilsystemet. Alla LocalResource som inte passar det villkoret avvisas för lokalisering. Kontrollen efter behörigheter, inkluderar läsåtkomst till filen för "andra". Det här scenariot fungerar inte direkt när du är värd för HDInsight-kluster i Azure Data Lake, eftersom Azure Data Lake nekar all åtkomst till "andra" på rotmappsnivå.

#### <a name="workaround"></a>Lösning

Ange läs-körbehörigheter för **andra** via hierarkin, till exempel på **/**, **/kluster** och **/kluster/ekonomi** som visas i tabellen ovan.

## <a name="see-also"></a>Se även

- [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-storage-gen2.md)
