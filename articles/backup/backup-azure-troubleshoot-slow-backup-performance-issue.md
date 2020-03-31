---
title: Felsöka långsam säkerhetskopiering av filer och mappar
description: Innehåller felsökningsvägledning som hjälper dig att diagnostisera orsaken till prestandaproblem för Azure Backup
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 6c650ee735ffcdd50f4361a867fa592f4965ab68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408700"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup

Den här artikeln innehåller felsökningsvägledning som hjälper dig att diagnostisera orsaken till långsamma säkerhetskopieringsprestanda för filer och mappar när du använder Azure Backup. När du använder Azure Backup-agenten för att säkerhetskopiera filer kan säkerhetskopieringsprocessen ta längre tid än förväntat. Den här fördröjningen kan orsakas av något av följande:

* [Det finns flaskhalsar i prestanda på datorn som säkerhetskopieras.](#cause1)
* [En annan process eller antivirusprogram stör Azure Backup-processen.](#cause2)
* [Säkerhetskopieringsagenten körs på en virtuell Azure-dator (VM).](#cause3)  
* [Du säkerhetskopierar ett stort antal (miljoner) filer.](#cause4)

Innan du börjar felsöka problem rekommenderar vi att du hämtar och installerar den [senaste Azure Backup-agenten](https://aka.ms/azurebackup_agent). Vi gör frekventa uppdateringar av säkerhetskopieringsagenten för att åtgärda olika problem, lägga till funktioner och förbättra prestanda.

Vi rekommenderar också starkt att du granskar [vanliga frågor om Azure Backup-tjänsten](backup-azure-backup-faq.md) för att se till att du inte har några av de vanliga konfigurationsproblemen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Orsak: Säkerhetskopieringsjobb som körs i opoptimeiserat läge

* MARS-agenten kan köra säkerhetskopieringsjobbet i **optimerat läge** med hjälp av USN-ändringsjournal (uppdateringssekvensnummer) eller **ooptimerat läge** genom att söka efter ändringar i kataloger eller filer genom att skanna hela volymen.
* Ooptimeiserat läge är långsamt eftersom agenten måste skanna varje fil på volymen och jämföra med metadata för att bestämma de ändrade filerna.
* För att kontrollera detta, öppna **Jobbinformation** från MARS-agentkonsolen och kontrollera statusen för att se om det står **Överföring av data (ooptimeras, kan ta längre tid)** som visas nedan:

    ![Köra i opoptimeiserat läge](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Följande villkor kan leda till att säkerhetskopieringsjobbet körs i opoptimeiserat läge:
  * Första säkerhetskopieringen (kallas även initial replikering) körs alltid i opoptimeiserat läge
  * Om det tidigare säkerhetskopieringsjobbet misslyckas körs nästa schemalagda säkerhetskopieringsjobb som ooptimetiserat.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Orsak: Flaskhalsar i prestanda på datorn

Flaskhalsar på datorn som säkerhetskopieras kan orsaka förseningar. Datorns möjlighet att läsa eller skriva till disk, eller tillgänglig bandbredd för att skicka data över nätverket, kan till exempel orsaka flaskhalsar.

Windows tillhandahåller ett inbyggt verktyg som kallas [Performance Monitor](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) för att upptäcka dessa flaskhalsar.

Här är några prestandaräknare och intervall som kan vara till hjälp för att diagnostisera flaskhalsar för optimala säkerhetskopior.

| Räknare | Status |
| --- | --- |
| Logisk disk(fysisk disk)--%inaktiv |* 100% tomgång till 50% tomgång = Friska</br>* 49% tomgång till 20% tomgång = Varning eller monitor</br>* 19% inaktiv till 0% tomgång = Kritisk eller out of Spec |
| Logisk disk(fysisk disk)--%Genomsnittlig disksek läs- eller skrivning |* 0,001 ms till 0,015 ms = Friska</br>* 0,015 ms till 0,025 ms = Varning eller monitor</br>* 0,026 ms eller längre = Kritisk eller out of Spec |
| Logisk disk(fysisk disk)--Aktuell diskkölängd (för alla instanser) |80 förfrågningar om mer än 6 minuter |
| Minne - Pool icke-växlingsbara byte |* Mindre än 60% av poolen konsumeras = Friska<br>* 61% till 80% av poolen förbrukas = Varning eller monitor</br>* Större än 80% pool förbrukas = Kritisk eller Out of Spec |
| Minne - Pool växlingar |* Mindre än 60% av poolen konsumeras = Friska</br>* 61% till 80% av poolen förbrukas = Varning eller monitor</br>* Större än 80% pool förbrukas = Kritisk eller Out of Spec |
| Minnes- Tillgängliga Megabyte |* 50% av ledigt minne tillgängligt eller mer = Friska</br>* 25% av ledigt minne tillgängligt = Monitor</br>* 10% av ledigt minne tillgängligt = Varning</br>* Mindre än 100 MB eller 5% av ledigt minne tillgängligt = Kritisk eller out of Spec |
| Processor-\%Processortid (alla instanser) |* Mindre än 60% konsumeras = Friska</br>* 61% till 90% konsumeras = Övervaka eller försiktighet</br>* 91% till 100% konsumeras = Kritisk |

> [!NOTE]
> Om du anser att infrastrukturen är den skyldige rekommenderar vi att du defragmenterar diskarna regelbundet för bättre prestanda.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Orsak: En annan process eller ett antivirusprogram som stör Azure Backup

Vi har sett flera instanser där andra processer i Windows-systemet har påverkat prestandan för Azure Backup-agentprocessen negativt. Om du till exempel använder både Azure Backup-agenten och ett annat program för att säkerhetskopiera data, eller om antivirusprogram körs och har ett lås på filer som ska säkerhetskopieras, kan flera lås på filer orsaka konkurrens. I det här fallet kan säkerhetskopian misslyckas eller så kan jobbet ta längre tid än förväntat.

Den bästa rekommendationen i det här scenariot är att stänga av det andra säkerhetskopieringsprogrammet för att se om säkerhetskopieringstiden för Azure Backup-agenten ändras. Vanligtvis är det tillräckligt att se till att flera säkerhetskopieringsjobb inte körs samtidigt för att förhindra att de påverkar varandra.

För antivirusprogram rekommenderar vi att du utesluter följande filer och platser:

* C:\Program\Microsoft Azure Recovery Services Agent\bin\cbengine.exe som en process
* C:\Program\Microsoft Azure Recovery Services Agent\mappar
* Scratch-plats (om du inte använder standardplatsen)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Orsak: Säkerhetskopieringsagent som körs på en virtuell Azure-dator

Om du kör säkerhetskopieringsagenten på en virtuell dator blir prestandan långsammare än när du kör den på en fysisk dator. Detta förväntas på grund av IOPS begränsningar.  Du kan dock optimera prestanda genom att växla de dataenheter som säkerhetskopieras till Azure Premium Storage. Vi arbetar med att åtgärda problemet och korrigeringen kommer att vara tillgänglig i en framtida version.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Orsak: Säkerhetskopiera ett stort antal (miljoner) filer

Det tar längre tid att flytta en stor mängd data än att flytta en mindre mängd data. I vissa fall är säkerhetskopieringstiden inte bara relaterad till inte bara storleken på data, men också antalet filer eller mappar. Detta gäller särskilt när miljontals små filer (några byte till några kilobyte) säkerhetskopieras.

Detta beror på att när du säkerhetskopierar data och flyttar dem till Azure katalogiserar Azure dina filer samtidigt. I vissa sällsynta fall kan katalogåtgärden ta längre tid än förväntat.

Följande indikatorer kan hjälpa dig att förstå flaskhalsen och därmed arbeta med nästa steg:

* **Användargränssnittet visar förloppet för dataöverföringen**. Uppgifterna överförs fortfarande. Nätverkets bandbredd eller storleken på data kan orsaka förseningar.
* **Användargränssnittet visar inte förloppet för dataöverföringen**. Öppna loggarna som finns på C:\Program Files\Microsoft Azure Recovery Services Agent\Temp och kontrollera sedan om filenProvider::EndData-posten i loggarna. Den här posten betyder att dataöverföringen har slutförts och att katalogåtgärden pågår. Avbryt inte säkerhetskopieringsjobben. Vänta i stället lite längre på att katalogåtgärden ska slutföras. Om problemet kvarstår kontaktar du [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support).

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor om säkerhetskopiering av filer och mappar](backup-azure-file-folder-backup-faq.md)