---
title: Azure DSC-tillägget för Linux
description: Installerar OMI och DSC-paket för att tillåta en virtuell Linux-dator som ska konfigureras med hjälp av Desired State Configuration.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2e30f3bb40e23e5b0b1951759bdff3a9f02cc9d5
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236071"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-tillägg för Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) är en hanteringsplattform som hjälper dig att hantera IT-avdelningen och infrastruktur för utveckling med konfiguration som kod.

DSCForLinux tillägg publiceras och stöds av Microsoft. OMI och DSC-agenten installeras på virtuella Azure-datorer i tillägget. DSC-tillägget kan också utföra följande åtgärder


- Registrera Linux VM till Azure Automation-konto för att kunna hämta konfigurationerna från Azure Automation-tjänsten (registrera ExtensionAction)
- Push-MOF-konfigurationer för Linux VM (Push ExtensionAction)
- Tillämpa Meta MOF-konfigurationen för Linux VM konfigurera Hämtningsservern för att kunna hämta nodkonfiguration (Pull ExtensionAction)
- Installera anpassade DSC-moduler för Linux VM (installera ExtensionAction)
- Ta bort anpassade DSC-moduler för Linux VM (ta bort ExtensionAction)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

DSC-Linux-tillägget har stöd för alla de [på Azure-godkända Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) utom:

| Distribution | Version |
|---|---|
| Debian | Alla versioner |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Internetanslutning

Tillägget DSCForLinux kräver att den virtuella måldatorn är ansluten till internet. Till exempel kräver registrera tillägget anslutning till Automation-tjänsten. För andra åtgärder, till exempel hämtning, hämta, kräver installera anslutning till azure storage/github. Det beror på inställningarna som tillhandahålls av kunden.

## <a name="extension-schema"></a>Tilläggsschema

### <a name="11-public-configuration"></a>1.1 offentliga konfiguration

Här är parametrarna för offentliga konfiguration som stöds:

* `FileUri`: (valfritt, string) URI: n i MOF-filen/metadata MOF-filen/anpassat resource ZIP-filen.
* `ResourceName`: (valfritt, string) namnet på modulen anpassad resurs
* `ExtensionAction`: (valfritt, sträng) anger vad som gör ett tillägg. Giltiga värden: Registrera, skicka, hämta, installera, ta bort. Om inte anges, betraktas den som Push-åtgärd som standard.
* `NodeConfigurationName`: (valfritt, string) namnet på en nodkonfiguration tillämpas.
* `RefreshFrequencyMins`: (valfritt, int) anger hur ofta (i minuter) DSC försöker att hämta konfigurationen från hämtningsservern. 
       Om det skiljer sig från den aktuella på målnoden konfigurationen på hämtningsservern, den kopieras till arkivet väntande och tillämpas.
* `ConfigurationMode`: (valfritt, sträng) anger hur DSC ska tillämpa konfigurationen. Giltiga värden är: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (valfritt, int) anger hur ofta (i minuter) DSC säkerställer att konfigurationen är i önskat läge.

> [!NOTE]
> Om du använder en version < 2.3 är lägesparametern samma som ExtensionAction. Läget verkar vara en term som är överbelastad. Om du vill undvika förvirringen därför används ExtensionAction från version 2.3 och senare. Tillägget stöder både läge och ExtensionAction för bakåtkompatibilitet. 
>

### <a name="12-protected-configuration"></a>1.2 skyddade konfiguration

Här är parametrarna för skyddade konfiguration som stöds:

* `StorageAccountName`: (valfritt, string) namnet på lagringskontot som innehåller filen
* `StorageAccountKey`: (valfritt, string) nyckeln för lagringskontot som innehåller filen
* `RegistrationUrl`: (valfritt, string) URL: en för Azure Automation-konto
* `RegistrationKey`: (valfritt, string) åtkomstnyckeln för Azure Automation-konto


## <a name="scenarios"></a>Scenarier

### <a name="register-to-azure-automation-account"></a>Registrera till Azure Automation-konto
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Tillämpa en MOF-konfigurationsfilen (i Azure Storage-konto) till den virtuella datorn

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Tillämpa en MOF-konfigurationsfilen (i offentlig lagring) till den virtuella datorn

public.json
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

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Tillämpa en meta MOF-konfigurationsfilen (i Azure Storage-konto) till den virtuella datorn

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Tillämpa en meta MOF-konfigurationsfilen (i offentlig lagring) till den virtuella datorn
public.json
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

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Installera en anpassad resurs-modul (ZIP-filen i Azure Storage-konto) till den virtuella datorn
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Installera en anpassad resurs-modul (ZIP-filen i offentlig lagring) till den virtuella datorn
public.json
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

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Ta bort en anpassad resurs-modul från den virtuella datorn
public.json
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

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är perfekt när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distribution, till exempel Kom igång med Azure Automation. 

Exempelmall för Resource Manager är [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) och [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Mer information om Azure Resource Manager-mall på [redigera Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Azure CLI-distribution

### <a name="21-using-azure-cliazure-cli"></a>2.1. Med hjälp av [**Azure CLI**] [azure-cli]
Innan du distribuerar DSCForLinux tillägg, bör du konfigurera din `public.json` och `protected.json`, enligt olika scenarier i avsnitt 3.

#### <a name="211-classic"></a>2.1.1. Klassisk
Klassiskt läge kallas även Azure Service Management-läge. Du kan växla till den genom att köra:
```
$ azure config mode asm
```

Du kan distribuera DSCForLinux tillägget genom att köra:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Om du vill veta den senast tillgängliga versionen i tillägget, kör du:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
Du kan växla till läget Azure Resource Manager genom att köra:
```
$ azure config mode arm
```

Du kan distribuera DSCForLinux tillägget genom att köra:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> I Azure Resource Manager-läge `azure vm extension list` är inte tillgänglig för tillfället.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Med hjälp av [**Azure PowerShell**] [azure powershell]

#### <a name="221-classic"></a>2.2.1 klassisk

Du kan logga in på ditt Azure-konto (Azure Service Management-läge) genom att köra:

```powershell>
Add-AzureAccount
```

Och distribuera DSCForLinux tillägget genom att köra:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Du behöver ändra innehållet i $privateConfig och $publicConfig enligt olika scenarier i senare avsnitt 
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

#### <a name="222resource-manager"></a>2.2.2.Resource Manager

Du kan logga in på ditt Azure-konto (Azure Resource Manager-läge) genom att köra:

```powershell>
Login-AzAccount
```

Klicka på [ **här** ](../../azure-resource-manager/powershell-azure-resource-manager.md) mer information om hur du använder Azure PowerShell med Azure Resource Manager.

Du kan distribuera DSCForLinux tillägget genom att köra:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Du behöver ändra innehållet i $privateConfig och $publicConfig enligt olika scenarier i senare avsnitt 
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

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Felkod: 51 representerar-distribution som stöds inte eller stöds inte tillägget åtgärd.
I vissa fall kan finns DSC Linux tillägget kan inte installeras OMI när senare version av OMI redan på datorn. [felsvar: (000003) Nedgradering är inte tillåtet]



### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/community/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [virtuella datorer, tillägg och funktioner i Linux](features-linux.md).
