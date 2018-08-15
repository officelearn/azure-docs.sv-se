---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 130cc66831b25621cb022eb19005c624fcd71b9e
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "40105514"
---
**Senast dokumentera uppdatering**: 14 augusti 2018 10:00 FM PST.

Utlämnande av en [ny klass av processorsäkerhetsproblem](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) kallas spekulativ körning sidokanal attacker har lett till frågor från kunder som vill ha mer tydligare.  

Microsoft har distribuerat åtgärder i alla våra molntjänster. Den infrastruktur som kör Azure och isolerar slutkundsarbetsbelastningar från varandra är skyddad. Det innebär att potentiella angripare med hjälp av samma infrastruktur inte kan attacker ditt program med dessa problem.

Med hjälp av Azure [minne bevarande Underhåll](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance) när det är möjligt att minimera påverkan för kunden och eliminera behovet av omstarter. Azure kommer fortsätta utnyttja dessa metoder när du gör systemomfattande uppdateringar till värden och skydda våra kunder.

Mer information om hur säkerheten är integrerad i alla aspekter av Azure är tillgängligt på den [dokumentation om Azure Security](https://docs.microsoft.com/azure/security/) plats. 

> [!NOTE] 
> Eftersom det här dokumentet publicerades först, har flera varianter av den här klassen säkerhetsproblem lämnats. Microsoft fortsätter att vara mycket har investerat i skydda våra kunder och ge vägledning. Den här sidan kommer att uppdateras när vi fortsätter att släppa ytterligare korrigeringar. 
> 
> Den 14 augusti 2018 branschen uppges en ny spekulativ körning sida kanal sårbarhet kallas [L1 Terminal fel](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF) som har tilldelats flera CVEs ([CVE-2018-3615, CVE 2018 3620, och CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)). Denna säkerhetsrisk påverkar Intel® Core®-processorer och Intel® Xeon®-processorer. Microsoft har distribuerat åtgärder i alla sina molntjänster som förstärker isolering mellan kunderna. Läs nedan för ytterligare vägledning för att skydda mot L1TF och föregående sårbarheter ([Spectre Variant 2 CVE-2017-5715 och Meltdown Variant 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)).
>  






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

Du kan aktivera ytterligare säkerhetsfunktioner inuti den virtuella datorn eller Molntjänsten.

### <a name="windows"></a>Windows 

Måloperativsystemets måste vara uppdaterade för att aktivera de här ytterligare säkerhetsfunktioner. Även om ett stort antal spekulativ körning sida kanal åtgärder är aktiverade som standard, ytterligare funktioner som beskrivs här måste vara aktiverat manuellt och kan orsaka en prestandapåverkan. 

**Steg 1**: [kontakta Azure-supporten](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) till exponerar uppdaterad inbyggd programvara (microcode) till dina virtuella datorer. 

**Steg 2**: aktivera Kernel virtuella adress skuggning (KVAS) och OS gren Target inmatning (BTI). Följ instruktionerna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) att aktivera skydd via den `Session Manager` registernycklar. En omstart krävs. 

**Steg 3**: för distributioner som använder [kapslad virtualisering](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 och endast E3): dessa anvisningar gäller inuti den virtuella datorn som du använder som en Hyper-V-värd. 

1. Följ instruktionerna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) att aktivera skydd via den `MinVmVersionForCpuBasedMitigations` registernycklar.  
 
1. Ange vilket hypervisor scheduler **Core** genom att följa anvisningarna [här](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types). 

**Steg 4**: Följ instruktionerna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) att verifiera skydd aktiveras med hjälp av den [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell-modulen. 

> [!NOTE]
> Om du tidigare har hämtat den här modulen kommer du behöva installera den senaste versionen.
>

Alla virtuella datorer ska visa:

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>Aktivera ytterligare säkerhetsfunktioner i uppsättningen kräver att måloperativsystemet är fullständigt uppdaterad. Vissa åtgärder kommer att aktiveras som standard. I följande avsnitt beskrivs de funktioner som är inaktiverade som standard och/eller beroende maskinvarustöd (microcode). Aktivera dessa funktioner kan det orsaka en prestandapåverkan. Referera till ditt operativsystem leverantörens dokumentation för ytterligare instruktioner
 
**Steg 1**: [kontakta Azure-supporten](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) till exponerar uppdaterad inbyggd programvara (microcode) till dina virtuella datorer.
 
**Steg 2**: aktivera gren Target inmatning (BTI) OS-stöd för att minimera CVE 2017 5715 (Spectre Variant 2) genom att följa operativsystemet leverantörens dokumentation. 
 
**Steg 3**: aktivera Kernel sidan tabell isolering (KPTI) att minimera CVE 2017 5754 (Meltdown Variant 3) genom att följa operativsystemet leverantörens dokumentation. 
 
Mer information finns tillgängliga från providern för ditt operativsystem:  
 
- [RedHat och CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [SUSE](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>Nästa steg

Mer information finns i [skydda Azure-kunder från processorproblem](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
