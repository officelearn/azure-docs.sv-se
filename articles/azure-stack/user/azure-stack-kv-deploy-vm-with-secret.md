---
title: Distribuera en virtuell dator med säkert lagrade lösenord i Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar en virtuell dator med ett lösenord som lagras i Azure Stack Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: e35a63a36a84316815d609afa178f9a896415c2b
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584124"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Skapa en virtuell dator med ett säkert lösenord som lagras i Azure Stack Key Vault

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Den här artikeln visar att distribuera en Windows Server-datorn med ett lösenord som lagras i Azure Stack Key Vault. Med ett key vault-lösenord är säkrare än att skicka ett lösenord i oformaterad text.

## <a name="overview"></a>Översikt

Du kan lagra värden, till exempel ett lösenord som en hemlighet i ett nyckelvalv för Azure Stack. När du har skapat en hemlighet som kan du använda den i Azure Resource Manager-mallar. Använda hemligheter med Resource Manager ger följande fördelar:

* Du behöver inte ange hemlighet manuellt varje gång som du distribuerar en resurs.
* Du kan ange vilka användare eller tjänstens huvudnamn kan komma åt en hemlighet.

## <a name="prerequisites"></a>Förutsättningar

* Du måste prenumerera på ett erbjudande som innehåller Key Vault-tjänsten.
* [Installera PowerShell för Azure Stack.](azure-stack-powershell-install.md)
* [Konfigurera din PowerShell-miljö.](azure-stack-powershell-configure-user.md)

Följande steg beskriver processen som krävs för att skapa en virtuell dator genom att hämta det lösenord som lagras i Key Vault:

1. Skapa ett Key Vault hemliga.
2. Uppdatera filen azuredeploy.parameters.JSON.
3. Distribuera mallen.

> ! [OBS]  
> Du kan använda de här stegen från Azure Stack Development Kit eller från en extern klient om du är ansluten via VPN.

## <a name="create-a-key-vault-secret"></a>Skapa ett Key Vault hemliga

Följande skript skapar ett nyckelvalv och lagrar ett lösenord i nyckelvalvet som en hemlighet. Använd den `-EnabledForDeployment` parameter när du skapar nyckelvalvet. Den här parametern ser till att nyckelvalvet kan refereras från Azure Resource Manager-mallar.

```PowerShell

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

När du kör föregående skript innehåller utdata hemliga URI: N. Anteckna denna URI. Du måste referera till den i den [distribuera Windows-dator med lösenordet i nyckelvalvet mallen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Ladda ned den [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) mapp på din utvecklingsdator. Den här mappen innehåller de `azuredeploy.json` och `azuredeploy.parameters.json` filer som du behöver i nästa steg.

Ändra den `azuredeploy.parameters.json` enligt dina miljövärden. Parametrarna särskilt intressanta är valvnamnet vault-resursgrupp och Hemlig-URI (som genererats med föregående skript). Följande fil är ett exempel på en parameterfil:

## <a name="update-the-azuredeployparametersjson-file"></a>Uppdatera filen azuredeploy.parameters.JSON

Uppdatera filen azuredeploy.parameters.JSON med KeyVault för URI, secretName, adminUsername av värden för virtuella datorer enligt din miljö. Följande JSON-filen visas ett exempel på filen med mallparametrar:

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

```PowerShell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

När mallen har distribuerats, resulterar det i följande utdata:

![Utdata för distribution](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Nästa steg

[Distribuera en exempelapp med Key Vault](azure-stack-kv-sample-app.md)

[Distribuera en virtuell dator med ett Key Vault-certifikat](azure-stack-kv-push-secret-into-vm.md)
