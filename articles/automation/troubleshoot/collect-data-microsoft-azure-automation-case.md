---
title: Data som ska samlas in när du öppnar ett ärende för Microsoft Azure Automation| Microsoft-dokument
description: I den här artikeln beskrivs en del av den information som du bör samla in innan du öppnar ett ärende för Azure Automation med Microsoft Azure Support.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679407"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Data som du behöver samla in när du öppnar ett ärende för Microsoft Azure Automation

I den här artikeln beskrivs en del av den information som du bör samla in innan du öppnar ett ärende för Azure Automation med Microsoft Azure Support. Denna information krävs inte för att öppna ärendet. Det kan dock hjälpa Microsoft att lösa problemet snabbare. Du kan också bli tillfrågad om dessa data av supportteknikern när du har öppnat ärendet.

## <a name="basic-data"></a>Grundläggande data

Samla in grundläggande data som beskrivs i knowledge [base-artikeln 4034605 – så här samlar du in diagnostik med Azure Automation-skript](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Data för problem med uppdateringshantering på Linux

1. Förutom de objekt som visas i KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)kör du följande logginsamlingsverktyg:

   [OMS Linux Agent Logg Collector](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Komprimera innehållet i mappen **/var/opt/microsoft/omsagent/run/automationworker/och** skicka sedan den komprimerade filen till Azure Support.
 
3. Kontrollera att ID:n för arbetsytan som Log Analytics-agenten för Linux rapporterar är samma som ID:n för arbetsytan som övervakas för uppdateringar.

## <a name="data-for-update-management-issues-on-windows"></a>Problem med data för uppdateringshantering i Windows

1. Samla in data för de objekt som anges i [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exportera följande händelseloggar till EVTX-formatet:

   * System
   * Program
   * Säkerhet
   * Operations Manager
   * Microsoft-SMA/Drift

3. Kontrollera att ID:n för arbetsytan som agenten rapporterar till är samma som ID:n för arbetsytan som övervakas av Windows-uppdateringar.

## <a name="data-for-job-issues"></a>Data för jobbproblem

1. Samla in data för de objekt som anges i [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Samla in jobb-ID:et för jobbet som har ett problem:

   1. Gå till **Automation-konton**i Azure-portalen .
   2. Välj det Automation-konto som du felsöker och anteckna namnet.
   3. Välj **Jobb**.
   4. Välj det jobb som du felsöker.
   5. Leta efter GUID-värdet i **Jobb-ID**i fönstret Projektsammanfattning .

   ![Jobb-ID i fönstret Projektsammanfattning](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Samla in ett exempel på skriptet som du kör.

4. Samla in loggfilerna:

   1. Gå till **Automation-konton**i Azure-portalen .
   2. Välj det Automation-konto som du felsöker.
   3. Välj **Jobb**.
   4. Välj det jobb som du felsöker.
   5. Välj **Alla loggar**.
   6. Samla in data i den resulterande rutan.

   ![Data som visas under Alla loggar](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Data för modulproblem

Förutom de [grundläggande dataobjekten](#basic-data)samlar du in följande information:

* De steg du har följt, så att problemet kan återskapas.
* Skärmbilder av eventuella felmeddelanden.
* Skärmdumpar av de aktuella modulerna och deras versionsnummer.

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
