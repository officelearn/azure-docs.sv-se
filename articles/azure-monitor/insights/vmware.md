---
title: VMware Monitoring-lösningen i Azure Monitor | Microsoft Docs
description: Läs mer om hur VMware Monitoring-lösningen kan hjälpa hantera loggar och övervaka ESXi-värdar.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: magoedte
ms.openlocfilehash: eac6a27c3bcf64462a9f3d9a57da6df736f30c78
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883283"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware Monitoring (inaktuell)-lösning i Azure Monitor

![VMware-symbol](./media/vmware/vmware-symbol.png)

> [!NOTE]
> VMware Monitoring-lösningen är inaktuell.  Kunder som redan har installerat lösningen kan fortsätta att använda den, men VMware Monitoring kan inte läggas till nya arbetsytor.

VMware Monitoring-lösningen i Azure Monitor är en lösning som hjälper dig att skapa en centraliserad loggning och övervakning metod för stora VMware-loggar. Den här artikeln beskrivs hur du kan felsöka, samla in och hantera ESXi-värdar i en enda plats med hjälp av lösningen. Med lösningen kan se du detaljerad information för alla ESXi-värdar i en enda plats. Du kan se översta antalet händelser, status och trender för virtuella datorer och ESXi-värdar som tillhandahålls via ESXi-värd loggarna. Du kan felsöka genom att visa och söka i centraliserade ESXi-värd-loggar. Och du kan skapa aviseringar baserat på loggsökningsfrågor.

Lösningen använder inbyggda syslog-funktioner för ESXi-värd att skicka data till ett mål-dator som har Log Analytics-agenten. Lösningen skriva inte filer i syslog inom den Virtuella måldatorn. Log Analytics-agenten öppnar port 1514 och lyssnar efter detta. När den tar emot data i Log Analytics-agenten skickar data till Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

* Lägga till VMware Monitoring-lösning för din prenumeration med hjälp av metoden som beskrivs i [installera en övervakningslösning](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>VMware ESXi-värdar som stöds
vSphere ESXi-värd 5.5, 6.0 och 6.5

#### <a name="prepare-a-linux-server"></a>Förbereda en Linux-server
Skapa en Linux-operativsystem virtuell dator tar emot alla syslog-data från ESXi-värdar. Den [Log Analytics Linux-agenten](../learn/quick-collect-linux-computer.md) är den samling för alla ESXi-värd syslog-data. Du kan använda flera ESXi-värdar för att vidarebefordra loggar till en enda Linux-server, som i följande exempel.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![Syslog-flöde](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurera sysloginsamling
1. Konfigurera syslog-vidarebefordran för VSphere. Detaljerad information för att konfigurera syslog-vidarebefordran finns i [konfigurera syslog på ESXi 5.0 och senare (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Gå till **ESXi Värdkonfigurationen** > **programvara** > **avancerade inställningar** > **Syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. I den *Syslog.global.logHost* fältet, lägga till din Linux-server och portnumret *1514*. Till exempel `tcp://hostname:1514` eller `tcp://123.456.789.101:1514`
1. Öppna brandväggen för ESXi-värden för syslog. **Konfiguration för ESXi-värd** > **programvara** > **säkerhetsprofil** > **brandväggen** och öppna **Egenskaper**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Kontrollera vSphere-konsolen och verifiera den syslog är rätt konfigurerad. Kontrollera att porten på ESXI-värden **1514** har konfigurerats.
1. Ladda ned och installera Log Analytics-agenten för Linux på Linux-servern. Mer information finns i den [dokumentationen för Log Analytics-agenten för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. När Log Analytics-agenten för Linux är installerad går du till /etc/opt/microsoft/omsagent/sysconf/omsagent.d katalogen och kopiera vmware_esxi.conf filen till katalogen /etc/opt/microsoft/omsagent/conf/omsagent.d och ändringen ägare/grupp och behörigheterna för filen. Exempel:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Starta om Log Analytics-agenten för Linux genom att köra `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Testa anslutningen mellan Linux-servern och ESXi-värden med hjälp av den `nc` på ESXi-värden. Exempel:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. I Azure-portalen, utför du en loggfråga för `VMware_CL`. När Azure Monitor och samlar in syslog-data, behåller den syslog-format. I portalen vissa specifika fält fångas som *värdnamn* och *ProcessName*.  

    ![typ](./media/vmware/type.png)  

    Om sökresultaten Visa loggen liknar bilden ovan, är du inställd på att använda VMware Monitoring lösningens instrumentpanel.  

## <a name="vmware-data-collection-details"></a>VMware data samling information
VMware Monitoring-lösningen samlar in olika mått och loggfiler prestandadata från ESXi-värdar med Log Analytics-agenter för Linux som du har aktiverat.

I följande tabell visas data samlingsmetoder och annan information om hur data samlas in.

| Plattform | Log Analytics-agenten för Linux | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agenten data som skickas via hanteringsgruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |var 3: e minut |

I följande tabell visas exempel på datafält som samlas in av VMware Monitoring-lösningen:

| fältnamn | beskrivning |
| --- | --- |
| Device_s |VMware-lagringsenheter |
| ESXIFailure_s |typer av fel |
| EventTime_t |tid när händelsen inträffade |
| HostName_s |ESXi-värdnamn |
| Operation_s |Skapa en virtuell dator eller ta bort virtuell dator |
| ProcessName_s |Händelsenamn |
| ResourceId_s |namnet på VMware-värden |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Status för VMware-SCSI |
| SyslogMessage_s |Syslog-data |
| UserName_s |användare som skapas eller tas bort VM |
| VMName_s |VM-namn |
| Dator |värddatorn |
| TimeGenerated |tidpunkt data genererades |
| DataCenter_s |VMware-datacenter |
| StorageLatency_s |Storage-svarstid (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Översikt över VMware Monitoring-lösningen
VMware-panelen visas i Log Analytics-arbetsytan. Det ger en övergripande bild av eventuella fel. När du klickar på panelen gå i en instrumentpanelsvy.

![kakelsättning](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Gå en instrumentpanelsvy
I den **VMware** instrumentpanelsvyn blad är ordnade efter:

* Felberäkning Status
* Främsta värden efter händelseantal
* Översta antalet händelser
* Aktiviteter för virtuell dator
* Diskhändelser för ESXi-värd

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Klicka på ett blad för att öppna Log Analytics search fönstret som visar detaljerad information för bladet.

Härifrån kan redigera du loggfråga för att modifiera den efter något mer specifikt. Mer information om hur du skapar loggfrågor finns i [hitta data med hjälp av loggfrågor i Azure Monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Hitta händelser för ESXi-värd
En enda ESXi-värd genererar flera loggar, utifrån sina processer. VMware Monitoring-lösningen centraliserar dem och sammanfattar antalet händelser. Den här centraliserade vyn hjälper dig att förstå vilka ESXi-värden har ett stort antal händelser och vilka händelser inträffar oftast i din miljö.

![händelse](./media/vmware/events.png)

Du kan gå vidare genom att klicka på en ESXi-värd eller en händelsetyp.

När du klickar på en ESXi-värdnamnet, kan du visa information från den ESXi-värden. Om du vill begränsa resultaten med händelsetyp, lägger du till `“ProcessName_s=EVENT TYPE”` i en sökfråga. Du kan välja **ProcessName** i sökrutan filtrera. Som begränsar information åt dig.

![öka detaljnivån](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Hitta hög VM-aktiviteter
En virtuell dator kan skapas och tas bort på valfri ESXi-värd. Det är användbart för en administratör att identifiera hur många virtuella datorer som skapas av en ESXi-värd. Som i sin tur, hjälper dig för att förstå prestanda och kapacitetsplanering. Det är viktigt att hålla reda på händelser för VM-aktiviteter när du hanterar din miljö.

![öka detaljnivån](./media/vmware/vmactivities1.png)

Om du vill se ytterligare ESXi-värd-uppgifter för skapande av virtuell dator klickar du på en ESXi-värdnamnet.

![öka detaljnivån](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Vanliga loggfrågor om
Lösningen innehåller andra användbara frågor som kan hjälpa dig att hantera din ESXi-värdar, t.ex hög lagringsutrymme och lagringssvarstiden sökvägsfel.

![frågor](./media/vmware/queries.png)


#### <a name="save-queries"></a>Spara frågor
Sparar loggfrågor är en standard funktion i Azure Monitor och kan hjälpa dig att hålla alla frågor som du har hittat användbart. När du skapar en fråga som användbara kan du spara det genom att klicka på den **Favoriter**. En sparad fråga kan du enkelt återanvända det senare i den [min instrumentpanel](../learn/tutorial-logs-dashboards.md) sidan där du kan skapa dina egna anpassade instrumentpaneler.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Skapa aviseringar från frågor
När du har skapat dina frågor, kanske du vill använda frågorna för att meddela dig när specifika händelser äger rum. Se [aviseringar i Log Analytics](../platform/alerts-overview.md) information om hur du skapar aviseringar. Exempel på aviseringar frågor och andra fråga-exempel finns i [övervaka VMware med Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogginlägg.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Vad behöver jag göra på ESXi värd inställningen? Vilken effekt det har på min aktuella miljön?
Lösningen använder inbyggda ESXi-värd Syslog-vidarebefordran mekanism. Du behöver inte några ytterligare Microsoft-programvara på ESXi-värden för att samla in loggarna. Den bör ha ett låga påverkan på din befintliga miljö. Du måste dock ange syslog-vidarebefordran, vilket är en ESXI-funktion.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Behöver jag startar om min ESXi-värden?
Nej. Den här processen kräver inte en omstart. Ibland uppdateras vSphere korrekt inte Syslog-enheten. I detta fall är att logga in på ESXi-värden och läsa in Syslog-enheten. Igen, du behöver starta om värden så att den här processen inte är störande för din miljö.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Kan jag öka eller minska mängden loggdata som skickas till Log Analytics?
Du kan Ja. Du kan använda inställningar för Loggnivå för ESXi-värden i vSphere. Logginsamling baseras på den *info* nivå. Så om du vill granska virtuell dator skapas eller tas bort måste du behålla den *info* nivå på Hostd. Mer information finns i den [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Varför Hostd inte tillhandahåller data till Log Analytics? Min log är inställningen information.
Det uppstod en ESXi-värd-bugg för syslog-tidsstämpel. Mer information finns i den [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). När du har installerat lösningen ska Hostd fungera normalt.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Kan jag har flera ESXi-värdar som vidarebefordrar syslog-data till en enskild virtuell dator med omsagent?
Ja. Du kan ha flera ESXi-värdar som vidarebefordrar till en enskild virtuell dator med omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Varför visas inte data som flödar till Log Analytics?
Det kan finnas flera anledningar:

* ESXi-värden inte korrekt skicka data till den virtuella datorn som kör omsagent. Om du vill testa, utför du följande steg:

  1. Bekräfta genom att logga in på ESXi-värden med hjälp av ssh och kör följande kommando: `nc -z ipaddressofVM 1514`

      Om detta inte lyckas, vSphere-inställningarna i avancerade förmodligen inte åtgärda. Se [konfigurera sysloginsamling](#configure-syslog-collection) information om hur du ställer in ESXi-värden för syslog-vidarebefordran.
  1. Om syslog-portanslutningen har slutförts, men du fortfarande inte ser några data, sedan ladda in syslog på ESXi-värden med ssh och kör följande kommando: `esxcli system syslog reload`
* Den virtuella datorn med Log Analytics-agenten har inte angetts korrekt. Utför följande steg för att testa detta:

  1. Log Analytics lyssnar på port 1514. Kontrollera att den är öppen med följande kommando: `netstat -a | grep 1514`
  1. Du bör se port `1514/tcp` öppna. Om du inte gör det, kan du kontrollera att omsagent är korrekt installerad. Syslog-porten är inte öppen på den virtuella datorn om du inte ser portinformationen.

    a. Kontrollera att Log Analytics-agenten körs med hjälp av `ps -ef | grep oms`. Om den inte körs, starta den genom att köra kommandot `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Öppna filen `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Kontrollera att rätt användare och grupp-inställning är giltig, vilket liknar: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Om filen inte finns eller är fel, användare och grupp-inställning vidta åtgärder med [förbereda en Linux-server](#prepare-a-linux-server).

## <a name="next-steps"></a>Nästa steg
* Använd [logga frågor](../log-query/log-query-overview.md) ha data i Log Analytics för att visa detaljerad VMware.
* [Skapa dina egna instrumentpaneler](../learn/tutorial-logs-dashboards.md) som visar data för VMware-värd.
* [Skapa aviseringar](../platform/alerts-overview.md) när specifika händelser för VMware-värd inträffar.
