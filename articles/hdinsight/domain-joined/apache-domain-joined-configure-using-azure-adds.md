---
title: "Konfigurera domänanslutna HDInsight-kluster med Azure Active Directory Domain Services - Azure | Microsoft Docs"
description: "Lär dig hur du skapar och konfigurerar domänanslutna HDInsight-kluster med Azure Active Directory Domain Services"
services: hdinsight
documentationcenter: 
author: bprakash
manager: jhubbard
editor: cgronlun
tags: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: bhanupr
ms.openlocfilehash: a0156915c329dfad1424cfd1f10a6ebb27c56acc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Konfigurera domänanslutna HDInsight-kluster med Azure Active Directory Domain Services

Domänanslutna kluster ger flera användare företaget säkerhetsfunktioner i HDInsight. Domänanslutna HDInsight-kluster är anslutna till active directory-domäner, så att användarna kan använda sina domänautentiseringsuppgifter att autentisera med kluster och köra jobb för stordata. 

Det finns två sätt att konfigurera en domänkontrollant så att en domänansluten HDInsight-kluster kan ansluta till:

- Azure Active Directory Domain Services (Azure AD DS)
- Active Directory-domänkontrollant på Azure IaaS-VM

I den här artikeln får du lära dig hur du konfigurerar en domänansluten HDInsight-kluster med Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Skapa Azure ADDS

Du måste skapa en Azure AD DS innan du kan skapa ett HDInsight-kluster. För att skapa ett Azure lägger till, se [aktivera Azure Active Directory Domain Services med Azure-portalen](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Endast innehavaradministratörer har behörighet att skapa domain services. Om du använder Azure Data Lake lagring (ADLS) som standardlagring för HDInsight, kontrollera standard Azure AD-klient för ADLS är samma som i domänen för HDInsight-klustret. Multifaktorautentisering måste inaktiveras för användare som har åtkomst till klustret för att göra detta skapar för att arbeta med Azure Data Lake Store.

När tjänsten domän har etablerats, måste du skapa ett tjänstkonto i den **Azure AD-DC-administratörer** gruppen för att skapa HDInsight-klustret. Kontot måste vara en global administratör för Azure AD.

Du måste aktivera säker LDAP för Azure AD Domain Services-hanterad domän. För att aktivera säker LDAP, se [konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Skapa en domänansluten HDInsight-kluster

Nästa steg är att skapa HDInsight-kluster med hjälp av AAD-DS och det tjänstkonto som skapats i föregående avsnitt.

Det är lättare att placera både Azure AD domain service och HDInsight-klustret i samma Azure virtuella network(VNet). Om de finns i olika Vnet, måste du peer-båda Vnet. Mer information finns i [virtuella nätverk peering](../../virtual-network/virtual-network-peering-overview.md).

När du skapar en domänansluten HDInsight-kluster måste du ange följande parametrar:

- **Domännamn**: det domännamn som är associerade med Azure AD DS. Till exempel contoso.onmicrosoft.com
- **Namnet på användaren**: kontot i gruppen administratörer med Azure AD-Domänkontrollant som skapats i föregående avsnitt. Till exempel hdiadmin@contoso.onmicrosoft.com. Den här domänanvändaren är administratör för detta domänanslutna HDInsight-kluster.
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

