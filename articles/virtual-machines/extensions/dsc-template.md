---
title: Önskat tillståndskonfigurationstillägg med Azure Resource Manager-mallar
description: Lär dig mer om Resource Manager-malldefinitionen för DSC-tillägget (Desired State Configuration) i Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: Dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: ef781653332984a7fb6d71ef91d53cbf77e6c91c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72437950"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Önskat tillståndskonfigurationstillägg med Azure Resource Manager-mallar

I den här artikeln beskrivs Azure Resource Manager-mallen för [DSC-tilläggshanteraren (Desired State Configuration).](dsc-overview.md) Många av exemplen använder **RegistrationURL** (som tillhandahålls som en sträng) och **RegistrationKey** (tillhandahålls som en [PSCredential)](/dotnet/api/system.management.automation.pscredential)för att gå ombord med Azure Automation. Mer information om hur du hämtar dessa värden finns i [Onboarding-datorer för hantering av Azure Automation State Configuration - Säker registrering](/azure/automation/automation-dsc-onboarding#secure-registration).

> [!NOTE]
> Du kan stöta på lite olika schemaexempel. Ändringen i schemat inträffade i oktober 2016-versionen. Mer information finns i [Uppdatera från ett tidigare format](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Mallexempel för en Windows VM

Följande kodavsnitt finns i avsnittet **Resurs** i mallen.
DSC-tillägget ärver standardtilläggsegenskaper.
Mer information finns i [klassen VirtualMachineExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-30",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Mallexempel för Windows skalningsuppsättningar för virtuella datorer

En nod för skalningsuppsättning för virtuell dator har ett **egenskapsavsnitt** med attributet **VirtualMachineProfile, extensionProfile.**
Lägg till information om DSC-tillägg under **tillägg.**

DSC-tillägget ärver standardtilläggsegenskaper.
Mer information finns i [klassen VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Detaljerad information om inställningar

Använd följande schema i **inställningsavsnittet** i Azure DSC-tillägget i en Resource Manager-mall.

En lista över de argument som är tillgängliga för standardkonfigurationsskriptet finns i [Standardkonfigurationsskriptet](#default-configuration-script).

```json
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
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
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
```

## <a name="details"></a>Information

| Egenskapsnamn | Typ | Beskrivning |
| --- | --- | --- |
| settings.wmfVersion |sträng |Anger den version av WMF (Windows Management Framework) som ska installeras på den virtuella datorn. Om du ställer in den här egenskapen på **den** senaste versionen av WMF installeras den senaste versionen av WMF. För närvarande är de enda möjliga värdena för den här egenskapen **4.0,** **5.0,** **5.1**och **senaste**. Dessa möjliga värden är föremål för uppdateringar. Standardvärdet är **det senaste**. |
| settings.configuration.url |sträng |Anger den URL-plats där DSC-konfigurationen .zip-filen ska hämtas. Om url:en kräver en SAS-token för åtkomst anger du egenskapen **protectedSettings.configurationUrlSasToken** till värdet för din SAS-token. Den här egenskapen krävs om **settings.configuration.script** eller **settings.configuration.function** har definierats. Om inget värde anges för dessa egenskaper anropas standardkonfigurationsskriptet för att ange LCM-metadata (Location Configuration Manager) och argument ska anges. |
| settings.configuration.script |sträng |Anger filnamnet på skriptet som innehåller definitionen av DSC-konfigurationen. Det här skriptet måste finnas i rotmappen för ZIP-filen som hämtas från den URL som anges av egenskapen **settings.configuration.url.** Den här egenskapen krävs om **settings.configuration.url** eller **settings.configuration.script** har definierats. Om inget värde anges för dessa egenskaper anropas standardkonfigurationsskriptet för att ange LCM-metadata och argument ska anges. |
| settings.configuration.function settings.configuration. |sträng |Anger namnet på DSC-konfigurationen. Konfigurationen som namnges måste inkluderas i skriptet som **settings.configuration.script** definierar. Den här egenskapen krävs om **settings.configuration.url** eller **settings.configuration.function** har definierats. Om inget värde anges för dessa egenskaper anropas standardkonfigurationsskriptet för att ange LCM-metadata och argument ska anges. |
| settings.configurationArguments |Samling |Definierar alla parametrar som du vill skicka till DSC-konfigurationen. Den här egenskapen är inte krypterad. |
| settings.configurationData.url |sträng |Anger url:en som du vill hämta filen konfigurationsdata från (.psd1) som ska användas som indata för DSC-konfigurationen. Om url:en kräver en SAS-token för åtkomst anger du egenskapen **protectedSettings.configurationDataUrlSasToken** till värdet för din SAS-token. |
| settings.privacy.dataCollection settings.privacy.dataCollection settings.privacy.dataCollection settings. |sträng |Aktiverar eller inaktiverar telemetrisamling. De enda möjliga värdena för den här **egenskapen**är Aktivera , **Inaktivera**, **''** **eller $null**. Om du lämnar den här egenskapen tom eller null aktiveras telemetri. Standardvärdet är **''**. Mer information finns i [Azure DSC-datainsamling](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)för tillägg . |
| settings.advancedOptions.downloadMappings settings settings.advancedOptions.downloadMappings settings.advancedOptions.downloadMappings settings |Samling |Definierar alternativa platser som WMF kan hämtas från. Mer information finns i [Azure DSC-tillägg 2.8 och hur du mappar nedladdningar av tilläggsberoenden till din egen plats](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Samling |Definierar alla parametrar som du vill skicka till DSC-konfigurationen. Den här egenskapen är krypterad. |
| protectedSettings.configurationUrlSasToken |sträng |Anger den SAS-token som ska användas för att komma åt den URL som **settings.configuration.url** definierar. Den här egenskapen är krypterad. |
| protectedSettings.configurationDataUrlSasToken |sträng |Anger den SAS-token som ska användas för att komma åt den URL som **settings.configurationData.url** definierar. Den här egenskapen är krypterad. |

## <a name="default-configuration-script"></a>Standardkonfigurationsskript

Mer information om följande värden finns i [Grundläggande inställningar för Lokal konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Du kan använda standardkonfigurationsskriptet för DSC-tillägg för att bara konfigurera LCM-egenskaperna som visas i följande tabell.

| Egenskapsnamn | Typ | Beskrivning |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential (PSCredential) |Obligatorisk egenskap. Anger nyckeln som används för en nod för att registrera sig med Azure Automation-tjänsten som lösenord för ett PowerShell-autentiseringsobjekt. Det här värdet kan identifieras automatiskt med hjälp av **listtangentersmetoden** mot Automation-kontot.  Se [exemplet](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |sträng |Obligatorisk egenskap. Anger URL:en för slutpunkten Automation där noden försöker registrera. Det här värdet kan identifieras automatiskt med hjälp av **referensmetoden** mot Automation-kontot. |
| settings.configurationArguments.NodeConfigurationName settings.configuration |sträng |Obligatorisk egenskap. Anger nodkonfigurationen i automationskontot som ska tilldelas noden. |
| settings.configurationArguments.ConfigurationMode |sträng |Anger läget för LCM. Giltiga alternativ är **ApplyOnly**, **ApplyandMonitor**och **ApplyandAutoCorrect**.  Standardvärdet är **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins settings.configurationArguments.RefreshFrequencyMins settings.configurationArguments.RefreshFrequencyMins settings. | uint32 (på andra) | Anger hur ofta LCM försöker kontrollera med Automation-kontot efter uppdateringar.  Standardvärdet är **30**.  Minimivärdet är **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins settings.configurationArguments.ConfigurationModeFrequencyMins settings.configurationArguments.ConfigurationModeFrequencyMins settings. | uint32 (på andra) | Anger hur ofta LCM validerar den aktuella konfigurationen. Standardvärdet är **15**. Minimivärdet är **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Anger om en nod kan startas om automatiskt om en DSC-åtgärd begär den. Standardvärdet är **falskt**. |
| settings.configurationArguments.ActionAfterReboot | sträng | Anger vad som händer efter en omstart när en konfiguration tillämpas. Giltiga alternativ är **ContinueConfiguration** och **StopConfiguration**. Standardvärdet är **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite settings.configurationArguments.AllowModuleOverwrite settings.configurationArguments.AllowModuleOverwrite settings. | boolean | Anger om LCM skriver över befintliga moduler på noden. Standardvärdet är **falskt**. |

## <a name="settings-vs-protectedsettings"></a>inställningar jämfört med skyddadeInställningar

Alla inställningar sparas i en inställningstextfil på den virtuella datorn.
Egenskaper som anges under **inställningar** är offentliga egenskaper.
Offentliga egenskaper krypteras inte i inställningstextfilen.
Egenskaper som anges under **protectedSettings krypteras** med ett certifikat och visas inte i oformaterad text i inställningsfilen på den virtuella datorn.

Om konfigurationen behöver autentiseringsuppgifter kan du inkludera autentiseringsuppgifterna i **protectedSettings:**

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Exempel på konfigurationsskript

I följande exempel visas standardbeteendet för DSC-tillägget, som är att tillhandahålla metadatainställningar till LCM och registrera sig med Tjänsten Automation DSC.
Konfigurationsargument krävs.
Konfigurationsargument skickas till standardkonfigurationsskriptet för att ange LCM-metadata.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exempel på konfigurationsskriptet i Azure Storage

Följande exempel kommer från [DSC-tilläggshanterarens översikt](dsc-overview.md).
I det här exemplet används Resource Manager-mallar i stället för cmdlets för att distribuera tillägget.
Spara konfigurationen IisInstall.ps1, placera den i en `iisinstall.zip`ZIP-fil (exempel: ) och ladda sedan upp filen i en tillgänglig URL.
I det här exemplet används Azure Blob-lagring, men du kan hämta ZIP-filer från valfri godtycklig plats.

I resource manager-mallen instruerar följande kod den virtuella datorn att hämta rätt fil och kör sedan rätt PowerShell-funktion:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Exempel med refererade registreringsvärden för Azure Automation

I följande exempel hämtas **RegistrationUrl** och **RegistrationKey** genom att referera till azure automation-kontoegenskaperna och använda **listkeys-metoden** för att hämta primärnyckeln (0).  I det här exemplet angavs parametrarna **automationAccountName** och **NodeConfigName** i mallen.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Uppdatera från ett tidigare format

Alla inställningar i ett tidigare format av tillägget (och som har de offentliga egenskaperna **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken**eller **Egenskaper**) anpassas automatiskt till det aktuella formatet för tillägget.
De springer precis som de gjorde förut.

Följande schema visar hur det tidigare inställningsschemat såg ut:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Så här anpassar sig det tidigare formatet till det aktuella formatet:

| Namn på aktuell egenskap | Föregående schemaekvivalenter |
| --- | --- |
| settings.wmfVersion |Inställningar. WMFVersion (WMFVersion) |
| settings.configuration.url |Inställningar. ModulerUrl |
| settings.configuration.script |Första delen av inställningarna. KonfigurationFunktion (före) \\ \\ |
| settings.configuration.function settings.configuration. |Andra delen av inställningarna. KonfigurationFunktion (efter \\ \\) |
| settings.configuration.module.name | Inställningar. ModulKälla |
| settings.configuration.module.version settings.configuration.module.version settings.configuration.module.version settings. | Inställningar. ModulVersion |
| settings.configurationArguments |Inställningar. Egenskaper |
| settings.configurationData.url |protectedSettings.DataBlobUri (utan SAS-token) |
| settings.privacy.dataCollection settings.privacy.dataCollection settings.privacy.dataCollection settings. |Inställningar. Privacy.dataInsamling |
| settings.advancedOptions.downloadMappings settings settings.advancedOptions.downloadMappings settings.advancedOptions.downloadMappings settings |Inställningar. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSetts.Egenskaper |
| protectedSettings.configurationUrlSasToken |Inställningar. SasToken (svenska) |
| protectedSettings.configurationDataUrlSasToken |SAS-token från protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>Felsökning

Här är några av de fel du kan stöta på och hur du kan åtgärda dem.

### <a name="invalid-values"></a>Ogiltiga värden

"Privacy.dataCollection är{0}' '.
De enda möjliga värdena är '', 'Aktivera' och 'Inaktivera'".
"WmfVersion är{0}' '.
Endast möjliga värden är ... och "senaste".

**Problem**: Ett angivet värde är inte tillåtet.

**Lösning**: Ändra det ogiltiga värdet till ett giltigt värde.
Mer information finns i tabellen i [Information](#details).

### <a name="invalid-url"></a>Ogiltig URL

"ConfigurationData.url är{0}' '. Detta är inte en giltig WEBBADRESS" "DataBlobUri är '{0}'. Detta är inte en giltig URL"{0}"Configuration.url är ' '. Detta är inte en giltig WEBBADRESS"

**Problem**: En medföljande URL är ogiltig.

**Lösning**: Kontrollera alla medföljande webbadresser.
Se till att alla webbadresser matchas till giltiga platser som tillägget kan komma åt på fjärrdatorn.

### <a name="invalid-registrationkey-type"></a>Ogiltig registreringsnyckeltyp

"Ogiltig typ för parameterregistreringsnyckel av typen PSCredential."

**Problem**: *RegistrationKey-värdet* i protectedSettings.configurationArguments kan inte anges som någon annan typ än en PSCredential.

**Lösning**: Ändra din protectedSettings.configurationArguments-post för RegistrationKey till en PSCredential-typ med hjälp av följande format:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Ogiltig konfigurationsargumenttyp

"Ogiltig konfigurationArguments {0}typ "

**Problem**: Egenskapen *ConfigurationArguments* kan inte matcha till ett **Hash-tabellobjekt.**

**Lösning**: Gör *egenskapen ConfigurationArguments* till en **Hash-tabell**.
Följ formatet i föregående exempel. Håll utkik efter offerter, kommatecken och klammerparenteser.

### <a name="duplicate-configurationarguments"></a>Duplicera konfigurationsarguments

"Hittade dubbla{0}argument " ' i både offentliga och skyddade konfigurationArguments"

**Problem**: *ConfigurationArguments* i offentliga inställningar och *ConfigurationArguments* i skyddade inställningar har egenskaper med samma namn.

**Lösning**: Ta bort en av de duplicerade egenskaperna.

### <a name="missing-properties"></a>Egenskaper som saknas

"inställningar. Configuration.function kräver att settings.configuration.url eller settings.configuration.module har angetts"

"inställningar. Configuration.url kräver att settings.configuration.script har angetts"

"inställningar. Configuration.script kräver att settings.configuration.url har angetts"

"inställningar. Configuration.url kräver att settings.configuration.function har angetts"

"protectedSettings.ConfigurationUrlSasToken kräver att settings.configuration.url har angetts"

"protectedSettings.ConfigurationDataUrlStoken kräver att settings.configurationData.url har angetts"

**Problem**: En definierad egenskap behöver en annan egenskap, som saknas.

**Lösningar**:

- Ange den saknade egenskapen.
- Ta bort egenskapen som behöver egenskapen saknas.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hur du använder skalningsuppsättningar för virtuella datorer med Azure DSC-tillägget](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Mer information om [DSC:s hantering av säkra autentiseringsuppgifter](dsc-credentials.md).
- Få en [introduktion till Azure DSC-tilläggshanteraren](dsc-overview.md).
- Mer information om PowerShell DSC finns i [PowerShells dokumentationscenter](/powershell/scripting/dsc/overview/overview).
