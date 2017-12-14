---
title: "Distribuera en virtuell dator med säkert lagrade lösenord på Azure-stacken | Microsoft Docs"
description: "Lär dig hur du distribuerar en virtuell dator med ett lösenord som lagras i Azure-stacken Key Vault"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: mabrigg
ms.openlocfilehash: 8d9a2cebd7a28ca13cf88518a7c83b217af4c0e1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>Skapa en virtuell dator genom att hämta lösenord som lagras i ett Nyckelvalv

När du vill skicka ett säkert värde, till exempel ett lösenord under distributionen kan du lagra värdet som en hemlighet i nyckelvalvet en Azure-stacken och refererar till den i Azure Resource Manager-mallar. Det gör att du inte behöver och manuellt ange hemligheten varje gång du distribuerar resurserna du kan också ange vilka användare eller tjänstens huvudnamn kan komma åt hemligheten. 

I den här artikeln vägleder vi dig igenom de steg som krävs för att distribuera en Windows-dator i Azure-stacken genom att hämta det lösenord som lagras i ett Nyckelvalv. Därför placeras aldrig lösenordet i klartext i parameterfilen mallen. Du kan använda de här stegen från Azure-stacken Development Kit eller från en extern klient om du är ansluten via VPN.

## <a name="prerequisites"></a>Krav
 
* Du måste prenumerera på ett erbjudande som innehåller Key Vault-tjänsten.  
* [Installera PowerShell för Azure-stacken.](azure-stack-powershell-install.md)  
* [Konfigurera Azure Stack användarens PowerShell-miljö.](azure-stack-powershell-configure-user.md)

Följande steg beskriver den process som krävs för att skapa en virtuell dator genom att hämta lösenord som lagras i ett Nyckelvalv:

1. Skapa ett Nyckelvalv hemliga.
2. Uppdatera azuredeploy.parameters.json-filen.
3. Distribuera mallen.

## <a name="create-a-key-vault-secret"></a>Skapa ett Nyckelvalv hemliga

Följande skript skapar ett nyckelvalv och lagrar ett lösenord i nyckelvalvet som en hemlighet. Använd den `-EnabledForDeployment` parameter när du skapar nyckelvalvet. Den här parametern säkerställer att nyckelvalvet kan refereras från Azure Resource Manager-mallar.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

När du kör skriptet tidigare innehåller utdata hemliga URI: N. Anteckna denna URI. Du måste referera till den i den [distribuera Windows-dator med lösenord i nyckelvalvet mallen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Hämta den [101-vm-säkra-lösenord](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) mapp på utvecklingsdatorn. Den här mappen innehåller de `azuredeploy.json` och `azuredeploy.parameters.json` filer som behövs i nästa steg.

Ändra den `azuredeploy.parameters.json` filen enligt miljövärden för din. Parametrarna särskilt intressanta är valvnamnet, resursgruppen valvet och hemlighet URI (som genererats av föregående skriptet). Följande fil är ett exempel på en parameterfil:

## <a name="update-the-azuredeployparametersjson-file"></a>Uppdatera azuredeploy.parameters.json-filen

Uppdatera filen azuredeploy.parameters.json med KeyVault-URI, secretName, adminUsername av värden för virtuella datorer i din miljö. Följande JSON-filen visas ett exempel på mallfilen parametrar: 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Malldistribution

Nu distribuera mallen med hjälp av följande PowerShell-skript:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
När mallen distribueras har resulterar det i följande utdata:

![Distribution av utdata](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>Nästa steg
[Distribuera en exempelapp med Key Vault](azure-stack-kv-sample-app.md)

[Distribuera en virtuell dator med en Key Vault-certifikat](azure-stack-kv-push-secret-into-vm.md)

