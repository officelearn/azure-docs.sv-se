---
title: Distribuera en virtuell dator med ett säkert lagrade på Azure-stacken | Microsoft Docs
description: Lär dig hur du distribuerar en virtuell dator och push-installera ett certifikat på den med hjälp av en nyckelvalvet i Azure-stacken
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 3950c9dfc5ff5f7ea1d170da086b4f97048ed81c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069041"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Skapa en virtuell dator och installera ett certifikat som hämtades från nyckelvalvet en Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Lär dig hur du skapar en Azure-stacken virtuell dator (VM) med ett nyckelvalv certifikat installerat.

## <a name="overview"></a>Översikt

Certifikat används i många scenarier, till exempel autentisering av Active Directory eller kryptera webbtrafik. Du kan lagra certifikat på ett säkert sätt som hemligheter i nyckelvalvet en Azure-stacken. Fördelarna med att använda Azure Stack Key Vault är:

* Certifikat visas inte i ett skript, kommandoradsverktyget historik eller mall.
* Process för hantering av certifikat har effektiviserats.
* Du har kontroll över nycklar som har åtkomst till certifikat.

### <a name="process-description"></a>Processbeskrivning av

Följande steg beskriver den process som krävs för att pressa ett certifikat på den virtuella datorn:

1. Skapa ett Nyckelvalv hemliga.
2. Uppdatera azuredeploy.parameters.json-filen.
3. Distribuera mallen

>[!NOTE]
>Du kan använda de här stegen från Azure-stacken Development Kit eller från en extern klient om du är ansluten via VPN.

## <a name="prerequisites"></a>Förutsättningar

* Du måste prenumerera på ett erbjudande som innehåller Key Vault-tjänsten.
* [Installera PowerShell för Azure-stacken.](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)

## <a name="create-a-key-vault-secret"></a>Skapa ett Nyckelvalv hemliga

Följande skript skapar ett certifikat i PFX-format, skapas en nyckelvalvet och lagrar certifikatet i nyckelvalvet som en hemlighet.

>[!IMPORTANT]
>Du måste använda den `-EnabledForDeployment` parameter när du skapar nyckeln felet. Den här parametern säkerställer att nyckelvalvet kan refereras från Azure Resource Manager-mallar.

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

När du kör skriptet tidigare innehåller utdata hemliga URI: N. Anteckna denna URI. Du måste referera till den i den [Push-certifikatet till Windows Resource Manager-mall](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Hämta den [vm-push-certifikat-windows-mallen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) mapp på utvecklingsdatorn. Den här mappen innehåller de `azuredeploy.json` och `azuredeploy.parameters.json` filer som behövs i nästa steg.

Ändra den `azuredeploy.parameters.json` filen enligt miljövärden för din. Parametrarna särskilt intressanta är valvnamnet, resursgruppen valvet och hemlighet URI (som genererats av föregående skriptet). Följande fil är ett exempel på en parameterfil:

## <a name="update-the-azuredeployparametersjson-file"></a>Uppdatera azuredeploy.parameters.json-filen

Uppdatera azuredeploy.parameters.json-filen med vaultName, hemliga URI, VmName och andra värden i din miljö. Följande JSON-filen visas ett exempel på mallfilen parametrar:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

När mallen distribueras har resulterar det i följande utdata:

![Mallresultat för distribution](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Azure-stacken skickar certifikatet till den virtuella datorn under distributionen. Certifikatets plats beror på operativsystemet för den virtuella datorn:

* Certifikatet har lagts till med certifikatarkivet som användaren har angett i certifikatsplatsen LocalMachine i Windows.
* I Linux, certifikatet placeras under katalogen /var/lib/waagent med filnamnet &lt;UppercaseThumbprint&gt;.crt för X509 certifikatfilen och &lt;UppercaseThumbprint&gt;.prv för den privata nyckeln .

## <a name="retire-certificates"></a>Dra tillbaka certifikat

Ta bort certifikat är en del av hanteringen för certifikatet. Du kan inte ta bort den äldre versionen av ett certifikat, men du kan inaktivera det genom att använda den `Set-AzureKeyVaultSecretAttribute` cmdlet.

I följande exempel visas hur du inaktiverar ett certifikat. Använda egna värden för den **VaultName**, **namn**, och **Version** parametrar.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en VM med ett Key Vault-lösenord](azure-stack-kv-deploy-vm-with-secret.md)
* [Att ett program kan komma åt Nyckelvalvet](azure-stack-kv-sample-app.md)
