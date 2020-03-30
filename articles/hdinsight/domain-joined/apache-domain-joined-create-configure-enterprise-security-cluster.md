---
title: Skapa, konfigurera kluster för enterprise security-paket – Azure
description: Lär dig hur du skapar och konfigurerar Enterprise Security Package-kluster i Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 4edafc0c07e967acfabf7fdc5b58c481b2cfccc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436049"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Skapa och konfigurera företagssäkerhetspaketkluster i Azure HDInsight

Esp (Enterprise Security Package) för Azure HDInsight ger dig tillgång till Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll för dina Apache Hadoop-kluster i Azure. HDInsight ESP-kluster gör det möjligt för organisationer som följer strikta säkerhetsprinciper för företag att behandla känsliga data på ett säkert sätt.

Den här guiden visar hur du skapar ett ESP-aktiverat Azure HDInsight-kluster. Den visar också hur du skapar en Windows IaaS VM där Active Directory och Domain Name System (DNS) är aktiverade. Använd den här guiden för att konfigurera nödvändiga resurser så att lokala användare kan logga in på ett ESP-aktiverat HDInsight-kluster.

Den server du skapar fungerar som en ersättning för din *faktiska* lokala miljö. Du ska använda den för installations- och konfigurationsstegen. Senare upprepar du stegen i din egen miljö.

Den här guiden hjälper dig också att skapa en hybrididentitetsmiljö med hjälp av lösenordshã¤ndeksynkronisering med Azure Active Directory (Azure AD). Guiden kompletterar [Använd ESP i HDInsight](apache-domain-joined-architecture.md).

Innan du använder den här processen i din egen miljö:

* Konfigurera Active Directory och DNS.
* Aktivera Azure AD.
* Synkronisera lokala användarkonton till Azure AD.

![Azure AD-arkitekturdiagram](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Skapa en lokal miljö

I det här avsnittet ska du använda en Azure Quickstart-distributionsmall för att skapa nya virtuella datorer, konfigurera DNS och lägga till en ny Active Directory-skog.

1. Gå till snabbstartsdistributionsmallen för att [skapa en virtuell Azure-dator med en ny Active Directory-skog](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Välj **Distribuera till Azure**.
1. Logga in på din Azure-prenumeration.
1. Ange följande information på sidan **Skapa en virtuell Azure-dator med en ny AD-skog:**

    |Egenskap | Värde |
    |---|---|
    |Prenumeration|Välj den prenumeration där du vill distribuera resurserna.|
    |Resursgrupp|Välj **Skapa ny**och ange namnet`OnPremADVRG`|
    |Location|Välj en plats.|
    |Administratörsanvändarnamn|`HDIFabrikamAdmin`|
    |Adminlösenord|Ange ett lösenord.|
    |Domännamn|`HDIFabrikam.com`|
    |Dns-prefix|`hdifabrikam`|

    Lämna de återstående standardvärdena.

    ![Mall för Skapa en virtuell Azure-dator med en ny Azure AD-skog](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Gå igenom **villkoren**och välj sedan **att jag godkänner de villkor som anges ovan.**
1. Välj **Inköp**och övervaka distributionen och vänta på att den ska slutföras. Distributionen tar cirka 30 minuter att slutföra.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurera användare och grupper för klusteråtkomst

I det här avsnittet ska du skapa de användare som ska ha åtkomst till HDInsight-klustret i slutet av den här guiden.

1. Anslut till domänkontrollanten med fjärrskrivbord.
    1. Från Azure-portalen navigerar du till **resursgrupperna** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Välj den offentliga IP-adressen i listrutan **IP-adress.**
    1. Välj **Hämta RDP-fil**och öppna sedan filen.
    1. Använd `HDIFabrikam\HDIFabrikamAdmin` som användarnamn.
    1. Ange det lösenord som du valde för administratörskontot.
    1. Välj **OK**.

1. Gå till **Verktyg** > För Active Directory – användare och datorer på instrumentpanelen **i Serverhanteraren** för domänkontrollant.**Active Directory Users and Computers**

    ![Öppna Active Directory Management på instrumentpanelen i Serverhanteraren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Skapa två nya användare: **HDIAdmin** och **HDIUser**. Dessa två användare loggar in på HDInsight-kluster.

    1. Högerklicka på `HDIFabrikam.com`sidan **Användare och datorer i Active Directory** och navigera sedan till **Ny** > **användare**.

        ![Skapa en ny Active Directory-användare](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. På sidan Nytt objekt - `HDIUser` **Användare** anger du för **förnamn** och **Användarnamn**. De andra fälten fyller i automatiskt. Välj sedan **Nästa**.

        ![Skapa det första administratörsanvändarobjektet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Ange ett lösenord för det nya kontot i popup-fönstret som visas. Välj **Lösenord upphör aldrig att gälla**och sedan **OK** i popup-meddelandet.
    1. Välj **Nästa**och **slutför** sedan för att skapa det nya kontot.
    1. Upprepa ovanstående steg för `HDIAdmin`att skapa användaren .

        ![Skapa ett andra administratörsanvändarobjekt](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Skapa en global säkerhetsgrupp.

    1. Högerklicka `HDIFabrikam.com`på **Användare och datorer i Active Directory**och navigera sedan till **Ny** > **grupp**.

    1. Skriv `HDIUserGroup` in i textrutan **Gruppnamn.**

    1. Välj **OK**.

    ![Skapa en ny Active Directory-grupp](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Skapa ett nytt objekt](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Lägg till medlemmar **i HDUSerGroup**.

    1. Högerklicka på **HDIUser** och välj **Lägg till i en grupp...**.
    1. Ange i rutan **Ange de objektnamn som ska** `HDIUserGroup`markeras. Välj sedan **OK**och **OK** igen i popup-programmet.
    1. Upprepa föregående steg för **HDIAdmin-kontot.**

        ![Lägg till medlems-HDIUser i gruppen HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Nu har du skapat din Active Directory-miljö. Du har lagt till två användare och en användargrupp som kan komma åt HDInsight-klustret.

Användarna synkroniseras med Azure AD.

### <a name="create-an-azure-ad-directory"></a>Skapa en Azure AD-katalog

1. Logga in på Azure Portal.
1. Välj **Skapa en** `directory`resurs och skriv . Välj **Azure Active Directory** > **Create**.
1. Under **Organisationsnamn** `HDIFabrikam`anger du .
1. Under **Ursprungligt**domännamn `HDIFabrikamoutlook`anger du .
1. Välj **Skapa**.

    ![Skapa en Azure AD-katalog](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Skapa en anpassad domän

1. Välj **Anpassade domännamn** **i**din nya Azure **Active Directory**.
1. Välj **+ Lägg till anpassad domän**.
1. Under **Anpassat domännamn** `HDIFabrikam.com`anger du och väljer sedan **Lägg till domän**.
1. Fyll sedan [i Lägg till din DNS-information i domänregistratorern](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Skapa en anpassad domän](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Skapa en grupp

1. Välj **Grupper**i din nya **Azure Active Directory**under **Hantera**.
1. Välj **+ Ny grupp**.
1. Ange `AAD DC Administrators`i textrutan **gruppnamn** .
1. Välj **Skapa**.

## <a name="configure-your-azure-ad-tenant"></a>Konfigurera din Azure AD-klient

Nu ska du konfigurera din Azure AD-klientorganisation så att du kan synkronisera användare och grupper från den lokala Active Directory-instansen till molnet.

Skapa en Active Directory-klientadministratör.

1. Logga in på Azure-portalen och välj din Azure AD-klient, **HDIFabrikam**.

1. Navigera till **Hantera** > **användare** > **Ny användare**.

1. Ange följande information för den nya användaren:

    **Identitet**

    |Egenskap |Beskrivning |
    |---|---|
    |Användarnamn|Skriv `fabrikamazureadmin` in i textrutan. Välj listrutan domännamn i listrutan domännamn`hdifabrikam.com`|
    |Namn| Ange `fabrikamazureadmin`.|

    **Lösenord**
    1. Välj **Låt mig skapa lösenordet**.
    1. Ange ett säkert lösenord som du väljer.

    **Grupper och roller**
    1. Markera **0 markerade grupper**.
    1. Välj **AAD DC-administratörer**och **välj**sedan .

    ![Dialogrutan Azure AD-grupper](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Välj **Användare**.
    1. Välj **Global administratör**och **välj**sedan .

    ![Dialogrutan Azure AD-roll](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Välj **Skapa**.

1. Låt sedan den nya användaren logga in på Azure-portalen där den uppmanas att ändra lösenordet. Du måste göra detta innan du konfigurerar Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synkronisera lokala användare med Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Konfigurera Microsoft Azure Active Directory Connect

1. Hämta Microsoft Azure [Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)från domänkontrollanten .

1. Öppna den körbara filen som du hämtade och godkänn licensvillkoren. Välj **Fortsätt**.

1. Välj **Använd standardinställningar**.

1. På sidan **Anslut till Azure AD** anger du användarnamn och lösenord för den globala administratören för Azure AD. Använd `fabrikamazureadmin@hdifabrikam.com` användarnamnet som du skapade när du konfigurerade Active Directory-klienten. Välj sedan **Nästa**.

    ![Sidan Anslut till Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. På sidan **Anslut till Active Directory Domain Services** anger du användarnamn och lösenord för ett företagsadministratörskonto. Använd användarnamnet `HDIFabrikam\HDIFabrikamAdmin` och lösenordet som du skapade tidigare. Välj sedan **Nästa**.

   ![Sidan Anslut till Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. På **sidan Azure AD-inloggningskonfiguration** väljer du **Nästa**.
   ![Sidan "Azure AD-inloggningskonfiguration"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. På sidan **Klar att konfigurera** väljer du **Installera**.

   ![Sidan "Redo att konfigurera"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. På sidan **Konfigurationens klara** väljer du **Avsluta**.
   ![Sidan "Konfigurationen klar"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. När synkroniseringen är klar bekräftar du att de användare som du skapade på IaaS-katalogen synkroniseras med Azure AD.
   1. Logga in på Azure Portal.
   1. Välj **Azure Active Directory** > **HDIFabrikam-användare** > **Users**.

### <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användartilldelad hanterad identitet som du kan använda för att konfigurera Azure AD Domain Services (Azure AD DS). Mer information finns i [Skapa, lista, ta bort eller tilldela en roll till en användartilldelad hanterad identitet med hjälp av Azure-portalen](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Logga in på Azure Portal.
1. Välj **Skapa en** `managed identity`resurs och skriv . Välj **Användartilldelade skapa hanterade identitet** > **Create**.
1. För **resursnamnet**anger `HDIFabrikamManagedIdentity`du .
1. Välj din prenumeration.
1. Under **Resursgrupp**väljer du `HDIFabrikam-CentralUS`Skapa **nytt** och anger .
1. Under **Plats**väljer du **Centrala USA**.
1. Välj **Skapa**.

![Skapa en ny användartilldelad hanterad identitet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Aktivera Azure AD DS

Följ dessa steg för att aktivera Azure AD DS. Mer information finns i [Aktivera Azure AD DS med hjälp av Azure-portalen](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Skapa ett virtuellt nätverk som är värd för Azure AD DS. Kör följande PowerShell-kod.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Logga in på Azure Portal.
1. Välj **Skapa resurs,** `Domain services`ange och välj**Skapa**Azure AD **Domain Services** > .
1. På sidan **Grunderna:**
    1. Under **Katalognamn**väljer du den Azure AD-katalog som du skapade: **HDIFabrikam**.
    1. För **DNS-domännamn**anger du *HDIFabrikam.com*.
    1. Välj din prenumeration.
    1. Ange resursgruppen **HDIFabrikam-CentralUS**. För **Plats**väljer du **Centrala USA**.

        ![Grundläggande information om Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. På **sidan Nätverk** väljer du det nätverk **(HDIFabrikam-VNET**) och undernätet **(AADDS-undernätet)** som du skapade med powershell-skriptet. Eller välj **Skapa ny** för att skapa ett virtuellt nätverk nu.

    ![Steget "Skapa virtuellt nätverk"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. På sidan **Administratörsgrupp** bör du se ett meddelande om att en grupp med namnet **AAD DC-administratörer** redan har skapats för att administrera den här gruppen. Du kan ändra medlemskapet i den här gruppen om du vill, men i det här fallet behöver du inte ändra den. Välj **OK**.

    ![Visa administratörsgruppen För Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Aktivera fullständig synkronisering på sidan **Synkronisering** genom att välja **Alla** > **OK**.

    ![Aktivera Azure AD DS-synkronisering](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. På sidan **Sammanfattning** verifierar du informationen för Azure AD DS och väljer **OK**.

    ![Sammanfattningen av "Aktivera Azure AD-domäntjänster"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

När du har aktiverat Azure AD DS körs en lokal DNS-server på virtuella Azure AD-datorer.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurera ditt virtuella Azure AD DS-nätverk

Följ följande steg för att konfigurera ditt virtuella Azure AD DS-nätverk **(HDIFabrikam-AADDSVNET)** för att använda dina anpassade DNS-servrar.

1. Leta reda på IP-adresserna för dina anpassade DNS-servrar.
    1. Välj `HDIFabrikam.com` Azure AD DS-resursen.
    1. Välj **Egenskaper**under **Hantera**.
    1. Hitta IP-adresserna under **IP-adress i virtuellt nätverk**.

    ![Hitta anpassade DNS-IP-adresser för Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Konfigurera **HDIFabrikam-AADDSVNET** för att använda anpassade IP-adresser 10.0.0.4 och 10.0.0.5.

    1. Under **Inställningar**väljer du **DNS-servrar**.
    1. Välj **anpassad**.
    1. I textrutan anger du den första IP-adressen (*10.0.0.4*).
    1. Välj **Spara**.
    1. Upprepa stegen för att lägga till den andra IP-adressen (*10.0.0.5*).

I vårt scenario har vi konfigurerat Azure AD DS för att använda IP-adresser 10.0.0.4 och 10.0.0.5, med samma IP-adress i azure AD DS virtuella nätverk:

![Sidan anpassade DNS-servrar](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Säkra LDAP-trafik

LDAP (Lightweight Directory Access Protocol) används för att läsa från och skriva till Azure Active Directory. Du kan göra LDAP-trafik konfidentiell och säker med hjälp av SSL-teknik (Secure Sockets Layer) eller TLS-teknik (Transport Layer Security). Du kan aktivera LDAP över SSL (LDAPS) genom att installera ett korrekt formaterat certifikat.

Mer information om säker LDAP finns i [Konfigurera LDAPS för en Azure AD DS-hanterad domän](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

I det här avsnittet skapar du ett självsignerat certifikat, hämtar certifikatet och konfigurerar LDAPS för **den HDIFabrikam** Azure AD DS-hanterade domänen.

Följande skript skapar ett certifikat för **HDIFabrikam**. Certifikatet sparas i sökvägen *LocalMachine.*

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Alla verktyg eller program som skapar en giltig PKCS-begäran (Public Key Cryptography Standards) \#10 kan användas för att bilda SSL-certifikatbegäran.

Kontrollera att certifikatet är installerat i datorns **personliga** arkiv:

1. Starta Microsoft Management Console (MMC).
1. Lägg till snapin-modulen **Certifikat** som hanterar certifikat på den lokala datorn.
1. Expandera**personliga** > **Certificates** **certifikat (lokal dator).** >  Ett nytt certifikat ska finnas i det **personliga** arkivet. Det här certifikatet utfärdas till det fullständigt kvalificerade värdnamnet.

    ![Verifiera att lokala certifikat skapas](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Högerklicka på certifikatet som du skapade i fönstret till höger. Peka **på Alla aktiviteter**och välj sedan **Exportera**.

1. På sidan **Exportera privat nyckel** väljer du **Ja, exporterar den privata nyckeln**. Datorn där nyckeln importeras behöver den privata nyckeln för att läsa de krypterade meddelandena.

    ![Sidan Exportera privat nyckel i guiden Exportera certifikat](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Lämna standardinställningarna på sidan **Exportera filformat** och välj sedan **Nästa**.
1. Skriv ett lösenord för den privata nyckeln på sidan **Lösenord.** För **kryptering**väljer du **TripleDES-SHA1**. Välj sedan **Nästa**.
1. På sidan **Fil att exportera** skriver du sökvägen och namnet på den exporterade certifikatfilen och väljer sedan **Nästa**. Filnamnet måste ha ett PFX-tillägg. Den här filen är konfigurerad i Azure-portalen för att upprätta en säker anslutning.
1. Aktivera LDAPS för en Azure AD DS-hanterad domän.
    1. Välj domänen `HDIFabrikam.com`på Azure-portalen .
    1. Under **Hantera**väljer du **Säker LDAP**.
    1. Välj **Aktivera**under **Säker LDAP**på sidan Säker **LDAP.**
    1. Bläddra efter pfx-certifikatfilen som du exporterade på datorn.
    1. Ange certifikatlösenordet.

    ![Aktivera säker LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Nu när du har aktiverat LDAPS kontrollerar du att den kan nås genom att aktivera port 636.
    1. I resursgruppen **HDIFabrikam-CentralUS** väljer du nätverkssäkerhetsgruppen **AADDS-HDIFabrikam.com-NSG**.
    1. Under **Inställningar**väljer du **Inställningar för säkerhetsregler** > **Lägg till**.
    1. På sidan **Lägg till inkommande säkerhetsregel** anger du följande egenskaper och väljer **Lägg till:**

        | Egenskap | Värde |
        |---|---|
        | Källa | Alla |
        | Källportintervall | * |
        | Mål | Alla |
        | Målportintervall | 636 |
        | Protokoll | Alla |
        | Åtgärd | Tillåt |
        | Prioritet | \<Önskat tal> |
        | Namn | Port_LDAP_636 |

    ![Dialogrutan "Lägg till inkommande säkerhetsregel"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** är den användartilldelade hanterade identiteten. Rollen HDInsight Domain Services Contributor är aktiverad för den hanterade identiteten som gör att den här identiteten kan läsa, skapa, ändra och ta bort domäntjänster.

![Skapa en användartilldelad hanterad identitet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Skapa ett ESP-aktiverat HDInsight-kluster

Det här steget kräver följande förutsättningar:

1. Skapa en ny resursgrupp *HDIFabrikam-WestUS* på platsen **Västra USA.**
1. Skapa ett virtuellt nätverk som är värd för ESP-aktiverade HDInsight-kluster.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Skapa en peer-relation mellan det virtuella nätverket`HDIFabrikam-AADDSVNET`som är värd för Azure AD DS (`HDIFabrikam-HDIVNet`) och det virtuella nätverk som ska vara värd för ESP-kompatibelt HDInsight-kluster ( ). Använd följande PowerShell-kod för att peer de två virtuella nätverken.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Skapa ett nytt Azure Data Lake Storage Gen2-konto som heter **Hdigen2store**. Konfigurera kontot med den användarhanterade identiteten **HDIFabrikamManagedIdentity**. Mer information finns i [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Konfigurera anpassad DNS på det virtuella nätverket **HDIFabrikam-AADDSVNET.**
    1. Gå till Azure-portalen > **resursgrupper** > **påPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-servrar**.
    1. Välj **Anpassad** och ange *10.0.0.4* och *10.0.0.5*.
    1. Välj **Spara**.

        ![Spara anpassade DNS-inställningar för ett virtuellt nätverk](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Skapa ett nytt ESP-aktiverat HDInsight Spark-kluster.
    1. Välj **Anpassad (storlek, inställningar, appar)**.
    1. Ange information för **Grunderna** (avsnitt 1). Kontrollera att **klustertypen** är **Spark 2.3 (HDI 3.6)**. Kontrollera att **resursgruppen** är **HDIFabrikam-CentralUS**.

    1. För **säkerhet + nätverk** (avsnitt 2) fyll i följande uppgifter:
        * Under **Enterprise Security Package**väljer du **Aktiverad**.
        * Välj **Användare av klusteradministratörer** och välj det **HDIAdmin-konto** som du skapade som lokal administratörsanvändare. Klicka på **Markera**.
        * Välj **arbetsgrupp för klusteråtkomstgrupp** > **HDIUserGroup**. Alla användare som du lägger till i den här gruppen i framtiden kan komma åt HDInsight-kluster.

            ![Välj arbetsgrupp för klusteråtkomstgrupp HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Slutför de andra stegen i klusterkonfigurationen och verifiera informationen i **klustersammanfattningen**. Välj **Skapa**.

1. Logga in på Ambari-användargränssnittet för `https://CLUSTERNAME.azurehdinsight.net`det nyskapade klustret på . Använd administratörens användarnamn `hdiadmin@hdifabrikam.com` och lösenord.

    ![Inloggningsfönstret för Apache Ambari-användargränssnittet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Välj **Roller**på klusterinstrumentpanelen .
1. På sidan **Roller,** under **Tilldela roller till dessa**, bredvid rollen **Klusteradministratör,** anger du gruppen *hdiusergroup*. 

    ![Tilldela klustrets administratörsroll till hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Öppna din SSH-klient (Secure Shell) och logga in på klustret. Använd **hdiuser** som du skapade i den lokala Active Directory-instansen.

    ![Logga in i klustret med hjälp av SSH-klienten](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Om du kan logga in med det här kontot har du konfigurerat ESP-klustret korrekt för att synkronisera med din lokala Active Directory-instans.

## <a name="next-steps"></a>Nästa steg

Läs [En introduktion till Apache Hadoop säkerhet med ESP](hdinsight-security-overview.md).
