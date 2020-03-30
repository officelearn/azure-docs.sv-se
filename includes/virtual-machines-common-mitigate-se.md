---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73935891"
---
**Senaste dokumentuppdatering:** 12 november 2019 10:00 PST.

Avslöjandet av en [ny klass av CPU-sårbarheter](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) som kallas spekulativa körning sidokanal attacker har resulterat i frågor från kunder som söker mer klarhet.  

Microsoft har distribuerat åtgärder för alla våra molntjänster. Infrastrukturen som kör Azure och isolerar kundarbetsbelastningar från varandra är skyddad. Det innebär att en potentiell angripare som använder samma infrastruktur inte kan angripa ditt program med hjälp av dessa säkerhetsproblem.

Azure använder [minnesbevarande underhåll](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) när det är möjligt, för att minimera kundernas påverkan och eliminera behovet av omstarter. Azure fortsätter att använda dessa metoder när du gör systemomfattande uppdateringar till värden och skyddar våra kunder.

Mer information om hur säkerhet är integrerat i alla aspekter av Azure finns på webbplatsen [för Azure Security Documentation.](https://docs.microsoft.com/azure/security/) 

> [!NOTE] 
> Sedan det här dokumentet först publicerades har flera varianter av den här sårbarhetsklassen avslöjats. Microsoft fortsätter att vara starkt investerade i att skydda våra kunder och ge vägledning. Den här sidan kommer att uppdateras när vi fortsätter att släppa ytterligare korrigeringar. 
> 
> Den 12 november 2019 [publicerade Intel](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) en teknisk rådgivning kring Intel® Transactional Synchronization Extensions (Intel® TSX) Transaction Asynchronous Abort (TAA) sårbarhet som tilldelas [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135). Det här säkerhetsproblemet påverkar Intel® Core®-processorer och Intel® Xeon®-processorer.  Microsoft Azure har släppt uppdateringar av operativsystemet och distribuerar ny mikrokod, eftersom den görs tillgänglig av Intel, i hela vår flotta för att skydda våra kunder mot dessa nya sårbarheter.   Azure arbetar nära med Intel för att testa och validera den nya mikrokoden innan den officiella versionen på plattformen. 
>
> **Kunder som kör otrodd kod i sin virtuella dator** måste vidta åtgärder för att skydda mot dessa sårbarheter genom att läsa nedan för ytterligare vägledning om alla spekulativa körning sidokanal sårbarheter (Microsoft Advisories ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)och [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Andra kunder bör utvärdera dessa sårbarheter ur ett försvarsingående perspektiv och överväga säkerhets- och prestandakonsekvenserna av deras valda konfiguration.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Hålla operativsystemen uppdaterade

Även om en OS-uppdatering inte krävs för att isolera dina program som körs på Azure från andra Azure-kunder, är det alltid en bra idé att hålla din programvara uppdaterad. De senaste säkerhetssammanslagningar för Windows innehåller mildrande åtgärder för flera spekulativa körningskanals sårbarheter. På samma sätt har Linux-distributioner släppt flera uppdateringar för att åtgärda dessa sårbarheter. Här är våra rekommenderade åtgärder för att uppdatera ditt operativsystem:

| Erbjudande | Rekommenderad åtgärd  |
|----------|---------------------|
| Azure Cloud Services  | Aktivera [automatisk uppdatering](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) eller se till att du kör det senaste gästoperativsystemet. |
| Virtuella Azure Linux-datorer | Installera uppdateringar från operativsystemets leverantör. Mer information finns i [Linux](#linux) senare i det här dokumentet. |
| Virtuella Azure Windows-datorer  | Installera den senaste samlade säkerhetskontrollen.
| Andra Azure PaaS-tjänster | Det behövs ingen åtgärd för kunder som använder dessa tjänster. Azure håller automatiskt dina OS-versioner uppdaterade. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Ytterligare vägledning om du kör kod som inte är betrodd 

Kunder som tillåter betrodda användare att köra godtycklig kod kanske vill implementera vissa ytterligare säkerhetsfunktioner i sina Virtuella Azure-datorer eller molntjänster. Dessa funktioner skyddar mot de informationsvektorer inom processen som flera spekulativa körningssårbarheter beskriver.

Exempel på scenarier där ytterligare säkerhetsfunktioner rekommenderas:

- Du tillåter kod som du inte litar på för att köra inuti den virtuella datorn.  
    - *Du kan till exempel tillåta att en av dina kunder laddar upp en binär eller ett skript som du sedan kör i ditt program*. 
- Du tillåter användare som du inte litar på att logga in på den virtuella datorn med lågbehörighetskonton.   
    - *Du kan till exempel tillåta en användare med låg behörighet att logga in på en av dina virtuella datorer med fjärrskrivbord eller SSH*.  
- Du tillåter ej betrodda användare åtkomst till virtuella datorer som implementeras via kapslad virtualisering.  
    - *Du kan till exempel styra Hyper-V-värden, men allokera de virtuella datorerna till användare som inte är betrodda*. 

Kunder som inte implementerar ett scenario som involverar kod som inte är betrodd behöver inte aktivera dessa ytterligare säkerhetsfunktioner. 

## <a name="enabling-additional-security"></a>Aktivera ytterligare säkerhet 

Du kan aktivera ytterligare säkerhetsfunktioner i din virtuella dator eller molntjänst om du kör kod som inte är betrodd. Samtidigt bör du se till att operativsystemet är uppdaterat för att aktivera säkerhetsfunktioner i din virtuella dator eller molntjänst

### <a name="windows"></a>Windows 

Ditt måloperativsystem måste vara uppdaterat för att dessa ytterligare säkerhetsfunktioner ska kunna aktiveras. Många spekulativa körningskanalbegränsningar är aktiverade som standard, men de ytterligare funktioner som beskrivs här måste aktiveras manuellt och kan orsaka prestandapåverkan. 


**Steg 1: Inaktivera hypertrådning på den virtuella datorn** - Kunder som kör ej betrodd kod på en virtuell dator med hypertrådar måste inaktivera hypertrådning eller flytta till en vm-storlek som inte är hypertrådad. Referera [till det här dokumentet](https://docs.microsoft.com/azure/virtual-machines/windows/acu) för en lista över storlekar med flera trådar för virtuella datorer (där förhållandet mellan vCPU och Core är 2:1). Om du vill kontrollera om den virtuella datorn har aktiverat hypertrådning läser du skriptet nedan med kommandoraden Windows inifrån den virtuella datorn.

Skriv `wmic` för att komma in i det interaktiva gränssnittet. Skriv sedan nedan för att visa mängden fysiska och logiska processorer på den virtuella datorn.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Om antalet logiska processorer är större än fysiska processorer (kärnor) aktiveras hypertrådning.  Om du kör en virtuell dator med flera trådar kontaktar du [Azure Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) för att inaktivera hypertrådning.  När hyper-threading är inaktiverad, **stöd kräver en fullständig VM omstart**. Se [Core antal](#core-count) för att förstå varför din VM core antal minskade.


**Steg 2:** Parallellt med steg 1 följer du instruktionerna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) för att kontrollera att skydd är aktiverade med modulen [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell.

> [!NOTE]
> Om du tidigare har hämtat den här modulen måste du installera den senaste versionen.
>


Utdata från PowerShell-skriptet bör ha nedanstående värden för att validera aktiverat skydd mot dessa säkerhetsproblem:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Om utdata `MDS mitigation is enabled: False`visar kontaktar du [Azure Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) för tillgängliga begränsningsalternativ.



**Steg 3:** Så här aktiverar du stöd för kernel virtual address shadowing (KVAS) och BTI-stöd (Branch Target Injection) och bti-stöd (Branch Target Injection) och följer instruktionerna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) för att aktivera skydd med `Session Manager` registernycklarna. En omstart krävs.


**Steg 4:** För distributioner som endast använder [kapslad virtualisering](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (endast D3 och E3): Dessa instruktioner gäller inuti den virtuella datorn som du använder som Hyper-V-värd.

1.  Följ instruktionerna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) för att `MinVmVersionForCpuBasedMitigations` aktivera skydd med registernycklarna.
2.  Ställ in hypervisor scheduler-typen på genom att `Core` följa instruktionerna [här](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>För att aktivera uppsättningen ytterligare säkerhetsfunktioner inuti kräver att måloperativsystemet är helt uppdaterat. Vissa åtgärder aktiveras som standard. I följande avsnitt beskrivs de funktioner som är inaktiverade som standard och/eller beroende av maskinvarustöd (mikrokod). Om du aktiverar dessa funktioner kan prestanda påverkas. Referera till operativsystemleverantörens dokumentation för ytterligare instruktioner


**Steg 1: Inaktivera hypertrådning på den virtuella datorn** - Kunder som kör ej betrodd kod på en virtuell dator med hypertrådar måste inaktivera hypertrådning eller flytta till en vm som inte är hypertrådad.  Referera [till det här dokumentet](https://docs.microsoft.com/azure/virtual-machines/linux/acu) för en lista över storlekar med flera trådar för virtuella datorer (där förhållandet mellan vCPU och Core är 2:1). Om du vill kontrollera om du kör en `lscpu` virtuell dator med hypertråd kör du kommandot i Linux-virtuella datorn. 

Om `Thread(s) per core = 2`har hypertrådning aktiverats. 

Om `Thread(s) per core = 1`, då hyper-threading har inaktiverats. 

 
Exempelutdata för en virtuell dator med hypertrådning aktiverat: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Om du kör en virtuell dator med flera trådar kontaktar du [Azure Support](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) för att inaktivera hypertrådning.  När hyper-threading är inaktiverad, **stöd kräver en fullständig VM omstart**. Se [Core antal](#core-count) för att förstå varför din VM core antal minskade.



**Steg 2**: Information om hur du kan minska mot någon av de underdragande sårbarheterna i den spekulativa körningssidan och kanal:   
 
- [Redhat och CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Antal kärnor

När en virtuell dator med hypertråd skapas allokerar Azure två trådar per kärna – dessa kallas vCPUs. När hypertrådning är inaktiverat tar Azure bort en tråd och ytor upp enstaka trådar (fysiska kärnor). Förhållandet mellan vCPU och CPU är 2:1, så när hyper-threading är inaktiverad, cpu-antalet i den virtuella datorn verkar ha minskat med hälften. En D8_v3-vm är till exempel en virtuell dator med hypertråd som körs på 8 virtuella processorer (2 trådar per kärnkärnor x 4 kärnor).  När hypertrådning är inaktiverad kommer processorer att sjunka till 4 fysiska kärnor med 1 tråd per kärna. 

## <a name="next-steps"></a>Nästa steg

Den här artikeln ger vägledning till nedanstående spekulativa körningsattacker i sidokanal som påverkar många moderna processorer:

[Spectre Härdsmälta:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)
- CVE-2017-5715 - Injektion av branchmål (BTI)  
- CVE-2017-5754 - Isolering av tabelltabeller för kärnsida (KPTI)
- CVE-2018-3639 – Spekulativ butik bypass (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows Kernel Information – variant av Spectre Variant 1
 
[L1 Terminal Fel (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Intel Software Guard Extensions (Intel SGX)
- CVE-2018-3620 - Operativsystem (OS) och systemhanteringsläge (SMM)
- CVE-2018-3646 – påverkar Virtual Machine Manager (VMM)

[Mikroarkektiv datasampling:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013) 
- CVE-2019-11091 - Mikroarkektiv dataprovtagning uncacheable minne (MDSUM)
- CVE-2018-12126 - Mikroarkektiv lagringsbuffertdatasprovtagning (MSBDS)
- CVE-2018-12127 - Mikroarkektiv belastningsport Dataprovtagning (MLPDS)
- CVE-2018-12130 - Mikroarkektiv fyllningsbuffertdatas provtagning (MFBDS)

Transaktionssynkronakro®n abort:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX Transaktion Asynkron Abort (TAA)








