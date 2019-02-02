---
title: Enterprise Security Package-konfiguration med hjälp av Azure Active Directory Domain Services - Azure HDInsight
description: Lär dig mer om att installera och konfigurera ett Enterprise-säkerhetspaketet för HDInsight-kluster med hjälp av Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/09/2018
ms.custom: seodec18
ms.openlocfilehash: b6cc65d10fc8924686d01c02177a9cb76f7a9571
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660926"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurera ett HDInsight-kluster med Enterprise Security-paket med hjälp av Azure Active Directory Domain Services

Enterprise Security Package (ESP)-kluster tillhandahåller flera användare åtkomst i Azure HDInsight-kluster. HDInsight-kluster med hjälp av ESP är anslutna till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med kluster och köra jobb för stordata. 

I den här artikeln får du lära dig hur du konfigurerar ett HDInsight-kluster med ESP med hjälp av Azure Active Directory Domain Services (Azure AD DS-).

>[!NOTE]  
>ESP är allmänt tillgänglig i HDI 3.6 för Apache Spark, interaktiv och Apache Hadoop. ESP för Apache HBase- och Apache Kafka-kluster finns i förhandsversion.

## <a name="enable-azure-ad-ds"></a>Aktivera Azure AD DS

> [!NOTE]  
> Endast klientadministratörer ha behörighet att aktivera Azure AD-DS. Om klusterlagring är Azure Data Lake Storage (ADLS) Gen1 och Gen2 måste du inaktivera Multi-Factor Authentication (MFA) för användare som behöver åtkomst till klustret med grundläggande Kerberose autentiseringar. Du kan använda [tillförlitliga IP-adresser](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#trusted-ips) eller [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) att inaktivera MFA för specifika användare endast när de ansluter till HDInsight-klustret VNET IP-intervall. Om du använder villkorlig åtkomst kontrollerar du att den AD-tjänstslutpunkten i aktiverad på HDInsight VNET.
>
>Om klusterlagring är Azure Blob Storage (WASB) kan du inte inaktivera MFA.



Aktivera AzureAD DS är en förutsättning innan du kan skapa ett HDInsight-kluster med ESP. Mer information finns i [aktivera Azure Active Directory Domain Services med Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

När Azure AD-DS har aktiverats kan alla användare och objekt synkroniserar från Azure Active Directory (AAD) till Azure AD DS-som standard. Längden på synkroniseringsåtgärden beror på antalet objekt i Azure AD. Synkroniseringen kan ta ett par dagar för hundratusentals objekt. 

Kunderna kan välja att synkronisera de grupper som behöver åtkomst till HDInsight-kluster. Det här alternativet för att synkronisera bara vissa grupper kallas *begränsade synkronisering*. Se [konfigurera omfång synkronisering från Azure AD till din hanterade domän](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) anvisningar.

När du aktiverar säker LDAP, placera domännamnet i ämnesnamnet och det alternativa certifikatmottagarnamnet i certifikatet. Exempel: om ditt domännamn är *contoso100.onmicrosoft.com*, kontrollera exakt samma namn finns i certifikatets ämnesnamn och Alternativt ämnesnamn. Mer information finns i [konfigurera säkert LDAP för en Azure AD DS-domän hanterad](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md). Nedan visas ett exempel på hur du skapar ett självsignerat certifikat och har domännamnet (*contoso100.onmicrosoft.com*) i både ämnesnamn och DNS-namn (Alternativt ämnesnamn):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
``` 

## <a name="check-azure-ad-ds-health-status"></a>Kontrollera status för Azure AD-DS-hälsa
Visa hälsotillståndet för din Azure Active Directory Domain Services genom att välja **hälsotillstånd** under den **hantera** kategori. Kontrollera statusen för Azure AD DS-är grönt (körs) och synkroniseringen är klar.

![Azure Active Directory Domain Services health](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Skapa och auktorisera en hanterad identitet

En **användartilldelade hanterad identitet** används för att förenkla och säkra domain services-åtgärder. När du tilldelar rollen deltagare för HDInsight Domain Services till den hanterade identitet den läsa, skapa, ändra och ta bort domain services-åtgärder. Vissa åtgärder, till exempel skapa organisationsenheter för domäntjänster och tjänsten principer som krävs för HDInsight Enterprise Security Package. Hanterade identiteter kan skapas i alla prenumerationer. Mer information finns i [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Skapa en hanterad Användartilldelad identitet om du vill konfigurera ESP kluster om du inte redan har en. Se [skapa, lista, ta bort eller tilldela en roll till en Användartilldelad hanterad identitet med hjälp av Azure portal](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) anvisningar. Tilldela sedan den **HDInsight Domain Services deltagare** rollen till den hanterade identitet i Azure AD DS-åtkomstkontroll (admin-behörighet för AAD-DS krävs för att göra den här rolltilldelningen).

![Active Directory Domain Services Access control i Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Tilldela den **HDInsight Domain Services deltagare** rollen ser till att den här identiteten har rätt åtkomst att utföra domain services-åtgärder som skapar organisationsenheter, tar bort organisationsenheter, etc. på AAD-DS-domän (för).

När den hanterade identitet skapas och rätt rollen, kan AAD-DS-administratören konfigurera vem som kan använda den här hanterad identitet. Om du vill konfigurera användare för den hanterade identitet administratören bör markera den hantera identitet i portalen och klicka sedan **åtkomstkontroll (IAM)** under **översikt**. Tilldela sedan till höger i **hanterade Identitetsoperatör** roll till användare eller grupper som vill skapa ESP för HDInsight-kluster. AAD-DS-administratören kan till exempel tilldela den här rollen i gruppen ”MarketingTeam” ”sjmsi” hanterad identitet, enligt bilden nedan. Det säkerställer att rätt personer i organisationen har åtkomst till att använda den här hanterad identitet i syfte att skapa ESP-kluster.

![HDInsight hanterad identitet operatorn rolltilldelning](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Nätverksöverväganden

> [!NOTE]  
> Azure AD DS måste distribueras i ett Azure Resource Manager (ARM) baserat virtuellt nätverk. Klassiska virtuella nätverk stöds inte för Azure AD DS. Se [aktivera Azure Active Directory Domain Services med Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-network) för mer information.

När du har aktiverat Azure AD-DS körs en lokal tjänst DNS (Domain Name)-server på AD-datorer (VM). Konfigurera din Azure AD DS virtuella nätverk (VNET) om du vill använda dessa anpassade DNS-servrar. För att hitta rätt IP-adresser, Välj **egenskaper** under den **hantera** kategori och titta på IP-adresser i listan under **IP-adress i virtuellt nätverk**.

![Hitta IP-adresser för lokal DNS-servrar](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Ändra konfigurationen för DNS-servrar i Azure AD DS-VNET att använda dessa anpassade IP-adresser genom att välja **DNS-servrar** under den **inställningar** kategori. Klicka på alternativknappen bredvid **anpassade**, ange den första IP-adressen i textrutan nedan och klicka på **spara**. Lägga till ytterligare IP-adresser med hjälp av samma steg.

![Uppdaterar virtuellt nätverk DNS-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Det är lättare att placera både Azure AD-DS-instans och HDInsight-klustret i samma Azure-nätverk. Om du planerar att använda olika virtuella nätverk måste du peerkoppla de virtuella nätverk så att domänkontrollanten är synlig för HDI-VMs. Mer information finns i [peerkoppling av virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md). 

När de virtuella nätverken har peerkopplats kan du konfigurera HDInsight VNET att använda en anpassad DNS-server och ange Azure AD-DS privata IP-adresser som DNS-serveradresser. När båda de virtuella nätverken använder samma DNS-servrar, ditt domännamn matchar med rätt IP-Adressen och kan nås från HDInsight. Till exempel om ditt domännamn är ”contoso.com” sedan efter det här steget pinga ”contoso.com” ska matcha rätt Azure AD DS-IP. 

![Konfigurera anpassade DNS-servrar för peer-kopplade virtuella nätverket](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Om du använder regler för Nätverkssäkerhetsgrupper (NSG) i ditt HDInsight-undernät, bör du tillåta de [krävs för IP-adresser](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#hdinsight-ip-1) för både inkommande och utgående trafik. 

**Att testa** om ditt nätverk är korrekt konfigurerad, ansluta till en windows-dator till HDInsight VNET/undernätet och pinga domännamnet (det ska matcha en IP-adress) och kör sedan **ldp.exe** att få åtkomst till Azure AD-DS-domän. Sedan **ansluta till den här windows-dator till domänen för att bekräfta** att alla nödvändiga RPC-anrop lyckas mellan klienten och servern. Du kan också använda **nslookup** att bekräfta åtkomst till ditt storage-konto eller någon extern DB som du kan använda (till exempel externa Hive-metaarkiv eller Ranger DB).
Bör du se till att alla de [krävs portar](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) är vitlistade i AAD-DS-undernät nätverkssäkerhetsgruppsregler, om AAD-DS skyddas av en NSG. Om domänanslutning av den här windows VM lyckas kan du gå vidare till nästa steg och skapa ESP-kluster.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Skapa ett HDInsight-kluster med ESP

Efter det att de föregående stegen korrekt, är nästa steg att skapa HDInsight-kluster med ESP aktiverat. När du skapar ett HDInsight-kluster kan du aktivera Enterprise Security Package i den **anpassade** fliken. Om du föredrar att använda en Azure Resource Manager-mall för distribution, använda en Portal en gång och ladda ned mallen fylls i automatiskt på den sista sidan ”Översikt” för senare användning.

![Azure HDInsight Enterprise security package domänverifiering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

När du har aktiverat ESP upptäckte vanliga felkonfigureringar relaterade till Azure AD DS-automatiskt och godkänts. Du kan fortsätta med nästa steg när du har korrigerat felen: 

![Azure HDInsight Enterprise security package det gick inte att verifiera domänen](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

När du skapar ett HDInsight-kluster med ESP måste du ange följande parametrar:

- **Kluster-administratörsanvändare**: Välj en administratör för ditt kluster från din synkroniserad Azure AD DS. Det här domänkontot måste redan vara synkroniserad och är tillgängliga i Azure AD DS.

- **Kluster åtkomstgrupper**: Säkerhetsgrupper vars användare som du vill synkronisera och har åtkomst till klustret ska vara tillgängliga i Azure AD DS. Till exempel HiveUsers grupp. Mer information finns i [skapar en grupp och Lägg till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS-URL: EN**: Ett exempel är ldaps://contoso.com:636.

Följande skärmbild visar en lyckad konfiguration i Azure portal:

![Azure HDInsight ESP Active Directory Domain Services-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Den hanterade identitet som du skapade kan väljas i listrutan användartilldelade hanterad identitet när du skapar ett nytt kluster.

![Azure HDInsight ESP Active Directory Domain Services-konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Nästa steg
* Konfigurera Hive-principer och köra Hive-frågor finns i [konfigurerar Apache Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md).
* Använda SSH för att ansluta till HDInsight-kluster med hjälp av ESP, finns i [använda SSH med Linux-baserade Apache Hadoop på HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
