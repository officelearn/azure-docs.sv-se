---
title: Data som ska samlas in när du öppnar ett ärende för Microsoft Azure Automation| Microsoft-dokument
description: I den här artikeln beskrivs en del av den grundläggande information som du bör samla in innan du öppnar ett ärende för Azure Automation med Microsoft Azure Support.
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
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849385"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Data som du behöver samla in när du öppnar ett ärende för Microsoft Azure Automation

I den här artikeln beskrivs en del av den grundläggande information som du bör samla in innan du öppnar ett ärende för Azure Automation med Microsoft Azure Support. Denna information krävs inte för att öppna ärendet. Det kan dock hjälpa Microsoft att lösa problemet snabbare. Du kan också bli tillfrågad om dessa data av supportteknikern när du har öppnat ärendet.

## <a name="collect-more-information"></a>Samla in mer information

Innan du öppnar ett ärende för Microsoft Azure Automation Support rekommenderar vi att du samlar in följande information.

### <a name="basic-data-collection"></a>Grundläggande datainsamling

Samla in de data som beskrivs i följande knowledge base-artikel:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Så här samlar du in Diagnostik med Azure Automation-skript

## <a name="collect-data-depending-on-issue"></a>Samla in data beroende på problem
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>För problem som påverkar uppdateringshantering på Linux

1. Förutom de objekt som visas i KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)kör du följande logginsamlingsverktyg:

   [OMS Linux Agent Logg Collector](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Komprimera innehållet i följande mapp och skicka sedan den komprimerade filen till Azure Support:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Kontrollera att arbetsyte-ID som OMS Linux Agent rapporterar till, är samma som arbetsytan som övervakas för uppdateringar.

### <a name="for-issues-that-affect-update-management-on-windows"></a>För problem som påverkar uppdateringshantering i Windows

Förutom de objekt som anges i [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)gör du följande:

1. Exportera följande händelseloggar till EVTX-formatet:

   * System
   * Program
   * Säkerhet
   * Operations Manager
   * Microsoft-SMA/Drift

2. Kontrollera att arbetsyte-ID som agenten rapporterar till är samma som arbetsytan som övervakas av Windows-uppdateringar.

### <a name="for-issues-that-affect-a-job"></a>För problem som påverkar ett jobb

Förutom de objekt som anges i [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)samlar du in följande information:

1. Samla in **JobID-numret** (för jobbet som har problem):

   1. Gå till bladet **Automation-konton** på Azure-portalen.
   2. Välj det **automationskonto** som du felsöker.
   3. Välj **Jobb**.
   4. Välj det jobb som du felsöker.
   5. Leta efter ett **jobb-ID** (UNDER **Jobbsammanfattning).**

   ![Jobb-ID i fönstret Projektsammanfattning](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Samla in kontonamnet:

   1. Gå till bladet **Automation-konton** på Azure-portalen.
   2. Få namnet på **det Automation-konto** som du felsöker.

3. Samla in exempel på skriptet som du kör.

4. Samla in loggfilerna:

   1. Gå till bladet **Automation-konton** på Azure-portalen.
   2. Välj det **automationskonto** som du felsöker.
   3. Välj **Jobb**.
   4. Välj det jobb som du felsöker.
   5. Välj **Alla loggar**.
   6. På det resulterande bladet samlar du in data.

   ![Data som visas under Alla loggar](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>För problem som påverkar moduler

Förutom objekten under "Grundläggande datainsamling" samlar du in följande information:

* De steg du följde så att problemet kan återskapas.
* En skärmbild av eventuella felmeddelanden.
* En skärmbild av de aktuella modulerna och deras versionsnummer.

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kontaktar du Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/).

Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
