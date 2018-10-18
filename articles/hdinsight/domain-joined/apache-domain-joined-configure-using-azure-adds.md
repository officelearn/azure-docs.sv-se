---
title: Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure AD-DS
description: Lär dig mer om att installera och konfigurera ett Enterprise-säkerhetspaketet för HDInsight-kluster med hjälp av Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/9/2018
ms.openlocfilehash: 851fa7c6a970d725a52bc84d7d057472e09c3ee9
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388348"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services

Enterprise Security Package (ESP)-kluster tillhandahåller flera användare åtkomst i Azure HDInsight-kluster. HDInsight-kluster med hjälp av ESP är anslutna till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med kluster och köra jobb för stordata. 

I den här artikeln får du lära dig hur du konfigurerar ett HDInsight-kluster med ESP med hjälp av Azure Active Directory Domain Services (Azure AD DS-).

>[!NOTE]
>ESP är allmänt tillgänglig i HDI 3.6 för Spark, interaktiv och Hadoop. ESP för HBase- och Kafka-kluster finns i förhandsversion.

## <a name="enable-azure-ad-ds"></a>Aktivera Azure AD DS

> [!NOTE]
> Endast klientadministratörer ha behörighet att skapa en Azure AD-DS-instans. Om klusterlagring är Azure Data Lake Store (ADLS) Gen1 och Gen2, inaktiverar du Multi-Factor Authentication (MFA) endast för användare som ska ha åtkomst till klustret. Om klusterlagring är Azure Blob Storage (WASB) kan du inte inaktivera MFA.

Aktivera AzureAD DS är en förutsättning innan du kan skapa ett HDInsight-kluster med ESP. Mer information finns i [aktivera Azure Active Directory Domain Services med Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

När Azure AD-DS har aktiverats kan alla användare och objekt synkroniserar från Azure Active Directory till Azure AD DS-som standard. Längden på synkroniseringsåtgärden beror på antalet objekt i Azure AD. Synkroniseringen kan ta ett par dagar för hundratusentals objekt. 

Kunderna kan välja att synkronisera de grupper som behöver åtkomst till HDInsight-kluster. Det här alternativet för att synkronisera bara vissa grupper kallas *begränsade synkronisering*. Se [konfigurera omfång synkronisering från Azure AD till din hanterade domän](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) anvisningar.

När du har aktiverat Azure AD-DS körs en lokal tjänst DNS (Domain Name)-server på AD-datorer (VM). Konfigurera din Azure AD DS virtuella nätverk (VNET) om du vill använda dessa anpassade DNS-servrar. För att hitta rätt IP-adresser, Välj **egenskaper** under den **hantera** kategori och titta på IP-adresser i listan under **IP-adress i virtuellt nätverk**.

![Hitta IP-adresser för lokal DNS-servrar](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Ändra konfigurationen för DNS-servrar i Azure AD DS-VNET att använda dessa anpassade IP-adresser genom att välja **DNS-servrar** under den **inställningar** kategori. Klicka på alternativknappen bredvid **anpassade**, ange den första IP-adressen i textrutan nedan och klicka på **spara**. Lägga till ytterligare IP-adresser med hjälp av samma steg.

![Uppdaterar virtuellt nätverk DNS-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)



När du aktiverar säker LDAP, placera domännamnet i ämnesnamnet eller det alternativa certifikatmottagarnamnet i certifikatet. Exempel: om ditt domännamn är *contoso.com*, kontrollera exakt samma namn finns i certifikatets ämnesnamn eller Alternativt ämnesnamn. Mer information finns i [konfigurera säkert LDAP för en Azure AD DS-domän hanterad](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="check-azure-ad-ds-health-status"></a>Kontrollera status för Azure AD-DS-hälsa
Visa hälsotillståndet för din Azure Active Directory Domain Services genom att välja **hälsotillstånd** under den **hantera** kategori. Kontrollera statusen för Azure AD DS-är grönt (körs) och synkroniseringen är klar.

![Azure Active Directory Domain Services-hälsa](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

Bör du se till att alla de [krävs portar](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) är vitlistade i AAD-DS-undernät NSG-regler om AAD-DS skyddas av en Nätverkssäkerhetsgrupp. 

## <a name="create-and-authorize-a-managed-identity"></a>Skapa och auktorisera en hanterad identitet
> [!NOTE]
> AAD-DS-administratörer har behörighet för att auktorisera den här hanterad identitet.

En **användartilldelade hanterad identitet** används för att förenkla domain services-åtgärder. När du tilldelar rollen deltagare för HDInsight Domain Services hanterad identitet den läsa, skapa, ändra och ta bort domain services-åtgärder. Vissa åtgärder, till exempel skapa organisationsenheter för domäntjänster och tjänsten principer som krävs för HDInsight Enterprise Security Package. Hanterade identiteter kan skapas i alla prenumerationer. Mer information finns i [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Skapa en hanterad Användartilldelad identitet om du vill konfigurera en hanterad identitet för användning med ESP för HDInsight-kluster om du inte redan har en. Se [skapa, lista, ta bort eller tilldela en roll till en Användartilldelad hanterad identitet med hjälp av Azure portal](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) anvisningar. Tilldela sedan den hantera identitet som den **HDInsight Domain Services deltagare** roll i Azure AD DS-åtkomstkontroll (admin-behörighet för AAD-DS krävs för att göra den här rolltilldelningen).

![Active Directory Domain Services Access control i Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Tilldela en hanterad identitet till den **HDInsight Domain Services deltagare** rollen säkerställer att identiteten har tillgång till att utföra vissa åtgärder med domain services på AAD-DS-domän.

När den hanterade identitet skapas och rätt rollen, kan AAD-DS-administratören konfigurera vem som kan använda den här hanterad identitet. Om du vill konfigurera användare för den hanterade identitet administratören bör markera den hantera identitet i portalen och klicka sedan **åtkomstkontroll (IAM)** under **översikt**. Tilldela sedan rollen ”hanterade Identitetsoperatör” till höger, till användare eller grupper som vill skapa ESP för HDInsight-kluster. AAD-DS-administratören kan till exempel tilldela den här rollen i gruppen ”MarketingTeam” ”sjmsi” hanterad identitet, enligt bilden nedan.

![HDInsight hanterad identitet operatorn rolltilldelning](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)


## <a name="create-a-hdinsight-cluster-with-esp"></a>Skapa ett HDInsight-kluster med ESP

Nästa steg är att skapa HDInsight-kluster med ESP aktiverat med Azure AD-DS.

Det är lättare att placera både Azure AD-DS-instans och HDInsight-klustret i samma Azure-nätverk. Om de finns i olika virtuella nätverk måste du peerkoppla de virtuella nätverk så att HDInsight virtuella datorer som är synliga för domänkontrollanten och kan läggas till domänen. Mer information finns i [peerkoppling av virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md). 

När de virtuella nätverken har peerkopplats kan du konfigurera HDInsight VNET att använda en anpassad DNS-server och ange Azure AD-DS privata IP-adresser som DNS-serveradresser. När båda de virtuella nätverken använder samma DNS-servrar, ditt domännamn matchar med rätt IP-Adressen och kan nås från HDInsight. Till exempel om ditt domännamn är ”contoso.com” sedan efter det här steget pinga ”contoso.com” ska matcha rätt Azure AD DS-IP. Om du vill testa om peering utförs korrekt, ansluta till en windows-dator till HDInsight VNET/undernätet och pinga domännamnet eller köra **ldp.exe** att få åtkomst till Azure AD-DS-domän. Sedan ansluta den här windows-dator till domänen för att bekräfta att alla nödvändiga RPC-anrop lyckas mellan klienten och servern.

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
