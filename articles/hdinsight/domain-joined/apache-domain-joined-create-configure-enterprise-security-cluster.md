---
title: Skapa och konfigurera Enterprise Security Package-kluster i Azure HDInsight
description: Lär dig hur du skapar och konfigurerar Enterprise Security Package-kluster i Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: e9cb9a902cf60fbd3b297a72a7dfa836ee18c835
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484590"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Skapa och konfigurera Enterprise Security Package-kluster i Azure HDInsight

Enterprise-säkerhetspaketet för Azure HDInsight ger dig åtkomst till Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll för dina Apache Hadoop-kluster i Azure. ESP för HDInsight-kluster kan organisationer som följer strikta företagets säkerhetsprinciper, att bearbeta känsliga data på ett säkert sätt.

Målet med den här guiden är korrekt konfigurera nödvändiga resurser så att den lokala användare kan logga in på en ESP aktiverad HDInsight-kluster. Den här artikeln beskriver de steg som krävs för att skapa ett Azure HDInsight-kluster för Enterprise Security Package aktiverat. Stegen beskriver hur du skapar en virtuell dator IaaS Windows med Active Directory & Services DNS (Domain Name) aktiverat. Den här servern ska fungera som en ersättning för din **faktiska** i lokala miljöer och gör att du kan gå igenom stegen för installation och konfiguration så att du kan upprepa dem senare i din egen miljö. Den här guiden beskriver också hur du skapar en hybridmiljö identitet med hjälp av lösenordshashsynkronisering med Azure Active Directory.

Den här guiden är avsedd att komplettera [Använd Enterprise Security Package i HDInsight](apache-domain-joined-architecture.md)

Konfigurera Active Directory och (Domain Name Services) innan du använder den här processen i din egen miljö. Dessutom aktivera Azure Active Directory och synkronisera lokala användarkonton till Azure Active Directory.

![Arkitekturdiagram](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Skapa en lokal miljö

Översikt: I det här avsnittet ska du använda en mall för snabbdistribution av Azure att skapa nya virtuella datorer, konfigurera (Domain Name Services) och en ny AD-skog.

1. Gå till [skapa en Azure-dator med en ny AD-skog](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), för att visa mallen för snabb distribution.

1. Klicka på **distribuera till Azure**.
1. Logga in på Azure-prenumerationen.
1. På den **skapa en Azure-dator med en ny AD-skog** skärmen, gör du följande:
    1. Välj den prenumeration där du vill att de resurser som distribueras från den **prenumeration** listrutan.
    1. Välj **Skapa nytt** bredvid **resursgrupp** och ange namnet **OnPremADVRG**
    1. Ange följande information för resten av mall-fält:

        * **Plats**: Centrala USA
        * **Administratörens användarnamn**: HDIFabrikamAdmin
        * **Adminlösenord**: < YOUR_PASSWORD >
        * **Domän**: HDIFabrikam.com
        * **DNS-Prefix**: hdifabrikam

        ![Mallen skapar virtuell Azure-dator och AD-skog](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Klicka på **köp**
    1. Övervaka distributionen och vänta tills den är klar.
    1. Bekräfta resurserna som skapas under rätt resursgrupp `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurera användare och grupper för åtkomst till klustret

Översikt: I det här avsnittet skapar du de användare som har åtkomst till HDInsight-kluster i slutet av den här guiden.

1. Ansluta till domänkontrollanten med hjälp av fjärrskrivbord.
    1. Om du har använt mallar som vi nämnde i början domänkontrollanten är en virtuell dator som kallas **adVM** i den `OnPremADVRG` resursgrupp.
    1. Gå till Azure portal > **resursgrupper** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Klicka på den **RDP** fliken och klicka sedan på **ladda ned RDP-filen**.
    1. Spara filen på din dator och öppna den.
    1. När du tillfrågas om autentiseringsuppgifter, använda `HDIFabrikam\HDIFabrikamAdmin` som användarnamn och sedan ange lösenordet som du valde för administratörskontot.

1. När fjärrskrivbordssessionen öppnas på domänkontrollerns VM, startar **Active Directory-användare och datorer** från den **Serverhanteraren** instrumentpanelen. Klicka på **verktyg** i det övre högra hörnet och sedan **Active Directory-användare och datorer** i listrutan.

    ![Serverhanteraren öppen Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Skapa två nya användare, **HDIAdmin**, **HDIUser**. Dessa användare kommer att användas för att logga in på HDInsight-kluster.

    1. I den **Active Directory-användare och datorer** klickar du på **åtgärd** > **New** > **användaren**.

        ![Skapa ny Active Directory-användare](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. I den **nytt objekt – användare** anger `HDIUser` som den **användarens inloggningsnamn** och klicka på **nästa**.

        ![Skapa första administratörsanvändare](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. I popup-fönstret som visas anger du önskat lösenord för det nya kontot. Markera rutan **lösenordet upphör aldrig att gälla**. HDIClick **OK**.
    1. Klicka på **Slutför** att skapa det nya kontot.
    1. Skapa en annan användare `HDIAdmin`.

        ![Skapa andra administratörsanvändare](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. I den **Active Directory-användare och datorer** klickar du på **åtgärd** > **New** > **grupp**. Skapa `HDIUserGroup` som en ny grupp.

    ![Skapa ny Active Directory-grupp](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Skapa ny grupp2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Lägg till den **HDIUser** skapade i föregående steg för att den **HDIUserGroup** som medlem.

    1. Högerklicka på den **HDIUserGroup** och klicka på **egenskaper**.
    1. Gå till **medlemmar** fliken och klicka på **Lägg till**.
    1. Ange `HDIUser` i rutan **ange objektnamn att välja** och klicka på **OK**.
    1. Upprepa föregående steg för det andra kontot `HDIAdmin`

        ![Lägg till medlemmar till gruppen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Nu har du skapat din Active Directory-miljö, tillsammans med två användare och en användargrupp för åtkomst till HDInsight-klustret.

Dessa användare kommer att synkroniseras med Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Skapa en ny Azure Active Directory

1. Logga in på Azure Portal.
1. Klicka på **skapa en resurs** och skriv **directory**. Välj **Azure Active Directory** > **skapa**.
1. Ange **HDIFabrikam** under **organisationsnamn**.
1. Ange **HDIFabrikamoutlook** under **inledande domännamnet**.
1. Klicka på **Skapa**.
1. Till vänster i Azure-portalen klickar du på **Azure Active Directory**.
1. Om det behövs klickar du på **växla katalog** att ändra till den nya katalogen som du skapade **HDIFabrikamoutlook**.
1. Under **hantera** klickar du på **anpassade domännamn** > **Lägg till anpassad domän**.
1. Ange **HDIFabrikam.com** under **anpassade domännamn** och klicka på **Lägg till domän**.

![Skapa en ny azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Skapa en ny anpassad domän](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurera Azure AD-klienten

Översikt: Nu konfigurerar du Azure AD-klienten så att du kan synkronisera användare och grupper från den lokala AD till molnet.

1. Skapa en AD-klientadministratör.
    1. Logga in på Azure Portal och välj din Azure AD-klient **HDIFabrikam**
    1. Välj **användare** under **hantera** och sedan **ny användare**.
    1. Ange följande information för den nya användaren:

        * Namn: fabrikamazureadmin
        * Användarnamn: fabrikamazureadmin@hdifabrikam.com
        * Lösenord: ett säkert lösenord för serveradministratören

    1. Klicka på den **grupper** avsnittet, Sök efter **AAD DC-administratörer**, och klicka på **Välj**.

        ![Grupper](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Klicka på den **katalogroll** och väljer **Global administratör** högra sida. Klicka på **OK**.

        ![Katalogroll](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Ange ett lösenord för användaren. Klicka på **Skapa**.

1. Om du vill ändra lösenordet för den nyligen skapade användaren <fabrikamazureadmin@hdifabrikam.com>. Logga in på Azure portal med identiteten och sedan kommer du uppmanas att ändra lösenordet.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synkronisera lokala användare till Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Ladda ned och installera Microsoft Azure Active Directory connect

1. [Hämta Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installera Microsoft Azure Active Directory connect på domänkontrollanten.
    1. Öppna den körbara filen som du hämtade i föregående steg och godkänner du licensvillkoren. Klicka på **Fortsätt**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Klicka på **Använd standardinställningar** och slutföra installationen.

        ![Använd standardinställningar](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Konfigurera synkronisering med lokala domänkontrollant

1. På den **Anslut till Azure AD** anger användarnamn och lösenord för global administratör för Azure AD. Klicka på **nästa**. Det här är användarnamnet `fabrikamazureadmin@hdifabrikam.com` som du skapade när du konfigurerar din AD-klient.
    ![Anslut till Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. På den **Anslut till Active Directory Domain Services** anger användarnamnet och lösenordet för ett företagsadministratörskonto. Klicka på **nästa**. Det här är användarnamnet `HDIFabrikam\HDIFabrikamAdmin` och dess motsvarande lösenord som du skapade tidigare.

   ![Ansluta till Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. På den **inloggningskonfiguration för Azure AD** klickar du på **nästa**.
    ![Azure AD-inloggningen konfiguration](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. På sidan klart att konfigurera skärmen, klickar du på **installera**.
    ![Installera](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. När den **Configuration slutföra** skärm som visas, klickar du på **avsluta**.
    ![Slutför konfiguration](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. När synkroniseringen är klar kontrollerar du om de användare som du skapade på IAAS Active Directory synkroniseras till Azure Active Directory.
    1. Logga in på Azure Portal.
    1. Välj **Azure Active Directory** > **HDIFabrikam** > **användare**.

### <a name="create-an-user-assigned-managed-identity"></a>Skapa en hanterad Användartilldelad identitet

Skapa en Användartilldelad hanterade identitet som används för att konfigurera Azure Active Directory Domain Services (Azure AD DS-). Läs mer om hur du skapar en hanterad Användartilldelad identitet, [skapa, lista, ta bort eller tilldela en roll till en Användartilldelad hanterad identitet med hjälp av Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Logga in på Azure Portal.
1. Klicka på **skapa en resurs** och skriv **hanterad identitet**. Välj **användaren tilldelats hanterad identitet** > **skapa**.
1. Ange **HDIFabrikamManagedIdentity** som den **resursnamn**.
1. Välj din prenumeration.
1. Under **resursgrupp** klickar du på **Skapa nytt** och ange **HDIFabrikam CentralUS**.
1. Välj **centrala USA** under **plats**.
1. Klicka på **Skapa**.

![Skapa en ny hanterad användartilldelade-identitet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Aktivera Azure Active Directory Domain Services

Mer information finns i [aktivera Azure Active Directory Domain Services med Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Skapa virtuellt nätverk till Azure AD-DS-värd. Kör följande powershell-kod.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Logga in på Azure Portal.
1. Klicka på **skapa resurs**, ange **domäntjänster** och välj **Azure AD Domain Services**.
1. På den **grunderna** skärmen gör du följande:
    1. Under **katalognamnet** väljer Azure Active Directory som skapats i den här artikeln **HDIFabrikam**.
    1. Ange en **DNS-domännamn** av **HDIFabrikam.com**.
    1. Välj din prenumeration.
    1. Ange resursgrupp **HDIFabrikam CentralUS** och **plats** av **centrala USA**.

        ![Azure ad ds-grundläggande information](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. På den **nätverk** klar, väljer nätverket (**HDIFabrikam VNET**) och undernätet (**AADDS undernät**) som du skapade med föregående PowerShell.skript. Du kan också använda den **Skapa nytt** alternativet för att skapa ett virtuellt nätverk nu.

    ![Välj nätverk](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. På den **administratörsgruppen** skärmen, bör du se ett meddelande som en grupp med namnet **AAD DC-administratörer** har redan skapats för att administrera den här gruppen. Du kan ändra medlemskap i den här gruppen, men det krävs inte för stegen i den här artikeln. Klicka på **OK**.

    ![Visa administratörsgruppen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. På den **synkronisering** skärmen, aktivera fullständig synkronisering genom att välja **alla** och klicka sedan på **OK**.

    ![Aktivera synkronisering](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. På den **sammanfattning** skärmen, och kontrollera information för Azure AD-DS och klicka på **Ok**.

    ![Kontrollera informationen om](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. När du har aktiverat Azure AD-DS körs en lokal tjänst DNS (Domain Name)-server på AD-datorer (VM).

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurera ditt virtuella nätverk i Azure AD-DS

Stegen i det här avsnittet hjälper dig att konfigurera ditt virtuella nätverk i Azure AD-DS (**HDIFabrikam AADDSVNET**) att använda din anpassade DNS-servrar.

1. Leta upp IP-adresserna för din anpassade DNS-servrar. Klicka på den **HDIFabrikam.com** AD DS-resurs, klickar du på **egenskaper** under **hantera**   och titta på de IP-adresser som anges **IP Adress i virtuellt nätverk**.

    ![Leta upp anpassade DNS-IP-adresser för Azure AD DS-](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Konfigurera **HDIFabrikam AADDSVNET** till anpassad IP-adresser `10.0.0.4` och `10.0.0.5`.

    1. Välj **DNS-servrar** under den **inställningar** kategori. Klicka på alternativknappen bredvid **anpassade**, ange den första IP-adressen (10.0.0.4) i textrutan nedan och klicka på **spara**.
    1. Lägga till ytterligare IP-adresser (10.0.0.5) med hjälp av samma steg.

1. I vårt scenario är att Azure AD-DS har konfigurerats för IP-adresser 10.0.0.4 och 10.0.0.5, ställa in samma IP-adress för AADDS virtuella nätverk som visas i bilden nedan.

    ![Visa anpassade dns-servrar](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Skydda LDAP-trafik

Lightweight Directory Access Protocol (LDAP) används för att läsa från och skriva till Active Directory. Du kan göra LDAP trafik konfidentiell och säker med hjälp av Secure Sockets Layer (SSL) / Transport Layer Security (TLS)-teknik. Du kan aktivera LDAP via SSL (LDAPS) genom att installera en korrekt formaterad certifikat.

Mer information om säker LDAP finns i [konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services en domän hanterad](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

I det här avsnittet ska du skapar ett självsignerat certifikat, hämta certifikatet och konfigurerar säker LDAP (LDAPS) för den **hdifabrikam** hanterad av Azure AD-DS.

Följande skript skapar ett certifikat för hdifabrikam. Certifikatet sparas under sökvägen ”LocalMachine”.

> [!Note] 
> Alla verktyg eller program som skapar en giltig PKCS \#10 begäran kan användas för att bilda en begäran för SSL-certifikat.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Kontrollera att certifikatet är installerat i datorn\'s personliga arkivet. Utför följande steg:

1. Starta Microsoft Management Console (MMC).
1. Lägga till snapin-modulen för certifikat, som hanterar certifikat på den lokala datorn.
1. Expandera **certifikat (lokal dator)** , expandera **personliga**, och expandera sedan **certifikat**. Ett nytt certifikat ska finnas i det personliga arkivet. Det här certifikatet är utfärdat till fullständigt kvalificerat värdnamn.

    ![Kontrollera skapandet av certifikat](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. I mittenfönstret högerklickar du på det certifikat som du skapade i föregående steg, peka på **alla uppgifter**, och klicka sedan på **exportera**.

1. På den **Exportera privat nyckel** klickar du på **Ja, exportera den privata nyckeln**. Den privata nyckeln måste anges för de krypterade meddelandena som ska läsas från den dator där nyckeln kommer att importeras.

    ![Exportera privat nyckel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. På den **filformat för Export** sidan lämnar du standardinställningarna och klicka sedan på **nästa**. 
1. På den **lösenord** anger du ett lösenord för den privata nyckeln väljer **TripleDES SHA1** för **kryptering** och klicka på **nästa**.
1. På den **fil som ska exporteras** sidan, anger du sökvägen och namnet för den exporterade certifikatfilen och klicka sedan på **nästa**.
1. Filnamnet måste vara .pfx-tillägget, den här filen har konfigurerats på Azure portal för att upprätta säker anslutning.
1. Aktivera säkert LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän.
    1. Välj domänen som **HDIFabrikam.com** från Azure-portalen.
    1. Klicka på **säkert LDAP** under **hantera**.
    1. På den **säker LDAP** klickar du på **aktivera** under **säker LDAP**.
    1. Sök efter den PFX-fil som du exporterade på datorn.
    1. Ange lösenordet för certifikatet.

    ![Aktivera säkert ldap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Nu när du har aktiverat säker LDAP kan du kontrollera att den kan nås genom att aktivera port 636.
    1. Klicka på den nya nätverkssäkerhetsgruppen **AADDS-HDIFabrikam.com-NSG** i den **HDIFabrikam CentralUS** resursgrupp.
    1. Under **inställningar** klickar du på **ingående säkerhetsregler** > **Lägg till**.
    1. På den **Lägg till ingående säkerhetsregel** skärmen, ange följande egenskaper och klicka på **Lägg till**:

        | Egenskap | Värde |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Mål | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Prioritet | <Desired Number> |
        | Name | Port_LDAP_636 |

    ![ingående säkerhetsregel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` är den Användartilldelad hanterad identitet, deltagarrollen för HDInsight Domain Services aktiveras för att den hanterade identitet som gör att den här identiteten att läsa, skapa, ändra och ta bort domain services-åtgärder.

    ![Skapa användare som tilldelats hanterad identitet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Skapa Enterprise Security Package aktiverat HDInsight-kluster

Det här steget kräver följande förutsättningar:

1. Skapa en ny resursgrupp `HDIFabrikam-WestUS` på plats `West US`.
1. Skapa ett virtuellt nätverk som är värd för ESP aktiverat HDInsight-kluster.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Skapa en peer-relation mellan det virtuella nätverket som är värd för AADDS (`HDIFabrikam-AADDSVNET`) och det virtuella nätverket som är värd för ESP aktiverat HDInsight-kluster (`HDIFabrikam-HDIVNet`). Använd följande powershell-kod för att peer-koppla dessa två virtuella nätverk.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Skapa ett nytt konto i Azure Data Lake Storage Gen2 **Hdigen2store**, som konfigurerats med identiteten som hanteras av användare **HDIFabrikamManagedIdentity**. Läs mer om hur du skapar Gen2 för Data Lake Storage-konton som aktiverats med användaren hanterade identiteter i [Använd Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Konfigurera anpassad DNS på den **HDIFabrikam AADDSVNET** virtuellt nätverk.
    1. Gå till Azure portal > **resursgrupper** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >   **DNS-servrar**.
    1. Välj **anpassade** och ange `10.0.0.4` och `10.0.0.5`.
    1. Klicka på **Spara**.

        ![Spara anpassade dns-inställningar](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Skapa ett nytt ESP-aktiverade HDInsight Spark-kluster.
    1. Klicka på **anpassad (storlek, inställningar, appar)** .
    2. Ange önskad information för avsnitt 1 **grunderna**. Se till att den **Klustertyp** är **Spark 2.3 (HDI 3.6)** och **resursgrupp** är **HDIFabrikam CentralUS**

    1. Under avsnittet 2 **säkerhet + nätverk**, gör du följande:
        1. Klicka på **aktiverat** under **Enterprise Security Package**.
        1. Klicka på **kluster administratörsanvändare** och välj den **HDIAdmin** konto som du skapade tidigare som en lokal administratörsanvändare. Klicka på **Välj**.

        1. Klicka på **åtkomst klustergrupp** och välj sedan **HDIUserGroup**. Alla användare som du lägger till den här gruppen i framtiden kommer att kunna komma åt HDInsight-kluster.

            ![Välj klustergrupp för åtkomst](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Slutför steg av klusterkonfigurationen och kontrollera informationen på den **klustersammanfattning**. Klicka på **Skapa**.

1. Logga in på Ambari UI för det nya klustret på `https://CLUSTERNAME.azurehdinsight.net` med ditt administratörsanvändarnamn `hdiadmin@hdifabrikam.com` och lösenord.

    ![logga in på Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Klicka på **roller** från instrumentpanelen för klustret.
1. På den **roller** anger gruppen **hdiusergroup** att tilldela den till den **Klusteradministratör** rollen under **tilldela roller till dessa**.

    ![tilldela administratörsroll för klustret till hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Öppna SSH-klient och logga in till klustret med den **hdiuser** som du skapade tidigare i den lokala Active Directory.

    ![logga in på klustret med SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Om du kan logga in med det här kontot har du ställt in ESP-klustret på rätt sätt för att synkronisera med din lokala active directory.

## <a name="next-steps"></a>Nästa steg

* [En introduktion till Apache Hadoop-säkerhet med Enterprise Security Package](apache-domain-joined-introduction.md)
