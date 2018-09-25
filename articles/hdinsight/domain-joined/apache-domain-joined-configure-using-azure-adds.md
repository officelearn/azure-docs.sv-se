---
title: Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure AD-DS
description: Lär dig att installera och konfigurera ett Enterprise-säkerhetspaketet för HDInsight-kluster med hjälp av Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968381"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services

Enterprise Security Package (ESP)-kluster tillhandahåller flera användare åtkomst i Azure HDInsight-kluster. HDInsight-kluster med hjälp av ESP är anslutna till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med kluster och köra jobb för stordata. 

I den här artikeln får du lära dig hur du konfigurerar ett HDInsight-kluster med ESP med hjälp av Azure Active Directory Domain Services (Azure AD DS-).

>[!NOTE]
>ESP är tillgänglig i HDI 3.6 + för Spark, interaktiv och Hadoop. ESP för HBase-kluster-typer finns i förhandsversion.


## <a name="enable-azure-ad-ds"></a>Aktivera Azure AD DS

Aktivera Azure AD-DS är en förutsättning innan du kan skapa ett HDInsight-kluster med ESP. Mer information finns i [aktivera Azure Active Directory Domain Services med Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Endast klientadministratörer ha behörighet att skapa en Azure AD-DS-instans. Om du använder Azure Data Lake Storage Gen1 som standardlagringen för HDInsight, se till att standard Azure AD-klient för Data Lake Storage Gen1 är densamma som domänen för HDInsight-klustret. Eftersom Hadoop är beroende av Kerberos- och grundläggande autentisering, måste multifaktorautentisering inaktiveras för användare som ska få åtkomst till klustret.

Det är säkert LDAP för en hanterad Azure AD-DS-domän. När du aktiverar LDAPS placera domännamnet i ämnesnamnet eller det alternativa ämnesnamnet i certifikatet. Mer information finns i [konfigurera säkert LDAP för en Azure AD DS-domän hanterad](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="add-managed-identity"></a>Lägg till hanterad identitet

När du har aktiverat Azure AD-DS skapar en hanterad identitet och kopplar den till den **HDInsight Domain Services deltagare** roll i Azure AD DS-åtkomstkontroll.

![Active Directory Domain Services Access control i Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Mer information finns i [vad är hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Skapa ett HDInsight-kluster med ESP

Nästa steg är att skapa HDInsight-kluster med ESP aktiverat med Azure AD-DS.

Det är lättare att placera både Azure AD-DS-instans och HDInsight-klustret i samma Azure-nätverk. Om du vill placera dem i olika virtuella nätverk måste du peerkoppla de virtuella nätverk så att HDInsight virtuella datorer har åtkomst till domänkontrollanten för att ansluta till de virtuella datorerna. Mer information finns i [peerkoppling av virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md).

När du skapar ett HDInsight-kluster, har du möjlighet att aktivera Enterprise Security Package att ansluta ditt kluster med Azure AD DS. 

![Azure HDInsight-säkerhet och nätverk](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

När du har aktiverat ESP upptäckte vanliga felkonfigureringar relaterade till Azure AD DS-automatiskt och godkänts.

![Azure HDInsight Enterprise security package domänverifiering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Tidig upptäckt sparar tid genom att du kan åtgärda fel innan du skapar klustret.

![Azure HDInsight Enterprise security package det gick inte att verifiera domänen](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

När du skapar ett HDInsight-kluster med ESP måste du ange följande parametrar:

- **Kluster-administratörsanvändare**: Välj en administratör för ditt kluster från din synkroniserad Azure AD DS-.

- **Kluster åtkomstgrupper**: vars användare som du vill synkronisera till klustret ska vara synkroniserade och är tillgängliga i Azure AD DS-säkerhetsgrupper. Till exempel HiveUsers. Om du vill ange flera användargrupper avgränsa dem med semikolon (;). I grupperna måste finnas i katalogen före etablering. Mer information finns i [skapar en grupp och Lägg till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Om gruppen inte finns uppstår ett fel: ”grupp HiveUsers hittades inte i Active Directory”.

- **LDAPS-URL**: ett exempel är ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Ange den fullständiga URL, inklusive ”ldaps: / /” och portnummer (: 636).

Följande skärmbild visar konfigurationerna i Azure portal:

   ![Azure HDInsight ESP Active Directory Domain Services-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Nästa steg
* Konfigurera Hive-principer och köra Hive-frågor finns i [konfigurera Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md).
* Använda SSH för att ansluta till HDInsight-kluster med hjälp av ESP, finns i [använda SSH med Linux-baserat Hadoop i HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

