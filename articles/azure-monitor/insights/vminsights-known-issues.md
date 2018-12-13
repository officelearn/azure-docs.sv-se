---
title: Azure Monitor för virtuella datorer (förhandsversion) kända problem | Microsoft Docs
description: Den här artikeln beskriver kända problem med Azure Monitor för virtuella datorer, en lösning i Azure som kombinerar hälso- och prestandaövervakning av Azure VM-operativsystem. Azure Monitor för virtuella datorer automatiskt identifierar programkomponenter och beroenden till andra resurser och mappar kommunikationen mellan dem.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: b2a60ddee58a7a9c37582085132ee8a1df767cb4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190364"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Kända problem med Azure Monitor för virtuella datorer (förhandsversion)

Den här artikeln beskriver kända problem med Azure Monitor för virtuella datorer, en lösning i Azure som kombinerar hälso- och prestandaövervakning av Azure VM-operativsystem. 

Följande är kända problem med hälsotillstånd-funktionen:

- Health-funktionen är aktiverad för alla virtuella datorer som är anslutna till Log Analytics-arbetsytan. Det här är det även när åtgärden börjar och slutar i en enda virtuell dator.
- När du inaktiverar övervakning för en virtuell dator med hjälp av metoderna som stöds och du försöker distribuera den igen, bör du distribuera den på samma arbetsyta. Avvikande beteende kan visas om du använder en ny arbetsyta och försök för att visa hälsotillståndet för den virtuella datorn.
- Om en Azure-dator har tagits bort eller tagits bort, visas den i VM-listvyn i tre till sju dagar. Även om du klickar på tillståndet för en virtuell dator har tagits bort eller borttagna öppnas den **hälsotillstånd diagnostik** visa och sedan startar en inläsning av slinga. Väljer namnet på den borttagna virtuella datorn öppnas ett fönster med ett meddelande om att den virtuella datorn har tagits bort.
- Tidsperiod och frekvensen för health-villkor kan inte ändras i den här versionen. 
- Health-villkor kan inte inaktiveras. 
- Efter distributionen kan det ta tid innan data visas i den **Azure Monitor** > **virtuella datorer** > **hälsotillstånd** fönstret eller  **Virtuella datorresursen** > **Insights** fönstret.
- Diagnostik för hälsa får uppdateringar snabbare än en annan vy. Informationen kan fördröjas när du växlar mellan vyer. 
- Sammanfattningen för aviseringar som har inkluderat med Azure Monitor för den virtuella datorn visar bara de aviseringar som att resultat från hälsoproblem som identifieras med övervakade virtuella datorer i Azure.
- Den **aviseringslistan** fönstret i den enda virtuella datorn och Azure Monitor visar aviseringar vars övervakarens villkor har angetts till *utlösta* under de senaste 30 dagarna. Aviseringar är inte kan konfigureras. Dock efter den **Aviseringslista** öppnas fönstret kan du klicka på Sammanfattning för att ändra filtret värdet för båda de Övervakare villkor och ett tidsintervall.
- I den **aviseringslistan** fönstret rekommenderar vi inte ändra den **resurstyp**, **Resource**, och **Monitor Service** filter. De har konfigurerats specifikt för lösningen. I den här listan visas fler fält än den **Azure monitor** > **aviseringar** listvyn gör.   
- I virtuella Linux-datorer i **hälsotillstånd diagnostik** vyn visar hela domännamnet för den virtuella datorn i stället för en användardefinierad VM-namn.
- Stänga av virtuella datorer uppdateras vissa av Hälsokriterier som *kritiska* och andra i *felfri*. Net VM-status visas som *kritiska*.
- Allvarlighetsgrad för hälsotillstånd kan inte ändras. Det kan bara aktiveras eller inaktiveras. Dessutom kan uppdateras vissa allvarlighetsgrader baserat på status för health-villkor.
- Om du ändrar inställningar för en instans för health kriterium ändras alla hälsotillstånd kriterier instanser av samma typ på den virtuella datorn. Till exempel om tröskelvärdet på disk ledigt utrymme hälsotillstånd kriterium instansen för logisk disk C: ändras, gäller detta tröskelvärde alla logiska diskar som identifieras och övervakas för samma virtuella dator.  
- Tröskelvärden för health-villkor som är riktade till en virtuell Windows-dator inte kan ändras, eftersom deras hälsotillstånd är inställda på *kör* eller *tillgängliga*. När du frågar hälsotillståndet från den [arbetsbelastning övervakaren API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), visas den *jämförelseoperator* värdet för **LessThan** eller **GreaterThan** med en *tröskelvärdet* värdet för **4** för tjänsten eller entiteten om:
   - Tjänstehälsa för DNS-klienten – tjänst inte körs. 
   - Tjänstehälsa för DHCP-klient – tjänst inte körs. 
   - Tjänstehälsa för RPC-tjänsten inte körs. 
   - Tjänstehälsa för Windows-brandväggen – tjänst inte körs.
   - Tjänstehälsa för Windows-händelseloggen – tjänst inte körs. 
   - Tjänstehälsa för Server – tjänst inte körs. 
   - Windows remote management service health – tjänsten körs inte. 
   - Filsystemfel eller skadade data – logisk Disk är inte tillgänglig.

- Tröskelvärden för följande kriterier för Linux-hälsotillstånd inte kan ändras, eftersom deras hälsotillstånd har redan angivits för *SANT*. Hälsotillståndet visar den *jämförelseoperator* med ett värde **LessThan** och *tröskelvärdet* värdet för **1** när en förfrågan från den Arbetsbelastningen övervaknings-API för entiteten, beroende på dess kontext:
   - Diskstatus för logisk – logisk disk är inte online / tillgängliga
   - Diskstatus – Disk är inte online / tillgängliga
   - Nätverkskortets Status - nätverkskort är inaktiverad  

- Den *total processoranvändning* health-villkor i Windows visar ett tröskelvärde för **inte lika med 4** i både på portalen och arbetsbelastningen övervaknings-API. Tröskelvärdet har uppnåtts när *total processoranvändning* är större än 95 och systemet Kölängden är större än 15. Det här kriteriet för hälsotillstånd kan inte ändras i den här versionen. 
- Konfigurationsändringar, till exempel att uppdatera ett tröskelvärde ta upp till 30 minuter, även om portalen eller arbetsbelastning övervakaren API kan uppdatera dem omedelbart. 
- Är inte tillgängliga i Windows enskilda processor- och logisk processor på health-villkor. Endast Total processoranvändning är tillgänglig för Windows-datorer. 
- Varningsregler som har definierats för varje hälsotillstånd kriterium visas inte i Azure-portalen. Du kan aktivera eller inaktivera en hälsovarning regel bara i den [arbetsbelastning övervakaren API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager). 
- Du kan inte tilldela en [Azure Monitor åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) för health-aviseringar i Azure-portalen. Du kan använda endast meddelande inställningen API: et för att konfigurera en åtgärdsgrupp som utlöses när en hälsovarning aktiveras. För närvarande kan du tilldela åtgärdsgrupper mot en virtuell dator så att alla *hälsovarningar* utlösta mot VM-utlösaren samma åtgärdsgrupper. Till skillnad från traditionella Azure-aviseringar finns det ingen i en separat åtgärdsgrupp för varje health-aviseringsregel. Dessutom stöds endast åtgärdsgrupper som är konfigurerade för att tillhandahålla e-post eller SMS-meddelanden när hälsovarningar utlöses. 

## <a name="next-steps"></a>Nästa steg
För att förstå de krav och metoder för att aktivera övervakning av dina virtuella datorer, granska [distribuera Azure Monitor för virtuella datorer](vminsights-onboard.md).
