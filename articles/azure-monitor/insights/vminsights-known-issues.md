---
title: Azure Monitor för virtuella datorer (förhandsversion) kända problem med | Microsoft Docs
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
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: 99f84e9784c448091c0257218855c3bf32c2f8f4
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715836"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Kända problem med Azure Monitor för virtuella datorer (förhandsversion)

Följande är kända problem med hälsotillstånd-funktionen i Azure Monitor för virtuella datorer:

- Health-funktionen är aktiverad för alla virtuella datorer är anslutna till Log Analytics-arbetsytan när den är initierad och klar från en enda virtuell dator.
- Om du efter inaktiveringen av övervakning av en virtuell dator med metoder som stöds när onboarding är försökas igen, bör det göras till samma arbetsyta.  Om en ny arbetsyta används när du visar hälsotillståndet för den virtuella datorn, kan det indikera avvikande beteende.
- Om en Azure-dator inte finns längre eftersom det har tagits bort eller ta bort visas den i VM-listvyn i tre till sju dagar. Dessutom när du klickar på tillståndet för en virtuell dator har tagits bort eller borttagna skulle starta den **hälsotillstånd diagnostik** vy för den, som sedan hamnar i en loop för inläsning. Väljer namnet på en borttagen virtuell dator startar ett blad med ett meddelande om den virtuella datorn har tagits bort.
- Tidsperiod och frekvensen för health-villkor kan inte ändras i den här versionen. 
- Health-villkor kan inte inaktiveras. 
- Efter integreringen, det kan ta tid innan data visas i Azure Monitor -> virtuella datorer -> hälsotillstånd eller från resursbladet -> insikter
- Diagnostik för hälsa får uppdateringar snabbare än en annan vy så att du avbrott information när du växlar mellan vyer  
- Aviseringar sammanfattning ingår i Azure Monitor för virtuell dator är endast för aviseringar som utlösts för hälsoproblem som har identifierats med övervakade virtuella datorer i Azure.
- Den **aviseringslistan** vyn i de enda virtuella datorn och Azure Monitor visar varnar vars övervakarens villkor har angetts till ”aktiverade” i de senaste 30 dagarna.  De kan inte konfigureras. Men när du klickar på sammanfattningen, en gång i **Aviseringslista** visningssida har startats kan du ändra filtret värdet för båda de Övervakare villkor och ett tidsintervall.
- På den **aviseringslistan** visningssida, föreslår vi att du inte ändrar den **resurstyp**, **Resource**, och **Monitor Service** filter som de har konfigurerats specifikt för lösningen (den här listan visar vissa extra fält som jämfört med Azure monitor -> aviseringar listvyn).    
- I virtuella Linux-datorer **hälsotillstånd diagnostik** innehåller hela domännamnet för den virtuella datorn i stället för en användardefinierad VM-namn.
- Stänga av virtuella datorer uppdateras några av dess health-villkor till ett kritiskt tillstånd och andra till felfritt tillstånd med net tillståndet för den virtuella datorn i ett kritiskt tillstånd.
- Allvarlighetsgrad för hälsotillstånd kan inte ändras, de kan endast aktiveras eller inaktiveras.  Dessutom kan vissa allvarlighetsgrader uppdatering baserad på tillståndet för health-villkor.
- Ändra inställningar för en instans för health kriterium leder till ändring av samma inställning i alla de hälsotillstånd kriterier instanserna av samma typ på den virtuella datorn. Till exempel om tröskelvärdet för disk Frigör utrymme hälsotillstånd kriterium instansen för logisk disk C: ändras och detta tröskelvärde ska gälla alla logiska diskar identifieras och övervakas för samma virtuella dator.   
- Tröskelvärden för följande kriterier för hälsotillstånd som riktar in sig på en virtuell Windows-dator inte kan ändras, eftersom deras hälsotillstånd redan inställda **kör** eller **tillgängliga**. När en förfrågan från den [arbetsbelastning övervakaren API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), health tillstånd visar den *jämförelseoperator* värdet för **LessThan** eller **GreaterThan**med en *tröskelvärdet* värdet för **4** för tjänsten eller entiteten om:
   - DNS-klienten Service Health-tjänsten körs inte 
   - DHCP-tjänsten klienthälsa – tjänsten körs inte 
   - Tjänstehälsa för RPC-tjänsten körs inte 
   - Tjänstehälsa för Windows-brandväggen – tjänsten körs inte
   - Tjänstehälsa för Windows-händelseloggen – tjänsten körs inte 
   - Tjänstehälsa för Server-tjänsten körs inte 
   - Windows fjärrhantering service health-tjänsten körs inte 
   - Filsystemfel eller skadade data – den logiska disken är inte tillgänglig

- Tröskelvärden för följande kriterier för Linux-hälsotillstånd inte kan ändras, eftersom deras hälsotillstånd redan inställda **SANT**.  Hälsotillstånd tillstånd visar den *jämförelseoperator* med ett värde **LessThan** och *tröskelvärdet* värdet för **1** när en förfrågan från arbetsbelastningen Övervaknings-API för entiteten beroende på dess kontext:
   - Diskstatus för logisk – logiska disken är inte online / tillgängliga
   - Diskstatus – disken inte är online / tillgängliga
   - Nätverkskortets Status - nätverkskort är inaktiverad  

- **Total processoranvändning** health-villkor i Windows visar ett tröskelvärde för **inte lika med 4** från portalen och efterfrågas från arbetsbelastningen övervaknings-API när CPU-belastningen är större än 95% och kölängden för systemet större än 15. Det här kriteriet för hälsotillstånd kan inte ändras i den här versionen.  
- Konfigurationsändringar, till exempel att uppdatera ett tröskelvärde tar upp till 30 minuter ska verkställas trots att portalen eller arbetsbelastning övervakaren API kan uppdatera omedelbart.  
- Enskilda processor- och logisk processor på health-villkor är inte tillgängliga i Windows, endast **Total processoranvändning** är tillgänglig för Windows-datorer.  
- Varningsregler som definierats för varje hälsotillstånd kriterium visas inte i Azure-portalen. De kan bara konfigureras från den [arbetsbelastning övervakaren API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) att aktivera eller inaktivera en health-aviseringsregel.  
- Tilldela en [Azure Monitor åtgärdsgrupp](../../monitoring-and-diagnostics/monitoring-action-groups.md) för health aviseringar är inte möjligt i Azure Portal. Du kan bara använda API: et för meddelande inställningen för att konfigurera en åtgärdsgrupp som utlöses när en hälsovarning aktiveras. För närvarande kan Åtgärdsgrupperna kan tilldelas mot en virtuell dator, så att alla *hälsovarningar* utlösta mot VM-utlösaren i samma åtgärd-grupperna. Det finns ingen i en separat åtgärdsgrupp för varje health-aviseringsregel, som traditionella Azure-aviseringar. Dessutom stöds endast åtgärdsgrupper som konfigurerats för att meddela genom att skicka ett e-postmeddelande eller ett SMS när hälsovarningar utlöses. 

## <a name="next-steps"></a>Nästa steg
Granska [publicera Azure Monitor för virtuella datorer](vminsights-onboard.md) att förstå kraven och metoder för att övervaka dina virtuella datorer.