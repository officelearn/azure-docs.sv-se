---
title: "Distribuera en virtuell dator med ett säkert lagrade på Azure-stacken | Microsoft Docs"
description: "Lär dig hur du distribuerar en virtuell dator och push-installera ett certifikat på den med hjälp av en nyckelvalvet i Azure-stacken"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: mabrigg
ms.openlocfilehash: e319f5c6d27d3a223764b0a5593480f02864ddbe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>Skapa en virtuell dator och inkludera certifikat som hämtats från ett nyckelvalv

Den här artikeln hjälper dig att skapa en virtuell dator i Azure-stacken och push-certifikat på den. 

## <a name="prerequisites"></a>Krav

* Du måste prenumerera på ett erbjudande som innehåller Key Vault-tjänsten. 
* [Installera PowerShell för Azure-stacken.](azure-stack-powershell-install.md)  
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)

Nyckelvalv i Azure-stacken används för att lagra certifikat. Certifikat är bra i många olika scenarier. Tänk dig ett scenario där du har en virtuell dator i Azure-stacken som kör ett program som behöver ett certifikat. Det här certifikatet kan användas för att kryptera för autentisering av Active Directory eller för SSL på en webbplats. Med certifikatet i en key vault hjälper dig se till att det är säkert.

I den här artikeln vägleder vi dig genom de steg som krävs för att pressa ett certifikat på en Windows-dator i Azure-stacken. Du kan använda de här stegen från Azure Stack Development Kit eller från en extern Windows-baserad klient om du är ansluten via VPN.

Följande steg beskriver den process som krävs för att pressa ett certifikat på den virtuella datorn:

1. Skapa ett Nyckelvalv hemliga.
2. Uppdatera azuredeploy.parameters.json-filen.
3. Distribuera mallen

## <a name="create-a-key-vault-secret"></a>Skapa ett Nyckelvalv hemliga

Följande skript skapar ett certifikat i PFX-format, skapas en nyckelvalvet och lagrar certifikatet i nyckelvalvet som en hemlighet. Du måste använda den `-EnabledForDeployment` parameter när du skapar nyckelvalvet. Den här parametern säkerställer att nyckelvalvet kan refereras från Azure Resource Manager-mallar.

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

Nu distribuera mallen med hjälp av följande PowerShell-skript:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

När mallen distribueras har resulterar det i följande utdata:

![Distribution av utdata](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

När den här virtuella datorn distribueras skickar Azure Stack certifikatet till den virtuella datorn. Certifikatet har lagts till med certifikatarkivet som användaren har angett i certifikatsplatsen LocalMachine i Windows. I Linux, certifikatet placeras under katalogen /var/lib/waagent med filnamnet &lt;UppercaseThumbprint&gt;.crt för X509 certifikatfilen och &lt;UppercaseThumbprint&gt;.prv för den privata nyckeln .

## <a name="retire-certificates"></a>Dra tillbaka certifikat

I föregående avsnitt visade vi hur att skicka ett nytt certifikat på en virtuell dator. Din gamla certifikatet är fortfarande på den virtuella datorn och kan inte tas bort. Men du kan inaktivera den äldre versionen av hemligheten med den `Set-AzureKeyVaultSecretAttribute` cmdlet. Följande är ett exempel på användning av denna cmdlet. Se till att ersätta valvnamnet, hemliga namnet och Versionsvärden enligt din miljö:

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en VM med ett Key Vault-lösenord](azure-stack-kv-deploy-vm-with-secret.md)
* [Att ett program kan komma åt Nyckelvalvet](azure-stack-kv-sample-app.md)


