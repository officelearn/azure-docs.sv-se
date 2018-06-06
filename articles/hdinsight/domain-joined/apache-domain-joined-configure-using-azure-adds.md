---
title: Konfigurera domänanslutna HDInsight-kluster med AAD-DS
description: Lär dig hur du skapar och konfigurerar domänanslutna HDInsight-kluster med Azure Active Directory Domain Services
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 7bde1c834038bdc2a010987b4e32fa49222101ee
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715283"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Konfigurera domänanslutna HDInsight-kluster med Azure Active Directory Domain Services

Domänanslutna kluster ger flera användare tillgång på HDInsight-kluster. Domänanslutna HDInsight-kluster är anslutna till en domän, så att användarna kan använda sina domänautentiseringsuppgifter att autentisera med kluster och köra jobb för stordata. 

I den här artikeln får du lära dig hur du konfigurerar en domänansluten HDInsight-kluster med Azure Active Directory Domain Services.

> [!NOTE]
> Skapa ett domänanslutna HDInsight-kluster måste Azure Active Directory Domain Services. Skapa en domänansluten HDInsight-kluster med Active Directory finns i Azure IaaS-virtuella datorer stöds inte längre.

## <a name="create-azure-adds"></a>Skapa Azure ADDS

Aktivera Azure AD Domain Services (AAD-DS) är en förutsättning innan du kan skapa en domänansluten HDInsight-kluster. För att aktivera en AAD-DS-instans, se [så här aktiverar du AAD-DS med hjälp av Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Endast innehavaradministratörer har behörighet att skapa en AAD-DS-instans. Om du använder Azure Data Lake lagring (ADLS) som standardlagring för HDInsight, kontrollera standard Azure AD-klient för ADLS är samma som i domänen för HDInsight-klustret. Sincde Hadoop bygger på Kerberos och grundläggande autentisering, multifaktorautentisering måste vara inaktiverat för användare som ska ha åtkomst till klustret.

När AAD-DS-instans har etablerats, måste du skapa ett tjänstkonto i AAD (som kommer att synkroniseras till AAD-DS) med rätt behörigheter. Om tjänstkontot redan finns, måste du återställa lösenordet och vänta tills synkroniseras med AAD-DS (återställningen leder till att skapa lösenords-hash för kerberos och det kan ta upp till 30 minuter att synkronisera med AAD-DS). Tjänstkontot måste ha följande behörigheter:

- Ansluta datorer till domänen och placera datorn säkerhetsobjekt i Organisationsenheten som du anger när klustret skapas.
- Skapa tjänstens huvudnamn i Organisationsenheten som du anger när klustret skapas.

> [!NOTE]
> Eftersom Apache Zeppelin använder domännamnet för att autentisera kontot administrativa, måste kontot ha samma domännamn som UPN-suffix för Apache Zeppelin ska fungera korrekt.

Mer information om organisationsenheter och hur du hanterar dem finns [skapa en Organisationsenhet i en AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Säker LDAP för AAD-DS-hanterade domän krävs. För att aktivera säker LDAP, se [hur du konfigurerar säker LDAP (LDAPS) för en AAD-DS hanterade domänen](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Skapa en domänansluten HDInsight-kluster

Nästa steg är att skapa HDInsight-kluster med hjälp av AAD-DS och det tjänstkonto som skapats i föregående avsnitt.

Det är lättare att placera både AAD-DS och HDInsight-klustret i samma Azure virtuella network(VNet). Om du vill placera dem i olika Vnet, måste du peer-dessa Vnet så att HDI virtuella datorer har en fri domänkontrollanten för att ansluta till de virtuella datorerna. Mer information finns i [virtuella nätverk peering](../../virtual-network/virtual-network-peering-overview.md).

När du skapar en domänansluten HDInsight-kluster måste du ange följande parametrar:

- **Domännamn**: det domännamn som är associerad med AAD-DS. Till exempel contoso.onmicrosoft.com
- **Namnet på användaren**: tjänstkonto i den hanterade domänen som skapas i föregående avsnitt. Till exempel hdiadmin@contoso.onmicrosoft.com. Den här domänanvändare ska vara administratör för HDInsight-kluster.
- **Domänlösenord**: lösenordet för tjänstkontot.
- **Organisationsenheten**: det unika namnet på den Organisationsenhet som du vill använda med HDInsight-kluster. Till exempel: OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com. Om Organisationsenheten inte finns, försök HDInsight-kluster att skapa en Organisationsenhet med behörigheten tjänstkontot har. (Till exempel om kontot finns i AAD-DS administratörsgruppen, den har rätt behörighet för att skapa en Organisationsenhet, annars kanske du måste först skapa en Organisationsenhet och ge service för fullständig kontroll över denna Organisationsenhet först - se [skapa en Organisationsenhet på en AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)).

    > [!IMPORTANT]
    > Det är viktigt att inkludera alla domänkontrollanter sepearated med kommatecken efter OU: N (t.ex. OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com).

- **LDAPS URL**: till exempel ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > Ange fullständiga url inklusive ldaps: / / och portnummer (: 636)

- **Åtkomst-användargrupp**: säkerhetsgrupperna vars användare som du vill synkronisera till klustret. Till exempel HiveUsers. Om du vill ange flera användargrupper, avgränsade med kommatecken (,).
 
Följande skärmbild visar konfigurationerna i Azure-portalen:

![Azure HDInsight domänanslutna Active Directory Domain Services-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa i [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* För att ansluta till domänanslutna HDInsight-kluster med hjälp av SSH finns [använda SSH med Linux-baserade Hadoop i HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

