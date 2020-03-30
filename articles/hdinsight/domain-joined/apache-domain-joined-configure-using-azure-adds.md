---
title: Enterprise Security med Azure AD DS – Azure HDInsight
description: Lär dig hur du konfigurerar och konfigurerar ett HDInsight Enterprise Security Package-kluster med hjälp av Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272843"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Konfigurationer av företagssäkerhetspaket med Azure Active Directory Domain Services i HDInsight

ESP-kluster (Enterprise Security Package) ger åtkomst till flera enheter i Azure HDInsight-kluster. HDInsight-kluster med ESP är anslutna till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren och köra stordatajobb.

I den här artikeln får du lära dig hur du konfigurerar ett HDInsight-kluster med ESP med hjälp av Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> ESP är allmänt tillgängligt i HDInsight 3.6 och 4.0 för dessa klustertyper: Apache Spark, Interactive, Hadoop och HBase. ESP för Apache Kafka klustertyp är i förhandsversion med bästa möjliga stöd bara. ESP-kluster som skapats före ESP GA-datumet (1 oktober 2018) stöds inte.

## <a name="enable-azure-ad-ds"></a>Aktivera Azure AD DS

> [!NOTE]  
> Endast klientadministratörer har behörighet att aktivera Azure AD DS. Om klusterlagringen är Azure Data Lake Storage Gen1 eller Gen2 måste du inaktivera Azure Multi-Factor Authentication endast för användare som behöver komma åt klustret med hjälp av grundläggande Kerberos-autentisering. 
>
> Du kan *bara* använda [betrodda IP-adresser](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) eller [villkorlig åtkomst](../../active-directory/conditional-access/overview.md) för att inaktivera multifaktorautentisering för specifika användare när de använder IP-intervallet för HDInsight-klustrets virtuella nätverk. Om du använder villkorlig åtkomst kontrollerar du att active directory-tjänstens slutpunkt är aktiverad i det virtuella HDInsight-nätverket.
>
> Om klusterlagringen är Azure Blob-lagring ska du inte inaktivera multifaktorautentisering.

Att aktivera Azure AD DS är en förutsättning innan du kan skapa ett HDInsight-kluster med ESP. Mer information finns i [Aktivera Azure Active Directory Domain Services med hjälp av Azure-portalen](../../active-directory-domain-services/tutorial-create-instance.md). 

När Azure AD DS är aktiverat börjar alla användare och objekt synkronisera från Azure Active Directory (Azure AD) till Azure AD DS som standard. Synkroniseringsåtgärdens längd beror på antalet objekt i Azure AD. Synkroniseringen kan ta några dagar för hundratusentals objekt. 

Domännamnet som du använder med Azure AD DS måste vara 39 tecken eller färre för att fungera med HDInsight.

Du kan välja att bara synkronisera de grupper som behöver åtkomst till HDInsight-kluster. Det här alternativet att synkronisera endast vissa grupper kallas *begränsad synkronisering*. Instruktioner finns i [Konfigurera begränsad synkronisering från Azure AD till din hanterade domän](../../active-directory-domain-services/scoped-synchronization.md).

När du aktiverar säker LDAP placerar du domännamnet i ämnesnamnet och ämnesalternativnamnet i certifikatet. Om ditt domännamn till exempel *är contoso100.onmicrosoft.com*kontrollerar du att det exakta namnet finns i certifikatämnesnamnet och ämnesalternativnamnet. Mer information finns i [Konfigurera säker LDAP för en Azure AD DS-hanterad domän](../../active-directory-domain-services/tutorial-configure-ldaps.md). 

I följande exempel skapas ett självsignerat certifikat. Domännamnet *contoso100.onmicrosoft.com* finns i `Subject` både (ämnesnamn) och `DnsName` (ämnesalternativnamn).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Kontrollera azure AD DS-hälsostatus
Visa hälsostatus för Azure Active Directory Domain Services genom att välja **Hälsa** i kategorin **Hantera.** Kontrollera att statusen för Azure AD DS är grön (körs) och synkroniseringen är klar.

![Azure AD DS-hälsa](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Skapa och auktorisera en hanterad identitet

Du kan använda en *användartilldelad hanterad identitet* för att förenkla och skydda domäntjänster. När du tilldelar rollen MEDARBETARE för **HDInsight Domain Services** till den hanterade identiteten kan den läsa, skapa, ändra och ta bort domäntjänster. 

Vissa domäntjänståtgärder, till exempel skapa driftsenheter och tjänsthuvudnamn, behövs för HDInsight Enterprise Security Package. Du kan skapa hanterade identiteter i valfri prenumeration. Mer information om hanterade identiteter i allmänhet finns i [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns [i Hanterade identiteter i Azure HDInsight](../hdinsight-managed-identities.md).

Om du vill konfigurera ESP-kluster skapar du en användartilldelad hanterad identitet om du inte redan har någon. Instruktioner finns i [Skapa, lista, ta bort eller tilldela en roll till en användartilldelad hanterad identitet med hjälp av Azure-portalen](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Tilldela sedan rollen **HDInsight Domain Services Contributor** till den hanterade identiteten i **åtkomstkontrollen** för Azure AD DS. Du behöver Azure AD DS-administratörsbehörighet för att kunna göra den här rolltilldelningen.

![Åtkomstkontroll för Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Genom att tilldela rollen medarbetare för **HDInsight Domain Services** säkerställer du att den här identiteten har korrekt (på uppdrag av) åtkomst för att utföra domäntjänståtgärder på Azure AD DS-domänen. Dessa åtgärder omfattar att skapa och ta bort OUs.

När den hanterade identiteten har skapats och fått rätt roll kan Azure AD DS-administratören konfigurera vem som kan använda den här hanterade identiteten. Först väljer administratören den hanterade identiteten i portalen och väljer sedan **Åtkomstkontroll (IAM)** under **Översikt**. Sedan till höger tilldelar administratören rollen **Managed Identity Operator** till de användare eller grupper som vill skapa HDInsight ESP-kluster. 

Azure AD DS-administratören kan till exempel tilldela den här rollen till **MarketingTeam-gruppen** för den **sjmsi-hanterade** identiteten, som visas i följande avbildning. Den här tilldelningen säkerställer att rätt personer i organisationen kan använda den hanterade identiteten för att skapa ESP-kluster.

![Användartilldelning av hdinsight-hanterad identitetsoperator](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Nätverksöverväganden

> [!NOTE]  
> Azure AD DS måste distribueras i ett Azure Resource Manager-baserat virtuellt nätverk. Klassiska virtuella nätverk stöds inte för Azure AD DS. Mer information finns i [Aktivera Azure Active Directory Domain Services med hjälp av Azure-portalen](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

När du har aktiverat Azure AD DS körs en DNS-server (Local Domain Name System) på virtuella datorer i Active Directory. Konfigurera ditt virtuella Azure AD DS-nätverk så att de använder dessa anpassade DNS-servrar. Om du vill hitta rätt IP-adresser väljer du **Egenskaper** i kategorin **Hantera** och tittar under **IP-adress i virtuellt nätverk**.

![Hitta IP-adresser för lokala DNS-servrar](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Ändra konfigurationen för DNS-servrarna i det virtuella Azure AD DS-nätverket så att de använder dessa anpassade IP-adresser genom att välja **DNS-servrar** i kategorin **Inställningar.** Välj sedan alternativet **Anpassad,** ange den första IP-adressen i textrutan och välj **Spara**. Lägg till fler IP-adresser med samma steg.

![Uppdatera DNS-konfigurationen för det virtuella nätverket](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Det är enklare att placera både Azure AD DS-instansen och HDInsight-klustret i samma virtuella Azure-nätverk. Om du planerar att använda olika virtuella nätverk måste du peer dessa virtuella nätverk så att domänkontrollanten är synlig för HDInsight virtuella datorer. Mer information finns i [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md). 

När de virtuella nätverken har peerats konfigurerar du det virtuella HDInsight-nätverket så att det använder en anpassad DNS-server och anger azure AD DS-privata IP-adresser som DNS-serveradresser. När båda virtuella nätverk använder samma DNS-servrar, kommer ditt anpassade domännamn att matcha till rätt IP och kommer att kunna nås från HDInsight. Om ditt domännamn till `contoso.com`exempel är , `ping contoso.com` bör du lösa till rätt Azure AD DS IP efter det här steget.

![Konfigurera anpassade DNS-servrar för ett peer-virtuellt nätverk](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Om du använder NSG-regler (Network Security Group) i undernätet HDInsight bör du tillåta de [nödvändiga IPs](../hdinsight-management-ip-addresses.md) för både inkommande och utgående trafik.

Om du vill testa om nätverket är korrekt konfigurerat ansluter du till en Virtuell Windows-dator till det virtuella HDInsight-nätverket/undernätet och pingar domännamnet. (Det bör lösa till en IP.) Kör **ldp.exe** för åtkomst till Azure AD DS-domänen. Gå sedan med den här Virtuella datorn i Windows till domänen för att bekräfta att alla nödvändiga RPC-anrop lyckas mellan klienten och servern. 

Du kan också använda **nslookup** för att bekräfta nätverksåtkomst till ditt lagringskonto eller någon extern databas som du kan använda (till exempel extern Hive-metabutik eller Ranger DB). Kontrollera att alla [nödvändiga portar](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) är tillåtna i Azure AD DS-undernätets NSG-regler, om en NSG hjälper till att skydda Azure AD DS. Om domänens anslutning till den här Virtuella Windows-datorn lyckas kan du fortsätta till nästa steg och skapa ESP-kluster.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Skapa ett HDInsight-kluster med ESP

När du har konfigurerat föregående steg korrekt är nästa steg att skapa HDInsight-klustret med ESP aktiverat. När du skapar ett HDInsight-kluster kan du aktivera Enterprise Security Package på fliken **Säkerhet + nätverk.** Om du föredrar att använda en Azure Resource Manager-mall för distribution kan du använda portalupplevelsen en gång och hämta den förifyllda mallen på sidan **Granska + skapa** för framtida återanvändning. 

Du kan också aktivera [HDInsight ID Broker-funktionen](identity-broker.md) när klustret skapas. Med funktionen ID Broker kan du logga in på Ambari med multifaktorautentisering och få de Kerberos-biljetter som krävs utan att behöva lösenordsh hashar i Azure AD DS.

> [!NOTE]  
> De första sex tecknen i ESP-klusternamnen måste vara unika i din miljö. Om du till exempel har flera ESP-kluster i olika virtuella nätverk väljer du en namngivningskonvention som säkerställer att de första sex tecknen på klusternamnen är unika.

![Domänvalidering för Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

När du har aktiverat ESP identifieras och valideras vanliga felkonfigurationer relaterade till Azure AD DS automatiskt. När du har åtgärdat dessa fel kan du fortsätta med nästa steg.

![Azure HDInsight Enterprise Security Package misslyckades domänvalidering](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

När du skapar ett HDInsight-kluster med ESP måste du ange följande parametrar:

- **Klusteradministratörsanvändare:** Välj en administratör för klustret från din synkroniserade Azure AD DS-instans. Det här domänkontot måste redan synkroniseras och vara tillgängligt i Azure AD DS.

- **Klusteråtkomstgrupper**: De säkerhetsgrupper vars användare du vill synkronisera och har åtkomst till klustret ska vara tillgängliga i Azure AD DS. Ett exempel är gruppen HiveUsers. Mer information finns i [Skapa en grupp och lägga till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL:** Ett `ldaps://contoso.com:636`exempel är .

Den hanterade identitet som du har skapat kan väljas från listrutan **Användartilldelade hanterade identitet** när du skapar ett nytt kluster.

![Azure HDInsight ESP Active Directory Domain Services hanterad identitet](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Nästa steg

* För att konfigurera Hive-principer och köra Hive-frågor finns i [Konfigurera Apache Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md).
* För att använda SSH för att ansluta till HDInsight-kluster med ESP, se [Använda SSH med Linux-baserade Apache Hadoop på HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
