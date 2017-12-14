---
title: "Felsöka Azure Automation Hybrid Runbook Worker | Microsoft Docs"
description: "Beskriv symptom, orsaker och lösningar för de vanligaste Hybrid Runbook Worker-problem i Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.openlocfilehash: 75f4ac1bc940a2b1d8e4ac6aeac8b80c642489da
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Felsökningstips för Hybrid Runbook Worker

Du får hjälp med att felsöka fel som du kan uppleva med Automation Hybrid Runbook Worker och ger förslag på lösningar för att lösa dem.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>En runbook-jobbet avslutas med statusen Pausad

Din runbook har pausats strax efter att försök att köra den tre gånger. Det finns villkor som kan avbryta runbook slutförs och relaterade felmeddelandet innehåller inte någon ytterligare information som anger varför. Den här artikeln innehåller felsökningssteg för problem relaterade till Runbook Worker-Hybrid runbook körning av fel.

Om din Azure problemet inte åtgärdas i den här artikeln, besöker du Azure-forum på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera problemet på dessa forum eller [ @AzureSupport på Twitter](https://twitter.com/AzureSupport). Du kan också filen en Azure-supportbegäran genom att välja **få support** på den [Azure-supporten](https://azure.microsoft.com/support/options/) plats.

### <a name="symptom"></a>Symtom
Runbook-körningen misslyckas och felet som returnerades är ”Jobbåtgärden 'Aktivera' inte kan köras eftersom processen oväntat stoppades. Jobbåtgärden gjordes 3 gånger ”.

Det finns flera möjliga orsaker till felet: 

1. Worker-hybriden finns bakom en proxyserver eller brandvägg
2. Worker-hybriden körs på datorn har mindre än minst [maskinvarukrav](automation-offering-get-started.md#hybrid-runbook-worker)  
3. Runbooks kan inte autentisera med lokala resurser

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Orsak 1: Runbook Worker-Hybrid är bakom en proxyserver eller brandvägg
Runbook Worker-hybriden körs på datorn finns bakom en brandvägg eller proxyserver server och utgående nätverksåtkomst kan inte beviljas eller konfigurerats korrekt.

#### <a name="solution"></a>Lösning
Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443. 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Orsak 2: Datorn har mindre än minimikraven för maskinvara
Datorer som kör Runbook Worker-hybriden ska uppfylla minimikraven för maskinvara innan du utser den som värd för den här funktionen. Annars kommer att bli överbelastad datorn beroende på resursutnyttjande andra bakgrundsprocesser och konkurrens på grund av runbooks under körning och orsaka fördröjningar för runbook-jobb eller timeout. 

#### <a name="solution"></a>Lösning
Kontrollera först tilldelad att köra funktionen Hybrid Runbook Worker datorn uppfyller minimikraven på maskinvara.  Om den finns, kan du övervaka användningen av CPU och minne för att fastställa alla korrelation mellan prestanda för Hybrid Runbook Worker-processer och Windows.  Om det finns minne eller CPU-belastning, kan detta tyda på att behöva uppgradera eller lägga till fler processorer eller öka minne för att adressera resurs flaskhalsar och åtgärda felet. Du kan också markera en annan beräkningsresurser som stöder de minimikrav och skala när arbetsbelastning indikera att öka krävs.         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Orsak 3: Runbooks inte autentisera med lokala resurser

#### <a name="solution"></a>Lösning
Kontrollera den **Microsoft SMA** händelseloggen för motsvarande händelse med beskrivning *Win32 processen avslutades med koden [4294967295]*.  Orsaken till felet är att du inte har konfigurerat autentisering i dina runbooks eller angivna kör som-autentiseringsuppgifter för Hybrid worker-gruppen.  Granska [Runbook-behörigheter](automation-hrw-run-runbooks.md#runbook-permissions) att bekräfta att du har konfigurerat autentisering korrekt för dina runbooks.  

## <a name="next-steps"></a>Nästa steg

Hjälp med felsökning av andra problem i Automation finns [felsökning av vanliga problem med Azure Automation](automation-troubleshooting-automation-errors.md) 