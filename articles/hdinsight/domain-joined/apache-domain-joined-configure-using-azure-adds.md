---
title: Enterprise Security Package med Azure Active Directory i HDInsight
description: Lär dig hur du konfigurerar och konfigurerar ett HDInsight-Enterprise Security Package kluster med Azure Active Directory Domain Services.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: aa18c4a078edf579e8d9c4c09df99100dfcea148
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918311"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Enterprise Security Package konfigurationer med Azure Active Directory Domain Services i HDInsight

Enterprise Security Package (ESP)-kluster ger åtkomst till flera användare på Azure HDInsight-kluster. HDInsight-kluster med ESP är anslutna till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren och köra stora data jobb.

I den här artikeln får du lära dig hur du konfigurerar ett HDInsight-kluster med ESP genom att använda Azure Active Directory Domain Services (Azure AD-DS).

> [!NOTE]  
> ESP är allmänt tillgängligt i HDInsight 3,6 och 4,0 för kluster typer: Apache Spark, Interactive, Hadoop och HBase. ESP för Apache Kafka kluster typ är i för hands version med stöd för bästa prestanda. ESP-kluster som skapats före ESP GA-datum (1 oktober 2018) stöds inte.

## <a name="enable-azure-ad-ds"></a>Aktivera Azure AD-DS

> [!NOTE]  
> Endast klient organisations administratörer har behörighet att aktivera Azure AD-DS. Om kluster lagringen är Azure Data Lake Storage (ADLS) gen1 eller Gen2 måste du inaktivera Multi-Factor Authentication (MFA) för användare som behöver åtkomst till klustret med hjälp av grundläggande Kerberos-autentiseringar. Du kan använda [betrodda IP-adresser](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) eller [villkorlig åtkomst](../../active-directory/conditional-access/overview.md) för att inaktivera MFA för vissa användare endast när de ansluter till HDInsight-klustrets VNet-IP-intervall. Om du använder villkorlig åtkomst ser du till att AD service-slutpunkten är aktive rad i HDInsight-VNET.
>
> Om kluster lagringen är Azure Blob Storage (WASB) ska du inte inaktivera MFA.

Aktivering av AzureAD-DS är en förutsättning innan du kan skapa ett HDInsight-kluster med ESP. Mer information finns i [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md). 

När Azure AD-DS är aktive rad börjar alla användare och objekt att synkronisera från Azure Active Directory (AAD) till Azure AD-DS som standard. Längden på synkroniseringsåtgärden beror på antalet objekt i Azure AD. Synkroniseringen kan ta några dagar i hundratals tusentals objekt. 

Domän namnet som du använder med Azure AD-DS måste vara 39 tecken eller mindre för att fungera med HDInsight.

Du kan välja att bara synkronisera de grupper som behöver åtkomst till HDInsight-kluster. Det här alternativet för att synkronisera endast vissa grupper kallas *omsynkronisering av omfång*. Instruktioner finns i [Konfigurera Omfångst synkronisering från Azure AD till din hanterade domän](../../active-directory-domain-services/scoped-synchronization.md) .

När du aktiverar säker LDAP ska du lägga till domän namnet i ämnes namnet och det alternativa ämnes namnet i certifikatet. Om ditt domän namn till exempel är *contoso100.onmicrosoft.com*, se till att det exakta namnet finns i certifikat mottagar namnet och alternativt namn för certifikat mottagare. Mer information finns i [Konfigurera säker LDAP för en Azure AD-DS-hanterad domän](../../active-directory-domain-services/tutorial-configure-ldaps.md). Nedan visas ett exempel på hur du skapar ett självsignerat certifikat och har domän namnet (*contoso100.onmicrosoft.com*) i både ämnes namn och DnsName (alternativt ämnes namn):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Kontrol lera Azure AD-DS hälso status
Visa hälso status för din Azure Active Directory Domain Services genom att välja **hälsa** under kategorin **Hantera** . Kontrol lera att statusen för Azure AD-DS är grön (körs) och att synkroniseringen är klar.

![Azure Active Directory Domain Services hälsa](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Skapa och auktorisera en hanterad identitet

En **användardefinierad hanterad identitet** används för att förenkla och säkra åtgärder för domän tjänster. När du tilldelar rollen HDInsight Domain Services Contributor till den hanterade identiteten kan den läsa, skapa, ändra och ta bort Domain Services-åtgärder. Vissa åtgärder för domän tjänster, till exempel skapa organisationsenheter och tjänst huvud namn krävs för HDInsight-Enterprise Security Package. Hanterade identiteter kan skapas i vilken prenumeration som helst. Mer information om hanterade identiteter i allmänhet finns i [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](../hdinsight-managed-identities.md).

Om du vill konfigurera ESP-kluster skapar du en användardefinierad hanterad identitet om du inte redan har en. Se [skapa, Visa, ta bort eller tilldela en roll till en användardefinierad hanterad identitet med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) för instruktioner. Sedan tilldelar du rollen **HDInsight Domain Services Contributor** till den hanterade identiteten i Azure AD-DS-åtkomst kontroll (AAD-DS Admin-behörighet) som krävs för att utföra den här roll tilldelningen.

![Azure Active Directory Domain Services åtkomst kontroll](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Genom att tilldela rollen **HDInsight Domain Services Contributor** ser du till att den här identiteten har rätt (på uppdrag av) åtkomst för att utföra domän tjänsters åtgärder, till exempel skapa organisationsenheter, ta bort organisationsenheter osv. på AAD-DS-domänen.

När den hanterade identiteten har skapats och fått rätt roll kan AAD-DS-administratören konfigurera vem som kan använda den här hanterade identiteten. Om du vill konfigurera användare för den hanterade identiteten bör administratören välja den hanterade identiteten i portalen och sedan klicka på **Access Control (IAM)** under **Översikt**. Tilldela sedan rollen **hanterad identitets operatör** till de användare eller grupper som vill skapa HDInsight ESP-kluster till höger. AAD-DS-administratören kan till exempel tilldela den här rollen till **MarketingTeam** -gruppen för **sjmsi** -hanterade identiteter som visas i följande bild. Detta säkerställer att rätt personer i organisationen har åtkomst att använda den här hanterade identiteten i syfte att skapa ESP-kluster.

![HDInsight-hanterad identitets operatör roll tilldelning](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Nätverksöverväganden

> [!NOTE]  
> Azure AD-DS måste distribueras i ett Azure Resource Manager-baserat vNET. Klassiska virtuella nätverk stöds inte för Azure AD-DS. Mer information finns i [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md#create-and-configure-the-virtual-network) .

När du har aktiverat Azure AD-DS körs en lokal Domain Name Service (DNS)-server på AD-Virtual Machines (VM). Konfigurera Azure AD-DS-Virtual Network (VNET) så att de använder dessa anpassade DNS-servrar. Om du vill hitta rätt IP-adresser väljer du **Egenskaper** under kategorin **Hantera** och tittar på IP-adresserna under **IP-adressen på Virtual Network**.

![Hitta IP-adresser för lokala DNS-servrar](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Ändra konfigurationen för DNS-servrarna i Azure AD-DS VNET för att använda de här anpassade IP-adresserna genom att välja **DNS-servrar** under kategorin **Inställningar** . Klicka sedan på alternativ knappen bredvid **anpassad**, ange den första IP-adressen i text rutan nedan och klicka på **Spara**. Lägg till ytterligare IP-adresser med samma steg.

![Uppdaterar VNET DNS-konfigurationen](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Det är enklare att placera både Azure AD-DS-instansen och HDInsight-klustret i samma virtuella Azure-nätverk. Om du planerar att använda olika virtuella nätverk måste du peer-koppla dessa virtuella nätverk så att domänkontrollanten är synlig för virtuella HDI-datorer. Mer information finns i peering för [virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md). 

När virtuella nätverk har peer-kopplats konfigurerar du HDInsight VNET för att använda en anpassad DNS-server och anger de privata IP-adresserna för Azure AD-DS som DNS-serveradresser. När båda virtuella nätverk använder samma DNS-servrar kommer ditt anpassade domän namn att matchas till rätt IP-adress och kan komma att bli tillgängligt från HDInsight. Om ditt domän namn till exempel är `contoso.com` efter det här steget, `ping contoso.com` bör du matcha till rätt Azure AD-DS IP.

![Konfigurera anpassade DNS-servrar för peer-kopplat VNET](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Om du använder regler för nätverks säkerhets grupper (NSG) i ditt HDInsight-undernät, bör du tillåta de [IP-adresser som krävs](../hdinsight-management-ip-addresses.md) för både inkommande och utgående trafik. 

Om du **vill testa** om nätverket är korrekt konfigurerat ansluter du en virtuell Windows-dator till HDInsight VNet/undernät och pingar domän namnet (det bör matcha till en IP-adress) och kör sedan **Ldp. exe** för att få åtkomst till Azure AD-DS-domänen. **Anslut sedan den här virtuella Windows-datorn till domänen för att bekräfta** att alla nödvändiga RPC-anrop lyckas mellan klienten och servern. Du kan också använda **nslookup** för att bekräfta nätverks åtkomst till ditt lagrings konto eller en extern databas som du kan använda (till exempel externa Hive-metaarkiv eller Ranger dB).
Du bör se till att alla [nödvändiga portar](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) är vit listas i säkerhets grupp reglerna för AAD-DS-undernät, om AAD-DS skyddas av en NSG. Om domänen som ansluter till den här virtuella Windows-datorn lyckas kan du fortsätta till nästa steg och skapa ESP-kluster.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Skapa ett HDInsight-kluster med ESP

När du har konfigurerat de föregående stegen korrekt är nästa steg att skapa HDInsight-klustret med ESP aktiverat. När du skapar ett HDInsight-kluster kan du aktivera Enterprise Security Package på fliken **anpassad** . Om du föredrar att använda en Azure Resource Manager mall för distribution ska du använda Portal upplevelsen en gång och hämta den ifyllda mallen på den sista sidan Sammanfattning för framtida åter användning.

> [!NOTE]  
> De första sex tecknen i ESP-klustrets namn måste vara unika i din miljö. Om du till exempel har flera ESP-kluster i olika virtuella nätverk bör du välja ett namn convension som säkerställer att de första sex tecknen i kluster namnen är unika.

![Azure HDInsight Enterprise säkerhets paket domän verifiering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

När du har aktiverat ESP identifieras och verifieras vanliga felkonfigurationer som är relaterade till Azure AD-DS automatiskt. När du har åtgärdat felen kan du fortsätta med nästa steg: 

![Azure HDInsight Enterprise säkerhets paket misslyckades med domän verifiering](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

När du skapar ett HDInsight-kluster med ESP måste du ange följande parametrar:

- **Kluster administratörs användare**: Välj en administratör för ditt kluster från din synkroniserade Azure AD-DS. Det här domän kontot måste redan vara synkroniserat och tillgängligt i Azure AD-DS.

- **Kluster åtkomst grupper**: De säkerhets grupper vars användare du vill synkronisera och har åtkomst till klustret ska vara tillgängliga i Azure AD-DS. Till exempel HiveUsers Group. Mer information finns i [skapa en grupp och lägga till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS-URL**: Ett exempel är `ldaps://contoso.com:636`.

Följande skärm bild visar en lyckad konfiguration i Azure Portal:

![Azure HDInsight ESP Active Directory Domain Services konfiguration](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Den hanterade identitet som du har skapat kan väljas i list rutan användarens tilldelade hanterade identitet när du skapar ett nytt kluster.

![Azure HDInsight ESP Active Directory Domain Services hanterad identitet](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).

## <a name="next-steps"></a>Nästa steg

* Om du vill konfigurera Hive-principer och köra Hive-frågor, se [konfigurera Apache Hive principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md).
* Information om hur du använder SSH för att ansluta till HDInsight-kluster med ESP finns i [använda SSH med Linux-baserade Apache Hadoop på HDInsight från Linux, UNIX eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
