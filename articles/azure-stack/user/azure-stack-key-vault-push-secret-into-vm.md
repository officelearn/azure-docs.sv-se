---
title: Distribuera en virtuell dator med ett säkert lagrade certifikat i Azure Stack | Microsoft Docs
description: Lär dig att distribuera en virtuell dator och överföra ett certifikat på den med hjälp av ett nyckelvalv i Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.openlocfilehash: d0807d625cd26b891b9ecb51a8cd40979934583f
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192526"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Skapa en virtuell dator och installera ett certifikat som hämtats från ett nyckelvalv för Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Lär dig hur du skapar en Azure Stack-dator (VM) med ett key vault-certifikat installerat.

## <a name="overview"></a>Översikt

Certifikat används i många scenarier, till exempel autentisering till Active Directory, eller genom att kryptera webbtrafik. Du kan lagra certifikat säkert som hemligheter i ett nyckelvalv för Azure Stack. Fördelarna med att använda Azure Stack Key Vault är:

* Certifikat exponeras inte i ett skript, kommandoradshistorik eller mall.
* Process för hantering av certifikat har effektiviserats.
* Du har kontroll över de nycklar som kommer åt certifikaten.

### <a name="process-description"></a>Beskrivning av process

Följande steg beskriver processen som krävs för att skicka ett certifikat till den virtuella datorn:

1. Skapa ett Key Vault hemliga.
2. Uppdatera filen azuredeploy.parameters.JSON.
3. Distribuera mallen.

> [!NOTE]
> Du kan använda de här stegen från Azure Stack Development Kit eller från en extern klient om du är ansluten via VPN.

## <a name="prerequisites"></a>Förutsättningar

* Du måste prenumerera på ett erbjudande som innehåller Key Vault-tjänsten.
* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md).
* [Konfigurera PowerShell-miljö för Azure Stack-användarens](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Skapa ett Key Vault hemliga

Följande skript skapar ett certifikat i PFX-format, skapar ett nyckelvalv och lagrar certifikatet i nyckelvalvet som en hemlighet.

> [!IMPORTANT]
> Du måste använda den `-EnabledForDeployment` parameter när du skapar nyckelvalvet. Den här parametern säkerställer att nyckelvalvet kan refereras från Azure Resource Manager-mallar.

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

När du kör föregående skript innehåller utdata hemliga URI: N. Anteckna denna URI. Du måste referera till den i den [Push-certifikat som Windows Resource Manager-mall](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Ladda ned den [vm-push-certifikat – windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) tjänstmall-mapp till din utvecklingsdator. Den här mappen innehåller de `azuredeploy.json` och `azuredeploy.parameters.json` filer som du behöver i nästa steg.

Ändra den `azuredeploy.parameters.json` enligt dina miljövärden. Parametrarna särskilt intressanta är valvnamnet vault-resursgrupp och Hemlig-URI (som genererats med föregående skript). Följande avsnitt visar ett exempel på en parameterfil.

## <a name="update-the-azuredeployparametersjson-file"></a>Uppdatera filen azuredeploy.parameters.JSON

Uppdatera den `azuredeploy.parameters.json` filen med den `vaultName`, hemliga URI `VmName`, och andra värden enligt din miljö. Följande JSON-filen visas ett exempel på filen med mallparametrar:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen med hjälp av följande PowerShell-skript:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

När mallen har distribuerats, resulterar det i följande utdata:

![Distribution mallresultat](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

Azure Stack skickar certifikatet till den virtuella datorn under distributionen. Certifikatsplats beror på den Virtuella datorns operativsystem:

* I Windows, certifikatet har lagts till i **LocalMachine** certifikatplatsen med det certifikatarkiv som användaren har angett.
* I Linux, certifikatet placeras den `/var/lib/waagent directory`, med filnamnet &lt;UppercaseThumbprint&gt;.crt för X509 certifikatfilen och &lt;UppercaseThumbprint&gt;.prv för den privata nyckeln.

## <a name="retire-certificates"></a>Återkalla certifikat

Ta bort certifikat är en del av hanteringen för certifikatet. Du kan inte ta bort den äldre versionen av ett certifikat, men du kan inaktivera den genom att använda den `Set-AzureKeyVaultSecretAttribute` cmdlet.

I följande exempel visar hur du inaktiverar ett certifikat. Använd dina egna värden för den **VaultName**, **namn**, och **Version** parametrar.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en VM med ett Key Vault-lösenord](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Tillåt att ett program för att få åtkomst till Key Vault](azure-stack-key-vault-sample-app.md)
