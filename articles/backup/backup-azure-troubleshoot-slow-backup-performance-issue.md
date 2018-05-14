---
title: Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup | Microsoft Docs
description: Innehåller felsökningsinformation som hjälper dig att diagnostisera orsaken till Azure Backup prestandaproblem
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6ed1e2d7bfc08afe135cb85995fdebaa30202c23
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup
Den här artikeln innehåller felsökningsinformation som hjälper dig att diagnostisera orsaken till långsam prestanda vid säkerhetskopiering av filer och mappar när du använder Azure Backup. När du använder Azure Backup-agenten för att säkerhetskopiera filer, kan det ta längre tid än förväntat säkerhetskopieringsprocessen. Den här fördröjningen kan bero på en eller flera av följande:

* [Det finns prestandaflaskhalsar på den dator som säkerhetskopieras.](#cause1)
* [En annan process eller antivirusprogram kan störa Azure Backup-processen.](#cause2)
* [Backup-agenten körs på en Azure-dator (VM).](#cause3)  
* [Du säkerhetskopierar ett stort antal (miljoner) filer.](#cause4)

Innan du startar felsökning av problem, rekommenderar vi att du hämtar och installerar den [senaste Azure Backup-agenten](http://aka.ms/azurebackup_agent). Vi göra uppdateras ofta säkerhetskopieringsagenten åtgärda problemen, lägga till funktioner och förbättra prestanda.

Vi också rekommenderar att du läser igenom den [Azure Backup service vanliga frågor och svar](backup-azure-backup-faq.md) att se till att du inte har några vanliga problem i konfigurationen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Orsak: Prestandaflaskhalsar på datorn
Flaskhalsar på den dator som säkerhetskopieras kan orsaka fördröjningar. Datorns möjligheten att läsa eller skriva till disk, eller tillgänglig bandbredd för att skicka data över nätverket, kan till exempel orsaka flaskhalsar.

Windows tillhandahåller ett inbyggda verktyg som kallas [Prestandaövervakaren](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) för att identifiera dessa flaskhalsar.

Här följer några prestandaräknare och intervall som kan vara användbar vid diagnos av flaskhalsar för optimala säkerhetskopieringar.

| Räknare | Status |
| --- | --- |
| Logisk Disk (fysisk Disk)--% inaktiv |• 100% inaktiv till 50% inaktiv = felfri</br>• 49% inaktiv till 20% inaktiv = varnings- eller Övervakare</br>• 19% inaktiv 0% inaktiv = kritiskt eller Out-of-specifikationen |
| Logisk Disk (fysisk Disk)--% medel Disk sek läsning eller skrivning |• 0,001 ms 0.015 MS = felfri</br>• 0.015 ms 0.025 MS = varnings- eller Övervakare</br>• 0.026 ms eller längre = kritiskt eller Out-of-specifikationen |
| Logisk Disk (fysisk Disk)--Aktuell diskkölängd (för alla instanser) |80 begäranden i mer än 6 minuter |
| Minne – icke växlingsbart systemminne-byte |• Mindre än 60% av poolen förbrukas = felfri<br>• 61% till 80% av pool förbrukas = varnings- eller Övervakare</br>• Större än 80% poolen förbrukas = kritiskt eller Out-of-specifikationen |
| Minne – växlingsbart systemminne-byte |• Mindre än 60% av poolen förbrukas = felfri</br>• 61% till 80% av pool förbrukas = varnings- eller Övervakare</br>• Större än 80% poolen förbrukas = kritiskt eller Out-of-specifikationen |
| Minne – tillgängliga megabyte |• 50% av ledigt minne eller fler = felfri</br>• 25% av ledigt minne = Övervakare</br>• 10% av ledigt minne = varning</br>• Mindre än 100 MB eller 5% av ledigt minne = kritiskt eller Out-of-specifikationen |
| Processor –\%processortid (alla instanser) |• Mindre än 60% förbrukas = felfri</br>• 61% till 90% förbrukas = övervakaren eller varning</br>• 91 till 100% förbrukas = kritisk |

> [!NOTE]
> Om du finner att infrastrukturen är sällan, rekommenderar vi att du defragmenterar diskar regelbundet för bättre prestanda.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Orsak: En annan process eller antivirusprogram stör Azure Backup
Vi har sett flera instanser där andra processer i Windows-system har försämrat prestanda för Azure Backup agent-processen. Till exempel om du använder både Azure Backup-agenten och ett annat program för säkerhetskopiering av data, eller om antivirusprogrammet körs och har ett lås på filer som ska säkerhetskopieras, multipeln låser på orsaka filerna konkurrens. I den här situationen säkerhetskopieringen misslyckas eller jobbet kan ta längre tid än förväntat.

Bästa rekommendationerna i det här scenariot är att stänga av andra säkerhetskopieringsprogrammet att se om säkerhetskopieringstiden för Azure Backup-agenten ändras. Se till att flera säkerhetskopieringsjobb inte körs på samma gång är vanligtvis tillräckligt för att förhindra att de påverkar varandra.

Antivirusprogram rekommenderar vi att du utesluter följande filer och platser:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe som en process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappar
* Scratch platsen (om du inte använder standard plats)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Orsak: Backup-agenten körs på en virtuell Azure-dator
Om du kör Backup-agenten på en virtuell dator, blir prestanda långsammare än när du kör på en fysisk dator. Detta är förväntat på grund av begränsningar i IOPS.  Men kan du optimera prestanda genom att växla dataenheter som ska säkerhetskopieras till Azure Premium-lagring. Vi arbetar på att åtgärda problemet och korrigering kommer att finnas i en framtida version.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Orsak: Säkerhetskopiera ett stort antal (miljoner) filer
Flytta stora mängder data tar längre tid än att flytta en mindre mängd data. I vissa fall rör säkerhetskopieringstiden inte bara storleken på data, utan också hur många filer eller mappar. Detta gäller särskilt när miljontals små filer (några byte till några få kilobyte) ska säkerhetskopieras.

Detta beror på att när du är säkerhetskopiering av data och flytta den till Azure, Azure samtidigt katalogiserar dina filer. I vissa sällsynta fall, kan katalog-åtgärden ta längre tid än förväntat.

Följande faktorer kan hjälpa dig att förstå flaskhals och därefter arbeta med nästa steg:

* **Förloppet visas i Användargränssnittet för att överföra data**. Data överförs fortfarande. Nätverkets bandbredd eller storleken på data som orsakar fördröjningar.
* **Användargränssnittet visar inte förloppet för att överföra data**. Öppna loggarna på C:\Microsoft Azure Recovery Services Agent\Temp och sedan söka efter posten FileProvider::EndData i loggarna. Den här posten betyder att dataöverföringen har slutförts och katalog igen som händer. Inte avbryta säkerhetskopieringsjobb. Vänta i stället en liten stund för katalog-åtgärd ska slutföras. Om problemet kvarstår kontaktar du [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support).
