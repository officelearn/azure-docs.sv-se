---
title: Azure-hanterare för önskat tillståndskonfiguration
description: Ladda upp och tillämpa en PowerShell DSC-konfiguration på en Virtuell Azure-dator med DSC-tillägg
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 592c731d1851ac36cf9b57864750df0603b6c3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253967"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC-tillägg

## <a name="overview"></a>Översikt

PowerShell DSC-tillägget för Windows publiceras och stöds av Microsoft. Tillägget överför och tillämpar en PowerShell DSC-konfiguration på en Azure VM. DSC-tillägget anropar PowerShell DSC för att anta den mottagna DSC-konfigurationen på den virtuella datorn. I det här dokumentet beskrivs de plattformar, konfigurationer och distributionsalternativ som stöds för DSC-tillägget för virtuella datorer för Windows.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

DSC-tillägget stöder följande operativsystems

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Internetanslutning

DSC-tillägget för Windows kräver att den virtuella måldatorn kan kommunicera med Azure och platsen för konfigurationspaketet (.zip-filen) om den lagras på en plats utanför Azure. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för inställningsdelen av DSC-tillägget i en Azure Resource Manager-mall. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värde / Exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| utgivare | Microsoft.Powershell.DSC | sträng |
| typ | DSC | sträng |
| typHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Egenskapsvärden för inställningar

| Namn | Datatyp | Beskrivning
| ---- | ---- | ---- |
| settings.wmfVersion | sträng | Anger den version av Windows Management Framework som ska installeras på den virtuella datorn. Om du ställer in den här egenskapen på "senaste" installeras den mest uppdaterade versionen av WMF. De enda aktuella möjliga värdena för den här egenskapen är "4.0", "5.0" och "senaste". Dessa möjliga värden är föremål för uppdateringar. Standardvärdet är "senaste". |
| settings.configuration.url | sträng | Anger den URL-plats där DSC-konfigurationens zip-fil ska hämtas. Om url:en kräver en SAS-token för åtkomst måste du ange egenskapen protectedSettings.configurationUrlSasToken till värdet för din SAS-token. Den här egenskapen krävs om settings.configuration.script och/eller settings.configuration.function har definierats.
| settings.configuration.script | sträng | Anger filnamnet på skriptet som innehåller definitionen av DSC-konfigurationen. Skriptet måste finnas i rotmappen i zip-filen som hämtats från den URL som anges av egenskapen configuration.url. Den här egenskapen krävs om settings.configuration.url och/eller settings.configuration.script har definierats.
| settings.configuration.function settings.configuration. | sträng | Anger namnet på DSC-konfigurationen. Konfigurationen som namnges måste finnas i skriptet som definieras av configuration.script. Den här egenskapen krävs om settings.configuration.url och/eller settings.configuration.function har definierats.
| settings.configurationArguments | Samling | Definierar alla parametrar som du vill skicka till din DSC-konfiguration. Den här egenskapen kommer inte att krypteras.
| settings.configurationData.url | sträng | Anger url:en som du vill hämta filen konfigurationsdata från (.pds1) som ska användas som indata för DSC-konfigurationen. Om url:en kräver en SAS-token för åtkomst måste du ange egenskapen protectedSettings.configurationDataUrlSasToken till värdet för din SAS-token.
| settings.privacy.dataEnabled | sträng | Aktiverar eller inaktiverar telemetrisamling. De enda möjliga värdena för den här egenskapen är "Aktivera", "Inaktivera", "eller $null. Om du lämnar den här egenskapen tom eller null aktiveras telemetri
| settings.advancedOptions.forcePullAndApply | Bool | Den här inställningen är utformad för att förbättra upplevelsen av att arbeta med tillägget för att registrera noder med Azure Automation DSC.  Om värdet `$true`är väntar tillägget på den första körningen av konfigurationen som hämtas från tjänsten innan lyckades/misslyckas.  Om värdet är inställt på $false, refererar statusen som returneras av tillägget endast till om noden har registrerats med Azure Automation State Configuration framgångsrikt och nodkonfigurationen kommer inte att köras under registreringen.
| settings.advancedOptions.downloadMappings settings settings.advancedOptions.downloadMappings settings.advancedOptions.downloadMappings settings | Samling | Definierar alternativa platser för att hämta beroenden som WMF och .NET

### <a name="protected-settings-property-values"></a>Egenskapsvärden för skyddade inställningar

| Namn | Datatyp | Beskrivning
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | sträng | Definierar alla parametrar som du vill skicka till din DSC-konfiguration. Den här egenskapen kommer att krypteras. |
| protectedSettings.configurationUrlSasToken | sträng | Anger sas-token för åtkomst till URL:en som definieras av configuration.url. Den här egenskapen kommer att krypteras. |
| protectedSettings.configurationDataUrlSasToken | sträng | Anger sas-token för åtkomst till URL:en som definieras av configurationData.url. Den här egenskapen kommer att krypteras. |


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar.
Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distribution.
En exempelmall för Resource Manager som innehåller DSC-tillägget för Windows finns i [Snabbstartsgalleriet](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91)i Azure .

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tilläggspaketet hämtas och distribueras till den här platsen på den virtuella Azure-datorn
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Förlängningsstatusfilen innehåller understatus- och statusframgångs-/felkoder tillsammans med det detaljerade felet och beskrivningen för varje tilläggskörning.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Utdataloggar för tillägg loggas till följande katalog:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras betydelser

| Felkod | Betydelse | Möjliga åtgärder |
| :---: | --- | --- |
| 1000 | Allmänt fel | Meddelandet för det här felet tillhandahålls av det specifika undantaget i tilläggsloggar |
| 52 | Fel vid installation av tillägg | Meddelandet för det här felet tillhandahålls av det specifika undantaget |
| 1002 | Wmf-installationsfel | Fel vid installation av WMF. |
| 1004 | Ogiltigt zip-paket | Ogiltig dragkedja ; Det gick inte att packa upp dragkedjan |
| 1100 | Argumentfel | Anger ett problem i indata som tillhandahålls av användaren. Meddelandet för felet tillhandahålls av det specifika undantaget|


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
