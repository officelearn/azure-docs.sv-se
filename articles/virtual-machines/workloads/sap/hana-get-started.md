---
title: Installation av SAP HANA på virtuella Azure-datorer | Microsoft Dokument"
description: Guide för installation av SAP HANA på virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123354"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Installation av SAP HANA på virtuella Azure-datorer
## <a name="introduction"></a>Introduktion
Den här guiden hjälper dig att peka på rätt resurser för att distribuera HANA i virtuella Azure-datorer. Den här guiden kommer att peka dig på dokumentationsresurser som du behöver kontrollera innan du installerar SAP HANA i en Azure VM. Så att du kan utföra rätt steg för att sluta med en konfiguration som stöds av SAP HANA i Azure virtuella datorer.  

> [!NOTE]
> Den här guiden beskriver distributioner av SAP HANA till virtuella Azure-datorer. Information om hur du distribuerar SAP HANA i stora HANA-instanser finns i [Installera och konfigurera SAP HANA (stora instanser) på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation).
 
## <a name="prerequisites"></a>Krav
Den här guiden förutsätter också att du är bekant med:
* SAP HANA och SAP NetWeaver och hur du installerar dem lokalt.
* Så här installerar och använder du SAP HANA- och SAP-programinstanser på Azure.
* De begrepp och förfaranden som dokumenteras i:
   * Planering för SAP-distribution på Azure, som inkluderar Azure Virtual Network planering och Azure Storage-användning. Se [SAP NetWeaver på Virtuella Azure-datorer – planerings- och implementeringsguide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Distributionsprinciper och sätt att distribuera virtuella datorer i Azure. Se [Azure Virtual Machines-distribution för SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Hög tillgänglighetsbegrepp för SAP HANA som dokumenterats i [SAP HANA hög tillgänglighet för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Steg för steg före distribution
I det här avsnittet visas de olika stegen som du måste utföra innan du börjar med installationen av SAP HANA i en virtuell Azure-dator. Ordern räknas upp och bör därför följas upp som uppräknad:

1. Alla möjliga distributionsscenarier stöds inte på Azure. Därför bör du kontrollera dokument [SAP-arbetsbelastningen på Azure-virtuella datorn som stöds scenarier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) för det scenario du har i åtanke med din SAP HANA-distribution. Om scenariot inte finns med i listan måste du anta att det inte har testats och därför inte stöds
2. Förutsatt att du har en ungefärlig uppfattning om ditt minne krav för din SAP HANA-distribution, måste du hitta en passande Azure VM. Inte alla virtuella datorer som är certifierade för SAP NetWeaver, som dokumenteras i [SAP-stödanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533), är SAP HANA-certifierade. Sanningens källa för SAP HANA-certifierade virtuella Azure-datorer är webbplatsen [SAP HANA-maskinvarukatalogen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). De enheter som börjar med **S** är [HANA Stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) enheter och inte Azure virtuella datorer.
3. Olika Azure VM-typer har olika minsta operativsystemversioner för SUSE Linux eller Red Hat Linux. På webbplatsen [SAP HANA hårdvarukatalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)måste du klicka på en post i listan över SAP HANA-certifierade enheter för att få detaljerade data om denna enhet. Förutom den HANA-arbetsbelastning som stöds visas os-utgåvorna som stöds med dessa enheter för SAP HANA
4. Från och med operativsystemversioner måste du överväga vissa minsta kärnversioner. Dessa minimiversioner dokumenteras i dessa SAP-supportanteckningar:
    - [SAP-supportanteckning #2814271 SAP HANA Backup misslyckas på Azure med Checksum-fel](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP-stödanteckning #2753418 potentiell prestandaförsämring på grund av timerfallback](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP-supportanteckning #2791572 prestandaförsämring på grund av att VDSO-stöd saknas för Hyper-V i Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Baserat på os-versionen som stöds för den virtuella datorn typ av val, måste du kontrollera om önskad SAP HANA release stöds med det operativsystemet release. Läs [SAP-supportanteckning #2235581](https://launchpad.support.sap.com/#/notes/2235581) för en supportmatris av SAP HANA-versioner med de olika operativsystemversionerna.
5. Eftersom du kanske har hittat en giltig kombination av Azure VM-typ, operativsystemversion och SAP HANA-utgåva måste du checka in SAP-produkttillgänglighetsmatrisen. I SAP-tillgänglighetsmatrisen kan du ta reda på om den SAP-produkt som du vill köra mot DIN SAP HANA-databas stöds.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Steg-för-steg VM-distribution och gäst-OS överväganden
I den här fasen måste du gå igenom stegen som distribuerar den virtuella datorn för att installera HANA och så småningom optimera det valda operativsystemet efter installationen.

1. Välj basavbildningen från Azure-galleriet. Om du vill bygga din egen operativsystemavbildning för SAP HANA måste du känna till alla de olika paket som krävs för en lyckad SAP HANA-installation. Annars rekommenderas att använda SUSE- och Red Hat-avbildningarna för SAP eller SAP HANA från Azure-avbildningsgalleriet. Dessa avbildningar innehåller de paket som krävs för en lyckad HANA-installation. Baserat på ditt supportavtal med operativsystemets leverantör måste du välja en bild där du tar med din egen licens. Eller så väljer du en OS-avbildning som innehåller stöd
2. Om du väljer en gäst OS-avbildning som kräver att du tar med din egen licens, måste du registrera OS-avbildningen med din prenumeration, så att du kan ladda ner och tillämpa de senaste patcharna. Detta steg kommer att kräva offentlig tillgång till Internet. Om du inte ställer in din privata instans av till exempel en SMT-server i Azure.
3. Bestäm nätverkskonfigurationen för den virtuella datorn. Du kan läsa mer information i dokumentet [SAP HANA infrastrukturkonfigurationer och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Tänk på att det inte finns några nätverksdataflödeskvoter som du kan tilldela virtuella nätverkskort i Azure. Därför är det enda syftet med att styra trafiken genom olika virtuella nationella och regionala råd baserat på säkerhetshänsyn. Vi litar på att du hittar en supportabel kompromiss mellan komplexiteten i trafikroutning genom flera virtuella NATIONELLA nätverk och de krav som upprätthålls av säkerhetsaspekter.
3. Använd de senaste korrigeringsfilerna på operativsystemet när den virtuella datorn har distribuerats och registrerats. Registrerad antingen med din egen prenumeration. Eller om du redan väljer en avbildning som innehåller operativsystemstöd bör den virtuella datorn redan ha åtkomst till korrigeringsfilerna. 
4. Använd de låtar som behövs för SAP HANA. Dessa låtar visas i dessa SAP-supportanteckningar:

    - [SAP-supportanteckning #2694118 - Red Hat Enterprise Linux HA-tillägg på Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP-supportanteckning #1984787 - SUSE LINUX Enterprise Server 12: Installationsanteckningar](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP-supportanteckning #2578899 - SUSE Linux Enterprise Server 15: Installationsanteckning](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP-supportanteckning #2002167 - Red Hat Enterprise Linux 7.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP-supportanteckning #2292690 - SAP HANA DB: Rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP-supportanteckning #2772999 - Red Hat Enterprise Linux 8.x: Installation och konfiguration](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP-supportanteckning #2777782 - SAP HANA DB: Rekommenderade OS-inställningar för RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP-stödanteckning #2455582 - Linux: Köra SAP-program som sammanställts med GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP-supportanteckning #2382421 - Optimera nätverkskonfigurationen på HANA- och OS-nivå](https://launchpad.support.sap.com/#/notes/2382421)

1. Välj Azure-lagringstyp för SAP HANA. I det här steget måste du bestämma lagringslayout för SAP HANA-installation. Du kommer att använda antingen anslutna Azure-diskar eller inbyggda Azure NFS-resurser. De Azure-lagringstyper som stöds och kombinationer av olika Azure-lagringstyper som kan användas dokumenteras i [SAP HANA Azure-konfigurationer för virtuell datorlagring](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Ta de konfigurationer som dokumenterats som utgångspunkt. För icke-produktionssystem kanske du kan konfigurera lägre dataflöde eller IOPS. I produktionssyfte kan du behöva konfigurera lite mer dataflöde och IOPS.
2. Se till att du har konfigurerat [Azure Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) för dina volymer som innehåller DBMS-transaktionsloggarna eller gör om loggar när du använder virtuella datorer i M-serien eller Mv2-serien. Var medveten om begränsningarna för Write Accelerator som dokumenterats.
2. Kontrollera om [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat på den eller de vm(er) som distribueras.

> [!NOTE]
> Alla kommandon i de olika sap-tune-profilerna eller som beskrivs i anteckningarna kan inte köras på Azure. Kommandon som skulle manipulera energiläget för virtuella datorer returneras vanligtvis med ett fel eftersom energiläget för den underliggande Azure-värdmaskinvaran inte kan manipuleras.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Steg-för-steg-förberedelser som är specifika för virtuella Azure-datorer
En av Azure-detaljerna är installationen av ett Azure VM-tillägg som levererar övervakningsdata för SAP-värdagenten. Information om installationen av denna övervakning förlängning dokumenteras i:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) diskuterar FÖRBÄTTRAD SAP-övervakning med virtuella Linux-datorer på Azure 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) diskuterar information om SAPOSCOL på Linux 
-  [I SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) beskrivs viktiga övervakningsmått för SAP på Microsoft Azure
-  [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA-installation
Med Azure virtuella datorer distribueras och operativsystem registreras och konfigureras, kan du installera SAP HANA enligt SAP installera. Som en bra start för att komma till denna dokumentation, börja med denna SAP webbplats [HANA resurser](https://www.sap.com/products/hana/implementation/resources.html)

För SAP HANA-utskalningskonfigurationer med direktanslutna diskar med Azure Premium Storage eller Ultra-disk läser du detaljerna i [dokumentets SAP HANA-infrastrukturkonfigurationer och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Ytterligare resurser för SAP HANA-säkerhetskopiering
Information om hur du säkerhetskopierar SAP HANA-databaser på virtuella Azure-datorer finns i:
* [Reservguide för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Nästa steg
Läs dokumentationen:

- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





