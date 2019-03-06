---
title: Använda flera HDInsight-kluster med ett Azure Data Lake Storage-konto – Azure
description: Lär dig hur du använder mer än ett HDInsight-kluster med ett enda Data Lake Storage-konto
keywords: hdinsight storage,hdfs,structured data,unstructured data, data lake store
services: hdinsight,storage
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 0d57c65c93ffcd6c4c5249a1e5effeb457ed1736
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440904"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Använda flera HDInsight-kluster med ett Azure Data Lake Storage-konto

Från och med HDInsight version 3.5 kan skapa du HDInsight-kluster med Azure Data Lake Storage-konton som standard-filsystem.
Data Lake Storage har stöd för obegränsad lagring som gör det perfekt inte bara som värd för stora mängder data. men även som värd för flera HDInsight-kluster som delar ett enda Data Lake Storage-konto. Anvisningar för hur du skapar ett HDInsight-kluster med Data Lake Storage som lagring finns i [snabbstarten: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Den här artikeln innehåller rekommendationer till Data Lake Storage-administratören för att skapa en enkel och delade Data Lake Storage-konto som kan användas på flera **active** HDInsight-kluster. De här rekommendationerna gäller för som är värd för flera säkra såväl som icke-säker Apache Hadoop-kluster i ett delat Data Lake Storage-konto.


## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage fil- och nivå ACL: er

Resten av den här artikeln förutsätter att du har goda kunskaper om fil- och nivå ACL: er i Azure Data Lake Storage, som beskrivs i detalj i [åtkomstkontroll i Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Data Lake lagringsinställningar för flera HDInsight-kluster
Låt oss ta en mapphierarkin med två nivåer som förklarar rekommendationer för att använda flera HDInsight-kluster med ett Data Lake Storage-konto. Överväg att du har ett Data Lake Storage-konto med mappstrukturen **/kluster/ekonomi**. Med den här strukturen använda alla kluster som krävs av ekonomi organisationen /clusters/finance som lagringsplats. I framtiden, om en annan organisation, säger marknadsföring, om du vill skapa HDInsight-kluster med samma Data Lake Storage-konto, kan de Skapa/kluster /-marknadsföring. Nu ska vi bara använda **/kluster/ekonomi**.

Om du vill aktivera den här mappstrukturen som effektivt ska användas av HDInsight-kluster, måste lagring av Data Lake-administratören tilldela rätt behörighet, enligt beskrivningen i tabellen. De behörigheter som visas i tabellen motsvarar åtkomst-ACL: er och inte standard-ACL: er. 


|Mapp  |Behörigheter  |Ägande användare  |Ägande grupp  | Namngiven användare | Behörigheter för namngiven användare | Namngiven grupp | Behörigheter för namngiven grupp |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Tjänstens huvudnamn |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |admin |admin |Tjänstens huvudnamn |--x  |FINGRP |r-x         |
|/ kluster/ekonomi | rwxr-x--t |admin |FINGRP  |Tjänstens huvudnamn |rwx  |-  |-     |

I tabellen

- **administratören** är skapare och administratör för Data Lake Storage-konto.
- **Tjänstens huvudnamn** är Azure Active Directory (AAD) tjänstens huvudnamn som är associerade med kontot.
- **FINGRP** är en användargrupp som skapats i AAD som innehåller användare från finans-organisation.

Instruktioner om hur du skapar ett AAD-program (som även skapar ett huvudnamn för tjänsten), finns i [skapa ett AAD-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Anvisningar om hur du skapar en användargrupp i AAD finns i [hantera grupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Vissa viktiga saker att tänka på.

- Två nivå mappstrukturen (**/kluster/ekonomi/**) måste skapas och etableras med rätt behörighet av Data Lake Storage administratören **innan** använder storage-konto för kluster. Den här strukturen skapas inte automatiskt när du skapar kluster.
- I exemplet ovan rekommenderar att den ägande gruppen för **/kluster/ekonomi** som **FINGRP** och gör det möjligt att **r-x** åtkomst till FINGRP till hela mapphierarkin startar från roten. Detta säkerställer att medlemmarna i FINGRP kan navigera mappstrukturen från roten.
- I fall när olika AAD-tjänsthuvudnamn kan skapa kluster under **/kluster/ekonomi**, sticky-bit (när inställda på den **ekonomi** mapp) så att mappar skapas av en tjänstens huvudnamn Det går inte att tas bort av den andra.
- När mappstrukturen och behörigheter är på plats kan HDInsight-klusterskapningsprocessen skapar en klusterspecifika lagringsplats under **/kluster/ekonomi/**. Lagring för ett kluster med namnet fincluster01 kan till exempel vara **/clusters/finance/fincluster01**. Äganderätt och behörigheter för mappar som skapas av HDInsight-klustret visas i den här tabellen.

    |Mapp  |Behörigheter  |Ägande användare  |Ägande grupp  | Namngiven användare | Behörigheter för namngiven användare | Namngiven grupp | Behörigheter för namngiven grupp |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Tjänstens huvudnamn |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Rekommendationer för jobbet inkommande och utgående data

Vi rekommenderar att indata till ett jobb och utdata från ett jobb lagras i en mapp utanför **/kluster**. Detta garanterar att även om mappen klusterspecifika tas bort om du vill frigöra lagringsutrymme, jobbet indata och utdata är fortfarande tillgänglig för framtida bruk. I sådana fall måste du kontrollera att mapphierarkin för att lagra jobbet indata och utdata tillåter rätt åtkomstnivå för tjänstens huvudnamn.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Begränsa på kluster som delar ett enda lagringskonto

Gränsen för antalet kluster som kan dela ett enda konto för Data Lake Storage beror på arbetsbelastningen som körs på dessa kluster. För många kluster eller mycket stora arbetsbelastningar kan i klustren som delar ett lagringskonto orsaka den storage-konto ingående/utgående trafik till begränsas.

## <a name="support-for-default-acls"></a>Stöd för standard-ACL: er

När du skapar ett huvudnamn för tjänsten med namnet användaråtkomst (som visas i tabellen ovan), rekommenderar vi **inte** att lägga till den namngivna användaren med en standard-ACL. Etablering med namnet användare åtkomst med hjälp av standard-ACL: er resultat i tilldelningen av 770 behörigheter för ägande användare, ägande grupp och andra. Även om det här standardvärdet 770 inte tar bort behörigheter från ägande användare (7) eller ägande grupp (7), men det tar bort alla behörigheter för andra (0). Detta resulterar i ett känt problem med en viss användningsfall som beskrivs i detalj i de [kända problem och lösningar](#known-issues-and-workarounds) avsnittet.

## <a name="known-issues-and-workarounds"></a>Kända problem och lösningar

Det här avsnittet innehåller kända problem för användning av HDInsight med Data Lake Storage och sina lösningar.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Offentligt lokaliserade Apache Hadoop YARN-resurser

När ett nytt Azure Data Lake Storage-konto skapas, etableras automatiskt rotkatalogen med åtkomst-ACL behörighet bitarna till 770. Rotmappens ägande användaren är inställd på att användaren som skapade kontot (Data Lake Storage-administratör) och den ägande gruppen är inställt på den primära gruppen för den användare som skapade kontot. Ingen åtkomst har angetts för ”andra”.

De här inställningarna är kända för att påverka en specifik HDInsight användningsfall som hämtats i [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Jobbet bidrag kan misslyckas med ett felmeddelande som liknar detta:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Enligt informationen i YARN-JIRA länkad tidigare under lokaliseringen offentliga resurser, verifierar localizer att de begärda resurserna är verkligen offentliga genom att kontrollera deras behörigheter på fjärr-filsystemet. Alla LocalResource som inte passar villkoret avvisas för lokalisering. Kontroll av behörigheter, innehåller läsbehörighet till filen för ”andra”. Det här scenariot fungerar inte out-of the box när HDInsight-kluster i Azure Data Lake, eftersom Azure Data Lake nekar all åtkomst till ”andra” på rotnivå för mappen.

#### <a name="workaround"></a>Lösning
Ange Läs-körbehörighet för **andra** via hierarkin, till exempel på **/**, **/kluster** och   **/kluster/ekonomi** som visas i tabellen ovan.

## <a name="see-also"></a>Se också

* [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-storage-gen2.md)
