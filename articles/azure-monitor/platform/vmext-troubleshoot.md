---
title: Felsöka Azure Log Analytics VM-tillägget i Azure Monitor | Microsoft Docs
description: Beskriv problem, orsaker och upplösning för de vanligaste problemen med Log Analytics VM-tillägg för Windows och Linux virtuella Azure-datorer.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2019
ms.author: magoedte
ms.openlocfilehash: dd5e0749116ef335887ea634b9d2790c63bf171d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751923"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Felsökning av Log Analytics VM-tillägget i Azure Monitor
Du får hjälp med att felsöka fel som du kan uppleva med Log Analytics VM-tillägg för Windows och Linux-datorer som körs på Microsoft Azure och ger förslag på lösningar för att lösa dem.

Utför följande steg från Azure-portalen för att kontrollera status för tillägget.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver **virtuella datorer**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **virtuella datorer**.
3. Hitta och markera den i din lista över virtuella datorer.
3. Klicka på den virtuella datorn, **tillägg**.
4. Kontrollera i listan om Log Analytics-tillägget har aktiverats eller inte.  För Linux-agenten har listats som **OMSAgentforLinux** och för Windows, agenten anges med **MicrosoftMonitoringAgent**.

   ![VM-tillägg-vyn](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klicka på tillägget för att visa detaljer. 

   ![Information om VM-tillägg](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Felsöka Azure Windows VM-tillägg

Om den *Microsoft Monitoring Agent* VM-tillägget inte installera eller rapporter, kan du utföra följande steg för att felsöka problemet.

1. Kontrollera om Azure VM-agenten har installerats och fungerar korrekt med hjälp av stegen i [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Du kan också granska loggfilen för VM-agenten `C:\WindowsAzure\logs\WaAppAgent.log`
   * VM-agenten installeras inte om loggen inte finns.
   * [Installera Azure VM-agenten](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Granska loggfilerna i Microsoft Monitoring Agent-VM-tillägg `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Se till att den virtuella datorn kan köra PowerShell-skript
4. Kontrollera att behörigheterna för C:\Windows\temp inte har ändrats
5. Visa status för Microsoft Monitoring Agent genom att skriva följande i en upphöjd PowerShell-kommandotolk på den virtuella datorn `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Granska loggfilerna Microsoft Monitoring Agent i `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Mer information finns i [felsökning av Windows-tillägg](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Felsökning av Linux VM-tillägg
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Om den *Log Analytics-agenten för Linux* VM-tillägget inte installera eller rapporter, kan du utföra följande steg för att felsöka problemet.

1. Om du har tilläggsstatusen *okänd* kontrollera om Azure VM-agenten har installerats och fungerar korrekt genom att granska loggfilen för VM-agenten `/var/log/waagent.log`
   * VM-agenten installeras inte om loggen inte finns.
   * [Installera Azure VM-agenten på virtuella Linux-datorer](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. För andra felaktiga statusar granska Log Analytics-agenten för Linux VM-tillägget loggfiler `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` och `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Om tilläggsstatusen är felfri, men data överförs inte granska Log Analytics-agenten för Linux-loggfiler i `/var/opt/microsoft/omsagent/log/omsagent.log`

Mer information finns i [felsökning av Linux-tillägg](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Nästa steg

Ytterligare felsökningsinformation som rör Log Analytics-agenten för Linux som ligger på datorer utanför Azure, se [felsöka Azure Log Analytics Linux Agent](agent-linux-troubleshoot.md).  
