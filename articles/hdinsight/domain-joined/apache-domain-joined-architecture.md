---
title: "Arkitektur för domänanslutna Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du planerar för domänansluten HDInsight."
services: hdinsight
documentationcenter: 
author: bhanupr
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2017
ms.author: bprakash
ms.openlocfilehash: 5285199d22528ed6b9fa3b7dbc85e382e7b28569
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planera Azure-domänanslutna Hadoop-kluster i HDInsight

Standard HDInsight-klustret är ett kluster med en användare. Det passar för de flesta företag som har små applikationsteam som bygger stordataarbetsbelastningar. Många företag igång går mot en modell där klustren hanteras av IT-team och flera program team resursen kluster som Hadoop kommit över popularitet. Det gör att funktioner som kluster med flera användare är en av de mest efterfrågade funktionerna i Azure HDInsight.

I stället för att skapa flera användare autentisering och auktorisering, HDInsight förlitar sig på den mest populära identitetsleverantören--Active Directory (AD). Kraftfulla säkerhetsfunktionerna i AD kan användas för att hantera flera användare autentisering i HDInsight. Genom att integrera HDInsight med AD kan du kommunicera med kluster med hjälp av AD-autentiseringsuppgifter. De virtuella datorerna i HDInsight är domänansluten till AD och som är hur HDInsight mappar en AD-användare till en lokal Hadoop-användare, så alla tjänster som körs på HDInsight (Ambari, Hive server Ranger Spark thrift-servern och andra) fungerar sömlöst för den autentiserade användaren. Administratörer kan skapa starka auktoriseringsprinciper som använder Apache Ranger för att tillhandahålla rollbaserad åtkomstkontroll för resurser i HDInsight.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrera HDInsight med Active Directory

När du integrerar HDInsight med Active Directory är HDInsight-klusternoder domänansluten till en AD-domän. Kerberos-säkerheten är konfigurerad för Hadoop-komponenter på klustret. För varje Hadoop-komponenter skapas ett huvudnamn för tjänsten på Active Directory. En motsvarande dator huvudnamn skapas också för varje dator som är ansluten till domänen. Dessa tjänstens huvudnamn och datorn säkerhetsobjekt kan göra din Active Directory. Därför krävs det att tillhandahålla en organisationsenhet (OU) i Active Directory, där dessa säkerhetsobjekt är placerade. 

Sammanfattningsvis, måste du ställa in en miljö med:

- En Active Directory-domänkontrollant med LDAPS konfigurerats.
- Anslutningen från Hdinsights virtuella nätverket till Active Directory-domänkontrollant.
- En organisationsenhet som skapats på Active Directory.
- Ett konto som har behörighet att:

    - Skapa tjänstens huvudnamn i Organisationsenheten.
    - Ansluta datorer till domänen och skapa dator säkerhetsobjekt i Organisationsenheten.

Följande skärmbild visar en Organisationsenhet som skapats i contoso.com. Några av tjänstens huvudnamn och datorn säkerhetsobjekt som visas i skärmbilden samt.

![Domän ansluten HDInsight-kluster ou](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="two-ways-of-bringing-your-own-active-directory-domain-controllers"></a>Två sätt för att göra egna Active Directory-domänkontrollanter

Det finns två sätt som du kan ta Active Directory-domänkontrollanter för att skapa domänanslutna HDInsight-kluster. 

- **Azure Active Directory Domain Services**: den här tjänsten tillhandahåller en hanterad Active Directory-domän, som är helt kompatibel med Windows Server Active Directory. Microsoft hand tar om hantering, uppdatering och övervakning av AD-domänen. Du kan distribuera klustret utan att bekymra dig om att hantera domänkontrollanter. Användare, grupper och lösenord synkroniseras från din Azure Active Directory, så att användarna kan logga in till klustret med sina företagsuppgifter. Mer information finns i [konfigurera domänanslutna HDInsight-kluster med Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

- **Active Directory på Azure IaaS-VM**: I det här alternativet kan du distribuera och hantera din egen Windows Server Active Directory-domän på Azure IaaS-VM. Mer information finns i [konfigurera domänanslutna sandbox domänmiljö](./apache-domain-joined-configure.md).

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett domänanslutet HDInsight-kluster kan du läsa [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md).
* Information om hantering av domänanslutna HDInsight-kluster finns i [Hantera domänanslutna HDInsight-kluster](apache-domain-joined-manage.md).
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* Om du vill köra Hive-frågor med hjälp av SSH på domänanslutna HDInsight-kluster, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
