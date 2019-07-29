---
title: Skapa och konfigurera Enterprise Security Package kluster i Azure HDInsight
description: Lär dig hur du skapar och konfigurerar Enterprise Security Package kluster i Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 8da50757182609402ecb035b6f3e92959758ef46
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442299"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Skapa och konfigurera Enterprise Security Package kluster i Azure HDInsight

Med Enterprise Security Package för Azure HDInsight får du till gång till Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomst kontroll för dina Apache Hadoop-kluster i Azure. HDInsight ESP-kluster möjliggör organisationer, som följer strikta företags säkerhets principer, för att bearbeta känsliga data på ett säkert sätt.

Målet med den här guiden är att konfigurera nödvändiga resurser korrekt så att lokala användare kan logga in på ett ESP-aktiverat HDInsight-kluster. Den här artikeln vägleder dig genom de steg som krävs för att skapa ett Enterprise Security Package aktiverat Azure HDInsight-kluster. Stegen beskriver hur du skapar en virtuell Windows IaaS-dator med Active Directory & Domain Name Services (DNS) aktiverat. Den här servern fungerar som en ersättning för din **verkliga** lokala miljö och gör att du kan fortsätta med konfigurations-och konfigurations stegen så att du kan upprepa dem senare i din egen miljö. Den här guiden hjälper dig också att skapa en hybrid identitets miljö med hjälp av hash-synkronisering av lösen ord med Azure Active Directory.

Den här guiden är avsedd att komplettera [användnings Enterprise Security Package i HDInsight](apache-domain-joined-architecture.md)

Innan du använder den här processen i din egen miljö kan du konfigurera Active Directory och domän namns tjänster (DNS). Du kan också aktivera Azure Active Directory och synkronisera lokala användar konton till Azure Active Directory.

![arkitektur diagram](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Skapa en lokal miljö

Översikt: I det här avsnittet ska du använda en mall för snabb distribution av Azure för att skapa nya virtuella datorer, konfigurera Domain Name Services (DNS) och en ny AD-skog.

1. Gå till [skapa en virtuell Azure-dator med en ny AD-skog](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)för att Visa mallen för snabb distribution.

1. Klicka på **distribuera till Azure**.
1. Logga in på din Azure-prenumeration.
1. På sidan **skapa en virtuell Azure-dator med en ny AD-skog** utför du följande steg:
    1. Välj den prenumeration där du vill att resurserna ska distribueras från **prenumerations** List rutan.
    1. Välj **Skapa ny** bredvid **resurs grupp** och ange namnet **OnPremADVRG**
    1. Ange följande information för resten av mall fälten:

        * **Plats**: Centrala USA
        * **Admin-användar namn**: HDIFabrikamAdmin
        * **Administratörs lösen ord**: < YOUR_PASSWORD >
        * **Domän**: HDIFabrikam.com
        * **DNS-prefix**: hdifabrikam

        ![Skapa mall för Azure VM och AD-skog](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Klicka på **köp**
    1. Övervaka distributionen och vänta tills den är klar.
    1. Bekräfta att resurserna skapas under rätt resurs grupp `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurera användare och grupper för kluster åtkomst

Översikt: I det här avsnittet ska du skapa de användare som ska ha åtkomst till HDInsight-klustret i slutet av den här hand boken.

1. Anslut till domänkontrollanten med hjälp av fjärr skrivbord.
    1. Om du använde mallen i början är domänkontrollanten en virtuell dator som heter **adVM** i `OnPremADVRG` resurs gruppen.
    1. Gå till Azure Portal > **resurs grupper** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Klicka på fliken **RDP** och klicka sedan på **Ladda ned RDP-fil**.
    1. Spara filen på din dator och öppna den.
    1. När du uppmanas att ange `HDIFabrikam\HDIFabrikamAdmin` autentiseringsuppgifter använder du som användar namn och anger sedan det lösen ord som du valde för administratörs kontot.

1. När din fjärrskrivbordssession öppnas på den virtuella domänkontrollanten startar du **Active Directory användare och datorer** från **Serverhanteraren** instrument panelen. Klicka på **verktyg** i det övre högra hörnet och **Active Directory sedan användare och datorer** i list rutan.

    ![Serverhanteraren öppna Active Directory hantering](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Skapa två nya användare, **HDIAdmin**, **HDIUser**. Dessa två användare kommer att användas för att logga in på HDInsight-kluster.

    1. På skärmen **Active Directory användare och datorer** klickar du på **åtgärd** > **ny** > **användare**.

        ![Skapa ny Active Directory användare](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. På skärmen **nytt objekt – användare** anger `HDIUser` du som användarens **inloggnings namn** och klickar på **Nästa**.

        ![Skapa första administratörs användare](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. I popup-fönstret som visas anger du önskat lösen ord för det nya kontot. Markera kryss rutan som säger att **lösen ordet aldrig upphör att gälla**. HDIClick **OK**.
    1. Klicka på **Slutför** för att skapa det nya kontot.
    1. Skapa en annan `HDIAdmin`användare.

        ![Skapa andra administratörs användare](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. På skärmen **Active Directory användare och datorer** klickar du på **åtgärd** > **ny** > **grupp**. Skapa `HDIUserGroup` som en ny grupp.

    ![Skapa ny Active Directory grupp](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Skapa ny group2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Lägg till **HDIUser** som skapades i föregående steg till **HDIUserGroup** som medlem.

    1. Högerklicka på **HDIUserGroup** och klicka på **Egenskaper**.
    1. Gå till fliken **medlemmar** och klicka på **Lägg till**.
    1. Ange `HDIUser` **de objekt namn som ska väljas** i rutan och klicka på **OK**.
    1. Upprepa föregående steg för det andra kontot`HDIAdmin`

        ![Lägg till medlemmar i gruppen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Nu har du skapat din Active Directory-miljö, tillsammans med två användare och en användar grupp för åtkomst till HDInsight-klustret.

Dessa användare kommer att synkroniseras med Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Skapa en ny Azure Active Directory

1. Logga in på Azure Portal.
1. Klicka på **skapa en resurs** och skriv **katalogen**. Välj **Azure Active Directory** > **skapa**.
1. Ange **HDIFabrikam** under **organisations namn**.
1. Ange **HDIFabrikamoutlook** under **initialt domän namn**.
1. Klicka på **Skapa**.
1. Klicka på **Azure Active Directory**till vänster i Azure Portal.
1. Om det behövs klickar du på **Växla katalog** för att ändra till den nya katalog som du skapade **HDIFabrikamoutlook**.
1. Under **Hantera** klickar du på **anpassade domän namn** > **Lägg till anpassad domän**.
1. Ange **HDIFabrikam.com** under **anpassat domän namn** och klicka på **Lägg till domän**.

![skapa en ny Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![skapa en ny anpassad domän](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurera din Azure AD-klient

Översikt: Nu ska du konfigurera din Azure AD-klient så att du kan synkronisera användare och grupper från lokala AD till molnet.

1. Skapa en AD-innehavaradministratör.
    1. Logga in på Azure Portal och välj din Azure AD-klient **HDIFabrikam**
    1. Välj **användare** under **Hantera** och sedan **ny användare**.
    1. Ange följande information för den nya användaren:

        * Namn: fabrikamazureadmin
        * Användar namn:fabrikamazureadmin@hdifabrikam.com
        * Lösen ord: ett säkert lösen ord som du väljer

    1. Klicka på avsnittet **grupper** , Sök efter **AAD DC-administratörer**och klicka på **Välj**.

        ![Grupper](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Klicka på **katalog roll** avsnittet och välj **Global administratör** på den högra sidan. Klicka på **OK**.

        ![Katalogroll](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Ange ett lösen ord för användaren. Klicka på **Skapa**.

1. Om du vill ändra lösen ordet för den nyligen skapade användaren <fabrikamazureadmin@hdifabrikam.com>. Logga in på Azure Portal med identiteten så uppmanas du att ändra lösen ordet.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synkronisera lokala användare med Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Hämta och installera Microsoft Azure Active Directory Anslut

1. [Ladda ned Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installera Microsoft Azure Active Directory Anslut på domänkontrollanten.
    1. Öppna den körbara fil som du laddade ned i föregående steg och godkänn licens villkoren. Klicka på **Fortsätt**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Klicka på **Använd Express inställningar** och slutför installationen.

        ![Använd standardinställningar](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Konfigurera synkronisering med den lokala domänkontrollanten

1. På skärmen **Anslut till Azure AD** anger du användar namn och lösen ord för den globala administratören för Azure AD. Klicka på **Nästa**. Detta är det användar `fabrikamazureadmin@hdifabrikam.com` namn som du skapade när du konfigurerade din AD-klient.
    ![Anslut till Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. På skärmen **Anslut till Active Directory Domain Services** anger du användar namnet och lösen ordet för ett företags administratörs konto. Klicka på **Nästa**. Detta är användar namnet `HDIFabrikam\HDIFabrikamAdmin` och det matchande lösen ordet som du skapade tidigare.

   ![Anslut till Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Klicka på **Nästa**på sidan **konfiguration av Azure AD-inloggning** .
    ![Inloggnings konfiguration för Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. På skärmen redo att konfigurera klickar du på **Installera**.
    ![installationer](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. När skärmen **konfiguration är klar** visas klickar du på **Avsluta**.
    ![konfigurationen är klar](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. När synkroniseringen är klar bekräftar du om de användare som du skapade på IAAS-Active Directory synkroniseras till Azure Active Directory.
    1. Logga in på Azure Portal.
    1. Välj **Azure Active Directory** > HDIFabrikam- > **användare**.

### <a name="create-an-user-assigned-managed-identity"></a>Skapa en användardefinierad hanterad identitet

Skapa en användardefinierad hanterad identitet som ska användas för att konfigurera Azure Active Directory Domain Services (Azure AD-DS). Mer information om hur du skapar en användardefinierad hanterad identitet finns i [skapa, lista, ta bort eller tilldela en roll till en användare som tilldelats en hanterad identitet med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Logga in på Azure Portal.
1. Klicka på **skapa en resurs** och skriv **hanterad identitet**. Välj **användare tilldelad hanterad identitet** > **skapa**.
1. Ange **HDIFabrikamManagedIdentity** som **resurs namn**.
1. Välj din prenumeration.
1. Under **resurs grupp** klickar du på **Skapa nytt** och anger **HDIFabrikam-Central**.
1. Välj **centrala USA** under **plats**.
1. Klicka på **Skapa**.

![skapa en ny användare som tilldelats en hanterad identitet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Aktivera Azure Active Directory Domain Services

Mer information finns i [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Skapa Virtual Network som värd för Azure AD-DS. Kör följande PowerShell-kod.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Logga in på Azure Portal.
1. Klicka på **skapa resurs**, ange **domän tjänster** och välj **Azure AD Domain Services**.
1. Utför följande steg på skärmen **grundläggande** :
    1. Under **katalog namn** väljer du de Azure Active Directory som skapats för den här artikeln, **HDIFabrikam**.
    1. Ange ett **DNS-domännamn** för **HDIFabrikam.com**.
    1. Välj din prenumeration.
    1. Ange resurs gruppen **HDIFabrikam-Central** och **platsen** för **centrala USA**.

        ![grundläggande information om Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. På skärmen **nätverk** slutfört väljer du nätverket (**HDIFabrikam-VNet**) och under nätet (**AADDS-Subnet**) som du skapade med det tidigare PowerShell-skriptet. Eller så kan du använda alternativet **Skapa nytt** för att skapa ett virtuellt nätverk nu.

    ![Välj nätverk](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. På skärmen **Administratörs grupp** bör du se ett meddelande om att en grupp med namnet **AAD DC-administratörer** redan har skapats för att administrera den här gruppen. Du kan också ändra medlemskap i den här gruppen, men det är inte nödvändigt för stegen i den här artikeln. Klicka på **OK**.

    ![Visa administratörs grupp](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. På skärmen **synkronisering** aktiverar du slutför synkronisering genom att välja **alla** och klickar sedan på **OK**.

    ![Aktivera synkronisering](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. På sidan **Sammanfattning** kontrollerar du informationen för Azure AD-DS och klickar på **OK**.

    ![verifiera information](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. När du har aktiverat Azure AD-DS körs en lokal Domain Name Service (DNS)-server på AD-Virtual Machines (VM).

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurera ditt virtuella Azure AD-DS-nätverk

Stegen i det här avsnittet hjälper dig att konfigurera Azure AD-DS Virtual Network (**HDIFabrikam-AADDSVNET**) att använda dina anpassade DNS-servrar.

1. Leta upp IP-adresserna för dina anpassade DNS-servrar. Klicka på **HDIFabrikam.com** AD-DS-resursen, klicka på **Egenskaper** under **Hantera**   och titta på IP-adresserna som visas under **IP-adress på Virtual Network**.

    ![Hitta anpassade DNS IP-adresser för Azure AD-DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Konfigurera **HDIFabrikam-AADDSVNET** till anpassade IP `10.0.0.4` - `10.0.0.5`adresser och.

    1. Välj **DNS-servrar** under kategorin **Inställningar** . Klicka sedan på alternativ knappen bredvid **anpassad**, ange den första IP-adressen (10.0.0.4) i text rutan nedan och klicka på **Spara**.
    1. Lägg till ytterligare IP-adresser (10.0.0.5) med samma steg.

1. I vårt scenario har Azure AD-DS kon figurer ATS för att använda IP-adresser 10.0.0.4 och 10.0.0.5, ange samma IP-adress på AADDS VNet som visas i bilden nedan.

    ![Visa anpassade DNS-servrar](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Skydda LDAP-trafik

LDAP (Lightweight Directory Access Protocol) används för att läsa från och skriva till Active Directory. Du kan göra LDAP-trafik konfidentiell och säker med hjälp av Secure Sockets Layer (SSL)/Transport Layer Security (TLS). Du kan aktivera LDAP över SSL (LDAPs) genom att installera ett korrekt formaterat certifikat.

Mer information om säker LDAP finns i [Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services hanterad domän](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

I det här avsnittet skapar du ett självsignerat certifikat, laddar ned certifikatet och konfigurerar säkert LDAP (LDAPs) för den **hdifabrikam** Azure AD-DS-hanterade domänen.

Följande skript skapar ett certifikat för hdifabrikam. Certifikatet sparas under sökvägen "LocalMachine".

> [!Note] 
> Alla verktyg eller program som skapar en giltig PKCS \#10-begäran kan användas för att forma begäran om SSL-certifikat.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Kontrol lera att certifikatet är installerat i datorns\'personliga arkiv. Utför följande steg:

1. Starta Microsoft Management Console (MMC).
1. Lägg till snapin-modulen certifikat som hanterar certifikat på den lokala datorn.
1. Expandera **certifikat (lokal dator)** , expandera **personliga**och expandera sedan **certifikat**. Ett nytt certifikat bör finnas i det personliga arkivet. Certifikatet utfärdas till det fullständigt kvalificerade värd namnet.

    ![verifiera att certifikatet skapas](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. Högerklicka på det certifikat som du skapade i föregående steg i den högra rutan, peka på **alla aktiviteter**och klicka sedan på **Exportera**.

1. På sidan **Exportera privat nyckel** klickar du på **Ja, exportera den privata nyckeln**. Den privata nyckeln krävs för att de krypterade meddelandena ska kunna läsas från den dator där nyckeln ska importeras.

    ![Exportera privat nyckel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. På sidan **fil format** för export lämnar du standardinställningarna och klickar sedan på **Nästa**. 
1. På sidan **lösen ord** skriver du ett lösen ord för den privata nyckeln, väljer **TripleDES-SHA1** för **kryptering** och klickar på **Nästa**.
1. På sidan **fil som ska exporteras** anger du sökvägen till och namnet på den exporterade certifikat filen och klickar sedan på **Nästa**.
1. Fil namnet måste vara. pfx-tillägget, den här filen är konfigurerad på Azure Portal för att upprätta en säker anslutning.
1. Aktivera säker LDAP (LDAPs) för en Azure AD Domain Services hanterad domän.
    1. Välj domänen **HDIFabrikam.com** från Azure Portal.
    1. Klicka på **säkert LDAP** under **Hantera**.
    1. På **säkert LDAP** skärmen klickar du på **Aktivera** under **säkert LDAP**.
    1. Bläddra efter. PFX-certifikat filen som du exporterade på datorn.
    1. Ange certifikatets lösen ord.

    ![Aktivera säker LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Nu när du har aktiverat säkert LDAP kontrollerar du att det går att komma åt genom att aktivera port 636.
    1. Klicka på nätverks säkerhets gruppen **AADDS-HDIFabrikam.com-NSG** i resurs gruppen **HDIFabrikam-centrala** .
    1. Under **Inställningar** klickar du på **inkommande säkerhets regler** > **Lägg till**.
    1. På skärmen **Lägg till inkommande säkerhets regel** anger du följande egenskaper och klickar på **Lägg till**:

        | Egenskap | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Mål | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number\> |
        | Namn | Port_LDAP_636 |

    ![inkommande säkerhets regel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`är den användare som tilldelats hanterad identitet, rollen HDInsight Domain Services Contributor är aktive rad för den hanterade identitet som gör att den här identiteten kan läsa, skapa, ändra och ta bort Domain Services-åtgärder.

    ![Skapa användare tilldelad hanterad identitet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Skapa Enterprise Security Package aktiverat HDInsight-kluster

Det här steget kräver följande krav:

1. Skapa en ny resurs grupp `HDIFabrikam-WestUS` på platsen. `West US`
1. Skapa ett virtuellt nätverk som ska vara värd för ESP-aktiverat HDInsight-kluster.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Skapa en peer-relation mellan Virtual Network som är värd för AADDS`HDIFabrikam-AADDSVNET`() och Virtual Network som ska vara värd för det ESP-aktiverade`HDIFabrikam-HDIVNet`HDInsight-klustret (). Använd följande PowerShell-kod för att peer-koppla dessa två virtuella nätverk.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Skapa ett nytt Azure Data Lake Storage Gen2-konto, **Hdigen2store**, som har kon figurer ATS med **HDIFabrikamManagedIdentity**för användarens hanterade identiteter. Mer information om hur du skapar Data Lake Storage Gen2 konton som är aktiverade med hanterade identiteter i användare finns i [använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Konfigurera anpassad DNS på det virtuella **HDIFabrikam-AADDSVNET-** nätverket.
    1. Gå till Azure Portal > **resurs grupper** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-servrar**.
    1. Välj **anpassad** och ange `10.0.0.4` och `10.0.0.5`.
    1. Klicka på **Spara**.

        ![Spara anpassade DNS-inställningar](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Skapa ett nytt ESP-aktiverat HDInsight Spark-kluster.
    1. Klicka på **Anpassad (storlek, inställningar, appar)** .
    2. Ange önskad information för **grunderna**i avsnitt 1. Se till att **kluster typen** är **Spark 2,3 (HDI 3,6)** och att **resurs gruppen** är **HDIFabrikam-centrala**

    1. Utför följande steg under avsnitt 2 **säkerhet + nätverk**:
        1. Klicka på **aktive rad** under **Enterprise Security Package**.
        1. Klicka på **kluster administratörs användare** och välj det **HDIAdmin** -konto som du skapade tidigare som den lokala administratörs användaren. Klicka på **Välj**.

        1. Klicka på **kluster åtkomst grupp** och välj sedan **HDIUserGroup**. Alla användare som du lägger till i den här gruppen i framtiden kommer att kunna komma åt HDInsight-kluster.

            ![Välj kluster åtkomst grupp](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Slutför de andra stegen i kluster konfigurationen och kontrol lera informationen i **kluster sammanfattningen**. Klicka på **Skapa**.

1. Logga in på Ambari-användargränssnittet för det nyskapade klustret `https://CLUSTERNAME.azurehdinsight.net` med hjälp av administratörs användar `hdiadmin@hdifabrikam.com` namn och lösen ord.

    ![Logga in på Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Klicka på **roller** från kluster instrument panelen.
1. På sidan **roller** anger du gruppen **hdiusergroup** för att tilldela den till rollen **kluster administratör** under **tilldela roller till dessa**.

    ![tilldela rollen kluster administratör till hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Öppna SSH-klienten och logga in på klustret med hjälp av **hdiuser** som du skapade tidigare i den lokala Active Directory.

    ![Logga in på kluster med SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Om du kan logga in med det här kontot har du konfigurerat ditt ESP-kluster på ett korrekt sätt för att synkronisera med din lokala Active Directory.

## <a name="next-steps"></a>Nästa steg

* [En introduktion till Apache Hadoop-säkerhet med Enterprise Security Package](hdinsight-security-overview.md)
