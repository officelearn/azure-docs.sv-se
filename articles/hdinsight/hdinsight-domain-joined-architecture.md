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
ms.sourcegitcommit: 4ba44128ec19d3937643ac934ca3e787cb9819a3
ms.openlocfilehash: 690ba97d2b0634548a0ec424d441ad34d70667b9
ms.lasthandoff: 02/27/2017


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planera Azure-domänanslutna Hadoop-kluster i HDInsight

Traditionellt är Hadoop ett kluster för en användare. Det passar för de flesta företag som har små applikationsteam som bygger stordataarbetsbelastningar. Hadoop ökar i popularitet och många företag går mot en modell där kluster hanteras av IT-team, och flera applikationsteam delar kluster. Det gör att funktioner som kluster med flera användare är en av de mest efterfrågade funktionerna i Azure HDInsight.

I stället för att skapa en egen autentisering och auktorisering för flera användare använder HDInsight på den populäraste identitetsprovidern – Azure Active Directory (Azure AD). De kraftfulla säkerhetsfunktionerna i Azure AD kan användas för att hantera auktorisering av flera användare i HDInsight. Genom att integrera HDInsight med Azure AD kan du kommunicera med klusterna genom att använda dina autentiseringsuppgifter för Azure AD. HDInsight mappar Azure AD-användare till en lokal Hadoop-användare så att alla tjänster som körs på HDInsight (Ambari, Hive-server, Ranger, Spark Thrift-server med flera) fungerar sömlöst för autentiserade användare.

## <a name="integrate-hdinsight-with-azure-ad"></a>Integrera HDInsight med Azure AD

När du integrerar HDInsight med Azure AD domänansluts HDInsight-klusternoder till Azure AD-domänen. HDInsight skapar tjänsthuvudnamn för de Hadoop-tjänster som körs i klustret och placerar dem i en angiven organisationsenhet (OU) i Azure AD. HDInsight skapar även omvända DNS-mappningar i Azure AD-domänen för IP-adresserna för de noder som är anslutna till domänen.

Det finns flera olika arkitekturer som du kan använda för att uppnå den här konfigurationen. Du kan välja från följande arkitekturer.

**HDInsight integrerad med Azure AD som körs på Azure IaaS**

Det här är den enklaste arkitekturen för integrering av HDInsight med Azure AD. Azure AD-domänkontrollanten körs på en (eller flera) virtuella datorer (VM) i Azure. Dessa virtuella datorer finns vanligtvis i ett virtuellt nätverk. Du konfigurerar ett annat virtuellt nätverk för HDInsight-klustret. För att HDInsight ska ha åtkomst till Azure AD måste du peer-koppla dessa virtuella nätverk med hjälp av [VNet-till-VNet-peering](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md).

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Du kan inte använda Azure Data Lake Store med HDInsight-klustret i den här arkitekturen.


Krav för Azure AD:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i.
* [Lightweight Directory Access Protocols](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAPs) måste ställas in för att kommunicera med Azure AD. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDInsight-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild).
* Ett tjänstkonto eller ett användarkonto krävs. Använd det här kontot för att skapa HDInsight-klustret. Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Active Directory-domänen

**HDInsight integrerad med endast molnbaserad Azure AD**

För endast molnbaserad Azure AD måste du konfigurera en domänkontrollant så att HDInsight kan integreras med Azure AD. Detta uppnås med hjälp av [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). Azure AD DS skapar domänkontrollanter i molnet och tillhandahåller IP-adresser för dem. Två domänkontrollanter skapas för hög tillgänglighet.

Azure AD DS finns för närvarande bara i klassiska virtuella nätverk. Det är bara tillgängligt med den klassiska Azure-portalen. Virtuella HDInsight-nätverk finns i Azure Portal som måste vara peer-kopplad med den klassiska virtuella nätverket med VNet-till-VNet-peering.

> [!NOTE]
> För peering mellan ett klassiskt virtuellt nätverk och ett virtuellt Azure Resource Manager-nätverk krävs att båda virtuella nätverken är i samma region, och att de ingår i samma Azure-prenumeration.

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Krav för Azure AD:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras när du konfigurerar Azure AD DS. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDInsight-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild).
* [Hashvärden för lösenord](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) måste synkroniseras från Azure AD till Azure AD DS.
* Ett tjänstkonto eller ett användarkonto krävs. Använd det här kontot för att skapa HDInsight-klustret. Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Azure AD-domänen

**HDInsight integrerad med lokal Active Directory via VPN**

Den här arkitekturen liknar HDInsight integrerad med Azure AD som körs på Azure IaaS. Den enda skillnaden är att Azure AD finns lokalt och att HDInsight är tillgänglig för Azure AD via en [VPN-anslutning från Azure till ett lokalt nätverk](../expressroute/expressroute-introduction.md).

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> Du kan inte använda Azure Data Lake Store med HDInsight-klustret i den här arkitekturen.

Krav för Azure AD:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras för kommunikation med Azure AD. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDInsight-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild).
* Ett tjänstkonto eller ett användarkonto krävs. Använd det här kontot för att skapa HDInsight-klustret. Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Azure AD-domänen

**HDInsight integrerad med lokal Active Directory som är synkroniserad med Azure AD**

Den här arkitekturen liknar HDInsight integrerad med endast molnbaserad Azure AD. Den enda skillnaden är att den lokala Active Directory är synkroniserad med Azure AD. Konfigurera en domänkontrollant i molnet så att HDInsight kan integreras med Azure AD. Detta uppnås med hjälp av [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md). Azure AD DS skapar domänkontrollanter i molnet och tillhandahåller IP-adresser för dem. Två domänkontrollanter skapas för hög tillgänglighet.

Azure AD DS finns för närvarande bara i klassiska virtuella nätverk. Det är bara tillgängligt med den klassiska Azure-portalen. Virtuella HDInsight-nätverk finns i Azure Portal som måste vara peer-kopplad med den klassiska virtuella nätverket med VNet-till-VNet-peering.

> [!NOTE]
> För peering mellan ett klassiskt virtuellt nätverk och ett virtuellt Azure Resource Manager-nätverk krävs att båda virtuella nätverken är i samma region, och att de ingår i samma Azure-prenumeration.

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Krav för Azure AD:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras när du konfigurerar Azure AD DS. Certifikatet som används för att konfigurera LDAPS måste vara ett verkligt certifikat (inte ett självsignerat certifikat).
* Omvända DNS-zoner måste skapas på domänen för HDInsight-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild).
* [Hashvärden för lösenord](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) måste synkroniseras från Azure AD till Azure AD DS.
* Ett tjänstkonto eller ett användarkonto krävs. Använd det här kontot för att skapa HDInsight-klustret. Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Active Directory-domänen

**HDInsight integrerad med ett icke-förvalt Azure AD (rekommenderas endast för testning och utveckling)**

Den här arkitekturen liknar HDInsight integrerad med endast molnbaserad Azure AD. För de flesta företag är administratörsåtkomsten till Azure AD begränsad till vissa personer. När du vill göra en POC (proof-of-concept) eller prova att skapa ett domänanslutet kluster kan det vara fördelaktigt att skapa en Azure AD-instans i prenumerationen i stället för att vänta tills en administratör har konfigurerat krav i Azure AD. Eftersom det här är en Azure AD-instans som du skapat har du fullständiga behörigheter till den här Azure AD-instansen och kan konfigurera Azure AD DS.

Azure AD DS skapar domänkontrollanter i molnet och tillhandahåller IP-adresser för dem. Två domänkontrollanter skapas för hög tillgänglighet.

Azure AD DS finns bara i klassiska virtuella nätverk så du behöver ha åtkomst till den klassiska Azure-portalen och du måste skapa ett klassiskt virtuella nätverk för att konfigurera Azure AD DS. Virtuella HDInsight-nätverk finns i Azure Portal som måste vara peer-kopplad med den klassiska virtuella nätverket med VNet-till-VNet-peering.

> [!NOTE]
> För peering mellan det klassiska virtuella nätverket och virtuella Azure Resource Manager-nätverk krävs att båda virtuella nätverken är i samma region och att de ingår i samma Azure-prenumeration.

![Domänansluten HDInsight-klustertopologi](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Krav för Azure AD:

* Du måste skapa en [organisationsenhet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) som de virtuella datorerna i HDInsight-klustret och tjänsthuvudnamnen som används av klustret ska placeras i.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) måste konfigureras när du konfigurerar Azure AD DS. Du kan skapa en [självsignerat certifikat](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) för att konfigurera LDAPS. Men om du vill använda ett självsignerat certifikat måste du begära ett undantag från <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a>.
* Omvända DNS-zoner måste skapas på domänen för HDInsight-undernätets IP-adressintervall (till exempel 10.2.0.0/24 på föregående bild).
* [Hashvärden för lösenord](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) måste synkroniseras från Azure AD till Azure AD DS.
* Ett tjänstkonto eller ett användarkonto krävs. Använd det här kontot för att skapa HDInsight-klustret. Följande behörigheter krävs för kontot:

    - Behörigheter för att skapa tjänsthuvudnamnsobjekt och datorobjekt inom organisationsenheten
    - Behörigheter för att skapa regler för omvänd DNS-proxy
    - Behörigheter för att ansluta datorer till Azure Active Directory-domänen

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett domänanslutet HDInsight-kluster kan du läsa [Konfigurera domänanslutna HDInsight-kluster](hdinsight-domain-joined-configure.md).
* Information om hantering av domänanslutna HDInsight-kluster finns i [Hantera domänanslutna HDInsight-kluster](hdinsight-domain-joined-manage.md).
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](hdinsight-domain-joined-run-hive.md).
* Om du vill köra Hive-frågor med SSH på domänanslutna HDInsight-kluster kan du läsa [Använd SSH med Linux-baserade Hadoop på HDInsight från Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

