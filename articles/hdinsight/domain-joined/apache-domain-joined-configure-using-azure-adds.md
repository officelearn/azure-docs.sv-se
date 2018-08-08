---
title: Konfigurera ett domänanslutet HDInsight-kluster med hjälp av Azure AD DS
description: Lär dig att installera och konfigurera ett domänanslutet HDInsight-kluster med hjälp av Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 0d44812c92fd14bf87aac9a942241f8de55f2eec
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590600"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>Konfigurera ett domänanslutet HDInsight-kluster med hjälp av Azure Active Directory Domain Services

Domänanslutna kluster ger flera användare åtkomst i Azure HDInsight-kluster. Domänanslutna HDInsight-kluster är anslutna till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med kluster och köra jobb för stordata. 

I den här artikeln får du lära dig hur du konfigurerar ett domänanslutet HDInsight-kluster med hjälp av Azure Active Directory Domain Services (Azure AD DS).

## <a name="enable-azure-ad-ds"></a>Aktivera Azure AD DS

Aktivera Azure AD DS är en förutsättning innan du kan skapa ett domänanslutet HDInsight-kluster. Mer information finns i [aktivera Azure Active Directory Domain Services med Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Endast klientadministratörer ha behörighet att skapa en Azure AD DS-instans. Om du använder Azure Data Lake Storage Gen1 som standardlagringen för HDInsight, se till att standard Azure AD-klient för Data Lake Storage Gen1 är densamma som domänen för HDInsight-klustret. Eftersom Hadoop är beroende av Kerberos- och grundläggande autentisering, måste multifaktorautentisering inaktiveras för användare som ska få åtkomst till klustret.

När du etablerar Azure AD DS-instans, kan du skapa ett tjänstkonto i Azure Active Directory (Azure AD) med rätt behörigheter. Om det här service-kontot redan finns, återställa dess lösenord och vänta tills programmet synkroniseras till Azure AD DS. Den här återställningen resulterar i skapandet av Kerberos lösenords-hash och det kan ta upp till 30 minuter att synkronisera till Azure AD DS. 

Service-kontot måste ha följande behörigheter:

- Ansluta datorer till domänen och placera datorn säkerhetsobjekt i den Organisationsenhet som du anger när klustret skapas.
- Skapa tjänstens huvudnamn i Organisationsenheten som du anger när klustret skapas.

> [!NOTE]
> Eftersom Apache Zeppelin använder domännamnet för att autentisera administrationskontot tjänstkontot *måste* har samma domännamn som UPN-suffix för Apache Zeppelin ska fungera korrekt.

Läs mer om organisationsenheter och hur du hanterar dem i [skapa en OU på en hanterad Azure AD DS-domän](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Det är säkert LDAP för en hanterad Azure AD DS-domän. Mer information finns i [konfigurera säkert LDAP för en Azure AD DS en domän hanterad](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Skapa ett domänanslutet HDInsight-kluster

Nästa steg är att skapa HDInsight-kluster med hjälp av Azure AD DS och det tjänstkonto som du skapade i föregående avsnitt.

Det är lättare att placera både Azure AD DS-instans och HDInsight-klustret i samma Azure-nätverk. Om du vill placera dem i olika virtuella nätverk måste du peerkoppla de virtuella nätverk så att HDInsight virtuella datorer har åtkomst till domänkontrollanten för att ansluta till de virtuella datorerna. Mer information finns i [peerkoppling av virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md).

När du skapar ett domänanslutet HDInsight-kluster måste du ange följande parametrar:

- **Domännamn**: det domännamn som är associerat med Azure AD DS. Ett exempel är contoso.onmicrosoft.com.

- **Domänanvändarnamn**: tjänstkontot i domänkontrollanten lägger Azure till en domän hanterad som du skapade i föregående avsnitt. Ett exempel är hdiadmin@contoso.onmicrosoft.com. Den här domänanvändare ska vara administratör för det här HDInsight-klustret.

- **Domänlösenord**: lösenordet för tjänstkontot.

- **Organisationsenhet**: det unika namnet på den Organisationsenhet som du vill använda med HDInsight-kluster. Ett exempel är OU = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com. Om den här Organisationsenheten inte finns, försöker HDInsight-klustret skapa en Organisationsenhet med hjälp av de privilegier som tjänstkontot har. Till exempel om kontot tillhör administratörsgruppen på Azure AD DS, har den rätt behörighet för att skapa en Organisationsenhet. I annat fall måste du först skapa Organisationsenheten och ger service-konto fullständig kontroll över denna Organisationsenhet. Mer information finns i [skapa en OU på en hanterad Azure AD DS-domän](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

    > [!IMPORTANT]
    > Inkludera alla domänkontrollanter, avgränsade med kommatecken, efter Organisationsenheten (t.ex, OU = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com).

- **LDAPS-URL**: ett exempel är ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Ange den fullständiga URL, inklusive ”ldaps: / /” och portnummer (: 636).

- **Ha åtkomst till användargrupper**: säkerhetsgrupperna vars användare som du vill synkronisera till klustret. Till exempel HiveUsers. Om du vill ange flera användargrupper avgränsa dem med semikolon (;). I grupperna måste finnas i katalogen före etablering. Mer information finns i [skapar en grupp och Lägg till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Om gruppen inte finns uppstår ett fel: ”grupp HiveUsers hittades inte i Active Directory”.

Följande skärmbild visar konfigurationerna i Azure portal:

   ![Azure HDInsight domänanslutna Active Directory Domain Services-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Nästa steg
* Konfigurera Hive-principer och köra Hive-frågor finns i [konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* Använda SSH för att ansluta till domänanslutna HDInsight-kluster, finns i [använda SSH med Linux-baserat Hadoop i HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

