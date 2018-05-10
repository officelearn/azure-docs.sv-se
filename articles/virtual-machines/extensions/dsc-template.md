---
title: Desired State Configuration-tillägget med Azure Resource Manager-mallar
description: Läs mer om hanteraren för filserverresurser malldefinitionen för önskad tillstånd Configuration DSC ()-tillägget i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 7657f27e25af62f13371a9216596f142dc2491dd
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Desired State Configuration-tillägget med Azure Resource Manager-mallar

Den här artikeln beskriver Azure Resource Manager-mallen för den [önskade tillstånd Configuration (DSC) tillägget hanteraren](dsc-overview.md).

> [!NOTE]
> Du kan stöta på något annat schema exempel. Ändringen i schemat gjordes i oktober 2016-versionen. Mer information finns i [uppdatering från det tidigare formatet](#update-from-the-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Exempel för en virtuell Windows-dator

Följande kodavsnitt som ska ingå i den **resurs** avsnitt i mallen.
DSC-tillägg ärver standardegenskaperna för tillägget.
Mer information finns i [VirtualMachineExtension klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.76",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "protectedSettings": {
                "Items": {
                    "registrationKeyPrivate": "registrationKey"
                }
            },
            "publicSettings": {
                "configurationArguments": [{
                        "Name": "RegistrationKey",
                        "Value": {
                            "UserName": "PLACEHOLDER_DONOTUSE",
                            "Password": "PrivateSettingsRef:registrationKeyPrivate"
                        }
                    },
                    {
                        "RegistrationUrl": "registrationUrl",
                    },
                    {
                        "NodeConfigurationName": "nodeConfigurationName"
                    }
                ]
            }
        }
    }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Exempel för Windows virtuella skala anger

En virtuell dator scale set-nod har en **egenskaper** avsnitt som har en **VirtualMachineProfile extensionProfile** attribut.
Under **tillägg**, lägga till information för DSC-tillägg.

DSC-tillägg ärver standardegenskaperna för tillägget.
Mer information finns i [VirtualMachineScaleSetExtension klassen](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [{
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.Powershell",
            "type": "DSC",
            "typeHandlerVersion": "2.76",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "protectedSettings": {
                    "Items": {
                        "registrationKeyPrivate": "registrationKey"
                    }
                },
                "publicSettings": {
                    "configurationArguments": [{
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        {
                            "RegistrationUrl": "registrationUrl",
                        },
                        {
                            "NodeConfigurationName": "nodeConfigurationName"
                        }
                    ]
                }
            },
        }
    }]
}
```

## <a name="detailed-settings-information"></a>Information om detaljerade inställningar

Använd följande schema i den **inställningar** avsnitt i Azure DSC-tillägg i en Resource Manager-mall.

En lista över de argument som är tillgängliga för standard-konfigurationsskript finns [standard konfigurationsskript](#default-configuration-script).

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
| settings.wmfVersion |sträng |Anger vilken version av Windows Management Framework (WMF) och som ska installeras på den virtuella datorn. Den här egenskapen **senaste** installerar den senaste versionen av WMF. För närvarande endast möjliga värden för den här egenskapen är **4.0**, **5.0**, **5.0PP**, och **senaste**. Dessa möjliga värden är regleras av uppdateringar. Standardvärdet är **senaste**. |
| settings.configuration.url |sträng |Anger URL-plats som du vill hämta DSC-konfiguration ZIP-fil. Om den URL som kräver en SAS-token för åtkomst, ange den **protectedSettings.configurationUrlSasToken** egenskapen till värdet för SAS-token. Den här egenskapen är obligatorisk om **settings.configuration.script** eller **settings.configuration.function** definieras. Om inget värde anges för dessa egenskaper tillägget anropar standard konfigurationsskript för att ange plats Configuration Manager (MGM) metadata och argument måste anges. |
| Settings.Configuration.Script |sträng |Anger namnet på det skript som innehåller definitionen av DSC-konfigurationen. Det här skriptet måste vara i rotmappen på .zip-filen som har hämtats från den URL som anges av den **configuration.url** egenskapen. Den här egenskapen är obligatorisk om **settings.configuration.url** eller **settings.configuration.script** definieras. Om inget värde anges för dessa egenskaper tillägget anropar standard konfigurationsskript för att ange MGM metadata och argument måste anges. |
| settings.configuration.function |sträng |Anger namnet på DSC-konfigurationen. Den konfiguration som heter måste ingå i skriptet som **configuration.script** definierar. Den här egenskapen är obligatorisk om **settings.configuration.url** eller **settings.configuration.function** definieras. Om inget värde anges för dessa egenskaper tillägget anropar standard konfigurationsskript för att ange MGM metadata och argument måste anges. |
| settings.configurationArguments |Samling |Definierar de parametrar som du vill skicka till DSC-konfigurationen. Den här egenskapen är inte krypterad. |
| settings.configurationData.url |sträng |Anger den URL som du vill hämta din konfigurationsdatafilen (.psd1) ska användas som indata för DSC-konfigurationen. Om den URL som kräver en SAS-token för åtkomst, ange den **protectedSettings.configurationDataUrlSasToken** egenskapen till värdet för SAS-token. |
| settings.privacy.dataEnabled |sträng |Aktiverar eller inaktiverar telemetri samling. Endast möjliga värden för den här egenskapen är **aktivera**, **inaktivera**, **''**, eller **$null**. Om du lämnar den här egenskapen är tomt eller null kan telemetri. Standardvärdet är **''**. Mer information finns i [Azure DSC-tillägg-datainsamling](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Samling |Definierar alternativa platser som du vill hämta WMF. Mer information finns i [Azure DSC-tillägg 2.8 och hur du mappar nedladdningar av beroenden för tillägg till en egen plats](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Samling |Definierar de parametrar som du vill skicka till DSC-konfigurationen. Den här egenskapen är krypterad. |
| protectedSettings.configurationUrlSasToken |sträng |Anger SAS-token för att få åtkomst till URL: en som **configuration.url** definierar. Den här egenskapen är krypterad. |
| protectedSettings.configurationDataUrlSasToken |sträng |Anger SAS-token för att få åtkomst till URL: en som **configurationData.url** definierar. Den här egenskapen är krypterad. |

## <a name="default-configuration-script"></a>Standard-konfigurationsskript

Mer information om följande värden finns [Local Configuration Manager grundläggande inställningar](/powershell/dsc/metaconfig#basic-settings).
Du kan använda DSC-tillägg standard-konfigurationsskript för att konfigurera endast MGM egenskaper som visas i följande tabell.

| Egenskapsnamn | Typ | Beskrivning |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |SecureString |Obligatorisk egenskap. Anger den nyckel som används för att registrera med Azure Automation-tjänsten som lösenordet för en PowerShell-autentiseringsobjekt för en nod. Det här värdet kan identifieras automatiskt med hjälp av den **listkeys** metoden mot Automation-kontot. Värdet ska skyddas som en skyddad inställning. |
| settings.configurationArguments.RegistrationUrl |sträng |Obligatorisk egenskap. Anger URL för Automation-slutpunkten där noden försöker registrera. Det här värdet kan identifieras automatiskt med hjälp av den **referens** metoden mot Automation-kontot. |
| settings.configurationArguments.NodeConfigurationName |sträng |Obligatorisk egenskap. Anger nodkonfigurationen i Automation-konto för att tilldela noden. |
| settings.configurationArguments.ConfigurationMode |sträng |Anger läget för MGM. Giltiga alternativ är **ApplyOnly**, **ApplyandMonitor**, och **ApplyandAutoCorrect**.  Standardvärdet är **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | UInt32 | Anger hur ofta MGM försöker kontakta Automation-kontot för uppdateringar.  Standardvärdet är **30**.  Minimivärdet är **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | UInt32 | Anger hur ofta MGM verifierar den aktuella konfigurationen. Standardvärdet är **15**. Minimivärdet är **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolesk | Anger om en nod kan startas automatiskt, om en DSC-åtgärd begär den. Standardvärdet är **FALSKT**. |
| settings.configurationArguments.ActionAfterReboot | sträng | Anger vad som händer när datorn startas om när du använder en konfiguration. Giltiga alternativ är **ContinueConfiguration** och **StopConfiguration**. Standardvärdet är **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolesk | Anger om MGM skriver över befintliga moduler på noden. Standardvärdet är **FALSKT**. |

## <a name="settings-vs-protectedsettings"></a>Inställningar för vs. ProtectedSettings

Alla inställningarna sparas i en textfil för inställningarna på den virtuella datorn.
Egenskaper som anges **inställningar** är offentliga egenskaper.
Offentliga egenskaper är inte krypterad i text inställningsfil.
Egenskaper som anges **protectedSettings** krypteras med ett certifikat och visas inte i klartext i filen med inställningar på den virtuella datorn.

Om konfigurationen måste autentiseringsuppgifter, kan du inkludera autentiseringsuppgifterna i **protectedSettings**:

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

## <a name="example-configuration-script"></a>Exempel konfigurationsskript

I följande exempel visas standardbeteendet för DSC-tillägg som är att förse MGM Metadatainställningar och registrera med Automation DSC-tjänsten.
Konfigurationen argument är obligatoriska.
Konfigurationen argument skickades till standard konfigurationsskript ange MGM metadata.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl": "[parameters('registrationUrl1')]",
        "NodeConfigurationName": "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "Items": {
        "registrationKeyPrivate": "[parameters('registrationKey1']"
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exempel på användning av konfigurationsskript i Azure Storage

Följande exempel är från den [översikt över hanteraren av DSC-tillägget](dsc-overview.md).
Det här exemplet använder Resource Manager-mallar i stället för cmdlets för att distribuera tillägget.
Spara konfigurationen IisInstall.ps1, placera det i en .zip-fil och sedan ladda upp filen i en URL som kan nås.
Det här exemplet används Azure Blob storage, men du kan hämta ZIP-filer från en valfri plats.

Följande kod instruerar i Resource Manager-mall så att ladda ned rätt fil och kör sedan funktionen PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="update-from-a-previous-format"></a>Uppdatera från ett tidigare format

Alla inställningar i ett tidigare format för tillägget (och som har de gemensamma egenskaperna **ModulesUrl**, **ConfigurationFunction**, **SasToken**, eller  **Egenskaper för**) automatiskt anpassas till det aktuella formatet för tillägget.
De köras på samma sätt som de gjorde före.

Följande schema visar vilka tidigare inställningar schemat kan se ut:

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

Här är hur det tidigare formatet anpassar sig till det aktuella formatet:

| Egenskapsnamn | Tidigare schemat motsvarande |
| --- | --- |
| settings.wmfVersion |inställningar. WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| Settings.Configuration.Script |Första delen av inställningar. ConfigurationFunction (innan \\ \\) |
| settings.configuration.function |Andra delen av inställningar. ConfigurationFunction (när \\ \\) |
| settings.configurationArguments |inställningar. Egenskaper |
| settings.configurationData.url |protectedSettings.DataBlobUri (utan SAS-token) |
| settings.privacy.dataEnabled |inställningar. Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |inställningar. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |inställningar. SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS-token från protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Felsökning – felkoden 1100

Felkoden 1100 tyder på problem med användarindata DSC-tillägget.
Texten för dessa fel varierar och kan ändras.
Här följer några av de fel som kan uppstå och hur du kan åtgärda dem.

### <a name="invalid-values"></a>Ogiltiga värden

”Privacy.dataCollection är '{0}'.
Endast möjliga värden är ”,” aktivera ”och” inaktivera ””.
”WmfVersion är '{0}'.
Endast möjliga värden är... ' senaste ' ”.

**Problemet**: ett angivet värde tillåts inte.

**Lösningen**: ändra det ogiltiga värdet till ett giltigt värde.
Mer information finns i tabellen i [information](#details).

### <a name="invalid-url"></a>Ogiltig URL

”ConfigurationData.url är '{0}'. Detta är inte en giltig URL ”” DataBlobUri är '{0}'. Detta är inte en giltig URL ”” Configuration.url är '{0}'. Detta är inte en giltig URL ”

**Problemet**: A angivna Webbadressen inte är giltig.

**Lösningen**: Kontrollera alla dina angivna URL: er.
Se till att alla URL: er motsvara giltiga platser att tillägget kan komma åt på fjärrdatorn.

### <a name="invalid-configurationargument-type"></a>Ogiltig ConfigurationArgument-typ.

”Ogiltigt configurationArguments typen {0}”

**Problem**: den *ConfigurationArguments* egenskapen kan inte matchas till en **Hashtable** objekt.

**Lösningen**: se din *ConfigurationArguments* egenskapen en **Hashtable**.
Följ det format som anges i föregående exempel. Håll utkik efter citattecken, kommatecken och klammerparenteser.

### <a name="duplicate-configurationarguments"></a>Duplicera ConfigurationArguments

”Hitta dubblerade argument{0}' i både offentliga och skyddade configurationArguments”

**Problem**: den *ConfigurationArguments* i inställningar för offentliga och *ConfigurationArguments* i skyddade inställningarna har egenskaper med samma namn.

**Lösningen**: ta bort en av de duplicera egenskaperna.

### <a name="missing-properties"></a>Egenskaper som saknas

”Configuration.function kräver att configuration.url eller configuration.module har angetts”

”Configuration.url kräver att configuration.script anges”

”Configuration.script kräver att configuration.url anges”

”Configuration.url kräver att configuration.function anges”

”ConfigurationUrlSasToken kräver att configuration.url anges”

”ConfigurationDataUrlSasToken kräver att configurationData.url anges”

**Problemet**: en definierad egenskap måste en annan egenskap som saknas.

**Lösningar**:

- Ange egenskapen saknas.
- Ta bort egenskapen som måste egenskapen saknas.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [med virtuella skalan anger med Azure DSC-tillägg](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Hitta mer information om [hantering av DSC-säkra autentiseringsuppgifter](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Hämta en [introduktion till Azure DSC-tillägg hanteraren](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Mer information om PowerShell DSC går du till den [PowerShell Dokumentationscenter](/powershell/dsc/overview).
