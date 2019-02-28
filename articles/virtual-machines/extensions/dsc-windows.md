---
title: Azure Desired State Configuration-tilläggshanterare | Microsoft Docs
description: Ladda upp och tillämpa en PowerShell DSC-konfiguration för en Azure-dator med DSC-tillägg
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 4f4793e18185c16ef144de33b4f116eff89a9969
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960164"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC-tillägg

## <a name="overview"></a>Översikt

PowerShell DSC-tillägg för Windows är publicerat och stöds av Microsoft. Tillägget laddar upp och använder ett PowerShell DSC-konfiguration på en Azure-dator. DSC-tillägget anrop till PowerShell DSC för att införa den mottagna DSC-konfigurationen på den virtuella datorn. Det här dokumentet beskriver de plattformar som stöds, konfigurationer och distributionsalternativ för DSC-tillägget för virtuell dator för Windows.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

DSC-tillägget har stöd för följande operativsystem

Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Internetanslutning

DSC-tillägget för Windows kräver att den virtuella måldatorn ska kunna kommunicera med Azure och platsen för konfigurationspaketet (.zip-fil) om den är lagrad på en plats utanför Azure. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för inställningsavsnittet av DSC-tillägget i en Azure Resource Manager-mall. 

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
            "forcePullAndApply": false
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

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | datum |
| utgivare | Microsoft.Powershell.DSC | sträng |
| typ | DSC | sträng |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Egenskap inställningsvärden

| Namn | Datatyp | Beskrivning
| ---- | ---- | ---- |
| settings.wmfVersion | sträng | Anger vilken version av Windows Management Framework som ska installeras på den virtuella datorn. Den här egenskapen: senaste installerar den allra senaste versionen av WMF. Endast aktuella möjliga värden för den här egenskapen är ”4.0', '5.0” och ”senaste”. Dessa möjliga värden är föremål för uppdateringar. Standardvärdet är ”senaste”. |
| settings.configuration.url | sträng | Anger den URL: en plats där du kan hämta DSC-konfiguration zip-fil. Om den URL som kräver en SAS-token för åtkomst och måste du ange egenskapen protectedSettings.configurationUrlSasToken till värdet för din SAS-token. Den här egenskapen krävs om settings.configuration.script och/eller settings.configuration.function har definierats.
| settings.configuration.script | sträng | Anger namnet på det skript som innehåller definitionen av DSC-konfiguration. Det här skriptet måste vara i rotmappen i zip-filen som hämtas från den URL som anges av egenskapen configuration.url. Den här egenskapen krävs om settings.configuration.url och/eller settings.configuration.script har definierats.
| settings.configuration.function | sträng | Anger namnet på din DSC-konfiguration. Konfigurationen med namnet måste finnas i skriptet som definieras av configuration.script. Den här egenskapen krävs om settings.configuration.url och/eller settings.configuration.function har definierats.
| settings.configurationArguments | Samling | Definierar de parametrar som du vill skicka till DSC-konfiguration. Den här egenskapen kommer inte krypteras.
| settings.configurationData.url | sträng | Anger den URL som du vill ladda ned konfigurationsdatafilen (.pds1) från att använda som indata för DSC-konfiguration. Om den URL som kräver en SAS-token för åtkomst och måste du ange egenskapen protectedSettings.configurationDataUrlSasToken till värdet för din SAS-token.
| settings.privacy.dataEnabled | sträng | Aktiverar eller inaktiverar telemetriinsamling. Endast möjliga värden för den här egenskapen är ”aktivera”, ”inaktivera” ”,, eller $null. Lämna den här egenskapen tom eller null aktiverar telemetri
| settings.advancedOptions.forcePullAndApply | Bool | Den här inställningen är utformad för att förbättra upplevelsen av att arbeta med tillägget för att registrera noder med Azure Automation DSC.  Om värdet är `$true`, tillägget väntar på att den första körningen av konfigurationen innan det returneras lyckade/misslyckade som hämtats från tjänsten.  Om värdet anges till $false, status som returneras av tillägget kommer endast referera till om noden registrerades med Azure Automation-Tillståndskonfiguration och nodkonfigurationen körs inte vid registrering.
| settings.advancedOptions.downloadMappings | Samling | Definierar alternativa platser för att hämta beroenden, till exempel WMF och .NET

### <a name="protected-settings-property-values"></a>Skyddade inställningar egenskapsvärden

| Namn | Datatyp | Beskrivning
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | sträng | Definierar de parametrar som du vill skicka till DSC-konfiguration. Den här egenskapen kommer att krypteras. |
| protectedSettings.configurationUrlSasToken | sträng | Anger SAS-token för att komma åt den URL som definierats av configuration.url. Den här egenskapen kommer att krypteras. |
| protectedSettings.configurationDataUrlSasToken | sträng | Anger SAS-token för att komma åt den URL som definierats av configurationData.url. Den här egenskapen kommer att krypteras. |


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar.
Mallar är perfekt när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distribution.
En Resource Manager-mall som innehåller DSC-tillägget för Windows finns på den [Azure Quick Start-galleriet](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tilläggspaket hämtas och distribueras till den här platsen på den virtuella Azure-datorer
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Tillägget statusfilen innehåller sub status och slutfört/fel statuskoder tillsammans med detaljerade fel och beskrivning för varje tillägg som kör.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Tillägget utdataloggar loggas till följande katalog:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras innebörd

| Felkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 1000 | Allmänt fel | Meddelande för det här felet kommer från det här undantaget i loggar |
| 52 | Fel vid installation av tillägget | Meddelande för det här felet kommer från det här undantaget |
| 1002 | Installera WMF fel | Fel vid installation av WMF. |
| 1004 | Ogiltig Zip-paket | Ogiltig zip; Fel vid uppackning av ZIP-filen |
| 1100 | Argumentfel | Tyder på problem i indata som angetts av användaren. Meddelande för felet kommer från det här undantaget|


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
