---
title: Hantera Nyckelvalv i Azure Stack med hjälp av PowerShell | Microsoft Docs
description: Lär dig att hantera Nyckelvalv i Azure Stack med hjälp av PowerShell
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.openlocfilehash: 0d59c8c43d42babc1f25d014ad493fc59c5e0427
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352709"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Hantera Nyckelvalv i Azure Stack med hjälp av PowerShell

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan hantera Nyckelvalv i Azure Stack med hjälp av PowerShell. Lär dig hur du använder Key Vault PowerShell-cmdletar för att:

* Skapa ett nyckelvalv.
* Store och hantera kryptografiska nycklar och hemligheter.
* Ge användare eller program för att anropa åtgärder i valvet.

>[!NOTE]
>I Key Vault PowerShell-cmdlets som beskrivs i den här artikeln finns i Azure PowerShell SDK.

## <a name="prerequisites"></a>Förutsättningar

* Du måste prenumerera på ett erbjudande som innehåller Azure Key Vault-tjänsten.
* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md).
* [Konfigurera PowerShell-miljö för Azure Stack-användarens](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Aktivera din klientprenumeration för Key Vault-åtgärder

Innan du kan utfärda alla åtgärder mot ett nyckelvalv, måste du kontrollera att din klientorganisation prenumeration är aktiverat för valvet. Kontrollera att valvet åtgärder har aktiverats genom att köra följande kommando:

```PowerShell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Resultat**

Om prenumerationen har aktiverats för valvet åtgärder visar utdata ”RegistrationState” är ”registrerad” för alla typer av resurser för ett nyckelvalv.

![Registreringstillstånd för nyckelvalvet](media/azure-stack-key-vault-manage-powershell/image1.png)

Om valvet åtgärder inte är aktiverade kan du anropa följande kommando för att registrera Key Vault-tjänsten i din prenumeration:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Resultat**

Om registreringen lyckas returneras följande utdata:

![Registrera](media/azure-stack-key-vault-manage-powershell/image2.png) när du anropar key vault-kommandon kan du får ett felmeddelande som ”prenumerationen inte har registrerats för användning av namnområdet 'Microsoft.KeyVault” ”. Om du får ett felmeddelande, kontrollerar du att du har [aktiverat Key Vault-resursprovidern](#enable-your-tenant-subscription-for-vault-operations) genom att följa anvisningarna som nämndes tidigare.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Innan du skapar ett nyckelvalv måste du skapa en resursgrupp så att alla resurser som rör key vault finns i en resursgrupp. Använd följande kommando för att skapa en ny resursgrupp:

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force

```

**Resultat**

![Ny resursgrupp](media/azure-stack-key-vault-manage-powershell/image3.png)

Nu kan använda den **New-AzureRMKeyVault** kommando för att skapa ett nyckelvalv i resursgruppen som du skapade tidigare. Det här kommandot läser tre obligatoriska parametrar: resursgruppens namn, namn på key vault och geografisk plats.

Kör följande kommando för att skapa ett nyckelvalv:

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**Resultat**

![Nytt nyckelvalv](media/azure-stack-key-vault-manage-powershell/image4.png)

Kommandots utdata visar egenskaper för nyckelvalvet som du skapade. När ett program har åtkomst till det här valvet, måste den använda den **Valvets URI** egenskapen, som är ”https://vault01.vault.local.azurestack.external” i det här exemplet.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Distribution av Active Directory Federation Services (AD FS)

Du kan få den här varningen i en AD FS-distribution: ”Åtkomstprincip har inte angetts. Inga användare eller ett program har behörighet att använda det här valvet ”. Lös problemet genom att ställa in en åtkomstprincip för valvet genom att använda den [Set-AzureRmKeyVaultAccessPolicy](#authorize-an-application-to-use-a-key-or-secret) kommando:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Hantera nycklar och hemligheter

När du har skapat ett valv kan du använda följande steg för att skapa och hantera nycklar och hemligheter i valvet.

### <a name="create-a-key"></a>Skapa en nyckel

Använd den **Add-AzureKeyVaultKey** kommando för att skapa eller importera en programvaruskyddad nyckel i key vault.

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

Den **mål** används för att ange att nyckeln är programvara som skyddas. När nyckeln har skapats, visar kommandot information om nyckeln.

**Resultat**

![Ny nyckel](media/azure-stack-key-vault-manage-powershell/image5.png)

Du kan nu referera nyckeln med hjälp av dess URI. Om du skapar eller importera en nyckel som har samma namn som en befintlig nyckel, uppdateras den ursprungliga nyckeln med värdena som anges i den nya nyckeln. Du kan komma åt den tidigare versionen med hjälp av versionsspecifika URI för nyckeln. Exempel:

* Använd ”https://vault10.vault.local.azurestack.external:443/keys/key01” att alltid hämta den aktuella versionen.
* Använd ”https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a” att hämta den här specifika versionen.

### <a name="get-a-key"></a>Hämta en nyckel

Använd den **Get-AzureKeyVaultKey** kommando för att läsa en nyckel och dess egenskaper.

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Skapa en hemlighet

Använd den **Set-AzureKeyVaultSecret** kommando för att skapa eller uppdatera en hemlighet i ett valv. En hemlighet skapas om det inte redan finns. En ny version av hemligheten skapas om det finns redan.

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**Resultat**

![Skapa en hemlighet](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Hämta en hemlighet

Använd den **Get-AzureKeyVaultSecret** kommando för att läsa en hemlighet i key vault. Det här kommandot kan returnera alla eller specifika versioner av en hemlighet.

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Du kan auktorisera externa program att använda dem när du har skapat nycklar och hemligheter.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Auktorisera ett program att använda en nyckel eller hemlighet

Använd den **Set-AzureRmKeyVaultAccessPolicy** kommando för att auktorisera ett program för att få åtkomst till en nyckel eller hemlighet i nyckelvalvet.
I följande exempel valvnamnet är *ContosoKeyVault* och program som du vill auktorisera har klient-ID *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Kör följande kommando för att godkänna programmet. Alternativt kan du ange den **PermissionsToKeys** parametern för att ange behörigheter för en användare, program eller en säkerhetsgrupp.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Om du vill att samma program att läsa hemligheter i valvet, kör du följande cmdlet:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en virtuell dator med ett lösenord som lagras i Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Distribuera en virtuell dator med ett certifikat som lagras i Key Vault](azure-stack-kv-push-secret-into-vm.md)