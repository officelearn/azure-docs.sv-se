---
title: Distribuera Kubernetes i Azure Stack med hjälp av Active Directory Federation Services (AD FS) | Microsoft Docs
description: Lär dig hur du distribuerar Kubernetes i Azure Stack med hjälp av Active Directory Federation Services (AD FS).
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
ms.date: 02/05/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: a197a366d70958859eed47a9d66606adf80344e4
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891280"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Distribuera Kubernetes i Azure Stack med hjälp av Active Directory Federation Services

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!Note]  
> Kubernetes på Azure Stack är en förhandsversion. Azure Stack-frånkopplade scenariot stöds inte för närvarande av förhandsversionen.

Du kan följa stegen i den här artikeln för att distribuera och konfigurera resurser för Kubernetes. Följ dessa steg när Active Directory Federation Services (AD FS) är identity management-tjänsten.

## <a name="prerequisites"></a>Förutsättningar 

Kom igång genom att kontrollera att du har rätt behörigheter och att Azure Stack är klar.

1. Skapa en SSH offentlig och privat nyckel att logga in på Linux VM på Azure Stack. Du behöver den offentliga nyckeln när klustret skapas.

    Anvisningar om att generera en nyckel finns i [SSH-nyckel Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Kontrollera att du har en giltig prenumeration i din klient Azure Stack-portalen och att du har tillräckligt med offentliga IP-adresser tillgängliga att lägga till nya program.

    Klustret kan inte distribueras till ett Azure Stack **administratör** prenumeration. Du måste använda en **användaren** prenumeration. 

1. Om du inte har Kubernetes-kluster i din marketplace, kontakta administratören Azure Stack.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du måste arbeta med Azure Stack-administratör för att konfigurera tjänstens huvudnamn när du använder AD FS som ID-lösning. Tjänstens huvudnamn ger din ansökan ger åtkomst till Azure Stack-resurser.

1. Din administratör för Azure Stack får du ett certifikat och information för tjänstens huvudnamn. Den här informationen bör se ut:

    ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
    ```

2. Tilldela din nya tjänsthuvudnamnet rollen som deltagare för prenumerationen. Anvisningar finns i [tilldela en roll](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

3. Skapa ett nyckelvalv för att lagra certifikatet för distribution.

    - Du behöver följande typer av information:

        | Värde | Beskrivning |
        | ---   | ---         |
        | Azure Resource Manager-slutpunkten | Microsoft Azure Resource Manager är en management-ramverk som gör att administratörer kan distribuera, hantera och övervaka Azure-resurser. Azure Resource Manager kan hantera dessa uppgifter som en grupp i stället, i en enda åtgärd.<br>Slutpunkten i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/`<br>Slutpunkten i integrerade system är: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | Ditt prenumerations-ID | Den [prenumerations-ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) är hur du kommer åt erbjudanden i Azure Stack. |
        | Ditt användarnamn | Ditt användarnamn. |
        | Resursgruppens namn  | Namnet på en ny resursgrupp eller välj en befintlig resursgrupp. Resursnamnet måste vara alfanumeriskt och gemener. |
        | Namn på Nyckelvalv | Namnet på valvet.<br> Regex-mönster: `^[a-zA-Z0-9-]{3,24}$` |
        | Resursgruppsplats | Platsen för resursgruppen. Det här är den region som du väljer för din Azure Stack-installation. |

    - Öppna PowerShell med en upphöjd kommandotolk. Kör följande skript med parametrar uppdateras till dina värden:

    ```PowerShell  
        $armEndpoint="<Azure Resource Manager Endpoint>"
        $subscriptionId="<Your Subscription ID>"
        $username="<your user name >"
        $resource_group_name = "<the resource group name >"
        $key_vault_name = "<keyvault name>"
        $resource_group_location="<resource group location>"
        
        # Login Azure Stack Environment
        Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
        $mycreds = Get-Credential
        Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
        # Create new Resource group and key vault
        New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
        # Note, Do not omit -EnabledForTemplateDeployment flag
        New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
        # Obtain the security identifier(SID) of the active directory user
        $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
        $objectSID = $adUser.SID.Value
        # Set the key vault access policy
        Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
    ```

4. Ladda upp certifikatet i nyckelvalvet.

    - Du behöver följande typer av information:

        | Värde | Beskrivning |
        | ---   | ---         |
        | Sökväg för certifikatets | FQDN eller filsökväg till certifikatet. |
        | Certifikatlösenord | Lösenordet för certifikatet. |
        | Hemligt namn | Hemligheten gav i föregående steg. |
        | Namn på Nyckelvalv | Namnet på nyckelvalvet skapat i föregående steg. |
        | Azure Resource Manager-slutpunkten | Slutpunkten i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/`<br>Slutpunkten i integrerade system är: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | Ditt prenumerations-ID | Den [prenumerations-ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) är hur du kommer åt erbjudanden i Azure Stack. |

    - Öppna PowerShell med en upphöjd kommandotolk. Kör följande skript med parametrar uppdateras till dina värden:

    ```PowerShell  
        
    # upload the pfx to key vault
    $tempPFXFilePath = "<certificate path>"
    $password = "<certificate password>"
    $keyVaultSecretName = "<secret name>"
    $keyVaultName = "<keyvault name>"
    $armEndpoint="<Azure Resource Manager Endpoint>"
    $subscriptionId="<Your Subscription ID>"
    # Login Azure Stack Environment
    Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
    $mycreds = Get-Credential
    Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
    $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
    $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    $jsonObject = @"
    {
    "data": "$pfxAsBase64EncodedString",
    "dataType" :"pfx",
    "password": "$password"
    }
    "@
    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    ```

## <a name="deploy-kubernetes"></a>Distribuera Kubernetes

1. Öppna den [Azure Stack-portalen](https://portal.local.azurestack.external).

1. Välj **+ skapa en resurs** > **Compute** > **Kubernetes-kluster**. Klicka på **Skapa**.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Grundläggande inställningar

1. Välj **grunderna** i skapar Kubernetes-kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Välj din **prenumeration** -ID.

1. Ange namnet på en ny resursgrupp eller välj en befintlig resursgrupp. Resursnamnet måste vara alfanumeriskt och gemener.

1. Välj den **plats** för resursgruppen. Det här är den region som du väljer för din Azure Stack-installation.

### <a name="2-kubernetes-cluster-settings"></a>2. Inställningar för Kubernetes-kluster

1. Välj **Kubernetes klusterinställningar** i skapar Kubernetes-kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Ange den **Linux VM-administratörsanvändarnamn**. Användarnamn för Linux-datorer som ingår i Kubernetes-klustret och DVM.

1. Ange den **offentlig SSH-nyckel** används för alla Linux-datorer som skapats som en del av Kubernetes-kluster och DVM godkännande.

1. Ange den **Master profil DNS-Prefix** som är unikt för regionen. Detta måste vara en region – unikt namn, till exempel `k8s-12345`. Försök att välja det samma som resursgruppen namn som bästa praxis.

    > [!Note]  
    > Använda en DNS-prefix för nya och unika master profil för varje kluster.

1. Välj den **Kubernetes Master Pool profil antal**. Antalet innehåller antalet noder i poolen master. Det kan vara mellan 1 och 7. Det här värdet ska vara ett udda tal.

1. Välj **The VMSize med Kubernetes överordnade virtuella datorer**.

1. Välj den **Kubernetes Nodantal Pool profil**. Antalet innehåller antalet agenter i klustret. 

1. Välj den **Lagringsprofilen**. Du kan välja **Blob Disk** eller **Managed Disk**. Detta anger att noden VM-storlek för Kubernetes virtuella datorer. 

1. Välj **ADFS** för den **Azure Stack-identitetssystemet** för Azure Stack-installationen.

1. Ange den **tjänstens huvudnamn ClientId** används av providern för Kubernetes Azure-molnet. Klient-ID som identifieras som program-ID när din Azure Stack-administratör skapat tjänstens huvudnamn.

1. Ange den **Key Vault-resursgrupp**. 

1. Ange den **Key Vault-namnet**.

1. Ange den **nyckelvalvshemligheten**.

1. Ange den **Kubernetes Azure Cloud-providerversion**. Det här är version för Kubernetes Azure-providern. Azure Stack släpper en anpassad Kubernetes-version för varje Azure Stack-version.

### <a name="3-summary"></a>3. Sammanfattning

1. Välj Sammanfattning. Bladet visar ett verifieringsmeddelande om för dina inställningar för konfigurationer av Kubernetes-kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Granska dina inställningar.

3. Välj **OK** att distribuera klustret.

> [!TIP]  
>  Om du har frågor om distributionen kan du publicera din fråga eller se om någon redan har besvarat frågan i den [Azure Stack-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Nästa steg

[Ansluta till ditt kluster](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)
