---
title: Lägg till Kubernetes i Azure Stack Marketplace | Microsoft Docs
description: Lär dig hur du lägger till Kubernetes i Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: ca58059716ebebfaf663412b37014ae4f534d0e3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58081516"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Lägg till Kubernetes i Azure Stack Marketplace

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!note]  
> Kubernetes på Azure Stack är en förhandsversion. Azure Stack-frånkopplade scenariot stöds inte för närvarande av förhandsversionen.

Du kan erbjuda Kubernetes som ett Marketplace-objekt till dina användare. Användarna kan sedan distribuera Kubernetes i en enda, samordnad åtgärd.

I följande artikel titta på med en Azure Resource Manager-mall för att distribuera och etablera resurser för ett fristående Kubernetes-kluster. Innan du börjar, kontrollera Azure Stack och inställningar för globala Azure-klient. Samla in nödvändig information om Azure Stack. Lägga till nödvändiga resurser i din klient och Azure Stack Marketplace. Klustret är beroende av en Ubuntu-server, anpassade skript och Kubernetes-kluster Marketplace-objekt i marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Skapa en plan, ett erbjudande och en prenumeration

Skapa en plan, ett erbjudande och en prenumeration för Kubernetes Marketplace-objekt. Du kan också använda en befintlig plan och erbjudande.

1. Logga in på den [administrationsportalen.](https://adminportal.local.azurestack.external)

1. Skapa en plan som den grundläggande planen. Anvisningar finns i [skapa en plan i Azure Stack](azure-stack-create-plan.md).

1. Skapa ett erbjudande. Anvisningar finns i [skapa ett erbjudande i Azure Stack](azure-stack-create-offer.md).

1. Välj **erbjuder**, och hitta erbjudandet som du skapade.

1. Välj **översikt** på bladet erbjudandet.

1. Välj **ändra tillståndet**. Välj **Offentligt**.

1. Välj **+ skapa en resurs** > **erbjudanden och planer** > **prenumeration** att skapa en prenumeration.

    a. Ange en **visningsnamn**.

    b. Ange en **användaren**. Använda Azure AD-konto som är associerade med din klient.

    c. **Beskrivning av lagringsprovider**

    d. Ange den **Directory-klient** till Azure AD-klient för Azure Stack. 

    e. Välj **erbjuder**. Välj namnet på erbjudandet som du skapade. Anteckna prenumerations-ID.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Skapa ett huvudnamn för tjänsten och autentiseringsuppgifter i AD FS

Om du använder Active Directory Federation Services (AD FS) för identity management-tjänsten, behöver du skapa ett tjänstobjekt för användare som distribuerar ett Kubernetes-kluster.

1. Skapa och exportera ett självsignerat certifikat som används för att skapa tjänsten huvudnamn. 

    - Du behöver följande typer av information:

       | Värde | Beskrivning |
       | ---   | ---         |
       | Lösenord | Ange ett nytt lösenord för certifikatet. |
       | Lokala certifikatsökväg | Ange sökvägen och namnet på certifikatet. Exempel: `c:\certfilename.pfx` |
       | Certifikatets namn | Ange namnet på certifikatet. |
       | Plats för certifikatarkiv |  Till exempel, `Cert:\LocalMachine\My` |

    - Öppna PowerShell med en upphöjd kommandotolk. Kör följande skript med parametrar uppdateras till dina värden:

        ```PowerShell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "CN=<certificate name>"
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2.  Anteckna den nya certifikat-ID som visas i din PowerShell-session `1C2ED76081405F14747DC3B5F76BB1D83227D824`. ID: T används när du skapar tjänstens huvudnamn.

    ```PowerShell  
    VERBOSE: Generated new certificate 'CN=<certificate name>' (1C2ED76081405F14747DC3B5F76BB1D83227D824).
    ```

3. Skapa tjänstens huvudnamn med certifikat.

    - Du behöver följande typer av information:

       | Värde | Beskrivning                     |
       | ---   | ---                             |
       | ERCS IP | I ASDK Privilegierade slutpunkten är normalt `AzS-ERCS01`. |
       | Programnamn | Ange ett kort namn för programmet tjänstens huvudnamn. |
       | Plats för certifikatarkiv | Sökvägen på datorn där du har sparat certifikatet. Detta anges av lagringsplatsen och certifikat-ID som genereras i det första steget. Exempel: `Cert:\LocalMachine\My\1C2ED76081405F14747DC3B5F76BB1D83227D824` |

       När du uppmanas, Använd följande autentiseringsuppgifter för att ansluta till slutpunkten för behörighet. 
        - Användarnamn: Ange CloudAdmin-konto i formatet <Azure Stack domain>\cloudadmin. (Användarnamnet är azurestack\cloudadmin för ASDK,.)
        - Lösenord: Ange samma lösenord som angavs under installationen för AzureStackAdmin domänadministratörskontot.

    - Kör följande skript med parametrar uppdateras till dina värden:

        ```PowerShell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        $certStoreLocation="<certificate location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

        # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
        $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

        # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
        $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

        # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
        $TenantID = $AzureStackInfo.AADTenantID

        # Register an AzureRM environment that targets your Azure Stack instance
        Add-AzureRMEnvironment `
        -Name "AzureStackUser" `
        -ArmEndpoint $ArmEndpoint

        # Set the GraphEndpointResourceId value
        Set-AzureRmEnvironment `
        -Name "AzureStackUser" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - Huvudnamn tjänstinformation ut kodavsnittet nedan

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Lägg till en Ubuntu server-avbildning

Lägg till följande Ubuntu Server bild Marketplace:

1. Logga in på den [administrationsportalen](https://adminportal.local.azurestack.external).

1. Välj **alla tjänster**, och sedan under den **ADMINISTRATION** kategori, väljer **Marketplace management**.

1. Välj **+ Lägg till från Azure**.

1. Ange `Ubuntu Server`.

1. Välj den senaste versionen av servern. Kontrollera den fullständiga versionen och se till att du har den senaste versionen:
    - **Publisher**: Canonical
    - **Erbjuder**: UbuntuServer
    - **Version**: 16.04.201806120 (eller senaste versionen)
    - **SKU**: 16.04-LTS

1. Välj **ladda ned.**

## <a name="add-a-custom-script-for-linux"></a>Lägg till ett anpassat skript för Linux

Lägg till Kubernetes från Marketplace:

1. Öppna den [administrationsportalen](https://adminportal.local.azurestack.external).

1. Välj **alla tjänster** och sedan under den **ADMINISTRATION** kategori, väljer **Marketplace Management**.

1. Välj **+ Lägg till från Azure**.

1. Ange `Custom Script for Linux`.

1. Väljer du skriptet med följande profil:
   - **Erbjuder**: Anpassat skript för Linux 2.0
   - **Version**: 2.0.6 (eller senaste versionen)
   - **Publisher**: Microsoft Corp

     > [!Note]  
     > Mer än en version av anpassat skript för Linux kan anges. Du behöver att lägga till den senaste versionen av objektet.

1. Välj **ladda ned.**


## <a name="add-kubernetes-to-the-marketplace"></a>Lägg till Kubernetes i marketplace

1. Öppna den [administrationsportalen](https://adminportal.local.azurestack.external).

1. Välj **alla tjänster** och sedan under den **ADMINISTRATION** kategori, väljer **Marketplace Management**.

1. Välj **+ Lägg till från Azure**.

1. Ange `Kubernetes`.

1. Välj `Kubernetes Cluster`.

1. Välj **ladda ned.**

    > [!note]  
    > Det kan ta fem minuter för marketplace-objekt ska visas i Marketplace.

    ![Kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Uppdatera eller ta bort Kubernetes 

När du uppdaterar Kubernetes-objekt, tar du bort föregående objekt i Marketplace. Följ anvisningarna i den här artikeln för att lägga till Kubernetes i Marketplace.

Ta bort Kubernetes-objekt:

1. Ansluta till Azure Stack med PowerShell som en operatör. Anvisningar finns i [Anslut till Azure Stack med PowerShell som operatör](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Hitta det aktuella objektet i Kubernetes-kluster i galleriet.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Notera namnet på det aktuella objektet, till exempel `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Använd följande PowerShell-cmdlet för att ta bort objekt:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Nästa steg

[Distribuera ett Kubernetes till Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Översikt över erbjudna tjänster i Azure Stack](azure-stack-offer-services-overview.md)
