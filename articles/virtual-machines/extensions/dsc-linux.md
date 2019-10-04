---
title: Azure DSC-tillägg för Linux
description: Installerar OMI-och DSC-paket så att en virtuell Azure Linux-dator kan konfigureras med önskad tillstånds konfiguration.
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
ms.openlocfilehash: 6df6bb5c0be4bf1779541a815bd933965024809f
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960395"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-tillägg för Linux (Microsoft. OSTCExtensions. DSCForLinux)

Önskad tillstånds konfiguration (DSC) är en hanterings plattform som gör att du kan hantera din IT-och utvecklings infrastruktur med konfiguration som kod.

> ! Observera DSC-tillägget för Linux och [Azure Monitor tillägget för virtuell dator för Linux för](/virtual-machines/extensions/oms-linux) närvarande finns en konflikt och stöds inte i en sida vid sida-konfiguration.  Det innebär att du inte bör använda de två lösningarna tillsammans på samma virtuella dator.

DSCForLinux-tillägget har publicerats och stöds av Microsoft. Tillägget installerar OMI-och DSC-agenten på virtuella Azure-datorer. DSC-tillägget kan också utföra följande åtgärder


- Registrera den virtuella Linux-datorn till Azure Automation konto för att hämta konfigurationer från Azure Automation-tjänsten (registrera ExtensionAction)
- Skicka MOF-konfigurationer till den virtuella Linux-datorn (push-ExtensionAction)
- Använd meta MOF-konfiguration på den virtuella Linux-datorn för att konfigurera pull-server för att hämta Node-konfigurationen (pull-ExtensionAction)
- Installera anpassade DSC-moduler på den virtuella Linux-datorn (installera ExtensionAction)
- Ta bort anpassade DSC-moduler till den virtuella Linux-datorn (ta bort ExtensionAction)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

DSC Linux-tillägget stöder alla [Linux-distributioner](/virtual-machines/linux/endorsed-distros) som har godkänts på Azure, förutom:

| Distribution | Version |
|---|---|
| Debian | alla versioner |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Internetanslutning

DSCForLinux-tillägget kräver att den virtuella mål datorn är ansluten till Internet. Register tillägget kräver till exempel anslutning till Automation-tjänsten. För andra åtgärder, till exempel pull, pull, installation krävs anslutning till Azure Storage/GitHub. Det beror på inställningarna som tillhandahålls av kunden.

## <a name="extension-schema"></a>Tilläggsschema

### <a name="11-public-configuration"></a>1,1 offentlig konfiguration

Här följer alla offentliga konfigurations parametrar som stöds:

* `FileUri`: (valfri, sträng) URI för MOF-filen/meta MOF-filen/den anpassade resursens ZIP-fil.
* `ResourceName`: (valfritt, sträng) namnet på den anpassade modulen
* `ExtensionAction`: (valfritt, sträng) anger vad ett tillägg gör. giltiga värden: Registrera, skicka, Hämta, installera, ta bort. Om inget värde anges anses det som push-åtgärd som standard.
* `NodeConfigurationName`: (valfritt, sträng) namnet på den nod som ska tillämpas.
* `RefreshFrequencyMins`: (valfritt, int) anger hur ofta (i minuter) DSC försöker hämta konfigurationen från hämtnings servern. 
       Om konfigurationen på hämtnings servern skiljer sig från den nuvarande på målnoden kopieras den till den väntande lagringen och tillämpas.
* `ConfigurationMode`: (valfritt, sträng) anger hur DSC ska tillämpa konfigurationen. Giltiga värden är: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (valfritt, int) anger hur ofta (i minuter) DSC säkerställer att konfigurationen är i önskat tillstånd.

> [!NOTE]
> Om du använder en version < 2,3 är läges parametern samma som ExtensionAction. Läget verkar vara ett överbelastat villkor. Därför för att undvika förvirring används ExtensionAction från och med version 2,3. För bakåtkompatibilitet stöder tillägget både läge och ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1,2 skyddad konfiguration

Här följer alla skyddade konfigurations parametrar som stöds:

* `StorageAccountName`: (valfritt, sträng) namnet på det lagrings konto som innehåller filen
* `StorageAccountKey`: (valfritt, sträng) nyckeln för det lagrings konto som innehåller filen
* `RegistrationUrl`: (valfritt, sträng) URL: en för det Azure Automation kontot
* `RegistrationKey`: (valfritt, sträng) åtkomst nyckeln för Azure Automations kontot


## <a name="scenarios"></a>Scenarier

### <a name="register-to-azure-automation-account"></a>Registrera till Azure Automation konto
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
offentlig. JSON
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Tillämpa en MOF-konfigurationsfil (i Azure Storage-konto) på den virtuella datorn

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

offentlig. JSON
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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Tillämpa en MOF-konfigurationsfil (i offentlig lagring) på den virtuella datorn

offentlig. JSON
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

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Tillämpa en konfigurations fil för meta MOF (i Azure Storage-konto) på den virtuella datorn

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

offentlig. JSON
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Tillämpa en konfigurations fil för meta MOF (i offentlig lagring) på den virtuella datorn
offentlig. JSON
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

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Installera en anpassad resurspool (ZIP-fil i Azure Storage-konto) till den virtuella datorn
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
offentlig. JSON
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Installera en anpassad Resource module (ZIP-fil i offentlig lagring) på den virtuella datorn
offentlig. JSON
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

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Ta bort en anpassad resurspool från den virtuella datorn
offentlig. JSON
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

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration av distributions konfiguration, till exempel onboarding till Azure Automation. 

Exempel Resource Manager-mallen är [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) och [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Mer information om Azure Resource Manager mall finns i [redigera Azure Resource Manager mallar](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Azure CLI-distribution

### <a name="21-using-azure-cliazure-cli"></a>2.1. Använda [**Azure CLI**] [Azure-CLI]
Innan du distribuerar DSCForLinux-tillägget bör du konfigurera din `public.json` och `protected.json`, enligt de olika scenarierna i avsnitt 3.

#### <a name="211-classic"></a>punkt. Klassisk
Klassiskt läge kallas även för Azure Service Management-läge. Du kan växla till den genom att köra:
```
$ azure config mode asm
```

Du kan distribuera DSCForLinux-tillägget genom att köra:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

För att lära dig den senaste tilläggs versionen som är tillgänglig, kör:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>punkt. Resource Manager
Du kan växla till Azure Resource Manager läge genom att köra:
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
> I Azure Resource Manager läge är `azure vm extension list` inte tillgängligt för tillfället.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Använda [**Azure PowerShell**] [Azure-PowerShell]

#### <a name="221-classic"></a>2.2.1, klassisk

Du kan logga in på ditt Azure-konto (Azure Service Management-läge) genom att köra:

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

Du måste ändra innehållet i $privateConfig och $publicConfig enligt olika scenarier i avsnittet ovan 
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

Du kan logga in på ditt Azure-konto (Azure Resource Manager läge) genom att köra:

```powershell>
Login-AzAccount
```

Klicka [**här**](../../azure-resource-manager/manage-resources-powershell.md) om du vill veta mer om hur du använder Azure PowerShell med Azure Resource Manager.

Du kan distribuera DSCForLinux-tillägget genom att köra:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Du måste ändra innehållet i $privateConfig och $publicConfig enligt olika scenarier i avsnittet ovan 
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

Felkod: 51 representerar antingen ett distribution eller en tilläggs åtgärd som inte stöds.
I vissa fall kan DSC Linux-tillägget inte installera OMI när den senare versionen av OMI redan finns på datorn. [fel svar: (000003) Nedgradering tillåts inte]



### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/community/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [tillägg för virtuella datorer och funktioner för Linux](features-linux.md).
