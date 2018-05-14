---
title: Hantera Key Vault i Azure-stacken med hjälp av PowerShell | Microsoft Docs
description: Lär dig att hantera Key Vault i Azure-stacken med hjälp av PowerShell
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5e9de401f64a835c286c226bfac88caf5168b96e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Hantera Key Vault i Azure-stacken med hjälp av PowerShell

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan hantera Key Vault i Azure-stacken med hjälp av PowerShell. Lär dig hur du använder Key Vault PowerShell-cmdletar för att:

* Skapa en nyckelvalvet.
* Lagra och hantera krypteringsnycklar och hemligheter.
* Ge användare eller program att anropa åtgärder i valvet.

>[!NOTE]
>Key Vault PowerShell-cmdlets instruktionerna i den här artikeln finns i Azure PowerShell SDK.

## <a name="prerequisites"></a>Förutsättningar

* Du måste prenumerera på ett erbjudande som innehåller Azure Key Vault-tjänsten.
* [Installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Aktivera prenumerationen klient för Key Vault-åtgärder

Innan du kan utfärda några åtgärder mot ett nyckelvalv som du behöver kontrollera att prenumerationen klient är aktiverad för valvet åtgärder. Kontrollera att valvet åtgärder har aktiverats genom att köra följande kommando:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Resultat**

Om din prenumeration är aktiverad för valvet åtgärder visar utdata ”RegistrationState” ”registreras” för alla typer av resurser i en nyckelvalvet.

![Nyckelvalv registreringstillstånd](media/azure-stack-kv-manage-powershell/image1.png)

Om valvet operations inte är aktiverade, kan du anropa följande kommando för att registrera Key Vault-tjänsten i din prenumeration:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Resultat**

Om registreringen lyckas returneras följande utdata:

![Registrera](media/azure-stack-kv-manage-powershell/image2.png) när du anropar nyckelvalv kommandona du få ett fel som ”prenumerationen inte har registrerats för användning av namnrymden 'Microsoft.KeyVault'”. Om du får ett felmeddelande, kontrollerar du att du har [aktiverat Key Vault-resursprovidern](#enable-your-tenant-subscription-for-vault-operations) genom att följa instruktionerna som tidigare nämnts.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Innan du skapar ett nyckelvalv måste du skapa en resursgrupp, så att alla resurser som är relaterade till nyckelvalvet finns i en resursgrupp. Använd följande kommando för att skapa en ny resursgrupp:

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**Resultat**

![Ny resursgrupp](media/azure-stack-kv-manage-powershell/image3.png)

Nu kan använda den **ny AzureRMKeyVault** kommando för att skapa en nyckelvalvet i resursgruppen som du skapade tidigare. Det här kommandot läser tre obligatoriska parametrar: resursgruppens namn, nyckelvalv namn och geografisk plats.

Kör följande kommando för att skapa ett nyckelvalv:

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```

**Resultat**

![Nytt nyckelvalv](media/azure-stack-kv-manage-powershell/image4.png)

Kommandots utdata visar egenskaperna för nyckelvalvet som du skapade. När ett program ansluter till det här valvet, den måste använda den **valvet URI** -egenskap som är ”https://vault01.vault.local.azurestack.external” i det här exemplet.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Distribution av Active Directory Federation Services (AD FS)

I en AD FS-distribution kan du hämta den här varningen: ”åtkomstprincip har inte angetts. Inga användare eller ett program har behörighet att använda det här valvet ”. Lös problemet genom att ange en åtkomstprincip för valvet med hjälp av den [Set AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) kommando:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Hantera nycklar och hemligheter

När du har skapat ett valv, kan du använda följande steg för att skapa och hantera nycklar och hemligheter i valvet.

### <a name="create-a-key"></a>Skapa en nyckel

Använd den **Lägg till AzureKeyVaultKey** kommando för att skapa eller importera en programvaruskyddad nyckel i en nyckelvalvet.

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```

Den **mål** används för att ange att nyckeln är programvara som skyddas. När nyckeln har skapats kommando information om nyckeln.

**Resultat**

![Ny nyckel](media/azure-stack-kv-manage-powershell/image5.png)

Nu kan du referera nyckeln med hjälp av dess URI. Om du skapar eller importera en nyckel som har samma namn som en befintlig nyckel uppdateras den ursprungliga nyckeln med värdena i den nya nyckeln. Du kan komma åt den tidigare versionen med hjälp av nyckeln versionsspecifikt URI. Exempel:

* Använd ”https://vault10.vault.local.azurestack.external:443/keys/key01” alltid tillgång till den aktuella versionen.
* Använd ”https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a” att hämta den här specifika versionen.

### <a name="get-a-key"></a>Hämta en nyckel

Använd den **Get-AzureKeyVaultKey** kommando för att läsa en nyckel och information.

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>Skapa en hemlighet

Använd den **Set AzureKeyVaultSecret** kommando för att skapa eller uppdatera en hemlighet i ett valv. Om det inte redan finns skapas en hemlighet. En ny version av hemligheten som skapas om den redan finns.

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**Resultat**

![Skapa en hemlighet](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Hämta en hemlighet

Använd den **Get-AzureKeyVaultSecret** kommando för att läsa en hemlighet i en nyckelvalvet. Det här kommandot kan returnera alla eller vissa versioner av en hemlighet.

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

När du har skapat nycklar och hemligheter kan du tillåta externa program att använda dem.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Godkänna ett program att använda en nyckel eller hemlighet.

Använd den **Set AzureRmKeyVaultAccessPolicy** kommando för att godkänna ett program för att komma åt en nyckel eller hemlighet i nyckelvalvet.
I följande exempel är valvnamnet *ContosoKeyVault* och program som du vill tillåta har ett klient-ID för *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Kör följande kommando för att godkänna programmet. Alternativt kan du ange den **PermissionsToKeys** parametern för att ange behörigheter för en användare, program eller en säkerhetsgrupp.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Om du vill att samma programmet behörighet att läsa hemligheter i ditt valv, kör du följande cmdlet:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en virtuell dator med ett lösenord som lagras i Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Distribuera en virtuell dator med ett certifikat som lagras i Key Vault](azure-stack-kv-push-secret-into-vm.md)
