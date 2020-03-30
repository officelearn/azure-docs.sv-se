---
title: Så här använder du PerfInsights Linux i Microsoft Azure| Microsoft-dokument
description: Lär dig hur du använder PerfInsights för att felsöka prestandaproblem för Linux VM.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266993"
---
# <a name="how-to-use-perfinsights"></a>Så här använder du PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) är ett självhjälpsdiagnostikverktyg som samlar in och analyserar diagnostikdata och tillhandahåller en rapport som hjälper till att felsöka prestandaproblem på Virtuella Linux-datorer i Azure. PerfInsights kan köras på virtuella datorer som stöds som ett fristående verktyg, eller direkt från portalen med hjälp av [Prestandadiagnostik för virtuella Azure-datorer](performance-diagnostics.md).

Om du har prestandaproblem med virtuella datorer, innan du kontaktar supporten, kör du det här verktyget.

## <a name="supported-troubleshooting-scenarios"></a>Felsökningsscenarier som stöds

PerfInsights kan samla in och analysera flera typer av information. Följande avsnitt täcker vanliga scenarier.

### <a name="quick-performance-analysis"></a>Snabb prestandaanalys

Det här scenariot samlar in grundläggande information som lagring och maskinvarukonfiguration för din virtuella dator, olika loggar, inklusive:

- Information om operativsystem

- Information om PCI-enheter

- Allmänna loggar för gäst-OS

- Konfigurationsfiler

- Lagringsinformation

- Konfiguration av virtuell azure-dator (samlas in med [Azure Instance Metadata Service)](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

- Lista över processer som körs, Disk, Minne och CPU-användning

- Nätverksinformation

Detta är en passiv samling av information som inte bör påverka systemet.

>[!Note]
>Snabbprestandaanalysscenario inkluderas automatiskt i vart och ett av följande scenarier:

### <a name="performance-analysis"></a>Resultatanalys

Det här scenariot liknar snabbprestandaanalys men gör det möjligt att samla in diagnostikinformation under längre tid.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Vilken typ av information samlas in av PerfInsights

Information om virtuell Linux-dator, operativsystem, blockenheter, konsumenter med hög resurs, konfiguration och olika loggar samlas in. Här finns mer information:

- Operativsystem
  - Linux-distribution och version
  - Kernel-information
  - Information om drivrutiner

- Maskinvara
  - PCI-enheter`*`[ ]

- Processer och minne
  - Lista över processer (aktivitetsnamn, minne som används, filer öppnade)
  - Totalt, tillgängligt och ledigt fysiskt minne
  - Totalt, tillgängligt och ledigt växlingsminne
  - Profilering av CPU och bearbetar CPU-användning med 5-sekundersintervall
  - Profilering av processer minnesanvändning med 5-sekundersintervall

- Nätverk  
  - Lista över nätverkskort med kortstatistik
  - Tabell över nätverksroutning
  - Öppnade portar och status

- Lagring
  - Lista över spärra enheter
  - Lista över partitioner
  - Lista över monteringspunkter
  - MDADM-volyminformation
  - LVM volyminformation
  - Profileringsinspelning på alla diskar med 5 sekunders intervall

- Loggar
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[extension folder]/\*log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Utdata för journalkort för de senaste fem dagarna

- [Metadata för Azure-instans för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] PCI-information samlas ännu inte in på Debians och SLES-distributioner

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Kör PerfInsights Linux på din virtuella dator

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Vad måste jag veta innan jag kör verktyget

#### <a name="tool-requirements"></a>Verktygskrav

- Det här verktyget måste köras på den virtuella datorn som har prestandaproblemet.
- Python 2.7 måste installeras på den virtuella datorn

- Följande distributioner stöds för närvarande:

    | Distribution               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux-server        | 6.10`*`[ ], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 marketplace bild)|
    | CentOS                     | 6.5`*`[ ], 7.6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4`*`[ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Se [avsnittet Kända problem](#known-issues)

### <a name="known-issues"></a>Kända problem

- RHEL 8 har inte Python installerat som standard. Om du vill köra PerfInsights Linux måste du först installera Python 2.7

- Insamling av gästagentinformation kan misslyckas på CentOS 6.x

- PCI-enheter samlas inte in på Debianbaserade distributioner

- LVM-information samlas delvis in på vissa distributioner

### <a name="how-do-i-run-perfinsights"></a>Hur kör jag PerfInsights

Du kan köra PerfInsights på en virtuell dator genom att installera Azure Performance Diagnostics från Azure Portal. Du kan också köra den som ett fristående verktyg.

>[!Note]
>PerfInsights samlar helt enkelt in och analyserar data. Det gör inga ändringar i systemet.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Installera och köra PerfInsights från Azure-portalen

Mer information om det här alternativet finns i [Azure Performance Diagnostics](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Kör PerfInsights i fristående läge

Så här kör du perfInsights-verktyget:

1. Ladda ner [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) till en mapp på din virtuella dator och extrahera innehållet med hjälp av nedanstående kommandon från terminalen.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navigera till mappen som `perfinsights.py` innehåller filen `perfinsights.py` och kör sedan för att visa tillgängliga kommandoradsparametrar.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Skärmbild av PerfInsights Linux-kommandoradsutdata](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    Den grundläggande syntaxen för att köra PerfInsights-scenarier är:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Du kan använda exemplet nedan för att köra quick performance analysis scenario i 1 minut och skapa resultaten under /tmp/output-mappen:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Du kan använda exemplet nedan för att köra prestandaanalysscenario i 5 minuter och ladda upp resultattjärbollen till lagringskontot:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Innan du kör ett scenario uppmanar PerfInsights användaren att gå med på att dela diagnostisk information och godkänna licensavtalet. Använd **alternativet -a eller --accept-disclaimer-and-share-diagnostics** för att hoppa över dessa uppmaningar.
    >
    >Om du har en aktiv supportbiljett med Microsoft och kör PerfInsights enligt begäran från supportteknikern du arbetar med, se till att ange supportbiljettnumret med alternativet **-s eller --support-request.**

När körningen är klar visas en ny tjärafil i samma mapp som PerfInsights om inte ingen utdatamapp har angetts. Namnet på filen är **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz.** Du kan skicka den här filen till supportagenten för analys eller öppna rapporten i filen för att granska resultat och rekommendationer.

## <a name="review-the-diagnostics-report"></a>Granska diagnostikrapporten

I **filen PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** hittar du en HTML-rapport som beskriver resultaten av PerfInsights. Om du vill granska rapporten expanderar du filen **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz** och öppnar sedan filen **PerfInsights Report.html.**

### <a name="overview-tab"></a>Fliken Översikt

Fliken **Översikt** innehåller grundläggande körinformation och information om virtuella datorer. På fliken **Resultat** visas en sammanfattning av rekommendationerna från alla de olika avsnitten i PerfInsights-rapporten.

![Skärmbild av PerfInsights-rapporten](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Skärmbild av PerfInsights-rapporten](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Resultat som kategoriseras som höga är kända problem som kan orsaka prestandaproblem. Resultat som kategoriseras som medium representerar icke-optimala konfigurationer som inte nödvändigtvis orsakar prestandaproblem. Resultat kategoriseras som låg är informativa uttalanden bara.

Granska rekommendationerna och länkarna för alla höga och medelstora resultat. Läs mer om hur de kan påverka prestanda och även om metodtips för prestandaoptimerade konfigurationer.

### <a name="cpu-tab"></a>Fliken CPU

**Fliken CPU** innehåller information om systemomfattande CPU-förbrukning under PerfInsights-körningen. Information om hög CPU-användningsperioder och topp långvariga CPU-konsumenter kommer att vara till hjälp för att felsöka höga CPU-relaterade problem.

![Skärmbild av fliken PerfInsights Report CPU](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Fliken Lagring

I avsnittet **Resultat** visas olika resultat och rekommendationer relaterade till lagring.

**På flikarna Blockera enheter** och andra relaterade avsnitt, till exempel **partitioner,** **LVM**och **MDADM-flikar,** beskrivs hur blockenheter konfigureras och relateras till varandra.

![Skärmbild av fliken Lagring](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Skärmbild av fliken MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Fliken Linux

**Fliken Linux** innehåller information om maskinvaran och operativsystemet som körs i den virtuella datorn. Information inkluderar en lista över processer som körs och information om gästagent-, PCI-, CPU-, drivrutiner och LIS-drivrutiner.

![Skärmbild av fliken Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Nästa steg

Du kan ladda upp diagnostikloggar och rapporter till Microsoft Support för vidare granskning. När du arbetar med Microsoft Support-personalen kan de begära att du överför utdata som genereras av PerfInsights för att hjälpa till med felsökningsprocessen.

Följande skärmbild visar ett meddelande som liknar det du kan få:

![Skärmbild av exempelmeddelande från Microsoft Support](media/how-to-use-perfinsights-linux/support-email.png)

Följ instruktionerna i meddelandet för att komma åt filöverföringsarbetsytan. För ytterligare säkerhet måste du ändra ditt lösenord vid första användningen.

När du har loggat in hittar du en dialogruta för att ladda upp **filen PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** som samlades in av PerfInsights.
