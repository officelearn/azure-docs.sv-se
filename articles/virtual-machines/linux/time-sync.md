---
title: Tidssynkronisering för virtuella Linux-datorer i Azure
description: Tidssynkronisering för virtuella Linux-datorer.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: c3571d9ba94e1803259457d473ed3f1669ea67ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330584"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Tidssynkronisering för virtuella Linux-datorer i Azure

Tidssynkronisering är viktigt för säkerhet och händelsekorrelation. Ibland används den för implementering av distribuerade transaktioner. Tidsnoggrannheten mellan flera datorsystem uppnås genom synkronisering. Synkroniseringen kan påverkas av flera saker, inklusive omstarter och nätverkstrafik mellan tidskällan och datorn som hämtar tiden. 

Azure backas upp av infrastruktur som kör Windows Server 2016. Windows Server 2016 har förbättrat algoritmer som används för att korrigera tid och villkor den lokala klockan för att synkronisera med UTC.  Funktionen Korrekt tid i Windows Server 2016 förbättrade avsevärt hur VMICTimeSync-tjänsten som styr virtuella datorer med värden för korrekt tid. Förbättringar inkluderar mer exakt inledande tid på VM start eller VM-återställning och avbrott svarstid korrigering. 

>[!NOTE]
>En snabb översikt över Windows Tidstjänst kan du titta på den här [översiktsvideon](https://aka.ms/WS2016TimeVideo)på hög nivå .
>
> Mer information finns i [Exakt tid för Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Översikt

Noggrannheten för en datorklocka mäts på hur nära datorklockan är till tidsstandarden Coordinated Universal Time (UTC). UTC definieras av ett multinationellt urval av exakta atomklockor som bara kan vara avstängd med en sekund på 300 år. Men att läsa UTC kräver direkt specialiserad maskinvara. I stället synkroniseras tidsservrar till UTC och nås från andra datorer för att ge skalbarhet och robusthet. Varje dator har tid synkronisering tjänst körs som vet vilken tid servrar att använda och regelbundet kontrollerar om datorns klocka behöver korrigeras och justerar tiden om det behövs. 

Azure-värdar synkroniseras med interna Microsoft-tidsservrar som tar sin tid från Microsoft-ägda Stratum 1-enheter, med GPS-antenner. Virtuella datorer i Azure kan antingen vara beroende av att värden skickar den korrekta tiden *(värdtid)* till den virtuella datorn eller så kan den virtuella datorn direkt få tid från en tidsserver eller en kombination av båda. 

På fristående maskinvara läser Linux OS bara värdmaskinvaran vid uppstart. Därefter underhålls klockan med avbrotts timern i Linux-kärnan. I den här konfigurationen kommer klockan att glida över tiden. I nyare Linux-distributioner på Azure kan virtuella datorer använda VMICTimeSync-providern, som ingår i LINUX-integrationstjänsterna (LIS), för att fråga efter klockuppdateringar från värden oftare.

Interaktioner med den virtuella datorn med värden kan också påverka klockan. Under [minnesbevarande underhåll](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)pausas virtuella datorer i upp till 30 sekunder. Innan underhållet påbörjas visar till exempel vm-klockan 10:00:00 och varar 28 sekunder. När den virtuella datorn har återupptagits visas klockan på den virtuella datorn fortfarande 10:00:00, vilket skulle vara 28 sekunder bort. För att korrigera detta övervakar VMICTimeSync-tjänsten vad som händer på värden och uppmanar till ändringar som ska ske på de virtuella datorerna för att kompensera.

Utan tidssynkronisering fungerar skulle klockan på den virtuella datorn ackumulera fel. När det bara finns en virtuell dator kanske effekten inte är signifikant om inte arbetsbelastningen kräver mycket exakt tidtagning. Men i de flesta fall har vi flera, sammankopplade virtuella datorer som använder tid för att spåra transaktioner och tiden måste vara konsekvent under hela distributionen. När tiden mellan virtuella datorer är annorlunda kan du se följande effekter:

- Autentiseringen misslyckas. Säkerhetsprotokoll som Kerberos eller certifikatberoende teknik är beroende av att tiden är konsekvent i alla system.
- Det är mycket svårt att räkna ut vad som har hänt i ett system om loggar (eller andra data) inte kommer överens om i tid. Samma händelse skulle se ut som det inträffade vid olika tidpunkter, vilket gör korrelation svårt.
- Om klockan är avstängd kan faktureringen beräknas felaktigt.



## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns vanligtvis tre sätt att konfigurera tidssynkronisering för dina virtuella Linux-datorer som finns i Azure:

- Standardkonfigurationen för Azure Marketplace-avbildningar använder både NTP-tid och VMICTimeSync-värdtid. 
- Endast värd värd med VMICTimeSync.
- Använd en annan extern tidsserver med eller utan att använda VMICTimeSync-värdtid.


### <a name="use-the-default"></a>Använd standardvärdet

Som standard är de flesta Azure Marketplace-avbildningar för Linux konfigurerade för synkronisering från två källor: 

- NTP som primär, vilket får tid från en NTP-server. Ubuntu 16.04 LTS Marketplace-avbildningar använder till exempel **ntp.ubuntu.com**.
- VMICTimeSync-tjänsten som sekundär, som används för att kommunicera värdtiden till de virtuella datorerna och göra korrigeringar efter att den virtuella datorn har pausats för underhåll. Azure-värdar använder Microsoft-ägda Stratum 1-enheter för att hålla korrekt tid.

I nyare Linux-distributioner använder TJÄNSTEN VMICTimeSync precisionstidsprotokollet (PTP), men tidigare distributioner kanske inte stöder PTP och kommer att falla tillbaka till NTP för att få tid från värden.

Om du vill bekräfta att NTP `ntpq -p` synkroniseras korrekt kör du kommandot.

### <a name="host-only"></a>Endast värd 

Eftersom NTP-servrar som time.windows.com och ntp.ubuntu.com är offentliga, kräver synkroniseringstiden med dem att skicka trafik via Internet. Olika paketfördröjningar kan påverka kvaliteten på tidssynkroniseringen negativt. Om du tar bort NTP genom att växla till synkronisering endast för värd kan ibland förbättra dina tidssynkroniseringsresultat.

Det är logiskt att växla till tidssynkronisering endast för värdprogram om du upplever problem med tidssynkronisering med standardkonfigurationen. Prova synkroniseringen endast värd för att se om det skulle förbättra tidssynkroniseringen på den virtuella datorn. 

### <a name="external-time-server"></a>Extern tidsserver

Om du har specifika krav på tidssynkronisering finns det också ett alternativ för att använda externa tidsservrar. Externa tidsservrar kan ge specifik tid, vilket kan vara användbart för testscenarier, vilket säkerställer tidsenhetlighet med datorer som finns i datacenter som inte kommer från Microsoft eller hanterar skottsekunder på ett speciellt sätt.

Du kan kombinera en extern tidsserver med TJÄNSTEN VMICTimeSync för att ge resultat som liknar standardkonfigurationen. Att kombinera en extern tidsserver med VMICTimeSync är det bästa alternativet för att hantera problem som kan vara orsaken när virtuella datorer pausas för underhåll. 

## <a name="tools-and-resources"></a>Verktyg och resurser

Det finns några grundläggande kommandon för att kontrollera din tidssynkroniseringskonfiguration. Dokumentation för Linux-distribution kommer att ha mer information om det bästa sättet att konfigurera tidssynkronisering för den distributionen.

### <a name="integration-services"></a>Integreringstjänster

Kontrollera om integrationstjänsten (hv_utils) har lästs in.

```bash
lsmod | grep hv_utils
```
Du bör se något liknande detta:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Se om Hyper-V-integrationstjänsternas demon körs.

```bash
ps -ef | grep hv
```

Du bör se något liknande detta:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Kontrollera ptp

Med nyare versioner av Linux är en PTP-klockkälla (Precision Time Protocol) tillgänglig som en del av VMICTimeSync-providern. På äldre versioner av Red Hat Enterprise Linux eller CentOS 7.x kan [Linux Integration Services](https://github.com/LIS/lis-next) laddas ner och användas för att installera den uppdaterade drivrutinen. När du använder PTP kommer Linux-enheten att vara av formuläret /dev/ptp*x*. 

Se vilka PTP-klockkällor som är tillgängliga.

```bash
ls /sys/class/ptp
```

I det här exemplet är värdet som returneras *ptp0*, så vi använder det för att kontrollera klocknamnet. Kontrollera om du vill verifiera enheten genom att kontrollera klocknamnet.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Detta bör returnera **hyperv**.

### <a name="chrony"></a>Chrony

På Ubuntu 19.10 och senare versioner, Red Hat Enterprise Linux och CentOS 7.x är [kronont](https://chrony.tuxfamily.org/) konfigurerad för att använda en PTP-källklocka. I stället för kronont använder äldre Linux-utgåvor daemon (ntpd), som inte stöder PTP-källor. För att ptp ska kunna aktiveras i dessa versioner måste kroninna installeras och konfigureras manuellt (i chrony.conf) med hjälp av följande kod:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Mer information om Ubuntu och NTP finns i [Tidssynkronisering](https://help.ubuntu.com/lts/serverguide/NTP.html).

Mer information om Red Hat och NTP finns i [Konfigurera NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Mer information om kronode finns i [Använda kronode](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Om både kroniment- och TimeSync-källor är aktiverade samtidigt kan du markera en som **föredrar**, som anger den andra källan som en säkerhetskopia. Eftersom NTP-tjänster inte uppdaterar klockan för stora skevningar utom efter en lång period återställer VMICTimeSync klockan från pausade VM-händelser mycket snabbare än NTP-baserade verktyg.

Som standard accelererar kronode eller saktar systemklockan för att åtgärda en tidsdrift. Om drift blir för stor, misslyckas kronont att fixa drift. För att övervinna `makestep` detta kan parametern i **/etc/chrony.conf** ändras för att tvinga fram en timesync om avdriften överskrider det angivna tröskelvärdet.

 ```bash
makestep 1.0 -1
```

Här kommer chrony tvinga en tidsuppdatering om drift är större än 1 sekund. Så här installerar du ändringarna om kronidstjänsten:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

På SUSE- och Ubuntu-versioner före 19.10 konfigureras tidssynkronisering med hjälp av [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Mer information om Ubuntu finns i [Tidssynkronisering](https://help.ubuntu.com/lts/serverguide/NTP.html). Mer information om SUSE finns i avsnitt 4.5.8 i [SUSE Linux Enterprise Server 12 SP3 Release Notes](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Exakt tid för Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).


