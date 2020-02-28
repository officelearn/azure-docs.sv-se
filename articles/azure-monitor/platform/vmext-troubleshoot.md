---
title: Felsöka Azure Log Analytics VM-tillägget i Azure Monitor | Microsoft Docs
description: Beskriv problem, orsaker och upplösning för de vanligaste problemen med Log Analytics VM-tillägg för Windows och Linux virtuella Azure-datorer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 88d76fc0c215653cf732ba7b827d82187d738fd9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658480"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Felsöka Log Analytics VM-tillägget i Azure Monitor
Du får hjälp med att felsöka fel som du kan uppleva med Log Analytics VM-tillägg för Windows och Linux-datorer som körs på Microsoft Azure och ger förslag på lösningar för att lösa dem.

Utför följande steg från Azure-portalen för att kontrollera status för tillägget.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **virtuella datorer**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **virtuella datorer**.
3. Hitta och markera den i din lista över virtuella datorer.
3. Klicka på **tillägg**på den virtuella datorn.
4. Kontrollera i listan om Log Analytics-tillägget har aktiverats eller inte.  För Linux visas-agenten som **OMSAgentforLinux** och för Windows visas agenten som **MicrosoftMonitoringAgent**.

   ![VM-tillägg-vyn](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klicka på tillägget för att visa detaljer. 

   ![Information om VM-tillägg](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Felsöka Azure Windows VM-tillägg

Om VM-tillägget för *Microsoft Monitoring Agent* inte installeras eller rapporteras, kan du utföra följande steg för att felsöka problemet.

1. Kontrol lera om Azure VM-agenten är installerad och fungerar korrekt med hjälp av stegen i [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Du kan också granska logg filen för VM-agenten `C:\WindowsAzure\logs\WaAppAgent.log`
   * VM-agenten installeras inte om loggen inte finns.
   * [Installera Azure VM-agenten](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Granska loggfilerna för VM-tillägget för Microsoft Monitoring Agent i `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Se till att den virtuella datorn kan köra PowerShell-skript
4. Kontrollera att behörigheterna för C:\Windows\temp inte har ändrats
5. Visa status för Microsoft Monitoring Agent genom att skriva följande i ett upphöjd PowerShell-fönster på den virtuella datorn `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Granska loggfilerna för installation av Microsoft Monitoring Agent i `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Mer information finns i [Felsöka Windows-tillägg](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Felsökning av Linux VM-tillägg
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Om *Log Analytics agent för* VM-tillägget för Linux inte installeras eller rapporteras, kan du utföra följande steg för att felsöka problemet.

1. Om tilläggets status är *okänd* kontrollerar du om Azure VM-agenten är installerad och fungerar korrekt genom att granska logg filen för VM-agenten `/var/log/waagent.log`
   * VM-agenten installeras inte om loggen inte finns.
   * [Installera Azure VM-agenten på virtuella Linux-datorer](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. För annan status som inte är felfria kontrollerar du Log Analytics agent för virtuella Linux-loggfiler i `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` och `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Om tilläggets status är felfri, men data inte överförs, granska Log Analytics agent för Linux-loggfiler i `/var/opt/microsoft/omsagent/log/omsagent.log`

Mer information finns i [Felsöka Linux-tillägg](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Nästa steg

Ytterligare fel söknings anvisningar som rör Log Analytics-agenten för Linux som finns på datorer utanför Azure finns i [Felsöka Azure Log Analytics Linux-agenten](agent-linux-troubleshoot.md).  
