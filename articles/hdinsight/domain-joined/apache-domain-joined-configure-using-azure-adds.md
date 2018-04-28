---
title: Konfigurera domänanslutna HDInsight-kluster med AAD-DS
description: Lär dig hur du skapar och konfigurerar domänanslutna HDInsight-kluster med Azure Active Directory Domain Services
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 060ca8040f514ec1df48c2ca4568cbbb2a529267
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Konfigurera domänanslutna HDInsight-kluster med Azure Active Directory Domain Services

Domänanslutna kluster ger flera användare företaget säkerhetsfunktioner i HDInsight. Domänanslutna HDInsight-kluster är anslutna till active directory-domäner, så att användarna kan använda sina domänautentiseringsuppgifter att autentisera med kluster och köra jobb för stordata. 

I den här artikeln får du lära dig hur du konfigurerar en domänansluten HDInsight-kluster med Azure Active Directory Domain Services.

> [!NOTE]
> Active Directory på Azure IaaS-VM stöds inte längre.

## <a name="create-azure-adds"></a>Skapa Azure ADDS

Du måste skapa en Azure AD DS innan du kan skapa ett HDInsight-kluster. För att skapa ett Azure lägger till, se [aktivera Azure Active Directory Domain Services med Azure-portalen](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Endast innehavaradministratörer har behörighet att skapa domain services. Om du använder Azure Data Lake lagring (ADLS) som standardlagring för HDInsight, kontrollera standard Azure AD-klient för ADLS är samma som i domänen för HDInsight-klustret. Multifaktorautentisering måste inaktiveras för användare som har åtkomst till klustret för att göra detta skapar för att arbeta med Azure Data Lake Store.

När tjänsten AAD-domän har etablerats, behöver du skapa ett tjänstkonto i AAD (som kommer att synkroniseras till AAD-DS) med rätt behörighet för att skapa HDInsight-kluster. Om det här service-kontot redan finns måste du återställa sitt lösenord och vänta tills synkroniseras med AAD-DS (återställningen leder till att skapa lösenords-hash för kerberos och det kan ta upp till 30 minuter). Tjänstkontot måste ha följande behörighet:

- Ansluta datorer till domänen och placera datorn säkerhetsobjekt i Organisationsenheten som du anger när klustret skapas.
- Skapa tjänstens huvudnamn i Organisationsenheten som du anger när klustret skapas.

Du måste aktivera säker LDAP för Azure AD Domain Services-hanterad domän. För att aktivera säker LDAP, se [konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Skapa en domänansluten HDInsight-kluster

Nästa steg är att skapa HDInsight-kluster med hjälp av AAD-DS och det tjänstkonto som skapats i föregående avsnitt.

Det är lättare att placera både Azure AD domain service och HDInsight-klustret i samma Azure virtuella network(VNet). Om de finns i olika Vnet, måste du peer-båda Vnet. Mer information finns i [virtuella nätverk peering](../../virtual-network/virtual-network-peering-overview.md).

När du skapar en domänansluten HDInsight-kluster måste du ange följande parametrar:

- **Domännamn**: det domännamn som är associerade med Azure AD DS. Till exempel contoso.onmicrosoft.com
- **Namnet på användaren**: kontot i Azure AD-Domänkontrollant som skapats i föregående avsnitt. Till exempel hdiadmin@contoso.onmicrosoft.com. Den här domänanvändare kan administratören för den här datorn är domänansluten HDInsight-kluster.
- **Domänlösenord**: lösenordet för tjänstkontot.
- **Organisationsenheten**: det unika namnet på den Organisationsenhet som du vill använda med HDInsight-kluster. Till exempel: OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com. Om Organisationsenheten inte finns, försök HDInsight-kluster att skapa Organisationsenheten. 
- **LDAPS URL**: till exempel ldaps://contoso.onmicrosoft.com:636
- **Åtkomst-användargrupp**: säkerhetsgrupperna vars användare som du vill synkronisera till klustret. Till exempel HiveUsers. Om du vill ange flera användargrupper, avgränsade med kommatecken (,).
 
> [!NOTE]
> Eftersom Apache Zeppelin använder domännamnet för att autentisera kontot administrativa, måste kontot ha samma domännamn som UPN-suffix för Apache Zeppelin ska fungera korrekt.
 
Följande skärmbild visar konfigurationerna i Azure-portalen:

![Azure HDInsight domänanslutna Active Directory Domain Services-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa i [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* För att ansluta till domänanslutna HDInsight-kluster med hjälp av SSH finns [använda SSH med Linux-baserade Hadoop i HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

