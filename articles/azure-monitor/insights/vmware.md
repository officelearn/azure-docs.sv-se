---
title: VMware-övervakningslösning i Azure Monitor | Microsoft-dokument
description: Lär dig mer om hur VMware Monitoring-lösningen kan hjälpa till att hantera loggar och övervaka ESXi-värdar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664787"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware Monitoring(föråldrad) lösning i Azure Monitor

![VMware-symbol](./media/vmware/vmware-symbol.png)

> [!NOTE]
> VMware Monitoring-lösningen har inaktuell.  Kunder som redan har installerat lösningen kan fortsätta att använda den, men VMware Övervakning kan inte läggas till några nya arbetsytor.

VMware Monitoring-lösningen i Azure Monitor är en lösning som hjälper dig att skapa en centraliserad loggnings- och övervakningsmetod för stora VMware-loggar. I den här artikeln beskrivs hur du kan felsöka, fånga och hantera ESXi-värdarna på en enda plats med hjälp av lösningen. Med lösningen kan du se detaljerade data för alla dina ESXi-värdar på en enda plats. Du kan se de bästa antalet händelser, status och trender för VM- och ESXi-värdar som tillhandahålls via ESXi-värdloggarna. Du kan felsöka genom att visa och söka i centraliserade ESXi-värdloggar. Och du kan skapa aviseringar baserat på loggsökningsfrågor.

Lösningen använder inbyggda syslog-funktioner för ESXi-värden för att skicka data till en måldator, som har Log Analytics-agenten. Lösningen skriver dock inte filer i syslog inom måldatorn. Log Analytics-agenten öppnar port 1514 och lyssnar på detta. När den tar emot data skickar Log Analytics-agenten data till Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

* Lägg till VMware Monitoring-lösningen i din prenumeration med hjälp av processen som beskrivs i [Installera en övervakningslösning](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>VMware ESXi-värdar som stöds
vSphere ESXi Host 5.5, 6.0 och 6.5

#### <a name="prepare-a-linux-server"></a>Förbereda en Linux-server
Skapa en virtuell virtuell dator med Linux-operativsystem för att ta emot alla syslog-data från ESXi-värdarna. [Log Analytics Linux-agenten](../learn/quick-collect-linux-computer.md) är insamlingsplatsen för alla ESXi-värdsystemlogdata. Du kan använda flera ESXi-värdar för att vidarebefordra loggar till en enda Linux-server, som i följande exempel.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![syslog flöde](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurera syslog-samling
1. Ställ in syslog-vidarebefordran för VSphere. Detaljerad information som hjälper till att konfigurera syslog-vidarebefordran finns i [Konfigurera syslog på ESXi 5.0 och senare (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Gå till **ESXi Host Configuration** > **Software** > **Advanced Settings** > **Syslog**.
   ![vsphereconfig (vsphereconfig)](./media/vmware/vsphere1.png)  
1. Lägg till Din Linux-server och portnummer *1514*i fältet *Syslog.global.logHost* . Det kan till exempel vara `tcp://hostname:1514` eller `tcp://123.456.789.101:1514`
1. Öppna ESXi-värdbrandväggen för syslog. **ESXi Host Configuration** > **Software** > **Security Profile** > **Firewall** och öppna **egenskaper**.  

    ![vspherefw (vspherefw)](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Kontrollera att syslog är korrekt konfigurerat i vSphere-konsolen. Bekräfta på ESXI-värden att port **1514** är konfigurerad.
1. Ladda ner och installera Log Analytics-agenten för Linux på Linux-servern. Mer information finns i [Dokumentationen för Log Analytics-agenten för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. När log analytics-agenten för Linux har installerats går du till katalogen /etc/opt/microsoft/omsagent/sysconf/omsagent.d och kopierar filen vmware_esxi.conf till katalogen /etc/opt/microsoft/omsagent/conf/omsagent.d och ändra filens ägare/grupp och behörigheter. Ett exempel:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Starta om Log Analytics-agenten för Linux genom att köra `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Testa anslutningen mellan Linux-servern och ESXi-värden `nc` med kommandot på ESXi-värden. Ett exempel:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. I Azure-portalen utför du `VMware_CL`en loggfråga för . När Azure Monitor samlar in syslog-data behåller den syslog-formatet. I portalen fångas vissa specifika fält in, till exempel *Hostname* och *ProcessName*.  

    ![typ](./media/vmware/type.png)  

    Om sökresultaten för visningsloggen liknar bilden ovan är du inställd på att använda instrumentpanelen för VMware-övervakningslösningar.  

## <a name="vmware-data-collection-details"></a>Information om insamling av VMware-data
VMware Monitoring-lösningen samlar in olika prestandamått och loggdata från ESXi-värdar med hjälp av Log Analytics-agenterna för Linux som du har aktiverat.

I följande tabell visas metoder för datainsamling och annan information om hur data samlas in.

| 
    plattform
   | Log Analytics-agent för Linux | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agentdata som skickas via hanteringsgrupp | insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |var tredje minut |

Följande tabell visar exempel på datafält som samlats in av VMware Monitoring-lösningen:

| fältnamn | description |
| --- | --- |
| Device_s |VMware-lagringsenheter |
| ESXIFailure_s |feltyper |
| EventTime_t |den tid då händelsen inträffade |
| HostName_s |Värdnamn för ESXi |
| Operation_s |skapa virtuell dator eller ta bort virtuell dator |
| ProcessName_s |händelsenamn |
| ResourceId_s |namnet på VMware-värden |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSI-status |
| SyslogMessage_s |Syslog-data |
| UserName_s |användare som skapade eller tog bort virtuell dator |
| VMName_s |VM-namn |
| Dator |värddator |
| TimeGenerated |när data genererades |
| DataCenter_s |VMware datacenter |
| StorageLatency_s |lagringsfördröjning (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Översikt över VMware Monitoring-lösning
VMware-panelen visas på arbetsytan Log Analytics. Det ger en hög nivå bild av eventuella fel. När du klickar på panelen går du in i en instrumentpanelsvy.

![kakelsättning](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigera i instrumentpanelsvyn
I **VMware-instrumentpanelsvyn** ordnas bladen efter:

* Antal status för fel
* Toppvärd efter antal händelser
* Antal populära händelser
* Aktiviteter för virtuella datorer
* ESXi-värddiskhändelser

![lösning1](./media/vmware/solutionview1-1.png)

![lösning2](./media/vmware/solutionview1-2.png)

Klicka på ett blad för att öppna sökfönstret i Log Analytics som visar detaljerad information som är specifik för bladet.

Härifrån kan du redigera loggfrågan för att ändra den för något specifikt. Mer information om hur du skapar loggfrågor finns i [Hitta data med hjälp av loggfrågor i Azure Monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Hitta ESXi-värdhändelser
En enda ESXi-värd genererar flera loggar, baserat på deras processer. VMware Monitoring-lösningen centraliserar dem och sammanfattar antalet händelser. Den här centraliserade vyn hjälper dig att förstå vilken ESXi-värd som har en hög mängd händelser och vilka händelser som inträffar oftast i din miljö.

![händelse](./media/vmware/events.png)

Du kan öka detaljnivån ytterligare genom att klicka på en ESXi-värd eller en händelsetyp.

När du klickar på ett ESXi-värdnamn visar du information från den ESXi-värden. Om du vill begränsa resultaten med `“ProcessName_s=EVENT TYPE”` händelsetypen lägger du till i sökfrågan. Du kan välja **ProcessName** i sökfiltret. Det begränsar informationen åt dig.

![Borra](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Hitta aktiviteter med hög virtuell dator
En virtuell dator kan skapas och tas bort på alla ESXi-värdar. Det är bra för en administratör att identifiera hur många virtuella datorer som en ESXi-värd skapar. Det i sin tur, hjälper till att förstå prestanda och kapacitetsplanering. Att hålla reda på vm-aktivitetshändelser är avgörande när du hanterar din miljö.

![Borra](./media/vmware/vmactivities1.png)

Om du vill se ytterligare ESXi-värddata för att skapa virtuella datorer klickar du på ett ESXi-värdnamn.

![Borra](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Vanliga loggfrågor
Lösningen innehåller andra användbara frågor som kan hjälpa dig att hantera dina ESXi-värdar, till exempel högt lagringsutrymme, lagringsfördröjning och sökvägsfel.

![Frågor](./media/vmware/queries.png)


#### <a name="save-queries"></a>Spara frågor
Att spara loggfrågor är en standardfunktion i Azure Monitor och kan hjälpa dig att hålla alla frågor som du har funnit användbara. När du har skapat en fråga som du tycker är användbar sparar du den genom att klicka på **Favoriter**. Med en sparad fråga kan du enkelt återanvända den senare från sidan [Min instrumentpanel](../learn/tutorial-logs-dashboards.md) där du kan skapa egna anpassade instrumentpaneler.

![DockerDashboardVis](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Skapa aviseringar från frågor
När du har skapat dina frågor kanske du vill använda frågorna för att varna dig när specifika händelser inträffar. Se [Aviseringar i Logganalys](../platform/alerts-overview.md) för information om hur du skapar aviseringar. Exempel på aviseringar frågor och andra frågeexempel finns i [Monitor VMware med hjälp av Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogginlägg.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Vad behöver jag göra på ESXi-värdinställningen? Vilken inverkan kommer det att ha på min nuvarande miljö?
Lösningen använder den inbyggda ESXi Host Syslog-vidarebefordransmekanismen. Du behöver ingen ytterligare Microsoft-programvara på ESXi-värden för att fånga loggarna. Det bör ha en låg inverkan på din befintliga miljö. Du måste dock ställa in syslog forwarding, vilket är ESXI-funktionalitet.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Måste jag starta om min ESXi-värd?
Nej. Den här processen kräver ingen omstart. Ibland uppdaterar vSphere inte syslogen på rätt sätt. I så fall loggar du in på ESXi-värden och laddar om syslogen. Återigen behöver du inte starta om värden, så den här processen är inte störande för din miljö.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Kan jag öka eller minska volymen loggdata som skickas till Log Analytics?
Ja, det kan du. Du kan använda inställningarna för ESXi-värdloggnivå i vSphere. Loggsamlingen baseras på *informationsnivå.* Så om du vill granska skapande eller borttagning av virtuella datorer måste du behålla *informationsnivån* på Värddatorn. Mer information finns i [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Varför tillhandahåller Hostd inte data till Log Analytics? Min logginställning är inställd på info.
Det fanns en ESXi-värdbugg för syslog-tidsstämpeln. Mer information finns i [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). När du har tillämpat lösningen ska Hostd fungera normalt.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Kan jag ha flera ESXi-värdar som vidarebefordrar syslog-data till en enda virtuell dator med omsagent?
Ja. Du kan ha flera ESXi-värdar som vidarebefordrar till en enda virtuell dator med omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Varför ser jag inte data flödar in i Log Analytics?
Det kan finnas flera orsaker:

* ESXi-värden driver inte data till den virtuella datorn som kör omsagent. Testa:

  1. För att bekräfta, logga in på ESXi-värden med ssh och kör följande kommando:`nc -z ipaddressofVM 1514`

      Om detta inte lyckas är vSphere-inställningarna i den avancerade konfigurationen sannolikt inte korrekta. Se [Konfigurera syslog-samling](#configure-syslog-collection) för information om hur du konfigurerar ESXi-värden för syslog-vidarebefordran.
  1. Om syslog-portanslutningen lyckas, men du fortfarande inte ser några data, laddar du om syslogen på ESXi-värden med hjälp av ssh för att köra följande kommando:`esxcli system syslog reload`
* Den virtuella datorn med Log Analytics-agenten är inte korrekt inställd. Så här testar du detta:

  1. Log Analytics lyssnar på port 1514. Kör följande kommando om du vill kontrollera att den är öppen:`netstat -a | grep 1514`
  1. Du skulle `1514/tcp` se porten öppen. Om du inte gör det kontrollerar du att omsagensen är korrekt installerad. Om du inte ser portinformationen är syslogporten inte öppen på den virtuella datorn.

    a. Kontrollera att Log Analytics-agenten `ps -ef | grep oms`körs med hjälp av . Om den inte körs startar du processen genom att köra kommandot`sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Öppna filen `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Kontrollera att rätt användar- och gruppinställning är giltig, ungefär som:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Om filen inte finns eller om användar- och gruppinställningen är felaktig vidtar du korrigerande åtgärder genom [att förbereda en Linux-server](#prepare-a-linux-server).

## <a name="next-steps"></a>Nästa steg
* Använd [loggfrågor](../log-query/log-query-overview.md) i Log Analytics för att visa detaljerade VMware-värddata.
* [Skapa egna instrumentpaneler](../learn/tutorial-logs-dashboards.md) som visar VMware-värddata.
* [Skapa aviseringar](../platform/alerts-overview.md) när specifika VMware-värdhändelser inträffar.
