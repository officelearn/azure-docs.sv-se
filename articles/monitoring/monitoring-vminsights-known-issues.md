---
title: Azure Monitor för kända problem för virtuella datorer | Microsoft Docs
description: Azure Monitor för virtuella datorer är en lösning i Azure som kombinerar hälsotillstånd och prestanda för övervakning av Azure VM-operativsystem, samt automatiskt identifierar programkomponenter och beroenden till andra resurser och mappar kommunikationen mellan dem. Den här artikeln beskriver kända problem.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 819c3e74355cf80c7a998abb8b02b10c9e077059
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062776"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>Kända problem med Azure Monitor för virtuella datorer

Följande är kända problem med hälsotillstånd-funktionen i Azure Monitor för virtuella datorer:

- Tidsperiod och frekvensen för health-villkor kan inte ändras i den här versionen. 
- Health-villkor kan inte inaktiveras. 
- Efter integreringen, det kan ta tid innan data visas i Azure Monitor -> virtuella datorer -> hälsotillstånd eller från resursbladet -> insikter
- Diagnostik för hälsa får uppdateringar snabbare än en annan vy så att du avbrott information när du växlar mellan vyer  
- Sammanfattning av aviseringar som angavs i Azure Monitor för VM-hälsa är endast för aviseringar som utlösts för hälsoproblem som har identifierats med övervakade virtuella datorer i Azure.
- Den **aviseringslistan** vyn i de enda virtuella datorn och Azure Monitor visar varnar vars övervakarens villkor har angetts till ”aktiverade” i de senaste 30 dagarna.  De kan inte konfigureras. Men när du klickar på sammanfattningen, en gång i **Aviseringslista** visningssida har startats kan du ändra filtret värdet för båda de Övervakare villkor och ett tidsintervall.
- På den **aviseringslistan** visningssida, föreslår vi att du inte ändrar den **resurstyp**, **Resource**, och **Monitor Service** filter som de har konfigurerats specifikt för lösningen (den här listan visar vissa extra fält som jämfört med Azure monitor -> aviseringar listvyn).    
- I virtuella Linux-datorer **hälsotillstånd diagnostik** innehåller hela domännamnet för den virtuella datorn i stället för en användardefinierad VM-namn.
- Stänga av virtuella datorer uppdateras några av dess health-villkor till ett kritiskt tillstånd och andra till felfritt tillstånd med net tillståndet för den virtuella datorn i ett kritiskt tillstånd.
- Allvarlighetsgrad för hälsotillstånd kan inte ändras, de kan endast aktiveras eller inaktiveras.  Dessutom kan vissa allvarlighetsgrader uppdatering baserad på tillståndet för health-villkor.
- Ändra inställningar för en instans för health kriterium leder till ändring av samma inställning i alla de hälsotillstånd kriterier instanserna av samma typ på den virtuella datorn. Till exempel om tröskelvärdet för disk Frigör utrymme hälsotillstånd kriterium instansen för logisk disk C: ändras och detta tröskelvärde ska gälla alla logiska diskar identifieras och övervakas för samma virtuella dator.   
- Tröskelvärden för vissa Windows health-villkor som Tjänstehälsa för DNS-klienten inte kan ändras, eftersom deras felfritt tillstånd är redan låst till den **kör**, **tillgängliga** läget för tjänsten eller entitet beroende på kontexten.  I stället värdet representeras av siffran 4, den kommer att konverteras till den faktiska visningssträngen i en framtida version.  
- Tröskelvärden för vissa Linux-Hälsokriterier är inte kan ändras, till exempel hälsan för logisk Disk som de redan har angetts ska utlösas på feltillstånd.  De visar huruvida något har online/offline eller aktiverats eller inaktiverats, och representeras och samma genom att visa värdet 1 eller 0.
- Uppdaterar filtret Resursgrupp i valfri resursgrupp när du använder den i stor skala med Azure monitor -> virtuella datorer -> hälsotillstånd listvyer med förvalda prenumeration och resursgrupp ->, kommer vyn listan att visa **inget resultat**.  Gå tillbaka till Azure Monitor -> virtuella datorer -> fliken hälsa och välj den önskade prenumerationen och resursgruppen och gå sedan till listvyn.

## <a name="next-steps"></a>Nästa steg
Granska [publicera Azure Monitor för virtuella datorer](monitoring-vminsights-onboard.md) att förstå kraven och metoder för att övervaka dina virtuella datorer.