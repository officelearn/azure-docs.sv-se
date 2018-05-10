---
title: Felsöka Azure Automation Hybrid Runbook Worker
description: Beskriv symptom, orsaker och lösningar för de vanligaste Hybrid Runbook Worker-problem i Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 37b61dfa8c8b760943f5a4561cc7f9f0db309a61
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Felsökningstips för Hybrid Runbook Worker

Du får hjälp med att felsöka fel som du kan uppleva med Automation Hybrid Runbook Worker och ger förslag på lösningar för att lösa dem.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>En runbook-jobbet avslutas med statusen Pausad

Din runbook har pausats strax efter att försök att köra den tre gånger. Det finns villkor som kan avbryta runbook slutförs och relaterade felmeddelandet innehåller inte någon ytterligare information som anger varför. Den här artikeln innehåller felsökningssteg för problem relaterade till Runbook Worker-Hybrid runbook körning av fel.

Om din Azure problemet inte åtgärdas i den här artikeln, besöker du Azure-forum på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera problemet på dessa forum eller [ @AzureSupport på Twitter](https://twitter.com/AzureSupport). Du kan också filen en Azure-supportbegäran genom att välja **få support** på den [Azure-supporten](https://azure.microsoft.com/support/options/) plats.

### <a name="symptom"></a>Symtom

Runbook-körningen misslyckas och felet som returnerades är ”Jobbåtgärden 'Aktivera' inte kan köras eftersom processen oväntat stoppades. Jobbåtgärden gjordes tre gånger ”.

Det finns flera möjliga orsaker till felet:

1. Worker-hybriden finns bakom en proxyserver eller brandvägg
2. Runbooks kan inte autentisera med lokala resurser

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Orsak 1: Runbook Worker-Hybrid är bakom en proxyserver eller brandvägg

Runbook Worker-hybriden körs på datorn finns bakom en brandvägg eller proxyserver server och utgående nätverksåtkomst inte beviljas eller konfigurerats korrekt.

#### <a name="solution"></a>Lösning

Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443.

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Orsak 2: Datorn har mindre än minimikraven för maskinvara

Datorer som kör Runbook Worker-hybriden ska uppfylla minimikraven för maskinvara innan du utser den som värd för den här funktionen. Annars blir överbelastad datorn beroende på resursutnyttjande andra bakgrundsprocesser och konkurrens på grund av runbooks under körning och orsaka fördröjningar för runbook-jobb eller timeout.

#### <a name="solution"></a>Lösning

Kontrollera först tilldelad att köra funktionen Hybrid Runbook Worker datorn uppfyller minimikraven på maskinvara. Om den finns, kan du övervaka användningen av CPU och minne för att fastställa alla korrelation mellan prestanda för Hybrid Runbook Worker-processer och Windows. Om det finns minne eller CPU-belastning, kan detta tyda på att behöva uppgradera eller lägga till fler processorer eller öka minne för att adressera resurs flaskhalsar och åtgärda felet. Du kan också markera en annan beräkningsresurser som stöder de minimikrav och skala när arbetsbelastning indikera att öka krävs.

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Orsak 3: Runbooks inte autentisera med lokala resurser

#### <a name="solution"></a>Lösning

Kontrollera den **Microsoft SMA** händelseloggen för motsvarande händelse med beskrivning *Win32 processen avslutades med koden [4294967295]*. Orsaken till felet är att du inte har konfigurerat autentisering i dina runbooks eller angivna kör som-autentiseringsuppgifter för Hybrid worker-gruppen. Granska [Runbook-behörigheter](automation-hrw-run-runbooks.md#runbook-permissions) att bekräfta att du har konfigurerat autentisering korrekt för dina runbooks.

## <a name="next-steps"></a>Nästa steg

Hjälp med felsökning av andra problem i Automation finns [felsökning av vanliga problem med Azure Automation](automation-troubleshooting-automation-errors.md)