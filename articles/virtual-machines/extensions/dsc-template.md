---
title: Desired State Configuration-tillägget med Azure Resource Manager-mallar
description: Läs mer om malldefinitionen för Resource Manager-för tillägget Desired State Configuration (DSC) i Azure.
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
ms.openlocfilehash: d007869bb8bad1a2f0775a1ab2c1bf5d27c1cb8f
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866233"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Desired State Configuration-tillägget med Azure Resource Manager-mallar

Den här artikeln beskrivs Azure Resource Manager-mallen för den [Desired State Configuration (DSC)-tilläggshanterare](dsc-overview.md).

> [!NOTE]
> Du kan stöta på något annat schema exempel. Ändringen i schemat utfördes i oktober 2016-versionen. Mer information finns i [uppdateringen från ett tidigare format](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Exempelmall för en virtuell Windows-dator

Följande kodfragment används i den **Resource** avsnitt i mallen.
DSC-tillägget ärver standard tilläggsegenskaper.
Mer information finns i [VirtualMachineExtension klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

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
                        "RegistrationUrl": "registrationUrl"
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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Anger exempelmall för Windows VM-skalningsuppsättning

En VM scale set-nod har en **egenskaper** avsnitt som har en **VirtualMachineProfile extensionProfile** attribut.
Under **tillägg**, lägger du till information för DSC-tillägget.

DSC-tillägget ärver standard tilläggsegenskaper.
Mer information finns i [VirtualMachineScaleSetExtension klass](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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
                            "RegistrationUrl": "registrationUrl"
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

## <a name="detailed-settings-information"></a>För detaljerad inställningsinformation

Använd följande schema i den **inställningar** avsnitt av Azure DSC-tillägget i en Resource Manager-mall.

En lista över de argument som är tillgängliga för standard-konfigurationsskript finns i [standard konfigurationsskript](#default-configuration-script).

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
| settings.wmfVersion |sträng |Anger vilken version av Windows Management Framework (WMF) och som ska installeras på den virtuella datorn. Denna egenskap anges till **senaste** installerar den senaste versionen av WMF. För närvarande endast möjliga värden för den här egenskapen är **4.0**, **5.0**, **5.0PP**, och **senaste**. Dessa möjliga värden är föremål för uppdateringar. Standardvärdet är **senaste**. |
| settings.configuration.url |sträng |Anger den URL: en plats där du kan hämta DSC-konfiguration .zip-filen. Om den URL som kräver en SAS-token för åtkomst, ange den **protectedSettings.configurationUrlSasToken** egenskapen till värdet för din SAS-token. Den här egenskapen krävs om **settings.configuration.script** eller **settings.configuration.function** har definierats. Om inget värde anges för dessa egenskaper tillägget anropar standard konfigurationsskript för att ange plats Configuration Manager (LCM) metadata och argument måste anges. |
| Settings.Configuration.Script |sträng |Anger namnet på det skript som innehåller definitionen av DSC-konfiguration. Det här skriptet måste vara i rotmappen på .zip-filen som laddas ned från den URL som anges av den **configuration.url** egenskapen. Den här egenskapen krävs om **settings.configuration.url** eller **settings.configuration.script** har definierats. Om inget värde anges för dessa egenskaper tillägget anropar standard konfigurationsskript för att ställa in MGM metadata och argument måste anges. |
| settings.configuration.function |sträng |Anger namnet på din DSC-konfiguration. Den konfiguration som heter måste ingå i skriptet som **configuration.script** definierar. Den här egenskapen krävs om **settings.configuration.url** eller **settings.configuration.function** har definierats. Om inget värde anges för dessa egenskaper tillägget anropar standard konfigurationsskript för att ställa in MGM metadata och argument måste anges. |
| settings.configurationArguments |Samling |Definierar de parametrar som du vill skicka till DSC-konfiguration. Den här egenskapen är inte krypterad. |
| settings.configurationData.url |sträng |Anger den URL som du vill ladda ned konfigurationsdatafilen (.psd1) från att använda som indata för DSC-konfiguration. Om den URL som kräver en SAS-token för åtkomst, ange den **protectedSettings.configurationDataUrlSasToken** egenskapen till värdet för din SAS-token. |
| settings.privacy.dataEnabled |sträng |Aktiverar eller inaktiverar telemetriinsamling. Endast möjliga värden för den här egenskapen är **aktivera**, **inaktivera**, **''**, eller **$null**. Lämna den här egenskapen tom eller null kan telemetri. Standardvärdet är **''**. Mer information finns i [Azure DSC-tillägg-datainsamling](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Samling |Definierar alternativa platser som ska hämta WMF från. Mer information finns i [Azure DSC-tillägget 2.8 och mappa nedladdningar av beroenden för tillägg till din egen plats](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Samling |Definierar de parametrar som du vill skicka till DSC-konfiguration. Den här egenskapen är krypterad. |
| protectedSettings.configurationUrlSasToken |sträng |Anger SAS-token för att få åtkomst till URL: en som **configuration.url** definierar. Den här egenskapen är krypterad. |
| protectedSettings.configurationDataUrlSasToken |sträng |Anger SAS-token för att få åtkomst till URL: en som **configurationData.url** definierar. Den här egenskapen är krypterad. |

## <a name="default-configuration-script"></a>Standard konfigurationsskript

Mer information om följande värden finns i [grundläggande inställningar för lokal konfigurationshanterare](/powershell/dsc/metaconfig#basic-settings).
Du kan använda konfigurationsskript för DSC-tillägget standard för att konfigurera endast de LCM-egenskaper som visas i följande tabell.

| Egenskapsnamn | Typ | Beskrivning |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |SecureString |Obligatorisk egenskap. Anger den nyckel som används för en nod för att registrera med Azure Automation-tjänsten som lösenord för ett objekt för PowerShell-autentiseringsuppgift. Det här värdet kan identifieras automatiskt med hjälp av den **listnycklar** metoden mot Automation-kontot. Värdet bör skyddas som en skyddad. |
| settings.configurationArguments.RegistrationUrl |sträng |Obligatorisk egenskap. Anger URL till slutpunkten för automatisering där noden försöker registrera. Det här värdet kan identifieras automatiskt med hjälp av den **referens** metoden mot Automation-kontot. |
| settings.configurationArguments.NodeConfigurationName |sträng |Obligatorisk egenskap. Anger nodkonfigurationen i Automation-kontot ska tilldelas noden. |
| settings.configurationArguments.ConfigurationMode |sträng |Anger läget för LCM. Giltiga alternativ är **ApplyOnly**, **ApplyandMonitor**, och **ApplyandAutoCorrect**.  Standardvärdet är **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | UInt32 | Anger hur ofta LCM försöker kontrollera med Automation-konto för uppdateringar.  Standardvärdet är **30**.  Minsta tillåtna värde är **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | UInt32 | Anger hur ofta MGM verifierar den aktuella konfigurationen. Standardvärdet är **15**. Minsta tillåtna värde är **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolesk | Anger om en nod kan startas automatiskt, om en DSC-åtgärd begär den. Standardvärdet är **FALSKT**. |
| settings.configurationArguments.ActionAfterReboot | sträng | Anger vad som händer när en omstart när du använder en konfiguration. Giltiga alternativ är **ContinueConfiguration** och **StopConfiguration**. Standardvärdet är **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolesk | Anger om LCM skriver över befintliga moduler på noden. Standardvärdet är **FALSKT**. |

## <a name="settings-vs-protectedsettings"></a>Jämfört med inställningar ProtectedSettings

Alla inställningar sparas i en textfil i inställningar på den virtuella datorn.
Egenskaper som visas under **inställningar** är offentliga egenskaper.
Offentliga egenskaper inte är krypterade i textfilen inställningar.
Egenskaper som visas under **protectedSettings** krypteras med ett certifikat och visas inte i oformaterad text i filen på den virtuella datorn.

Om konfigurationen behöver autentiseringsuppgifter, kan du ange autentiseringsuppgifterna i **protectedSettings**:

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

## <a name="example-configuration-script"></a>Exempelskript för IP-konfiguration

I följande exempel visas standardbeteendet för DSC-tillägg, vilket är att förse MGM, Metadatainställningar och registrera med Automation DSC-tjänsten.
Konfiguration av argument är obligatoriska.
Konfiguration av argumenten skickas till standard-konfigurationsskript att ställa in MGM metadata.

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

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exempel på användning av skript för konfiguration i Azure Storage

I följande exempel är från den [hanteringsöversikt för DSC-tillägget](dsc-overview.md).
Det här exemplet använder Resource Manager-mallar i stället för cmdletar för att distribuera tillägget.
Spara konfigurationen IisInstall.ps1, placera den i en .zip-fil och överför sedan filen i en URL som kan nås.
Det här exemplet används Azure Blob storage, men du kan ladda ned ZIP-filer från vilken plats som valfri.

Följande kod instruerar den virtuella datorn ska ladda ned rätt fil och kör sedan funktionen PowerShell i Resource Manager-mall:

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

Alla inställningar i ett tidigare format av tillägget (och som har de offentliga egenskaperna **ModulesUrl**, **ConfigurationFunction**, **SasToken**, eller  **Egenskaper för**) anpassar sig automatiskt till det aktuella formatet av tillägget.
De körs på samma sätt som de gjorde före.

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

Här är hur det tidigare formatet anpassas till det aktuella formatet:

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

Felkoden 1100 tyder på problem med användarinformation till DSC-tillägget.
Texten i de här felen varierar och kan ändras.
Här är några av de fel som kan uppstå och hur du kan åtgärda dem.

### <a name="invalid-values"></a>Ogiltiga värden

”Privacy.dataCollection är '{0}'.
Endast möjliga värden är ”,” aktivera ”och” inaktivera ””.
”WmfVersion är '{0}'.
Endast möjliga värden är... och ”senaste” ”.

**Problemet**: ett angivet värde tillåts inte.

**Lösningen**: ändra det ogiltiga värdet till ett giltigt värde.
Mer information finns i tabellen i [information](#details).

### <a name="invalid-url"></a>Ogiltig URL

”ConfigurationData.url är '{0}'. Detta är inte en giltig URL ”” DataBlobUri är '{0}'. Detta är inte en giltig URL ”” Configuration.url är '{0}'. Detta är inte en giltig URL ”

**Problemet**: A angivna Webbadressen inte är giltig.

**Lösningen**: Kontrollera alla dina angivna URL: er.
Se till att alla URL: er matcha till giltiga platser att tillägget har åtkomst till på fjärrdatorn.

### <a name="invalid-configurationargument-type"></a>Ogiltig ConfigurationArgument-typ

”Ogiltigt configurationArguments typ {0}”

**Problemet**: den *ConfigurationArguments* egenskapen kan inte matchas till en **hash-tabell** objekt.

**Lösningen**: Kontrollera din *ConfigurationArguments* egenskapen en **hash-tabell**.
Följ det format som anges i föregående exempel. Bevaka citattecken och kommatecken klammerparenteser.

### <a name="duplicate-configurationarguments"></a>Duplicera ConfigurationArguments

”Hitta duplicerade argument{0}' i både offentliga och skyddade configurationArguments”

**Problemet**: den *ConfigurationArguments* i inställningarna för offentliga och *ConfigurationArguments* i skyddade inställningarna har egenskaper med samma namn.

**Lösningen**: ta bort en av de duplicerade egenskaperna.

### <a name="missing-properties"></a>Saknade egenskaper

”Configuration.function kräver att configuration.url eller configuration.module har angetts”

”Configuration.url kräver att configuration.script anges”

”Configuration.script kräver att configuration.url anges”

”Configuration.url kräver att configuration.function anges”

”ConfigurationUrlSasToken kräver att configuration.url anges”

”ConfigurationDataUrlSasToken kräver att configurationData.url anges”

**Problemet**: en definierad egenskap behöver en annan egenskap som saknas.

**Lösningar**:

- Ange egenskapen som saknas.
- Ta bort egenskapen som måste egenskapen som saknas.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [använder virtuella skalningsuppsättningar med Azure DSC-tillägget](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Hitta mer information om [DSCs säker autentiseringshantering](dsc-credentials.md).
- Hämta en [introduktion till Azure DSC-tilläggshanterare](dsc-overview.md).
- Mer information om PowerShell DSC, går du till den [PowerShell dokumentationscentret](/powershell/dsc/overview).
