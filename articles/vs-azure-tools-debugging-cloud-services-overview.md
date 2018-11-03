---
title: Alternativ för felsökning av Azure-molntjänster | Microsoft Docs
description: Distribuera Azure cloud services
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: e4ca0f710257530a05acdec2b240ae2870d4fa80
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969309"
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Lär dig om olika sätt att felsöka en Azure-molntjänst
Den här artikeln innehåller länkar till de olika sätt att felsöka en Azure-molntjänst. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Felsökning av en Azure cloud Services i Visual Studio
Du kan spara tid och pengar genom att använda Azure compute-emulatorn för att felsöka cloud Services på en lokal dator. Du kan felsöka en tjänst lokalt innan du distribuerar den för att förbättra tillförlitlighet och prestanda utan att betala för beräkningstiden. Vissa fel inträffa dock bara när du kör en molntjänst i Azure. Fel som inträffar bara när du kör en molntjänst i Azure kan felsökas genom att aktivera fjärrfelsökning när du publicerar din tjänst och sedan koppla felsökaren till en rollinstans. Mer information finns i [felsöka din molntjänst på din lokala dator](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-intellitrace"></a>Med hjälp av IntelliTrace 
Om du använder Visual Studio Enterprise för att skriva roller riktad .NET Framework 4.5, kan du aktivera IntelliTrace när du distribuerar en Azure-molntjänst från Visual Studio. IntelliTrace innehåller en logg som du kan använda med Visual Studio för att felsöka ditt program som om den kördes i Azure. Mer information finns i [felsöka en publicerad molntjänst med IntelliTrace och Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Fjärrfelsökning 
Du kan aktivera fjärrfelsökning med cloud services vid tidpunkten när du distribuerar Molntjänsten från Visual Studio. Om du väljer att aktivera fjärrfelsökning för en distribution, är fjärrfelsökning fjärrtjänster installerade på de virtuella datorer som kör varje rollinstansen. Dessa tjänster – till exempel `msvsmon.exe` – inte påverkar prestanda eller leder till extra kostnader. Mer information finns i [felsöka en molntjänst i Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>Nästa steg
- [Felsökning av en Azure-molntjänst eller virtuell dator i Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) -mer information om hur du felsöker Azure-molntjänster.