---
title: Distribuera en virtuell dator med säkert lagrade lösenord på Azure-stacken | Microsoft Docs
description: Lär dig hur du distribuerar en virtuell dator med ett lösenord som lagras i Azure-stacken Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: 4239eb31afd4abc8b3555f0ee353f5d96716d623
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068989"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Skapa en virtuell dator med ett säkert lösenord som lagras i Azure-stacken Key Vault

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Den här artikeln steg genom att distribuera en virtuell dator för Windows Server som använder ett lösenord som lagras i Azure Key Vault för stacken. Nyckelvalv lösenord är säkrare än att skicka ett lösenord i oformaterad text.

## <a name="overview"></a>Översikt

Du kan lagra värden, till exempel ett lösenord som en hemlighet i nyckelvalvet en Azure-stacken. När du har skapat en hemlighet kan du använda det i Azure Resource Manager-mallar. Med hjälp av hemligheter med Resource Manager ger följande fördelar:

* Du behöver inte ange hemlighet manuellt varje gång du distribuerar en resurs.
* Du kan ange vilka användare eller tjänstens huvudnamn kan komma åt en hemlighet.

## <a name="prerequisites"></a>Förutsättningar

* Du måste prenumerera på ett erbjudande som innehåller Key Vault-tjänsten.
* [Installera PowerShell för Azure-stacken.](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö.](azure-stack-powershell-configure-user.md)

Följande steg beskriver den process som krävs för att skapa en virtuell dator genom att hämta lösenord som lagras i ett Nyckelvalv:

1. Skapa ett Nyckelvalv hemliga.
2. Uppdatera azuredeploy.parameters.json-filen.
3. Distribuera mallen.

>[OBS] Du kan använda de här stegen från Azure-stacken Development Kit eller från en extern klient om du är ansluten via VPN.

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
