---
title: Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup
description: Innehåller fel söknings vägledning som hjälper dig att diagnostisera orsaken till Azure Backup prestanda problem
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 592a46077bb9e3469f3a42a95173af1b6db93510
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "67704936"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup
Den här artikeln innehåller fel söknings vägledning som hjälper dig att diagnostisera orsaken till långsamma säkerhets kopierings prestanda för filer och mappar när du använder Azure Backup. När du använder Azure Backup Agent för att säkerhetskopiera filer kan säkerhets kopieringen ta längre tid än förväntat. Den här fördröjningen kan ha orsakats av ett eller flera av följande:

* [Det finns prestanda Flask halsar på datorn som säkerhets kopie ras.](#cause1)
* [En annan process eller ett antivirus program stör Azure Backup processen.](#cause2)
* [Säkerhets kopierings agenten körs på en virtuell Azure-dator (VM).](#cause3)  
* [Du säkerhetskopierar ett stort antal filer.](#cause4)

Innan du börjar felsöka problem rekommenderar vi att du hämtar och installerar den [senaste Azure Backup agenten](https://aka.ms/azurebackup_agent). Vi gör frekventa uppdateringar av säkerhets kopierings agenten för att åtgärda olika problem, lägga till funktioner och förbättra prestandan.

Vi rekommenderar också starkt att du läser igenom [vanliga frågor om Azure Backup-tjänsten](backup-azure-backup-faq.md) för att se till att du inte har några vanliga konfigurations problem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Orsak: Prestanda Flask halsar på datorn
Flask halsar på datorn som säkerhets kopie ras kan orsaka fördröjningar. Till exempel kan datorns möjlighet att läsa eller skriva till disk, eller tillgänglig bandbredd för att skicka data via nätverket, orsaka Flask halsar.

Windows innehåller ett inbyggt verktyg som kallas [prestanda övervakaren](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (PerfMon) för att identifiera Flask halsarna.

Här följer några prestanda räknare och intervall som kan vara till hjälp vid diagnostisering av flask halsar för optimala säkerhets kopieringar.

| Medelvärde | Status |
| --- | --- |
| Logisk disk (fysisk disk)--% inaktiv |• 100% inaktiv till 50% Idle = felfri</br>• 49% inaktiv till 20% inaktiv = varning eller Övervakare</br>• 19% inaktiv till 0% inaktiv = kritisk eller ur specifikation |
| Logisk disk (fysisk disk)--% AVG. Disk s Läs eller Skriv |• 0,001 MS till 0,015 MS = felfri</br>• 0,015 MS till 0,025 MS = varning eller Övervakare</br>• 0,026 MS eller längre = kritisk eller ur specifikationen |
| Logisk disk (fysisk disk)--Aktuell diskkölängd (för alla instanser) |80 förfrågningar i mer än 6 minuter |
| Minne – icke växlings Bart system minne-byte |• Mindre än 60% av poolen förbrukade = felfri<br>• 61% till 80% av poolen förbrukat = varning eller övervaka</br>• Större än 80% pool förbrukad = kritisk eller från specifikation |
| Minne--växlings Bart system minne-byte |• Mindre än 60% av poolen förbrukade = felfri</br>• 61% till 80% av poolen förbrukat = varning eller övervaka</br>• Större än 80% pool förbrukad = kritisk eller från specifikation |
| Minne – tillgängliga megabyte |• 50% ledigt minne tillgängligt eller mer = felfri</br>• 25% ledigt minne tillgängligt = övervaka</br>• 10% ledigt minne tillgängligt = varning</br>• Mindre än 100 MB eller 5% ledigt minne tillgängligt = kritiskt eller ur specifikationen |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processor--\`processor tid (alla instanser)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• Mindre än 60% förbrukat = felfri</br>• 61% till 90% förbrukat = övervaka eller varning</br>• 91% till 100% förbrukat = kritisk |

> [!NOTE]
> Om du fastställer att infrastrukturen är orsaken rekommenderar vi att du defragmenterar diskarna regelbundet för bättre prestanda.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Orsak: En annan process eller ett antivirus program som stör Azure Backup
Vi har sett flera instanser där andra processer i Windows-systemet har negativt prestanda för Azure Backup Agent processen. Om du till exempel använder både Azure Backup-agenten och ett annat program för att säkerhetskopiera data, eller om antivirus programmet körs och har låst filer som ska säkerhets kopie ras, kan flera lås på filer orsaka konkurrens. I den här situationen kan säkerhets kopieringen Miss Miss kan, eller så kan det ta längre tid än förväntat.

Den bästa rekommendationen i det här scenariot är att stänga av det andra säkerhets kopierings programmet för att se om säkerhets kopierings tiden för Azure Backup agenten ändras. Att se till att flera säkerhets kopierings jobb inte körs samtidigt är tillräckligt för att förhindra att de påverkar varandra.

För antivirus program rekommenderar vi att du undantar följande filer och platser:

* C:\Program\Microsoft Azure Recovery Services Agent\bin\cbengine.exe som en process
* C:\Program\Microsoft Azure Recovery Services agent \ mappar
* Arbets plats (om du inte använder standard platsen)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Orsak: Säkerhets kopierings agent som körs på en virtuell Azure-dator
Om du kör säkerhets kopierings agenten på en virtuell dator blir prestandan långsammare än när du kör den på en fysisk dator. Detta förväntas på grund av IOPS-begränsningar.  Du kan dock optimera prestanda genom att växla data enheter som säkerhets kopie ras till Azure Premium Storage. Vi arbetar med att åtgärda det här problemet och korrigeringen är tillgänglig i en framtida version.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Orsak: Säkerhetskopiera ett stort antal filer
Det tar längre tid att flytta en stor mängd data än att flytta en mindre data volym. I vissa fall är säkerhets kopierings tiden relaterad till inte bara storleken på data, utan också antalet filer eller mappar. Detta gäller särskilt när miljon tals små filer (några byte till några kilobyte) säkerhets kopie ras.

Det här problemet beror på att när du säkerhetskopierar data och flyttar dem till Azure, katalogiserar Azure samtidigt dina filer. I vissa sällsynta fall kan katalog åtgärden ta längre tid än förväntat.

Följande indikatorer kan hjälpa dig att förstå Flask halsen och därefter arbeta på nästa steg:

* **UI visar förloppet för data överföringen**. Data överförs fortfarande. Nätverks bandbredden eller data storleken kan orsaka fördröjningar.
* **Användar gränssnittet visar inte förloppet för data överföringen**. Öppna loggfilerna som finns i C:\Program\Microsoft Azure Recovery Services Agent\Temp och kontrol lera sedan posten FileProvider:: EndData i loggarna. Den här posten indikerar att data överföringen är slutförd och att katalog åtgärden sker. Avbryt inte säkerhets kopierings jobben. Vänta i stället lite längre tills katalog åtgärden har slutförts. Kontakta [Azure](https://portal.azure.com/#create/Microsoft.Support)-supporten om problemet kvarstår.
