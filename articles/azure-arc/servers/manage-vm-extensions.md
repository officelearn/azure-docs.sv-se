---
title: Hantering av VM-tillägg med Azure Arc-aktiverade servrar (förhands granskning)
description: Azure Arc-aktiverade servrar (för hands version) kan hantera distribution av virtuella dator tillägg som tillhandahåller konfiguration och automatisering av virtuella datorer med icke-virtuella datorer i Azure.
ms.date: 06/17/2020
ms.topic: conceptual
ms.openlocfilehash: 259a55c0e5cb4074871fd70a61e6c61d5cf3c50a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018169"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers-preview"></a>Hantering av virtuella dator tillägg med Azure Arc-aktiverade servrar (förhands granskning)

Tillägg för virtuella datorer (VM) är små program som ger konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer. Om en virtuell dator till exempel behöver programvaruinstallation, antivirusskydd eller körning av ett skript på den kan ett VM-tillägg användas.

Med Azure Arc-aktiverade servrar (för hands version) kan du distribuera virtuella Azure-tillägg till virtuella Windows-och Linux-datorer som inte är Azure-datorer, vilket fören klar hanteringen av hybrid datorn lokalt, i Edge och i andra moln miljöer genom deras livs cykel.

## <a name="key-benefits"></a>Viktiga fördelar

Azure Arc-aktiverade servrar (för hands version) stöd för VM-tillägg ger följande viktiga fördelar:

* Använd [Azure Automation tillstånds konfiguration](../../automation/automation-dsc-overview.md) för att centralt lagra konfigurationer och upprätthålla det önskade läget för Hybrid anslutna datorer som är aktiverade via DSC VM-tillägget.

* Samla in loggdata för analys med [loggar i Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) aktiverat via det virtuella Log Analytics agent-tillägget. Detta är användbart för att utföra komplexa analyser mellan data från olika källor.

* Med [Azure Monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)analyseras prestandan för dina virtuella Windows-och Linux-datorer och övervaka deras processer och beroenden på andra resurser och externa processer. Detta uppnås genom att aktivera både VM-tillägg för Log Analytics agent och beroende agent.

* Ladda ned och kör skript på Hybrid anslutna datorer med tillägget för anpassat skript. Det här tillägget är användbart för konfiguration av distribution, program varu installation eller andra konfigurations-eller hanterings uppgifter.

## <a name="availability"></a>Tillgänglighet

Funktioner för virtuella dator tillägg är bara tillgängliga i listan över [regioner som stöds](overview.md#supported-regions). Se till att du har registrerat datorn i någon av dessa regioner.

## <a name="extensions"></a>Tillägg

I den här för hands versionen har vi stöd för följande VM-tillägg på Windows-och Linux-datorer.

|Filnamnstillägg |Operativsystem |Publisher |Ytterligare information |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Anpassat skript tillägg för Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Windows PowerShell DSC-tillägg](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics-agent |Windows |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics VM-tillägg för Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft-beroende agent | Windows |Microsoft.Compute | [Tillägg för virtuell dator för beroende agent för Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. extension |[Anpassat skript tillägg för Linux version 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[PowerShell DSC-tillägg för Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-agent |Linux |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics VM-tillägg för Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft-beroende agent | Linux |Microsoft.Compute | [Tillägg för virtuell dator för beroende agent för Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

VM-tillägg kan köras med Azure Resource Manager mallar, från Azure Portal eller Azure PowerShell på Hybrid servrar som hanteras av Arc-aktiverade servrar (för hands version).

Mer information om paketet för Azure Connected Machine agent och information om tilläggs Agent komponenten finns i [agent översikt](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Förutsättningar

Den här funktionen är beroende av följande Azure-resurs leverantörer i din prenumeration:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Om de inte redan är registrerade följer du stegen under [Registrera Azure Resource providers](agent-overview.md#register-azure-resource-providers).

Det virtuella dator tillägget för Log Analytics agent för Linux kräver python 2. x installerat på mål datorn.

### <a name="connected-machine-agent"></a>Ansluten dator agent

Kontrol lera att din dator matchar de versioner av Windows och Linux-operativsystem som [stöds](agent-overview.md#supported-operating-systems) för den Azure-anslutna dator agenten.

Den lägsta versionen av den anslutna dator agenten som stöds av den här funktionen är:

* Windows-0,7. x
* Linux-0,8. x

Om du vill uppgradera datorn till den version av agenten som krävs, se [uppgraderings agenten](manage-agent.md#upgrading-agent).

## <a name="enable-extensions-from-the-portal"></a>Aktivera tillägg från portalen

VM-tillägg kan tillämpas på den hanterade datorn för Server (för hands version) via Azure Portal.

1. Gå till [Azure Portal](https://aka.ms/arcserver-preview)i webbläsaren.

2. I portalen bläddrar du till **datorer – Azure-båge** och väljer hybrid datorn i listan.

3. Välj **tillägg**och välj sedan **Lägg till**. Välj det tillägg du vill använda i listan över tillgängliga tillägg och följ anvisningarna i guiden. I det här exemplet ska vi distribuera Log Analytics VM-tillägget. 

    ![Välj VM-tillägg för den valda datorn](./media/manage-vm-extensions/add-vm-extensions.png)

    I följande exempel visas installationen av Log Analytics VM-tillägget från Azure Portal:

    ![Installera Log Analytics VM-tillägg](./media/manage-vm-extensions/mma-extension-config.png)

    För att slutföra installationen måste du ange arbetsyte-ID och primär nyckel. Om du inte är bekant med hur du hittar den här informationen kan du läsa [Hämta arbetsyte-ID och nyckel](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. När du har bekräftat nödvändig information som har angetts väljer du **skapa**. En sammanfattning av distributionen visas och du kan granska distributionens status.

>[!NOTE]
>Även om flera tillägg kan grupperas tillsammans och bearbetas, installeras de seriellt. När den första tilläggs installationen har slutförts görs ett försök att installera nästa tillägg.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager mall och köras med mallen. Med de VM-tillägg som stöds av Arc-aktiverade servrar (för hands version) kan du distribuera VM-tillägget som stöds på Linux-eller Windows-datorer med Azure PowerShell. Varje exempel nedan innehåller en mallfil och en parameter fil med exempel värden som du kan använda för mallen.

>[!NOTE]
>Även om flera tillägg kan grupperas tillsammans och bearbetas, installeras de seriellt. När den första tilläggs installationen har slutförts görs ett försök att installera nästa tillägg.

### <a name="deploy-the-log-analytics-vm-extension"></a>Distribuera Log Analytics VM-tillägget

För att enkelt distribuera Log Analytics agenten finns följande exempel på hur du installerar agenten på Windows eller Linux.

#### <a name="template-file-for-linux"></a>Mallfil för Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Mallfil för Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Parameter fil

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Spara mallen och parametervärdena på disk och redigera parameter filen med lämpliga värden för din distribution. Du kan sedan installera tillägget på alla anslutna datorer i en resurs grupp med följande kommando. Kommandot använder parametern *TemplateFile* för att ange mallen och parametern *TemplateParameterFile* för att ange en fil som innehåller parametrar och parameter värden.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Distribuera tillägget för anpassat skript

Om du vill använda tillägget för anpassat skript kan du köra följande exempel på Windows och Linux. Om du inte är bekant med tillägget för anpassat skript, se tillägg [för anpassat skript för Windows](../../virtual-machines/extensions/custom-script-windows.md) eller [anpassat skript tillägg för Linux](../../virtual-machines/extensions/custom-script-linux.md). Det finns ett par olika egenskaper som du bör känna till när du använder det här tillägget med hybrid datorer:

* Listan över operativ system som stöds med det anpassade skript tillägget för Azure VM kan inte tillämpas på Azure Arc-aktiverade servrar. Du hittar en lista över vilka OSs-funktioner som stöds för Arc-aktiverade servrar [här](agent-overview.md#supported-operating-systems).

* Konfigurations information om Azure Virtual Machine Scale Sets eller klassiska virtuella datorer är inte tillämplig.

* Om dina datorer behöver hämta ett skript externt och bara kan kommunicera via en proxyserver, måste du [Konfigurera den anslutna dator agenten](manage-agent.md#update-or-remove-proxy-settings) för att ställa in miljövariabeln för proxyservern.

Konfigurationen för det anpassade skript tillägget anger saker som skript plats och kommandot som ska köras. Den här konfigurationen anges i en Azure Resource Manager-mall som anges nedan för både Linux-och Windows hybrid-datorer.

#### <a name="template-file-for-linux"></a>Mallfil för Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Mallfil för Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>Distribuera PowerShell DSC-tillägget

Om du vill använda PowerShell DSC-tillägget är följande exempel tillgängligt för körning på Windows och Linux. Om du inte känner till PowerShell DSC-tillägget, se [Översikt över DSC-tilläggs hanterare](../../virtual-machines/extensions/dsc-overview.md). Det finns ett par olika egenskaper som du bör känna till när du använder det här tillägget med hybrid datorer:

* Listan över operativ system som stöds med Azure VM PowerShell DSC-tillägget kan inte tillämpas på Azure Arc-aktiverade servrar. Du hittar en lista över vilka OSs-funktioner som stöds för Arc-aktiverade servrar [här](agent-overview.md#supported-operating-systems).

* Om dina datorer behöver hämta ett skript externt och bara kan kommunicera via en proxyserver, måste du [Konfigurera den anslutna dator agenten](manage-agent.md#update-or-remove-proxy-settings) för att ställa in miljövariabeln för proxyservern.

#### <a name="template-file-for-linux"></a>Mallfil för Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Mallfil för Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Beroendeagent

Om du vill använda tillägget Azure Monitor beroende agent, finns följande exempel på att köras på Windows och Linux. Om du inte är bekant med beroende agenten kan du läsa mer i [Översikt över Azure Monitor agenter](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>Mallfil för Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Mallfil för Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="troubleshooting"></a>Felsökning

Information om tillstånd för tilläggs distributioner kan hämtas från Azure Portal.

Följande fel söknings steg gäller för alla VM-tillägg.

1. Kontrol lera gäst agents loggen genom att titta på aktiviteten när ditt tillägg har allokerats i `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` för Windows, och för Linux under `/var/lib/GuestConfig/ext_mgr_logs` .

2. Kontrol lera tilläggs loggarna för det angivna tillägget för mer information i `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` för Windows. Utökning av utdata loggas till en fil för varje tillägg som installeras på Linux under `/var/log/GuestConfig/extension_logs` .

3. Sök efter felkoder, kända problem och fel söknings avsnitt för specifika dokumentation Ytterligare felsöknings information för varje tillägg finns i avsnittet **fel sökning och support** i Översikt över tillägget. Detta inkluderar beskrivningen av felkoder som skrivs till loggen. Tilläggs artiklarna är länkade i tilläggs [tabellen](#extensions) som tidigare fanns i den här artikeln.

4. Titta på system loggarna. Kontrol lera om det finns andra åtgärder som kan ha stör tillägget, till exempel en tids krävande installation av ett annat program som kräver exklusiv åtkomst till paket hanteraren.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-policy.md)och mycket mer.

- Läs mer om den [Log Analytics agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).