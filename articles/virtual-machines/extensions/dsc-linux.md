---
title: Azure DSC-tillägg för Linux
description: Installerar OMI- och DSC-paket så att en Virtuell Azure Linux-dator kan konfigureras med önskad tillståndskonfiguration.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250626"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-tillägg för Linux (Microsoft.OSTCExtensions.DSCForLinux)

DSC (Desired State Configuration) är en hanteringsplattform som du kan använda för att hantera din IT- och utvecklingsinfrastruktur med konfiguration som kod.

> [!NOTE]
> DSC-tillägget för Linux och [Azure Monitor-tillägget för virtuella datorer för Linux](/azure/virtual-machines/extensions/oms-linux) utgör för närvarande en konflikt och stöds inte i en konfiguration sida vid sida. Använd inte de två lösningarna tillsammans på samma virtuella dator.

DSCForLinux-tillägget publiceras och stöds av Microsoft. Tillägget installerar OMI- och DSC-agenten på virtuella Azure-datorer. DSC-tillägget kan också göra följande åtgärder:


- Registrera Linux-virtuella datorn till ett Azure Automation-konto för att hämta konfigurationer från Azure Automation-tjänsten (Register ExtensionAction).
- Push MOF-konfigurationer till Linux VM (Push ExtensionAction).
- Använd meta MOF-konfiguration på Linux VM för att konfigurera en pull-server för att hämta nodkonfiguration (Pull ExtensionAction).
- Installera anpassade DSC-moduler till Linux VM (Install ExtensionAction).
- Ta bort anpassade DSC-moduler från Linux VM (Remove ExtensionAction).

 

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

DSC Linux-tillägget stöder alla [Linux-distributioner som är godkända på Azure](/azure/virtual-machines/linux/endorsed-distros) utom:

| Distribution | Version |
|---|---|
| Debian | Alla versioner |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Internetanslutning

DSCForLinux-tillägget kräver att den virtuella måldatorn ansluts till internet. Till exempel kräver registertillägget anslutning till automationstjänsten. För andra åtgärder som Pull, Pull kräver installation anslutning till Azure Storage och GitHub. Det beror på inställningar som tillhandahålls av kunden.

## <a name="extension-schema"></a>Tilläggsschema

### <a name="public-configuration"></a>Offentlig konfiguration

Här är alla offentliga konfigurationsparametrar som stöds:

* `FileUri`: (valfritt, sträng) Uri i MOF-filen, meta MOF-filen eller anpassad resurs zip-fil.
* `ResourceName`: (valfritt, sträng) Namnet på den anpassade resursmodulen.
* `ExtensionAction`: (valfritt, sträng) Anger vad ett tillägg gör. Giltiga värden är Registrera, Push, Pull, Install och Remove. Om det inte anges betraktas det som en push-åtgärd som standard.
* `NodeConfigurationName`: (valfritt, sträng) Namnet på en nodkonfiguration som ska tillämpas.
* `RefreshFrequencyMins`: (valfritt, int) Anger hur ofta (i minuter) som DSC försöker hämta konfigurationen från pull-servern. 
       Om konfigurationen på pull-servern skiljer sig från den aktuella på målnoden kopieras den till det väntande arkivet och tillämpas.
* `ConfigurationMode`: (valfritt, sträng) Anger hur DSC ska använda konfigurationen. Giltiga värden är ApplyOnly, ApplyAndMonitor och ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (valfritt, int) Anger hur ofta (i minuter) DSC säkerställer att konfigurationen är i önskat tillstånd.

> [!NOTE]
> Om du använder en version tidigare än 2.3 är lägesparametern samma som ExtensionAction. Läget verkar vara en överbelastad term. För att undvika förvirring används ExtensionAction från version 2.3 och framåt. För bakåtkompatibilitet stöder tillägget både läge och ExtensionAction. 
>

### <a name="protected-configuration"></a>Skyddad konfiguration

Här är alla skyddade konfigurationsparametrar som stöds:

* `StorageAccountName`: (valfritt, sträng) Namnet på lagringskontot som innehåller filen
* `StorageAccountKey`: (valfritt, sträng) Nyckeln till lagringskontot som innehåller filen
* `RegistrationUrl`: (valfritt, sträng) URL:en för Azure Automation-kontot
* `RegistrationKey`: (valfritt, sträng) Åtkomstnyckeln för Azure Automation-kontot


## <a name="scenarios"></a>Scenarier

### <a name="register-an-azure-automation-account"></a>Registrera ett Azure Automation-konto
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
offentliga.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell-format
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Använda en MOF-konfigurationsfil (i ett Azure-lagringskonto) på den virtuella datorn

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

offentliga.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell-format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Använda en MOF-konfigurationsfil (i offentligt lagringsutrymme) på den virtuella datorn

offentliga.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell-format
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Använda en konfigurationsfil för meta MOF (i ett Azure-lagringskonto) på den virtuella datorn

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

offentliga.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell-format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Använda en konfigurationsfil för meta MOF (i offentligt lagringsutrymme) på den virtuella datorn
offentliga.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell-format
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Installera en anpassad resursmodul (en zip-fil i ett Azure-lagringskonto) till den virtuella datorn
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
offentliga.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell-format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Installera en anpassad resursmodul (en zip-fil i offentligt lagringsutrymme) på den virtuella datorn
offentliga.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell-format
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Ta bort en anpassad resursmodul från den virtuella datorn
offentliga.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell-format
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distributionen, till exempel introduktion till Azure Automation. 

Exempelmallen Resource Manager är [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) och [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Mer information om Azure Resource Manager-mallen finns i [Skapa Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md).


## <a name="azure-cli-deployment"></a>Azure CLI-distribution

### <a name="use-azure-cliazure-cli"></a>Använd [Azure CLI][azure-cli]
Innan du distribuerar DSCForLinux-tillägget `protected.json` konfigurerar du och `public.json` enligt de olika scenarierna i avsnitt 3.

#### <a name="classic"></a>Klassisk

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Det klassiska distributionsläget kallas också Azure Service Management-läge. Du kan växla till den genom att köra:
```
$ azure config mode asm
```

Du kan distribuera DSCForLinux-tillägget genom att köra:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Om du vill veta den senaste tilläggsversionen som finns tillgänglig kör du:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Du kan växla till Azure Resource Manager-läge genom att köra:
```
$ azure config mode arm
```

Du kan distribuera DSCForLinux-tillägget genom att köra:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> I Azure Resource `azure vm extension list` Manager-läge är det inte tillgängligt för tillfället.
>

### <a name="use-azure-powershellazure-powershell"></a>Använd [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Klassisk

Du kan logga in på ditt Azure-konto i Azure Service Management-läge genom att köra:

```powershell>
Add-AzureAccount
```

Och distribuera DSCForLinux-tillägget genom att köra:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Ändra innehållet i $privateConfig och $publicConfig enligt olika scenarier i föregående avsnitt.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

Du kan logga in på ditt Azure-konto i Azure Resource Manager-läge genom att köra:

```powershell>
Login-AzAccount
```

Mer information om hur du använder Azure PowerShell med Azure Resource Manager finns i [Hantera Azure-resurser med hjälp av Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Du kan distribuera DSCForLinux-tillägget genom att köra:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Ändra innehållet i $privateConfig och $publicConfig enligt olika scenarier i föregående avsnitt.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utdata för tilläggskörning loggas till följande fil:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Felkod: 51 representerar antingen distribution som inte stöds eller åtgärden för tillägg som inte stöds.
I vissa fall misslyckas DSC Linux-tillägget att installera OMI när det redan finns en högre version av OMI i datorn. [felsvar: (000003)Nedgradering tillåts inte]



### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kontaktar du Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/community/). Du kan också arkivera en Azure Support-incident. Gå till [Azure Support-webbplatsen](https://azure.microsoft.com/support/options/)och välj **Hämta support**. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure Support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [Tillägg och funktioner för virtuella datorer för Linux](features-linux.md).
