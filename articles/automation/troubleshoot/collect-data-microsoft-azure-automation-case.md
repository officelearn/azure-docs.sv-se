---
title: Data som ska samlas in när du öppnar ett ärende för Microsoft Azure Automation | Microsoft Docs
description: I den här artikeln beskrivs en del av den grundläggande information som du bör samla in innan du öppnar ett ärende för Azure Automation med Microsoft Azure stöd.
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849385"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Data som du behöver samla in när du öppnar ett ärende för Microsoft Azure Automation

I den här artikeln beskrivs en del av den grundläggande information som du bör samla in innan du öppnar ett ärende för Azure Automation med Microsoft Azure stöd. Den här informationen krävs inte för att öppna ärendet. Det kan dock hjälpa Microsoft att lösa problemet snabbare. Du kan också uppmanas att utföra dessa data av support teknikern när du har öppnat ärendet.

## <a name="collect-more-information"></a>Samla in mer information

Innan du öppnar ett ärende för Microsoft Azure Automation-stöd rekommenderar vi att du samlar in följande information.

### <a name="basic-data-collection"></a>Grundläggande data insamling

Samla in de data som beskrivs i följande kunskaps bas artikel:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) så här fångar du Azure Automation-skriptbaserad diagnostik

## <a name="collect-data-depending-on-issue"></a>Samla in data beroende på problem
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>För problem som påverkar Uppdateringshantering på Linux

1. Utöver de objekt som listas i KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)kör du följande verktyg för logg insamling:

   [Logg insamlare för OMS Linux-Agent](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Komprimera innehållet i följande mapp och skicka sedan den komprimerade filen till Azure-supporten:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Kontrol lera att arbetsyte-ID: t OMS Linux-agenten rapporterar till, är detsamma som arbets ytan som övervakas för uppdateringar.

### <a name="for-issues-that-affect-update-management-on-windows"></a>För problem som påverkar Uppdateringshantering i Windows

Förutom de objekt som listas i [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)gör du följande:

1. Exportera följande händelse loggar till EVTX-formatet:

   * System
   * Program
   * Säkerhet
   * Operations Manager
   * Microsoft-SMA/Operational

2. Kontrol lera att det arbetsyte-ID som agenten rapporterar till är detsamma som arbets ytan som övervakas av Windows-uppdateringar.

### <a name="for-issues-that-affect-a-job"></a>För problem som påverkar ett jobb

Utöver de objekt som listas i [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)samlar du in följande information:

1. Samla in **JobID** -numret (för jobbet som har problem):

   1. Gå till bladet **Automation-konton** på Azure Portal.
   2. Välj det **Automation-konto** som du felsöker.
   3. Välj **jobb**.
   4. Välj det jobb som du felsöker.
   5. Under **jobb Sammanfattning**söker du efter ett **jobb-ID** (GUID).

   ![Jobb-ID i fönstret jobb Sammanfattning](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Samla in konto namnet:

   1. Gå till bladet **Automation-konton** på Azure Portal.
   2. Hämta namnet på det **Automation-konto** du felsöker.

3. Samla in exempel på det skript som du kör.

4. Samla in loggfilerna:

   1. Gå till bladet **Automation-konton** på Azure Portal.
   2. Välj det **Automation-konto** som du felsöker.
   3. Välj **jobb**.
   4. Välj det jobb som du felsöker.
   5. Välj **alla loggar**.
   6. Samla in data på det resulterande bladet.

   ![Data som listas under alla loggar](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>För problem som påverkar moduler

Förutom objekten under "grundläggande data insamling" samlar du in följande information:

* De steg du följde så att problemet kan återskapas.
* En skärm bild av eventuella fel meddelanden.
* En skärm bild av aktuella moduler och deras versions nummer.

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/).

Du kan också fil en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
