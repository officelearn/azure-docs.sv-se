---
title: Felsöka Azure Log Analytics VM-tillägget | Microsoft Docs
description: Beskriv symptom, orsaker och upplösning för de flesta vanliga problem med Log Analytics VM-tillägg för Windows och Linux virtuella Azure-datorer.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 700d6b2c3bcd39aed38bf75556bcdcb59d1ab78b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128834"
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Felsöka Log Analytics VM-tillägget
Du får hjälp med att felsöka fel som du kan uppleva med Log Analytics VM-tillägg för Windows och Linux virtuella datorer som körs på Microsoft Azure och ger förslag på lösningar för att lösa dem.

Utför följande steg för att kontrollera status för filnamnstillägget från Azure-portalen.

1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser, skriver **virtuella datorer**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **virtuella datorer**.
3. Hitta och markera den i din lista över virtuella datorer.
3. Klicka på den virtuella datorn, **tillägg**.
4. Kontrollera i listan om logganalys-tillägget har aktiverats eller inte.  För Linux-agenten har listats som **OMSAgentforLinux** och för Windows, visas agenten som **MicrosoftMonitoringAgent**.

   ![VM-tillägg View](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klicka på tillägget för att visa detaljer. 

   ![Information för VM-tillägg](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Felsöka Azure Windows VM-tillägget

Om den *Microsoft Monitoring Agent* VM-tillägget inte installation eller rapporter, kan du utföra följande steg för att felsöka problemet.

1. Kontrollera om den Virtuella Azure-agenten är installerad och fungerar korrekt med hjälp av stegen i [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Du kan också granska loggfilen för VM-agent `C:\WindowsAzure\logs\WaAppAgent.log`
   * VM-agenten installeras inte om loggen inte finns.
   * [Installera Azure VM-agenten](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Bekräfta Microsoft Monitoring Agent tillägget pulsslag aktiviteten körs med följande steg:
   * Logga in på den virtuella datorn
   * Öppna Schemaläggaren och Sök efter den `update_azureoperationalinsight_agent_heartbeat` aktivitet
   * Bekräfta aktiviteten är aktiverad och körs varje en minut
   * Kontrollera loggfilen pulsslag i `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Granska loggfilerna i Microsoft Monitoring Agent VM-tillägget `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Se till att den virtuella datorn kan köra PowerShell-skript
5. Kontrollera behörigheter på C:\Windows\temp inte har ändrats
6. Visa status för Microsoft Monitoring Agent genom att skriva följande i ett upphöjt PowerShell-fönster på den virtuella datorn `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Granska installationsloggfilerna för Microsoft Monitoring Agent i `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Mer information finns i [felsökning av Windows-tillägg](../virtual-machines/windows/extensions-oms.md).

## <a name="troubleshooting-linux-vm-extension"></a>Felsökning av Linux VM-tillägget
Om den *OMS-Agent för Linux* VM-tillägget inte installation eller rapporter, kan du utföra följande steg för att felsöka problemet.

1. Om tillståndets status är *okänd* kontrollera om den Virtuella Azure-agenten är installerat och fungerar korrekt genom att granska loggfilen för VM-agent `/var/log/waagent.log`
   * VM-agenten installeras inte om loggen inte finns.
   * [Installera Azure VM-agenten på virtuella Linux-datorer](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Granska OMS-Agent för andra ohälsosamt statustyper för Linux VM-tillägget loggfiler `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` och `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Om tillståndets status är felfri, men data överförs inte granska OMS-Agent för Linux-loggfiler i `/var/opt/microsoft/omsagent/log/omsagent.log`

Mer information finns i [felsökning Linux tillägg](../virtual-machines/linux/extensions-oms.md).

## <a name="next-steps"></a>Nästa steg

Ytterligare felsökningsinformation som rör OMS-Agent för Linux finns på datorer utanför Azure finns [felsöka Azure Log Analytics Linux-agenten](log-analytics-agent-linux-support.md).  
