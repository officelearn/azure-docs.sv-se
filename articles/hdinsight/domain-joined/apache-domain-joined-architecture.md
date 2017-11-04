---
title: "Arkitektur för domänanslutna Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du planerar för domänansluten HDInsight."
services: hdinsight
documentationcenter: 
author: saurinsh
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
ms.date: 09/21/2017
ms.author: saurinsh
ms.openlocfilehash: 9deb5e4dbd925c4fdc523d8d21afbcfbf85947b8
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planera Azure-domänanslutna Hadoop-kluster i HDInsight

Traditionellt är Hadoop ett kluster för en användare. Det passar för de flesta företag som har små applikationsteam som bygger stordataarbetsbelastningar. Hadoop ökar i popularitet och många företag går mot en modell där kluster hanteras av IT-team, och flera applikationsteam delar kluster. Det gör att funktioner som kluster med flera användare är en av de mest efterfrågade funktionerna i Azure HDInsight.

I stället för att skapa flera användare autentisering och auktorisering, HDInsight förlitar sig på den mest populära identitetsleverantören--Active Directory (AD). Kraftfulla säkerhetsfunktionerna i AD kan användas för att hantera flera användare auktorisering i HDInsight. Genom att integrera HDInsight med AD kan du kommunicera med kluster med hjälp av AD-autentiseringsuppgifter. HDInsight mappar en AD-användare till en lokal Hadoop-användare, vilket betyder att alla tjänster som körs på HDInsight (Ambari, Hive server Ranger Spark thrift-servern och andra) fungerar sömlöst för den autentiserade användaren.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrera HDInsight med Active Directory

När du integrerar HDInsight med Active Directory är HDInsight-klusternoder domänansluten till en AD-domän. HDInsight skapar tjänstens huvudnamn för Hadoop-tjänster som körs på klustret och placerar dem i en angiven organisationsenhet (OU) i domänen. HDInsight skapas också omvänd DNS-avbildningar i domänen för IP-adresserna för de noder som är anslutna till domänen.

Det finns två distributionsalternativ för Active Directory:
* **[Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md):** tjänsten ger en hanterad Active Directory-domän som är helt kompatibel med Windows Server Active Directory. Microsoft hand tar om hantering, uppdatering och övervakning av AD-domänen. Du kan distribuera klustret utan att bekymra dig om att hantera domänkontrollanter. Användare, grupper och lösenord synkroniseras från din Azure Active Directory, så att användarna kan logga in till klustret med sina företagsuppgifter.

* **Windows Server Active Directory-domän i Azure IaaS-VM:** i det här alternativet kan du distribuera och hantera din egen Windows Server Active Directory-domän på Azure IaaS-VM. 

Det finns flera olika arkitekturer som du kan använda för att uppnå den här konfigurationen. Du kan välja från följande arkitekturer.


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>HDInsight integrerad med en Azure AD Domain Services hanteras AD-domänen
Du kan distribuera en [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS) hanterade domän. Azure AD DS tillhandahåller en hanterad AD-domän i Azure, vilket hanteras, uppdateras och övervakas av Microsoft. Den skapar två domänkontrollanter för hög tillgänglighet och inkluderar DNS-tjänster. Sedan kan du integrera HDInsight-klustret med det här hanterade domän. Med det här distributionsalternativet behöver du inte bekymra dig om att hantera, korrigering, uppdatering och övervakning av domänkontrollanter.

![Domänansluten HDInsight-klustertopologi](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Krav för att integrera med Azure AD Domain Services:

* [Etablera en Azure AD Domain Services-hanterad domän](../../active-directory-domain-services/active-directory-ds-getting-started.md).
* Skapa en [organisationsenhet](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)inom vilket du placerar virtuella datorer för HDInsight-kluster och tjänstens huvudnamn som används av klustret.
* Installationsprogrammet [LDAPS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md), när du konfigurerar Azure AD DS. Certifikatet som används för att ställa in LDAPS måste utfärdas av en offentlig certifikatutfärdare (inte ett självsignerat certifikat).
* Skapa omvänd DNS-zoner på den hanterade domänen för IP-adressintervall till HDInsight-undernätet (till exempel 10.2.0.0/24 i föregående bild).
* Konfigurera [synkronisering av lösenord autentiseringshasherna som krävs för NTLM och Kerberos-autentisering](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) från Azure AD till Azure AD DS-hanterad domän.
* Ett tjänstkonto eller ett användarkonto krävs. Använd det här kontot för att skapa HDInsight-klustret. Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Azure AD-domänen


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>HDInsight integrerad med Windows Server AD körs på Azure IaaS

Du kan distribuera Windows Server Active Directory Domain Services-rollen på en (eller flera) virtuella datorer (VM) i Azure och befordra dem för att vara domänkontrollanter. Dessa virtuella datorer för en domänkontrollant kan distribueras med hjälp av resource manager-distributionsmodellen till samma virtuella nätverk som HDInsight-klustret. Om domänkontrollanterna distribueras till ett annat virtuellt nätverk, behöver du peer dessa virtuella nätverk med hjälp av [VNet-till-VNet-peering](../../virtual-network/virtual-network-create-peering.md). 

[Mer information om-distribution av Windows Server Active Directory på Azure Virtual Machines](../../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![Domänansluten HDInsight-klustertopologi](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Du kan inte använda Azure Data Lake Store med HDInsight-klustret i den här arkitekturen.


Krav för integrering med Windows Server Active Directory på virtuella Azure-datorer:

* Du måste skapa en [organisationsenhet](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i.
* [Protokoll för Lightweight Directory Access](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAPs) måste ställas in för att kommunicera med AD. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDInsight-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild).
* Ett tjänstkonto eller ett användarkonto krävs. Använd det här kontot för att skapa HDInsight-klustret. Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Active Directory-domänen


## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett domänanslutet HDInsight-kluster kan du läsa [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md).
* Information om hantering av domänanslutna HDInsight-kluster finns i [Hantera domänanslutna HDInsight-kluster](apache-domain-joined-manage.md).
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* Om du vill köra Hive-frågor med hjälp av SSH på domänanslutna HDInsight-kluster, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
