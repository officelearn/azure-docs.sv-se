---
title: Använda flera HDInsight-kluster med ett Azure Data Lake Store-konto - Azure | Microsoft Docs
description: Lär dig hur du använder fler än ett HDInsight-kluster med ett enda Data Lake Store-konto
keywords: hdinsight lagring, hdfs, strukturerade data, Ostrukturerade data, datasjölager
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 48e5a8d270701c43276e1d248d8ea4dc748d15b2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Använda flera HDInsight-kluster med ett Azure Data Lake Store-konto

Från och med HDInsight version 3.5 kan skapa du HDInsight-kluster med Azure Data Lake Store-konton som standard filsystemet.
Data Lake Store stöder obegränsad lagring som gör det perfekta inte värd för stora mängder data. men även som värd för flera HDInsight-kluster som delar ett enda Data Lake Store-konto. Anvisningar om hur du skapar ett HDInsight-kluster med Data Lake Store som lagring finns [skapa HDInsight-kluster med Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Den här artikeln innehåller rekommendationer för Data Lake lagra administratör för att konfigurera en enskild och delad lagring Datasjökontot som kan användas på flera **active** HDInsight-kluster. De här rekommendationerna gäller för värd för flera säkra som inte är säkra Hadoop-kluster på ett delade Data Lake store-konto.


## <a name="data-lake-store-file-and-folder-level-acls"></a>Data Lake Store fil- och nivå ACL: er

Resten av den här artikeln förutsätter att du har goda kunskaper om fil- och nivå ACL: er i Azure Data Lake Store, som beskrivs i detalj i [åtkomstkontroll i Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Data Lake Store-inställningar för flera HDInsight-kluster
Låt oss ta ett mapphierarkin med två nivåer som förklarar rekommendationer för att använda fler HDInsight-kluster med ett Data Lake Store-konto. Överväg att du har ett Data Lake Store-konto med mappstrukturen **/kluster/ekonomi**. Med den här strukturen kan alla kluster som krävs av ekonomi organisationen använda /clusters/finance som lagringsplats. I framtida om en annan organisation, säg marknadsföring, om du vill skapa HDInsight-kluster med samma Data Lake Store-konto, kan de skapar/kluster/marknadsföring. Nu ska vi använda **/kluster/ekonomi**.

Om du vill aktivera den här mappstrukturen att användas av HDInsight-kluster måste Data Lake Store-administratör tilldela rätt behörighet, enligt beskrivningen i tabellen. De behörigheter som visas i tabellen motsvarar åtkomst ACL: er och inte standard-ACL: er. 


|Mapp  |Behörigheter  |Ägande användare  |Ägande grupp  | Namngiven användare | Namngiven användarbehörighet | Namngiven grupp | Namngivna gruppbehörigheter |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |administratör |administratör  |Tjänstens huvudnamn |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |administratör |administratör |Tjänstens huvudnamn |--x  |FINGRP |r-x         |
|/ kluster/ekonomi | rwxr-x--t |administratör |FINGRP  |Tjänstens huvudnamn |rwx  |-  |-     |

I tabellen

- **Admin** är skapare och administratör i Data Lake Store-konto.
- **Tjänstens huvudnamn** är associerat med kontot Azure Active Directory (AAD) tjänstens huvudnamn.
- **FINGRP** är en användargrupp som skapats i AAD som innehåller användare från ekonomi-organisation.

Anvisningar om hur du skapar ett AAD-program (som skapar också ett huvudnamn för tjänsten) finns [skapa ett AAD-program](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Anvisningar om hur du skapar en användargrupp i AAD finns [hantera grupper i Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Några viktiga saker att tänka på.

- Två nivå mappstrukturen (**/kluster/ekonomi/**) måste skapas och etablerats med rätt behörighet av Data Lake Store-admin **innan** använder lagringskontot för kluster. Den här strukturen skapas inte automatiskt när du skapar kluster.
- Exemplet ovan rekommenderar inställningen gruppen ägande av **/kluster/ekonomi** som **FINGRP** och gör det möjligt att **r-x** åtkomst till FINGRP till hela hierarkin för att börja från roten. Detta säkerställer att medlemmarna i FINGRP kan navigera mappstrukturen från roten.
- I fallet när olika AAD tjänstens huvudnamn kan skapa kluster under **/kluster/ekonomi**, Fäst-bitars (på den **ekonomi** mapp) säkerställer att mappar som skapas av en tjänstens huvudnamn inte kan tas bort av den andra.
- När mappstrukturen och behörigheter är på plats, HDInsight klusterskapandeprocessen skapar en klusterspecifika lagring loaction under **/kluster/ekonomi/**. Lagring för ett kluster med namnet fincluster01 kan till exempel vara **/clusters/finance/fincluster01**. Ägarskap och behörigheter för mappar som skapas av HDInsight-klustret visas i den här tabellen.

    |Mapp  |Behörigheter  |Ägande användare  |Ägande grupp  | Namngiven användare | Namngiven användarbehörighet | Namngiven grupp | Namngivna gruppbehörigheter |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Tjänstens huvudnamn |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Rekommendationer för jobbet indata och utdata

Vi rekommenderar att mata in data till ett jobb och utdata från ett jobb lagras i en mapp utanför **/kluster**. Detta säkerställer att även om mappen klusterspecifika tas bort om du vill frigöra lagringsutrymme jobbet indata och utdata är fortfarande tillgänglig för framtida användning. I så fall kan du kontrollera att mapphierarkin för lagring av jobbet indata och utdata tillåter rätt åtkomstnivå för tjänstens huvudnamn.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Begränsa på kluster som delar ett enda storage-konto

Gränsen för antalet kluster som kan dela ett enda Data Lake Store-konto beror på arbetsbelastningen som körs på dessa kluster. Med för många kluster eller väldigt tunga arbetsbelastningar på kluster som delar ett lagringskonto kan orsaka det lagring konto ingång-/ utgång till hämta begränsas.

## <a name="support-for-default-acls"></a>Stöd för standard-ACL: er

När du skapar ett huvudnamn för tjänsten med namnet användaråtkomst (som visas i tabellen ovan), rekommenderar vi **inte** att lägga till den namngivna användaren med en standard-ACL. Etablering med namnet användaren åtkomst med standard-ACL: er resultat tilldelningen av 770 behörigheter för ägande användare och äger gruppen. När det här standardvärdet 770 inte tar bort behörigheter från det ägande användare (7) eller äger-grupp (7), men det tar bort alla behörigheter för andra (0). Detta resulterar i ett känt problem med en viss användningsfall som diskuteras i detalj i den [kända problem och lösningar](#known-issues-and-workarounds) avsnitt.

## <a name="known-issues-and-workarounds"></a>Kända problem och lösningar

Det här avsnittet innehåller kända problem för att använda HDInsight med Data Lake Store och sina lösningar.

### <a name="publicly-visible-localized-yarn-resources"></a>Synligt offentligt lokaliserade YARN-resurser

När ett nytt Azure Data Lake store-konto har skapats kan etableras automatiskt rotkatalogen med ACL Access behörighet bitarna till 770. Rotmappens äger användaren är inställd på att användaren som skapade kontot (Data Lake Store-admin) och gruppen ägande är inställd på primär grupp för användaren som skapade kontot. Ingen åtkomst har angetts för ”övriga”.

De här inställningarna är känt att påverkar en viss HDInsight användningsfall hämtas [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Jobbet bidrag kan misslyckas med felmeddelandet ut ungefär så här:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Enligt informationen i YARN-JIRA länkade tidigare under lokaliseringen offentliga resurser validerar i localizer att de begärda resurserna verkligen offentliga genom att kontrollera deras behörigheter för fjärråtkomst-filsystemet. Alla LocalResource som inte uppfyller villkoret avvisas för lokalisering. Kontrollera behörigheter, inkluderar läsbehörighet till filen för ”andra”. Det här scenariot fungerar inte out box när värd HDInsight-kluster i Azure Data Lake eftersom Azure Data Lake nekar åtkomst till ”andra” på rotnivå för mappen.

#### <a name="workaround"></a>Lösning
Ange Läs-körningsbehörighet **andra** via hierarkin, till exempel på **/**, **/kluster** och **/kluster/ekonomi** som visas i tabellen ovan.

## <a name="see-also"></a>Se också

* [Skapa ett HDInsight-kluster med Data Lake Store som lagring](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


