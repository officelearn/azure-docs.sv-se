---
title: Felsöka VM-tillägg för Azure Log Analytics
description: Beskriv symptom, orsaker och lösning för de vanligaste problemen med vm-tillägget Log Analytics för Windows och Virtuella Linux Azure-datorer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: e16531484505f055c1383aff5adb40518719d98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054586"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Felsöka Log Analytics-tillägget för virtuella datorer i Azure Monitor
Den här artikeln innehåller hjälp med felsökning av fel som kan uppstå med tillägget Log Analytics VM för Windows och Virtuella Linux-datorer som körs på Microsoft Azure och föreslår möjliga lösningar för att lösa dem.

Om du vill verifiera status för tillägget utför du följande steg från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Alla tjänster** i Azure-portalen. Skriv **virtuella datorer**i listan över resurser . När du börjar skriva filtreras listan baserat på det du skriver. Välj **Virtuella datorer**.
3. Leta reda på och välj den i listan över virtuella datorer.
3. Klicka på **Tillägg**på den virtuella datorn.
4. Kontrollera om log analytics-tillägget är aktiverat eller inte i listan.  För Linux är agenten listad som **OMSAgentforLinux** och för Windows listas agenten som **MicrosoftMonitoringAgent**.

   ![Vyn VM-tillägg](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klicka på tillägget för att visa detaljer. 

   ![Information om vm-tillägg](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Felsöka Azure Windows VM-tillägg

Om tillägget *Microsoft Monitoring Agent* VM inte installeras eller rapporterar kan du utföra följande steg för att felsöka problemet.

1. Kontrollera om Azure VM-agenten är installerad och fungerar korrekt med hjälp av stegen i [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Du kan också granska vm-agentloggfilen`C:\WindowsAzure\logs\WaAppAgent.log`
   * Om loggen inte finns installeras inte VM-agenten.
   * [Installera Azure VM-agenten](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Granska loggfilerna för tillägget För Microsoft Monitoring Agent i`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Se till att den virtuella datorn kan köra PowerShell-skript
4. Kontrollera att behörigheterna för C:\Windows\temp inte har ändrats
5. Visa status för Microsoft Monitoring Agent genom att skriva följande i ett upphöjt PowerShell-fönster på den virtuella datorn`(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Granska installationsloggfilerna för Microsoft Monitoring Agent i`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Mer information finns i [felsöka Windows-tillägg](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Felsöka tillägg till Virtuella linux-datorer
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Om *Log Analytics-agenten för Tillägget Linux* VM inte installeras eller rapporterar kan du utföra följande steg för att felsöka problemet.

1. Om tilläggsstatusen är *Okänd* kontrollera om Azure VM-agenten är installerad och fungerar korrekt genom att granska vm-agentloggfilen`/var/log/waagent.log`
   * Om loggen inte finns installeras inte VM-agenten.
   * [Installera Azure VM-agenten på virtuella Linux-datorer](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Om du har andra felaktiga statusar kan du läsa `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` Log Analytics-agenten för Linux VM-tilläggsloggar filer i och`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Om tilläggsstatusen är felfri, men data inte överförs, granska Log Analytics-agenten för Linux-loggfiler i`/var/opt/microsoft/omsagent/log/omsagent.log`

Mer information finns i [felsökning av Linux-tillägg](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Nästa steg

Mer felsökningsvägledning för Log Analytics-agenten för Linux som finns på datorer utanför Azure finns i [Felsöka Azure Log Analytics Linux Agent](agent-linux-troubleshoot.md).  
