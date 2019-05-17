---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: ba41f6cce5233491020a0b42f4fd40dac060be57
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65815544"
---
**Senast dokumentera uppdatering**: 14 maj 2019 10:00 FM PST.

Utlämnande av en [ny klass av processorsäkerhetsproblem](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) kallas spekulativ körning sidokanal attacker har lett till frågor från kunder som vill ha mer tydligare.  

Microsoft har distribuerat åtgärder i alla våra molntjänster. Den infrastruktur som kör Azure och isolerar slutkundsarbetsbelastningar från varandra är skyddad. Det innebär att potentiella angripare med hjälp av samma infrastruktur inte kan attacker ditt program med dessa problem.

Med hjälp av Azure [minne bevarande Underhåll](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) när det är möjligt att minimera påverkan för kunden och eliminera behovet av omstarter. Azure kommer fortsätta utnyttja dessa metoder när du gör systemomfattande uppdateringar till värden och skydda våra kunder.

Mer information om hur säkerheten är integrerad i alla aspekter av Azure är tillgängligt på den [dokumentation om Azure Security](https://docs.microsoft.com/azure/security/) plats. 

> [!NOTE] 
> Eftersom det här dokumentet publicerades först, har flera varianter av den här klassen säkerhetsproblem lämnats. Microsoft fortsätter att vara mycket har investerat i skydda våra kunder och ge vägledning. Den här sidan kommer att uppdateras när vi fortsätter att släppa ytterligare korrigeringar. 
> 
> Den 14 maj 2019 [Intel uppges](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) en ny uppsättning spekulativ körning sida kanal säkerhetsproblem kallas mikroarkitekturstruktur Datasampling (MDS finns i Microsofts säkerhetsvägledning [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), som har tilldelats flera CVEs: 
> - CVE-2019-11091 - mikroarkitekturstruktur Data Sampling Uncacheable minne (MDSUM)
> - CVE-2018-12126 - mikroarkitekturstruktur Store bufferten Data Sampling (MSBDS) 
> - CVE-2018-12127 - mikroarkitekturstruktur belastningen Port Data Sampling (MLPDS)
> - CVE-2018-12130 - mikroarkitekturstruktur fyllning bufferten Data Sampling (MFBDS)
>
> Denna säkerhetsrisk påverkar Intel® Core®-processorer och Intel® Xeon®-processorer.  Microsoft Azure har släppt uppdateringar av operativsystemet och är att distribuera nya microcode som det tillgängliggörs av Intel i hela vår flotta att skydda våra kunder mot dessa nya problem.   Azure fungerar nära samarbete med Intel att testa och validera den nya microcode innan dess officiella versionen på plattformen. 
>
> **Kunder som använder inte är betrodd kod i sina Virtuella** måste du vidta åtgärder för att skydda mot dessa problem genom att läsa nedan för mer information om alla spekulativ körning sidokanal sårbarheter (Microsoft rådgivning ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), och [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Andra kunder bör utvärdera dessa problem från skydd på djupet perspektiv och överväga konsekvenserna för säkerhet och prestanda för den valda konfigurationen.



## <a name="keeping-your-operating-systems-up-to-date"></a>Uppdatera ditt operativsystem kontinuerligt

När en uppdatering av operativsystem inte krävs för att isolera dina program som körs på Azure från andra Azure-kunder, men det är alltid en bra idé att hålla programvaran uppdaterad. Den senaste Security uppdateringar för Windows innehåller åtgärder för flera spekulativ körning sida kanal säkerhetsproblem. På samma sätt kan har Linux-distributioner släppt flera uppdateringar för att åtgärda dessa sårbarheter. Här är våra rekommenderade åtgärder för att uppdatera operativsystemet:

| Erbjudande | Rekommenderad åtgärd  |
|----------|---------------------|
| Azure Cloud Services  | Aktivera [uppdateras automatiskt](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) eller se till att du kör den senaste gäst-OS. |
| Virtuella Azure Linux-datorer | Installera uppdateringar från leverantören av operativsystemet. Mer information finns i [Linux](#linux) senare i det här dokumentet. |
| Azure Windows-datorer  | Installera den senaste insamling av säkerhet.
| Andra Azure PaaS-tjänster | Det finns ingen åtgärd krävs för kunder som använder de här tjänsterna. Azure behåller automatiskt OS-versioner uppdaterad. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Mer information om du använder en icke betrodd kod 

Kunder som tillåter att opålitliga användare att köra godtycklig kod kanske vill implementera vissa ytterligare säkerhetsfunktioner i sina Azure-datorer eller molntjänster. De här funktionerna Skydda mot avslöjande av trafik bearbeta-vektorer som beskriver flera spekulativ körning säkerhetsproblem.

Scenarier där ytterligare säkerhetsfunktioner rekommenderas:

- Du kan kod som du inte litar för att köra inuti den virtuella datorn.  
    - *Exempelvis kan du tillåta att en av dina kunder för att ladda upp en binär eller skript som du sedan kör i ditt program*. 
- Du kan användare som du inte litar för att logga in till den virtuella datorn med låg Privilegierade konton.   
    - *Exempelvis kan du tillåta att en lågprivilegierad användare att logga in på en av dina virtuella datorer med hjälp av fjärrskrivbord eller SSH*.  
- Tillåt ej betrodda användare åtkomst till virtuella datorer som implementeras via kapslad virtualisering.  
    - *Exempelvis kan du styra Hyper-V-värden, men allokera de virtuella datorerna till opålitliga användare*. 

Kunder som inte implementerar ett scenario som rör icke betrodd kod behöver inte aktivera dessa ytterligare säkerhetsfunktioner. 

## <a name="enabling-additional-security"></a>Aktivera ytterligare säkerhet 

Du kan aktivera ytterligare säkerhetsfunktioner i din virtuella dator eller molntjänst om du använder en icke betrodd kod. Parallellt, se till att operativsystemet är uppdaterad för att aktivera säkerhetsfunktionerna i din virtuella dator eller molntjänst

### <a name="windows"></a>Windows 

Måloperativsystemets måste vara uppdaterade för att aktivera de här ytterligare säkerhetsfunktioner. Även om ett stort antal spekulativ körning sida kanal åtgärder är aktiverade som standard, ytterligare funktioner som beskrivs här måste vara aktiverat manuellt och kan orsaka en prestandapåverkan. 


**Steg 1: Inaktivera hypertrådning på den virtuella datorn** - kunder som kör icke betrodd kod på en hyperthreaded VM kommer att behöva inaktivera hypertrådning eller flytta till en icke-hyperthreaded VM-storlek. Om du vill kontrollera om den virtuella datorn har hypertrådning aktiverat, finns det med hjälp av Windows-kommandoraden från den virtuella datorn-skriptet nedan.

Typ `wmic` att ange det interaktiva gränssnittet. Skriv sedan nedan för att visa mängden fysiska och logiska processorer på den virtuella datorn.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Om antalet logiska processorer är större än fysiska processorer (kärnor), är hypertrådning aktiverat.  Om du kör en hyperthreaded VM [kontakta Azure-supporten](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) att hämta hypertrådning inaktiverat.  När hypertrådning är inaktiverat **supporten behöver en fullständig VM-omstart**. 


**Steg 2**: Parallellt till steg 1, följer du anvisningarna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) att verifiera skydd aktiveras med hjälp av den [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell-modulen.

> [!NOTE]
> Om du tidigare har hämtat den här modulen kommer du behöva installera den senaste versionen.
>


Utdata från PowerShell-skriptet ska ha den under värden att validera aktiverat skydd mot dessa problem:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Om utdata visar `MDS mitigation is enabled: False`så kan du [kontakta Azure-supporten](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) för minskning av tillgängliga alternativ.



**Steg 3**: Om du vill aktivera Kernel virtuella adress skuggning (KVAS) och grenen Target inmatning (BTI) OS-support, följer du anvisningarna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) att aktivera skydd med hjälp av den `Session Manager` registernycklar. En omstart krävs.


**Steg 4**: För distributioner som använder [kapslad virtualisering](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 och endast E3): Dessa anvisningar gäller inuti den virtuella datorn som du använder som en Hyper-V-värd.

1.  Följ instruktionerna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) att aktivera skydd med hjälp av den `MinVmVersionForCpuBasedMitigations` registernycklar.
2.  Ange vilket hypervisor scheduler `Core` genom att följa anvisningarna [här](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Aktivera ytterligare säkerhetsfunktioner i uppsättningen kräver att måloperativsystemet är fullständigt uppdaterad. Vissa åtgärder kommer att aktiveras som standard. I följande avsnitt beskrivs de funktioner som är inaktiverade som standard och/eller beroende maskinvarustöd (microcode). Aktivera dessa funktioner kan det orsaka en prestandapåverkan. Referera till ditt operativsystem leverantörens dokumentation för ytterligare instruktioner


**Steg 1: Inaktivera hypertrådning på den virtuella datorn** - kunder som kör icke betrodd kod på en hyperthreaded virtuell dator måste du inaktivera hypertrådning eller flyttar till en icke-hyperthreaded virtuell dator.  Kontrollera om du kör en hyperthreaded VM, köra den `lscpu` i Linux VM. 

Om `Thread(s) per core = 2`, och sedan hypertrådning har aktiverats. 

Om `Thread(s) per core = 1`, och sedan hypertrådning har inaktiverats. 

 
Exempel som utdata för en virtuell dator med hypertrådar aktiverade: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0,2,4,6
Off-line CPU(s) list:  1,3,5,7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Om du kör en hyperthreaded VM [kontakta Azure-supporten](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) att hämta hypertrådning inaktiverat.  När hypertrådning är inaktiverat **supporten behöver en fullständig VM-omstart**.


**Steg 2**: Skyddar mot någon av de nedan spekulativ körning sidokanal sårbarheter, referera till operativsystemet leverantörens dokumentation:   
 
- [RedHat och CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller vägledning om hur du den nedan spekulativ körning sidokanal attacker som påverkar många moderna processorer:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - gren Target inmatning (BTI)  
- CVE-2017-5754 - Kernel sidan tabell isolering (KPTI)
- CVE-2018-3639 – Speculative Store Bypass (KPTI) 
 
[L1 Terminal fel (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Intel Software Guard Extensions (Intel SGX)
- CVE-2018-3620 - operativsystem (OS) och System Management-läge (SMM)
- CVE 2018 3646 – påverkar Virtual Machine Manager (VMM)

[Mikroarkitekturstruktur Datasampling](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - mikroarkitekturstruktur Data Sampling Uncacheable minne (MDSUM)
- CVE-2018-12126 - mikroarkitekturstruktur Store bufferten Data Sampling (MSBDS)
- CVE-2018-12127 - mikroarkitekturstruktur belastningen Port Data Sampling (MLPDS)
- CVE-2018-12130 - mikroarkitekturstruktur fyllning bufferten Data Sampling (MFBDS)








