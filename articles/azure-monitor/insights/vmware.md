---
title: VMware-övervakning lösning i Azure Monitor | Microsoft Docs
description: Läs mer om hur VMware Monitoring-lösningen kan hjälpa hantera loggar och övervaka ESXi-värdar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664787"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware-övervakning (inaktuell) lösning i Azure Monitor

![VMware-symbol](./media/vmware/vmware-symbol.png)

> [!NOTE]
> VMware Monitoring-lösningen är inaktuell.  Kunder som redan har installerat lösningen kan fortsätta att använda den, men VMware Monitoring kan inte läggas till nya arbetsytor.

VMware-övervakning-lösningen i Azure Monitor är en lösning som hjälper dig att skapa en centraliserad loggnings-och övervaknings metod för stora VMware-loggar. Den här artikeln beskrivs hur du kan felsöka, samla in och hantera ESXi-värdar i en enda plats med hjälp av lösningen. Med lösningen kan se du detaljerad information för alla ESXi-värdar i en enda plats. Du kan se översta antalet händelser, status och trender för virtuella datorer och ESXi-värdar som tillhandahålls via ESXi-värd loggarna. Du kan felsöka genom att visa och söka i centraliserade ESXi-värd-loggar. Och du kan skapa aviseringar baserat på loggsökningsfrågor.

Lösningen använder inbyggda syslog-funktioner för ESXi-värd att skicka data till ett mål-dator som har Log Analytics-agenten. Lösningen skriva inte filer i syslog inom den Virtuella måldatorn. Log Analytics-agenten öppnar port 1514 och lyssnar efter detta. När den tar emot data skickar Log Analytics-agenten data till Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

* Lägg till VMware-övervakning-lösningen i din prenumeration med hjälp av processen som beskrivs i [installera en övervaknings lösning](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>VMware ESXi-värdar som stöds
vSphere ESXi-värd 5.5, 6.0 och 6.5

#### <a name="prepare-a-linux-server"></a>Förbereda en Linux-server
Skapa en Linux-operativsystem virtuell dator tar emot alla syslog-data från ESXi-värdar. [Log Analytics Linux-agenten](../learn/quick-collect-linux-computer.md) är samlings platsen för alla ESXi-värden för syslog-data. Du kan använda flera ESXi-värdar för att vidarebefordra loggar till en enda Linux-server, som i följande exempel.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![Syslog-flöde](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurera sysloginsamling
1. Konfigurera syslog-vidarebefordran för VSphere. Detaljerad information om hur du konfigurerar syslog-vidarebefordran finns i [Konfigurera syslog på ESXi 5,0 och högre (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Gå till **ESXi värd konfiguration** > **program** > **Avancerade inställningar** > **syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. I fältet *syslog. global. logHost* lägger du till Linux-servern och port numret *1514*. Till exempel `tcp://hostname:1514` eller `tcp://123.456.789.101:1514`
1. Öppna brandväggen för ESXi-värden för syslog. **ESXi värd konfiguration** > **program** > **säkerhets profil** > **brand vägg** och öppna **Egenskaper**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Kontrollera vSphere-konsolen och verifiera den syslog är rätt konfigurerad. Bekräfta att ESXI-värden som port **1514** är konfigurerad på.
1. Ladda ned och installera Log Analytics-agenten för Linux på Linux-servern. Mer information finns i [dokumentationen för Log Analytics agent för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. När Log Analytics-agenten för Linux är installerad går du till /etc/opt/microsoft/omsagent/sysconf/omsagent.d katalogen och kopiera vmware_esxi.conf filen till katalogen /etc/opt/microsoft/omsagent/conf/omsagent.d och ändringen ägare/grupp och behörigheterna för filen. Några exempel:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Starta om Log Analytics agent för Linux genom att köra `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Testa anslutningen mellan Linux-servern och ESXi-värden med hjälp av kommandot `nc` på ESXi-värden. Några exempel:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. I Azure Portal kör du en logg fråga för `VMware_CL`. När Azure Monitor samlar in syslog-data, behåller det syslog-formatet. I portalen samlas vissa fält in, till exempel *hostname* och *processname*.  

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
I vyn **VMware** Dashboard organiseras blad efter:

* Felberäkning Status
* Främsta värden efter händelseantal
* Översta antalet händelser
* Aktiviteter för virtuell dator
* Diskhändelser för ESXi-värd

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Klicka på ett blad för att öppna Log Analytics search fönstret som visar detaljerad information för bladet.

Härifrån kan du redigera logg frågan för att ändra den för något som är speciell. Mer information om hur du skapar logg frågor finns i [hitta data med hjälp av logg frågor i Azure Monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Hitta händelser för ESXi-värd
En enda ESXi-värd genererar flera loggar, utifrån sina processer. VMware Monitoring-lösningen centraliserar dem och sammanfattar antalet händelser. Den här centraliserade vyn hjälper dig att förstå vilka ESXi-värden har ett stort antal händelser och vilka händelser inträffar oftast i din miljö.

![händelse](./media/vmware/events.png)

Du kan gå vidare genom att klicka på en ESXi-värd eller en händelsetyp.

När du klickar på en ESXi-värdnamnet, kan du visa information från den ESXi-värden. Om du vill begränsa resultatet med händelse typen lägger du till `“ProcessName_s=EVENT TYPE”` i din Sök fråga. Du kan välja **processname** i Sök filtret. Som begränsar information åt dig.

![öka detaljnivån](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Hitta hög VM-aktiviteter
En virtuell dator kan skapas och tas bort på valfri ESXi-värd. Det är användbart för en administratör att identifiera hur många virtuella datorer som skapas av en ESXi-värd. Som i sin tur, hjälper dig för att förstå prestanda och kapacitetsplanering. Det är viktigt att hålla reda på händelser för VM-aktiviteter när du hanterar din miljö.

![öka detaljnivån](./media/vmware/vmactivities1.png)

Om du vill se ytterligare ESXi-värd-uppgifter för skapande av virtuell dator klickar du på en ESXi-värdnamnet.

![öka detaljnivån](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Vanliga logg frågor
Lösningen innehåller andra användbara frågor som kan hjälpa dig att hantera din ESXi-värdar, t.ex hög lagringsutrymme och lagringssvarstiden sökvägsfel.

![frågor](./media/vmware/queries.png)


#### <a name="save-queries"></a>Spara frågor
Att spara logg frågor är en standard funktion i Azure Monitor och kan hjälpa dig att hålla alla frågor som du har hittat användbara. När du har skapat en fråga som du tycker är användbar sparar du den genom att klicka på **Favoriter**. Med en sparad fråga kan du enkelt återanvända den senare från [min instrument panels](../learn/tutorial-logs-dashboards.md) sida där du kan skapa dina egna anpassade instrument paneler.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Skapa aviseringar från frågor
När du har skapat dina frågor, kanske du vill använda frågorna för att meddela dig när specifika händelser äger rum. Information om hur du skapar aviseringar finns [i varningar i Log Analytics](../platform/alerts-overview.md) . Exempel på aviserings frågor och andra fråge exempel finns i [Övervaka VMware med Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogg inlägg.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Vad behöver jag göra på ESXi värd inställningen? Vilken effekt det har på min aktuella miljön?
Lösningen använder inbyggda ESXi-värd Syslog-vidarebefordran mekanism. Du behöver inte några ytterligare Microsoft-programvara på ESXi-värden för att samla in loggarna. Den bör ha ett låga påverkan på din befintliga miljö. Du måste dock ange syslog-vidarebefordran, vilket är en ESXI-funktion.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Behöver jag startar om min ESXi-värden?
Nej. Den här processen kräver inte en omstart. Ibland uppdateras vSphere korrekt inte Syslog-enheten. I detta fall är att logga in på ESXi-värden och läsa in Syslog-enheten. Igen, du behöver starta om värden så att den här processen inte är störande för din miljö.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Kan jag öka eller minska mängden loggdata som skickas till Log Analytics?
Du kan Ja. Du kan använda inställningar för Loggnivå för ESXi-värden i vSphere. Logg insamling baseras på *informations* nivån. Så om du vill granska skapandet eller borttagning av virtuella datorer måste du behålla *informations* nivån på värd. Mer information finns i VMware- [kunskaps basen](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Varför Hostd inte tillhandahåller data till Log Analytics? Min log är inställningen information.
Det uppstod en ESXi-värd-bugg för syslog-tidsstämpel. Mer information finns i VMware- [kunskaps basen](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). När du har installerat lösningen ska Hostd fungera normalt.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Kan jag har flera ESXi-värdar som vidarebefordrar syslog-data till en enskild virtuell dator med omsagent?
Ja. Du kan ha flera ESXi-värdar som vidarebefordrar till en enskild virtuell dator med omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Varför visas inte data som flödar till Log Analytics?
Det kan finnas flera anledningar:

* ESXi-värden inte korrekt skicka data till den virtuella datorn som kör omsagent. Om du vill testa, utför du följande steg:

  1. Bekräfta genom att logga in på ESXi-värden med SSH och kör följande kommando: `nc -z ipaddressofVM 1514`

      Om detta inte lyckas, vSphere-inställningarna i avancerade förmodligen inte åtgärda. Se [Konfigurera syslog-samling](#configure-syslog-collection) för information om hur du konfigurerar ESXi-värden för syslog-vidarebefordran.
  1. Om syslog-port anslutningen lyckas, men du inte ser några data, laddar du om syslog på ESXi-värden med hjälp av SSH för att köra följande kommando: `esxcli system syslog reload`
* Den virtuella datorn med Log Analytics-agenten har inte angetts korrekt. Utför följande steg för att testa detta:

  1. Log Analytics lyssnar på port 1514. Kontrol lera att den är öppen genom att köra följande kommando: `netstat -a | grep 1514`
  1. Du bör se port `1514/tcp` öppen. Om du inte gör det, kan du kontrollera att omsagent är korrekt installerad. Syslog-porten är inte öppen på den virtuella datorn om du inte ser portinformationen.

    a. Verifiera att Log Analytics Agent körs genom att använda `ps -ef | grep oms`. Om den inte körs startar du processen genom att köra kommandot `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Öppna filen `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Kontrol lera att rätt användar-och grupp inställning är giltig, ungefär så här: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Om filen inte finns, eller om användar-och grupp inställningen är fel, vidtar du åtgärder genom att [förbereda en Linux-server](#prepare-a-linux-server).

## <a name="next-steps"></a>Nästa steg
* Använd [logg frågor](../log-query/log-query-overview.md) i Log Analytics om du vill visa detaljerade VMware-värdar.
* [Skapa dina egna instrument paneler](../learn/tutorial-logs-dashboards.md) som visar VMware-värd data.
* [Skapa aviseringar](../platform/alerts-overview.md) när vissa VMware-värd händelser inträffar.
