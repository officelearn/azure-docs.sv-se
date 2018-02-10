---
title: "Desired Configuration-tillägg för tillstånd med Azure Resource Manager-mallar | Microsoft Docs"
description: "Definition av Resource Manager-mall för önskad tillstånd Configuration tillägg i Azure"
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: f638d1530541526316f6e409f1efd44f136992a5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Önskad konfiguration tillägg av tillstånd med Azure Resource Manager-mallar

Den här artikeln beskriver Azure Resource Manager-mallen för den [Desired State Configuration-tillägget hanteraren](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

*Obs: du kan stöta på något annat schema exempel. * 
 *Ändringen i schemat gjordes i oktober 2016 släpper.* 
 *Information anges i avsnittet i den här sidan med titeln*
*[uppdatering från det tidigare formatet](##Updating-from-the-Previous-Format)*.

## <a name="template-example-for-a-windows-vm"></a>Exempel för en virtuell Windows-dator

Följande kodavsnitt försätts i avsnittet för mallen.
DSC-tillägg ärver standardegenskaperna för tillägget enligt beskrivningen i [VirtualMachineExtension-klass](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.)

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.72",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                    "configurationArguments": {
                        {
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        "RegistrationUrl" : "[parameters('registrationUrl1')]",
                        "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
```

## <a name="template-example-for-windows-vmss"></a>Exempel för Windows VMSS

En VMSS-nod har ett ”egenskaper” avsnitt med den ”VirtualMachineProfile”, ”extensionProfile”-attribut. DSC läggs till under ”tillägg”.

DSC-tillägg ärver standardegenskaperna för tillägget enligt beskrivningen i [VirtualMachineScaleSetExtension klassen](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.72",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configurationArguments": {
                                {
                                    "Name": "RegistrationKey",
                                    "Value": {
                                        "UserName": "PLACEHOLDER_DONOTUSE",
                                        "Password": "PrivateSettingsRef:registrationKeyPrivate"
                                    },
                                },
                                "RegistrationUrl" : "[parameters('registrationUrl1')]",
                                "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
                ]
            }
        }
```

## <a name="detailed-settings-information"></a>Information om detaljerade inställningar

Följande schema är inställningar del av Azure DSC-tillägg i en Azure Resource Manager-mall.

*En lista över de argument som är tillgängliga för konfigurationsskript standard*
*finns i avsnittet nedan med namnet*
*[standard konfigurationsskript](##Default-Configuration-Script) *.

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
| settings.wmfVersion |sträng |Anger versionen av Windows Management Framework som ska installeras på den virtuella datorn. Den här egenskapen till ”senaste” installerar den senaste uppdaterade versionen av WMF. Endast aktuella möjliga värden för den här egenskapen är **'4.0', '5.0', ' 5.0PP' och 'senaste'**. Dessa möjliga värden är regleras av uppdateringar. Standardvärdet är ”senaste”. |
| settings.configuration.url |sträng |Anger URL-plats som du vill hämta DSC-konfiguration zip-filen. Om den URL som kräver en SAS-token för åtkomst, måste du ställa in egenskapen protectedSettings.configurationUrlSasToken på värdet för SAS-token. Den här egenskapen är obligatorisk om settings.configuration.script och/eller settings.configuration.function definieras. Om inget värde anges för dessa egenskaper tillägget ringer standard konfigurationsskript för att ange MGM metadata och argument måste anges. |
| settings.configuration.script |sträng |Anger namnet på det skript som innehåller definitionen av DSC-konfigurationen. Det här skriptet måste vara i rotmappen på zip-filen som hämtats från den URL som anges av egenskapen configuration.url. Den här egenskapen är obligatorisk om settings.configuration.url och/eller settings.configuration.script definieras. Om inget värde anges för dessa egenskaper tillägget ringer standard konfigurationsskript för att ange MGM metadata och argument som ska användas. |
| settings.configuration.function |sträng |Anger namnet på DSC-konfigurationen. Konfigurationen med namnet måste finnas i skriptet som definieras av configuration.script. Den här egenskapen är obligatorisk om settings.configuration.url och/eller settings.configuration.function definieras. Om inget värde anges för dessa egenskaper tillägget ringer standard konfigurationsskript för att ange MGM metadata och argument måste anges. |
| settings.configurationArguments |Samling |Definierar de parametrar som du vill skicka DSC-konfigurationen. Den här egenskapen är inte krypterad. |
| settings.configurationData.url |sträng |Anger den URL som du vill hämta din konfigurationsdatafilen (.psd1) ska användas som indata för DSC-konfigurationen. Om den URL som kräver en SAS-token för åtkomst, måste du ställa in egenskapen protectedSettings.configurationDataUrlSasToken på värdet för SAS-token. |
| settings.privacy.dataEnabled |sträng |Aktiverar eller inaktiverar telemetri samling. Endast möjliga värden för den här egenskapen är **'Aktivera', inaktivera, '', eller $null**. Om du lämnar den här egenskapen är tomt eller null kan telemetri. Standardvärdet är ”. [Mer Information](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Samling |Definierar alternativa platser som du vill hämta WMF. [Mer Information](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Samling |Definierar de parametrar som du vill skicka DSC-konfigurationen. Den här egenskapen är krypterad. |
| protectedSettings.configurationUrlSasToken |sträng |Anger SAS-token för att komma åt den URL som definierats av configuration.url. Den här egenskapen är krypterad. |
| protectedSettings.configurationDataUrlSasToken |sträng |Anger SAS-token för att komma åt den URL som definierats av configurationData.url. Den här egenskapen är krypterad. |

## <a name="default-configuration-script"></a>Standard-konfigurationsskript

Mer information om dessa värden finns på dokumentationssidan [lokala Configuration Manager grundläggande inställningar](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings).
MGM egenskaper i tabellen nedan kan konfigureras med hjälp av DSC-tillägg-standard konfigurationsskript.

| Egenskapsnamn | Typ | Beskrivning |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |SecureString |Obligatorisk egenskap. Anger den nyckel som används för en nod för att registrera med Azure Automation-tjänsten som lösenordet för en PowerShell-autentiseringsobjekt. Det här värdet kan identifieras automatiskt med hjälp av metoden listkeys mot Automation-kontot och bör skyddas som en skyddad inställning. |
| settings.configurationArguments.RegistrationUrl |sträng |Obligatorisk egenskap. Anger Url för Azure Automation-slutpunkten där noden kommer att försöka registrera. Det här värdet kan identifieras automatiskt med referensmetoden mot Automation-kontot. |
| settings.configurationArguments.NodeConfigurationName |sträng |Obligatorisk egenskap. Anger konfigurationen av noden i Azure Automation-konto för att tilldela noden. |
| settings.configurationArguments.ConfigurationMode |sträng |Anger läget för Local Configuration Manager. Giltiga alternativ är ”ApplyOnly”, ”ApplyandMonitor” och ”ApplyandAutoCorrect”.  Standardvärdet är ”ApplyandMonitor”. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Anger hur ofta MGM om du försöker att kontrollera med Automation-kontot för uppdateringar.  Standardvärdet är 30.  Lägsta värdet är 15. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Anger hur ofta MGM valideras i den aktuella konfigurationen.  Standardvärdet är 15.  Lägsta värdet är 15. |
| settings.configurationArguments.RebootNodeIfNeeded | boolesk | Anger om en nod kan startas automatiskt, om en DSC-åtgärd begär den.  Standardvärdet är false. |
| settings.configurationArguments.ActionAfterReboot | sträng | Anger vad som händer när datorn startas om när du använder en konfiguration. Giltiga alternativ är ”ContinueConfiguration” och ”StopConfiguration”. Standardvärdet är ”ContinueConfiguration”. |
| settings.configurationArguments.AllowModuleOverwrite | boolesk | Anger om MGM ska skriva över befintliga moduler på noden.  Standardvärdet är false. |

## <a name="settings-vs-protectedsettings"></a>Inställningar för vs. ProtectedSettings

Alla inställningarna sparas i en textfil för inställningarna på den virtuella datorn.
Egenskaper under ”inställningar” är offentliga egenskaper eftersom de inte är krypterade i text inställningsfil.
Egenskaperna under 'protectedSettings' krypteras med ett certifikat och visas inte i klartext i den här filen på den virtuella datorn.

Om konfigurationen måste autentiseringsuppgifter, kan de ingå i protectedSettings:

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

## <a name="example"></a>Exempel

I följande exempel är standardbeteendet för DSC-tillägg, vilket är att ange Metadatainställningar för till lokala Configuration Manager och registrera med Azure Automation DSC-tjänsten.
Konfigurationen argument är obligatoriska och kommer att skickas till standard-konfigurationsskript ange MGM metadata.

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
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
  }
},
"protectedSettings": {
    "Items": {
                "registrationKeyPrivate": "[parameters('registrationKey1']"
            }
}
```

## <a name="example-using-configuration-script-in-azure-storage"></a>Exempel på användning av konfigurationsskript i Azure Storage

I följande exempel härleds från avsnittet ”komma igång” i den [översiktssidan för DSC-tillägg hanteraren](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
Det här exemplet använder Resource Manager-mallar i stället för cmdlets för att distribuera tillägget.
Spara konfigurationen ”IisInstall.ps1”, placera den i en. ZIP-filen och överföra filen i en URL som kan nås.
Det här exemplet använder Azure blob storage, men det är möjligt att ladda ned. ZIP-filer från en valfri plats.

Följande kod instruerar i Azure Resource Manager-mallen så att ladda ned rätt fil och kör funktionen PowerShell:

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

## <a name="updating-from-the-previous-format"></a>Uppdatering av från det tidigare formatet

Alla inställningar i föregående format (som innehåller de gemensamma egenskaperna ModulesUrl, ConfigurationFunction, SasToken eller egenskaper) automatiskt anpassas till det aktuella formatet och kör precis som de gjorde före.

Följande schema är vilka tidigare inställningar schemat kan se ut:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
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
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Första delen av inställningar. ConfigurationFunction (före '\\\\”) |
| settings.configuration.function |Andra delen av inställningar. ConfigurationFunction (när '\\\\”) |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (utan SAS-token) |
| settings.privacy.dataEnabled |settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS-token från protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Felsökning – felkoden 1100

Felkoden 1100 anger att det finns ett problem med användarindata DSC-tillägget.
Texten för dessa fel är variabel och kan ändras.
Här är några av de fel som kan du stöta på och hur du kan åtgärda dem.

### <a name="invalid-values"></a>Ogiltiga värden

”Privacy.dataCollection är: {0}.
Endast möjliga värden är ”,” aktivera ”och” inaktivera ””.
”WmfVersion är: {0}.
Endast möjliga värden är... ' senaste ' ”.

Problem: Det är inte tillåtet för ett angivet värde.

Lösning: Ändra det ogiltiga värdet till ett giltigt värde.
Se tabellen i avsnittet information.

### <a name="invalid-url"></a>Ogiltig URL

”ConfigurationData.url är: {0}. Detta är inte en giltig URL ”” DataBlobUri är: {0}. Detta är inte en giltig URL ”” Configuration.url är: {0}. Detta är inte en giltig URL ”

Problem: A angivna Webbadressen inte är giltig.

Lösning: Kontrollera att alla dina angivna URL: er.
Kontrollera att alla URL: er motsvara giltiga platser att tillägget kan komma åt på fjärrdatorn.

### <a name="invalid-configurationargument-type"></a>Ogiltig ConfigurationArgument-typ.

”Ogiltigt configurationArguments typ {0}”

Problem: Egenskapen ConfigurationArguments kan inte matchas till ett hash-objekt.

Lösning: Kontrollera ConfigurationArguments-egenskap en hash-tabell.
Följ det format som anges i föregående exempel.
Se upp för citattecken, kommatecken och klammerparenteser.

### <a name="duplicate-configurationarguments"></a>Duplicera ConfigurationArguments

”Finns duplicerade argument: {0} i både offentliga och skyddade configurationArguments”

Problem: ConfigurationArguments i inställningar för offentliga och ConfigurationArguments i skyddade inställningarna innehåller egenskaper med samma namn.

Lösning: Ta bort en av de duplicera egenskaperna.

### <a name="missing-properties"></a>Egenskaper som saknas
”Configuration.function kräver att configuration.url eller configuration.module har angetts”

”Configuration.url kräver att configuration.script anges”

”Configuration.script kräver att configuration.url anges”

”Configuration.url kräver att configuration.function anges”

”ConfigurationUrlSasToken kräver att configuration.url anges”

”ConfigurationDataUrlSasToken kräver att configurationData.url anges”

Problem: En definierad egenskap måste en annan egenskap som saknas.

Lösningar:

- Ange egenskapen saknas.
- Ta bort egenskapen som måste egenskapen saknas.

## <a name="next-steps"></a>Nästa steg

Läs mer om DSC och virtuella datorn anger i [med hjälp av virtuella Skalningsuppsättningarna med Azure DSC-tillägg](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).

Mer information finns på [hantering av DSC-säkra autentiseringsuppgifter](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om Azure DSC-tillägg-hanteraren finns [introduktion till Azure Desired State Configuration-tillägget hanteraren](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om PowerShell DSC [finns på PowerShell documentation center](https://msdn.microsoft.com/powershell/dsc/overview).
