---
title: Hantering av VM-tillägg med Azure Arc-aktiverade servrar
description: Azure Arc-aktiverade servrar kan hantera distribution av virtuella dator tillägg som tillhandahåller konfiguration och automatiserings uppgifter efter distributionen med icke-virtuella datorer i Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460894"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Hantering av virtuella dator tillägg med Azure Arc-aktiverade servrar

Tillägg för virtuella datorer (VM) är små program som ger konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer. Om en virtuell dator till exempel behöver programvaruinstallation, antivirusskydd eller körning av ett skript på den kan ett VM-tillägg användas.

Med Azure Arc-aktiverade servrar kan du distribuera virtuella Azure-tillägg till virtuella Windows-och Linux-datorer som inte är Azure-datorer, vilket fören klar hanteringen av hybrid datorn lokalt, i Edge och i andra moln miljöer via deras livs cykel. VM-tillägg kan hanteras med hjälp av följande metoder på dina hybrid datorer eller servrar som hanteras av Arc-aktiverade servrar:

- [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager-mallar](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Viktiga fördelar

Stöd för VM-tillägg för Azure Arc-aktiverade servrar ger följande viktiga fördelar:

- Använd [Azure Automation tillstånds konfiguration](../../automation/automation-dsc-overview.md) för att centralt lagra konfigurationer och upprätthålla det önskade läget för Hybrid anslutna datorer som är aktiverade via DSC VM-tillägget.

- Samla in loggdata för analys med [loggar i Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) aktiverat via det virtuella Log Analytics agent-tillägget. Detta är användbart för att utföra komplexa analyser mellan data från olika källor.

- Med [Azure Monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)analyseras prestandan för dina virtuella Windows-och Linux-datorer och övervaka deras processer och beroenden på andra resurser och externa processer. Detta uppnås genom att aktivera både VM-tillägg för Log Analytics agent och beroende agent.

- Ladda ned och kör skript på Hybrid anslutna datorer med tillägget för anpassat skript. Det här tillägget är användbart för konfiguration av distribution, program varu installation eller andra konfigurations-eller hanterings uppgifter.

## <a name="availability"></a>Tillgänglighet

Funktioner för virtuella dator tillägg är bara tillgängliga i listan över [regioner som stöds](overview.md#supported-regions). Se till att du har registrerat datorn i någon av dessa regioner.

## <a name="extensions"></a>Tillägg

I den här versionen har vi stöd för följande VM-tillägg på Windows-och Linux-datorer.

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

Mer information om paketet för Azure Connected Machine agent och information om tilläggs Agent komponenten finns i [agent översikt](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Förutsättningar

Den här funktionen är beroende av följande Azure-resurs leverantörer i din prenumeration:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Om de inte redan är registrerade följer du stegen under [Registrera Azure Resource providers](agent-overview.md#register-azure-resource-providers).

Det virtuella dator tillägget för Log Analytics agent för Linux kräver python 2. x installerat på mål datorn.

### <a name="connected-machine-agent"></a>Ansluten dator agent

Kontrol lera att din dator matchar de versioner av Windows och Linux-operativsystem som [stöds](agent-overview.md#supported-operating-systems) för den Azure-anslutna dator agenten.

Den lägsta versionen av den anslutna dator agenten som stöds av den här funktionen i Windows och Linux är 1,0-versionen.

Om du vill uppgradera datorn till den version av agenten som krävs, se [uppgraderings agenten](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Nästa steg

Du kan distribuera, hantera och ta bort VM-tillägg med hjälp av [Azure CLI](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md), från [Azure Portal](manage-vm-extensions-portal.md)eller [Azure Resource Manager mallar](manage-vm-extensions-template.md).
