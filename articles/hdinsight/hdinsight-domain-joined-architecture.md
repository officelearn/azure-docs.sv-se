---
title: "Domänansluten Azure HDInsight-arkitektur | Microsoft Docs"
description: "Lär dig hur du planerar för domänansluten HDInsight."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: b5cd321f3cd4c415f5e97ca550b7ab1679324921
ms.openlocfilehash: 94ef8147f1f73b293e818fc508096789373df7fc


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planera Azure-domänanslutna Hadoop-kluster i HDInsight

Traditionellt är Hadoop ett kluster för en användare. Det passar för de flesta företag som har små applikationsteam som bygger stordataarbetsbelastningar. Hadoop ökar i popularitet och många företag går mot en modell där kluster hanteras av IT-team, och flera applikationsteam delar kluster. Det gör att kluster med flera användare är en av de mest efterfrågade funktionerna i HDInsight.

I stället för att skapa en egen autentisering och auktorisering för flera användare använder HDInsight på den populäraste identitetsprovidern – Active Directory (AD). Säkerhetsgrupper är en kraftfull funktion i Active Directory som kan användas för att hantera auktorisering i HDInsight. Genom att integrera HDInsight med Active Directory kan Active Directory-användare kommunicera med klustren med hjälp av sina Active Directory-autentiseringsuppgifter. HDInsight mappar Active Directory-användare till en lokal Hadoop-användare så att alla tjänster som körs på HDInsight (Ambari, Hive-server, Ranger, Spark Thrift-server med flera) fungerar sömlöst för autentiserade användare.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrera HDInsight med Active Directory

När du integrerar HDInsight med Active Directory domänansluts HDInsight-klusternoder till Active Directory-domänen. HDInsight skapar tjänsthuvudnamn för de Hadoop-tjänster som körs i klustret och placerar dem i en angiven organisationsenhet (OU) i Active Directory. HDInsight skapar även omvända DNS-mappningar i Active Directory-domänen för IP-adresserna för de noder som är anslutna till domänen.

Det finns flera arkitekturer som du kan följa för att uppnå den här konfigurationen. Välj den arkitektur som fungerar bäst för dig.

**1. HDInsight integrerad med AD som körs på Azure IAAS**

Det här är den enklaste arkitekturen för integrering av HDInsight med Active Directory. Active Directory-domänkontrollanten körs på en (eller flera) virtuella datorer (VM) i Azure. Dessa virtuella datorer finns vanligtvis i ett virtuellt nätverk. Du konfigurerar ett annat virtuellt nätverk för HDInsight-kluster. För att HDInsight ska ha åtkomst till Active Directory måste du peer-koppla dessa virtuella nätverk med hjälp av en [VNet-till-VNet-peering](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md).

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Du kan inte använda Azure Data Lake Store med HDInsight-kluster i den här arkitekturen.
 

Krav för Active Directory:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras för kommunikation med Active Directory. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDInsight-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild).
* Ett tjänstkonto eller ett användarkonto krävs (som utfärdades för att skapa HDInsight-klustret). Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten.
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Active Directory-domänen.

**2. HDInsight integrerad med endast molnbaserad Azure AD**

För en endast molnbaserad Azure Active Directory (AD Azure) måste du konfigurera en domänkontrollant så att HDInsight kan integreras med Azure Active Directory. Detta uppnås med hjälp av [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). Azure AD DS skapar domänkontrollanter i molnet och tillhandahåller IP-adresser till dem. Två domänkontrollanter skapas för hög tillgänglighet.

Azure AD DS finns för närvarande bara i klassiska virtuella nätverk. Det är bara tillgängligt med den klassiska Azure-portalen. HDInsight VNet finns i Azure Portal som måste vara peer-kopplad med den klassiska VNet med VNet-till-VNet-peering.

> [!NOTE]
> För peering mellan ett klassiskt virtuellt nätverk och ett virtuellt Azure Resource Manager-nätverk krävs att båda de virtuella nätverken är i samma region, och att båda de virtuella nätverken ingår i samma Azure-prenumeration.

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Krav för Active Directory:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras när du konfigurerar AD DS. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDI-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild). 
* [Hashvärden för lösenord](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) måste synkroniseras från Azure AD till AD DS.
* Ett tjänstkonto eller ett användarkonto krävs (används för att skapa HDInsight-klustret). Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten.
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Active Directory-domänen.

**3. HDInsight integrerad med lokal AD via VPN**

Den här arkitekturen liknar arkitektur #1. Den enda skillnaden är att Active Directory finns lokalt och att HDInsight är tillgänglig för Active Directory via en [VPN-anslutning från Azure till det lokala nätverket](../expressroute/expressroute-introduction.md).

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> Du kan inte använda Azure Data Lake Store med HDInsight-kluster i den här arkitekturen.

Krav för Active Directory:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras för kommunikation med Active Directory. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDI-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild).
* Ett tjänstkonto eller ett användarkonto krävs (används för att skapa HDInsight-klustret). Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten.
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Active Directory-domänen.

**4. HDInsight integrerad med lokal AD som är synkroniserad med Azure AD**

Den här arkitekturen liknar arkitektur #2. Den enda skillnaden är att den lokala Active Directory är synkroniserad med Azure Active Directory. Du måste konfigurera en domänkontrollant så att HDInsight kan integreras med Azure Active Directory. Detta uppnås med hjälp av [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). AD DS skapar domänkontrollanter i molnet och ger dig IP-adresser till dem. Två domänkontrollanter skapas för hög tillgänglighet.

Azure AD DS finns för närvarande bara i klassiska virtuella nätverk. Det är bara tillgängligt med den klassiska Azure-portalen. HDInsight VNet finns i Azure Portal som måste vara peer-kopplad med den klassiska VNet med VNet-till-VNet-peering.

> [!NOTE]
> För peering mellan ett klassiskt virtuellt nätverk och ett virtuellt Azure Resource Manager-nätverk krävs att båda de virtuella nätverken är i samma region, och att båda de virtuella nätverken ingår i samma Azure-prenumeration.

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Krav för Active Directory:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras när du konfigurerar AD DS. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDI-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild). 
* [Hashvärden för lösenord](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) måste synkroniseras från Azure AD till AD DS.
* Ett tjänstkonto eller ett användarkonto krävs (används för att skapa HDInsight-klustret). Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten.
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Active Directory-domänen.

**5. HDInsight integrerad med ett icke-förvalt Azure AD (rekommenderas endast för testning och utveckling)**

Den här arkitekturen liknar arkitektur #2. För de flesta företag är administratörsåtkomsten till Active Directory begränsad till endast vissa personer. När du vill göra en POC (proof-of-concept) eller bara prova att skapa ett domänanslutet kluster kan det vara fördelaktigt att bara skapa en ny Azure Active Directory i prenumerationen, i stället för att vänta tills administratören har konfigurerat krav i Active Directory. Eftersom du skapat en Azure AD har du fullständiga behörigheter till denna Azure AD för att konfigurera AD DS.

AD DS skapar domänkontrollanter i molnet och ger dig IP-adresser till dem. Två domänkontrollanter skapas för hög tillgänglighet.

AD DS finns bara i klassiska VNets idag, och du behöver därför åtkomst till den klassiska portalen, och du behöver skapa ett klassiskt VNet för att konfigurera AD DS. HDInsight VNet finns i Azure Portal som måste vara peer-kopplad med den klassiska VNet med VNet-till-VNet-peering.

> [!NOTE]
> För peering mellan ett klassiskt virtuellt nätverk och ett virtuellt Azure Resource Manager-nätverk krävs att båda de virtuella nätverken är i samma region, och att båda de virtuella nätverken ingår i samma Azure-prenumeration.

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Krav för Active Directory:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras när du konfigurerar AD DS. Du kan skapa en [självsignerat certifikat](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) för att konfigurera LDAPS. Men om du vill använda ett självsignerat certifikat måste du begära ett undantag från <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a>.
* Omvända DNS-zoner måste skapas på domänen för HDI-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild). 
* [Hashvärden för lösenord](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) måste synkroniseras från Azure AD till AD DS.
* Ett tjänstkonto eller ett användarkonto krävs (används för att skapa HDInsight-klustret). Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten.
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Active Directory-domänen.

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett domänanslutet HDInsight-kluster kan du läsa i [Konfigurera domänanslutna HDInsight-kluster](hdinsight-domain-joined-configure.md).
* Information om hantering av domänanslutna HDInsight-kluster finns i [Hantera domänanslutna HDInsight-kluster](hdinsight-domain-joined-manage.md).
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa i [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](hdinsight-domain-joined-run-hive.md).
* Om du vill köra Hive-frågor med SSH på domänanslutna HDInsight-kluster kan du läsa i [Använd SSH med Linux-baserade Hadoop på HDInsight från Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md).




<!--HONumber=Feb17_HO1-->


