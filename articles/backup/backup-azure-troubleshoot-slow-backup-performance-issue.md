---
title: Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup
description: Innehåller felsökningsinformation för att hjälpa dig att diagnostisera orsaken till problem med Azure Backup-prestanda
services: backup
author: genlin
manager: cshepard
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ab338964a67991c0e97bf218f8aa004e0e5dfe38
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418254"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup
Den här artikeln innehåller felsökningsinformation för att hjälpa dig att diagnostisera orsaken till långsamma prestanda vid säkerhetskopiering för filer och mappar när du använder Azure Backup. När du använder Azure Backup-agenten för säkerhetskopiering av filer, kan säkerhetskopieringen ta längre tid än förväntat. Den här fördröjningen kan orsakas av en eller flera av följande:

* [Det finns flaskhalsar i prestanda på datorn som säkerhetskopieras.](#cause1)
* [En annan process eller ett antivirusprogram hindrar Azure Backup-processen.](#cause2)
* [Backup-agenten körs på en Azure-dator (VM).](#cause3)  
* [Du säkerhetskopierar ett stort antal (miljoner) filer.](#cause4)

Innan du startar felsökning av problem, rekommenderar vi att du hämtar och installerar den [senaste Azure Backup-agenten](http://aka.ms/azurebackup_agent). Vi gör frekventa uppdateringar för Backup-agenten för att åtgärda problemen, lägga till funktioner och förbättra prestanda.

Vi rekommenderar också starkt att du läser den [Azure Backup service FAQ](backup-azure-backup-faq.md) att kontrollera att du inte har någon av de vanligaste konfigurationsproblemen som.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Orsak: Flaskhalsar i prestanda på datorn
Flaskhalsar på datorn som säkerhetskopieras kan orsaka fördröjningar. Datorns möjligheten att läsa eller skriva till disk eller tillgänglig bandbredd för att skicka data över nätverket, kan till exempel orsaka flaskhalsar.

Windows tillhandahåller ett inbyggda verktyg som kallas [Prestandaövervakaren](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) för att identifiera dessa flaskhalsar.

Här följer några prestandaräknare och adressintervall som kan vara till hjälp diagnosticera flaskhalsar för optimal säkerhetskopiering.

| Räknare | Status |
| --- | --- |
| Logisk Disk (fysiska disken)--% inaktiv |• 100% inaktiv till 50% inaktiv = felfri</br>• 49% inaktiv till 20% inaktiv = varnings- eller Övervakare</br>• 19% inaktiv till 0% inaktiv = kritiska eller av Spec |
| Logisk Disk (fysiska disken)--% genomsn. Disk sek läsning eller skrivning |• 0,001 ms till 0.015 ms = felfri</br>• 0.015 ms till 0.025 ms = varnings- eller Övervakare</br>• 0.026 ms eller längre = kritiska eller av Spec |
| Logisk Disk (fysiska disken) – Aktuell diskkölängd (för alla instanser) |80 begäranden i mer 6 minuter |
| Minne – icke växlingsbart systemminne-byte |• Mindre än 60% av poolen som förbrukas = felfri<br>• 61% till 80% av pool förbrukas = varnings- eller Övervakare</br>• Är större än 80% pool förbrukas = kritiska eller av Spec |
| Minne – växlingsbart systemminne-byte |• Mindre än 60% av poolen som förbrukas = felfri</br>• 61% till 80% av pool förbrukas = varnings- eller Övervakare</br>• Är större än 80% pool förbrukas = kritiska eller av Spec |
| Minne – tillgängliga megabyte |• 50% av ledigt minne som är tillgängliga eller mer = felfri</br>• 25% av ledigt minne som är tillgängliga = skärm</br>• 10% av ledigt minne som är tillgängliga = varning</br>• Mindre än 100 MB eller 5% av ledigt minne som är tillgängliga = kritiska eller av Spec |
| Processor –\%processortid (alla instanser) |• Mindre än 60% förbrukas = felfri</br>• 61% till 90% förbrukas = övervakaren eller varning</br>• 91 till 100% förbrukas = kritiskt |

> [!NOTE]
> Om du har fastställt att infrastrukturen är sällan, rekommenderar vi att du defragmenterar diskar för bättre prestanda med jämna mellanrum.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Orsak: En annan process eller ett antivirusprogram som stör Azure Backup
Vi har sett flera instanser där andra processer i Windows-systemet negativt påverkas prestandan för Azure Backup agent-processen. Till exempel om du använder både Azure Backup-agenten och ett annat program för säkerhetskopiering av data, eller om antivirusprogrammet körs och har ett lås på filer som ska säkerhetskopieras, multipeln låser på orsaka filerna konkurrens. I det här fallet säkerhetskopieringen misslyckas eller jobbet kan ta längre tid än förväntat.

Bästa val i det här scenariot är att stänga av den andra säkerhetskopieringsprogrammet för att se om det ändrar säkerhetskopieringstiden för Azure Backup-agenten. Se till att flera säkerhetskopieringsjobb inte körs på samma gång är vanligtvis är tillräckliga för att förhindra att de påverkar varandra.

För antivirusprogram rekommenderar vi att du undantar följande filer och platser:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe som en process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappar
* Tillfällig plats (om du inte använder standardplatsen)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Orsak: Backup-agenten som körs på virtuella Azure-datorer
Om du kör Backup-agenten på en virtuell dator, kommer vara långsammare än när du kör på en fysisk dator. Detta förväntas på grund av begränsningar av IOPS.  Men kan du optimera prestanda genom att växla mellan de enheter som ska säkerhetskopieras till Azure Premium Storage. Vi arbetar på att åtgärda problemet och korrigeringen kommer att finnas i en framtida version.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Orsak: Säkerhetskopiering av ett stort antal (miljoner) filer
Flytta stora mängder data tar längre tid än att flytta en mindre mängd data. I vissa fall kan rör säkerhetskopieringstiden inte bara storleken på data, utan också hur många filer eller mappar. Detta gäller särskilt när miljontals små filer (några byte till några få kilobyte) säkerhetskopieras.

Detta beror på att när du säkerhetskopiering av data och flytta den till Azure, Azure samtidigt omkatalogiseras, dina filer. I vissa sällsynta fall kan kan katalogen åtgärden ta längre tid än förväntat.

Följande indikatorer kan hjälpa dig att förstå flaskhalsen och därefter arbetar med nästa steg:

* **Användargränssnittet visar förloppet för dataöverföringen**. Data överförs fortfarande. Nätverkets bandbredd eller storleken på data som orsakar fördröjningar.
* **Användargränssnittet visas inte förloppet för dataöverföringen**. Öppna loggar i C:\Program Files\Microsoft Azure Recovery Services Agent\Temp och sedan söka efter posten FileProvider::EndData i loggarna. Den här posten innebär det att dataöverföringen har slutförts och catalog åtgärden sker. Avbryt inte säkerhetskopieringsjobben. I stället vänta lite längre catalog-åtgärden slutförs. Om problemet kvarstår kontaktar du [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support).
