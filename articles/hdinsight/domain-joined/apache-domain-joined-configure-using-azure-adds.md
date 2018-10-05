---
title: Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure AD-DS
description: Lär dig mer om att installera och konfigurera ett Enterprise-säkerhetspaketet för HDInsight-kluster med hjälp av Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/3/2018
ms.openlocfilehash: 84ee24b9002237d0993a30190944dbd6dd190ac8
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48784959"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services

Enterprise Security Package (ESP)-kluster tillhandahåller flera användare åtkomst i Azure HDInsight-kluster. HDInsight-kluster med hjälp av ESP är anslutna till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med kluster och köra jobb för stordata. 

I den här artikeln får du lära dig hur du konfigurerar ett HDInsight-kluster med ESP med hjälp av Azure Active Directory Domain Services (Azure AD DS-).

>[!NOTE]
>ESP är allmänt tillgänglig i HDI 3.6 för Spark, interaktiv och Hadoop. ESP för HBase- och Kafka-kluster finns i förhandsversion.

## <a name="enable-azure-ad-ds"></a>Aktivera Azure AD DS

Aktivera AzureAD DS är en förutsättning innan du kan skapa ett HDInsight-kluster med ESP. Mer information finns i [aktivera Azure Active Directory Domain Services med Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

När Azure AD-DS har aktiverats kan alla användare och objekt synkroniserar från Azure Active Directory till Azure AD DS-som standard. Längden på synkroniseringsåtgärden beror på antalet objekt i Azure AD. Synkroniseringen kan ta ett par dagar för hundratusentals objekt. 

Kunderna kan välja att synkronisera de grupper som behöver åtkomst till HDInsight-kluster. Det här alternativet för att synkronisera bara vissa grupper kallas *begränsade synkronisering*. Se [konfigurera omfång synkronisering från Azure AD till din hanterade domän](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) anvisningar.

När du har aktiverat Azure AD-DS körs en lokal tjänst DNS (Domain Name)-server på AD-datorer (VM). Konfigurera din Azure AD DS virtuella nätverk (VNET) om du vill använda dessa anpassade DNS-servrar. För att hitta rätt IP-adresser, Välj **egenskaper** under den **hantera** kategori och titta på IP-adresser i listan under **IP-adress i virtuellt nätverk**.

![Hitta IP-adresser för lokal DNS-servrar](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Ändra konfigurationen för DNS-servrar i Azure AD DS-VNET att använda dessa anpassade IP-adresser genom att välja **DNS-servrar** under den **inställningar** kategori. Klicka på alternativknappen bredvid **anpassade**, ange den första IP-adressen i textrutan nedan och klicka på **spara**. Lägga till ytterligare IP-adresser med hjälp av samma steg.

![Uppdaterar virtuellt nätverk DNS-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

> [!NOTE]
> Endast klientadministratörer ha behörighet att skapa en Azure AD-DS-instans. Multifaktorautentisering måste inaktiveras endast för användare som ska få åtkomst till klustret.

När du aktiverar säker LDAP, placera domännamnet i ämnesnamnet eller det alternativa certifikatmottagarnamnet i certifikatet. Exempel: om ditt domännamn är *contoso.com*, kontrollera exakt samma namn finns i certifikatets ämnesnamn eller Alternativt ämnesnamn. Mer information finns i [konfigurera säkert LDAP för en Azure AD DS-domän hanterad](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-azure-ad-ds-health-status"></a>Kontrollera status för Azure AD-DS-hälsa

Visa hälsotillståndet för din Azure Active Directory Domain Services genom att välja **hälsotillstånd** under den **hantera** kategori. Kontrollera statusen för Azure AD DS-är grönt (körs) och synkroniseringen är klar.

![Azure Active Directory Domain Services-hälsa](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Lägg till hanterad identitet

Skapa en hanterad Användartilldelad identitet om du inte redan har en. Se [skapa, lista, ta bort eller tilldela en roll till en Användartilldelad hanterad identitet med hjälp av Azure portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) anvisningar. 

Den hanterade identitet används för att förenkla domain services-åtgärder. Den här identiteten har åtkomst till läsa, skapa, ändra och ta bort domain services åtgärder som behövs för HDInsight Enterprise Security Package som att skapa organisationsenheter och principer för tjänsten.

När du har aktiverat Azure AD-DS skapar en hanterad Användartilldelad identitet och kopplar den till den **HDInsight Domain Services deltagare** roll i Azure AD DS-åtkomstkontroll.

![Active Directory Domain Services Access control i Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Tilldela en hanterad identitet till den **HDInsight Domain Services deltagare** rollen säkerställer att identiteten har tillgång till att utföra vissa åtgärder med domain services på Azure AD-DS-domänen. Mer information finns i [vad är hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Skapa ett HDInsight-kluster med ESP

Nästa steg är att skapa HDInsight-kluster med ESP aktiverat med Azure AD-DS.

Det är lättare att placera både Azure AD-DS-instans och HDInsight-klustret i samma Azure-nätverk. Om de finns i olika virtuella nätverk måste du peerkoppla de virtuella nätverk så att HDInsight virtuella datorer som är synliga för domänkontrollanten och kan läggas till domänen. Mer information finns i [peerkoppling av virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md). Om du vill testa om peering utförs korrekt, ansluta till en virtuell dator till HDInsight VNET/undernätet och pinga domännamnet eller köra **ldp.exe** att få åtkomst till Azure AD-DS-domän.

När de virtuella nätverken har peerkopplats kan du konfigurera HDInsight VNET att använda en anpassad DNS-server och ange Azure AD-DS privata IP-adresser som DNS-serveradresser. När båda de virtuella nätverken använder samma DNS-servrar, ditt domännamn matchar med rätt IP-Adressen och kan nås från HDInsight. Till exempel om ditt domännamn är ”contoso.com” sedan efter det här steget pinga ”contoso.com” ska matcha rätt Azure AD DS-IP. Du kan sedan ansluta till en virtuell dator till den här domänen.

![Konfigurera anpassade DNS-servrar för peer-kopplade virtuella nätverket](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

När du skapar ett HDInsight-kluster kan aktivera du Enterprise Security Package på fliken anpassade.

![Azure HDInsight-säkerhet och nätverk](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

När du har aktiverat ESP upptäckte vanliga felkonfigureringar relaterade till Azure AD DS-automatiskt och godkänts.

![Azure HDInsight Enterprise security package domänverifiering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Tidig upptäckt sparar tid genom att du kan åtgärda fel innan du skapar klustret.

![Azure HDInsight Enterprise security package det gick inte att verifiera domänen](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

När du skapar ett HDInsight-kluster med ESP måste du ange följande parametrar:

- **Kluster-administratörsanvändare**: Välj en administratör för ditt kluster från din synkroniserad Azure AD DS-. Det här kontot måste redan vara synkroniserade och är tillgängliga i Azure AD DS.

- **Kluster åtkomstgrupper**: vars användare som du vill synkronisera till klustret ska vara tillgängliga i Azure AD DS-säkerhetsgrupper. Till exempel HiveUsers grupp. Mer information finns i [skapar en grupp och Lägg till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS-URL**: ett exempel är ldaps://contoso.com:636.

Följande skärmbild visar en lyckad konfiguration i Azure portal:

![Azure HDInsight ESP Active Directory Domain Services-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Den hanterade identitet som du skapade kan väljas i listrutan användartilldelade hanterad identitet när du skapar ett nytt kluster.

![Azure HDInsight ESP Active Directory Domain Services-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Nästa steg
* Konfigurera Hive-principer och köra Hive-frågor finns i [konfigurera Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md).
* Använda SSH för att ansluta till HDInsight-kluster med hjälp av ESP, finns i [använda SSH med Linux-baserat Hadoop i HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
