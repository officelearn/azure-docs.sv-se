---
title: Om Azure Site Recovery Deployment Planner för haveriberedskap för virtuella VMware-datorer till Azure | Microsoft Docs
description: Läs mer om Azure Site Recovery Deployment Planner för haveriberedskap för virtuella VMware-datorer till Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mayg
ms.openlocfilehash: 0597f185df35a92696ed9287d23778180319b3de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035800"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Om Azure Site Recovery Deployment Planner för VMware till Azure
Den här artikeln utgör användarhandboken för Distributionshanteraren för Azure Site Recovery vid produktionsdistribution av VMware till Azure.

## <a name="overview"></a>Översikt

Innan du börjar skydda virtuella VMware-datorer med hjälp av Azure Site Recovery, måste du allokera tillräckligt mycket bandbredd för din dagliga dataändringshastighet så att du når önskat mål för återställningspunkter. Du måste distribuera tillräckligt många konfigurationsservrar och processervrar lokalt.

Du måste också skapa rätt typ och antal för dina Azure Storage-målkonton. Du skapar antingen Standard Storage- eller Premium Storage-konton, och väger in tillväxt på källproduktionsservrarna på grund av ökad användning under en viss tid. Du väljer lagringstyp per virtuell dator baserat på arbetsbelastningens egenskaper (exempelvis läs- och skrivbehörighet, i/o-åtgärder per sekund [IOPS] eller dataomsättning) och Site Recovery-begränsningarna.

 Distributionshanteraren för Site Recovery är ett kommandoradsverktyg för haveriberedskapsscenarier för både Hyper-V till Azure och VMware till Azure. Du kan profilera dina virtuella VMware-datorer via fjärranslutning med det här verktyget (utan att produktionen påverkas alls) så att du får en uppfattning om vilken bandbredd och lagringsutrymme som kommer att behövas för replikering och redundanstest. Du kan köra verktyget utan att installera alla Site Recovery-komponenter lokalt. Du får bättre dataflödesresultat om du kör planeringsverktyget på en Windows-server som uppfyller minimikraven för den konfigurationsserver för Site Recovery som du kommer att ta i drift som ett av de första stegen i produktionsdistributionen.

Du kan se följande information i verktyget:

**Utvärdering av kompatibilitet**

* En utvärdering av den virtuella datorn stöds baserat på antal diskar, diskstorlek, IOPS, dataomsättning, starttyp (EFI/BIOS) och OS-version

**Nätverkets bandbreddsbehov kontra utvärdering av återställningspunktmål**

* Beräknad nätverksbandbredd som krävs för deltareplikering
* Dataflödet som Site Recovery kan få från lokala datorer till Azure
* Antalet virtuella datorer att bearbeta per batch sett till den beräknade bandbredd som krävs för att slutföra den inledande replikeringen inom viss tid
* Möjligt återställningspunktmål för en viss bandbredd
* Påverkan på det önskade återställningspunktmålet vid lägre brandbredder

**Krav på infrastruktur för Azure**

* Typ av lagringsutrymme (Standard Storage- eller Premium Storage-konto) som krävs för varje virtuell dator
* Totalt antal Standard Storage- och Premium Storage-konton som ska konfigureras för replikering
* Namnförslag för lagringskonton baserade på riktlinjer för Storage
* Lagringskontots placering för alla virtuella datorer
* Antalet Azure-kärnor som ska etableras innan redundanstest eller redundans för prenumerationen
* Den rekommenderade storleken på den virtuella Azure-datorn för varje lokal virtuell dator

**Krav på lokal infrastruktur**
* Antalet konfigurationsservrar och processervrar som måste distribueras lokalt

**Beräknad kostnad för haveriberedskap till Azure**
* Uppskattad total kostnad för haveriberedskap till Azure: databearbetning, lagring, nätverk och Site Recovery-licenskostnader
* Detaljerad analys av kostnader per virtuell dator


>[!IMPORTANT]
>
>Eftersom användningen troligtvis ökar med tiden utförs alla föregående verktygsberäkningar med ett antagande om en 30-procentig tillväxt för arbetsbelastningen. Beräkningarna använder också ett 95-procentigt percentilvärde för alla profileringsmått, t.ex. skrivbar IOPS och omsättning. Både tillväxtfaktorn och percentilberäkningen kan konfigureras. Om du vill veta mer om tillväxtfaktor, se avsnittet ”Överväganden för tillväxtfaktorer”. Mer information om percentilvärdet finns i avsnittet ”Percentilvärde som används för beräkningen”.
>

## <a name="support-matrix"></a>Stödmatris

| | **VMware till Azure** |**Hyper-V till Azure**|**Azure till Azure**|**Hyper-V till sekundär plats**|**VMware till sekundär plats**
--|--|--|--|--|--
Scenarier som stöds |Ja|Ja|Nej|Ja*|Nej
Version som stöds | vCenter 6.7, 6.5, 6.0 eller 5.5| Windows Server 2016, Windows Server 2012 R2 | Ej tillämpligt |Windows Server 2016, Windows Server 2012 R2|Ej tillämpligt
Konfiguration som stöds|vCenter, ESXi| Hyper-V-kluster, Hyper-V-värd|Ej tillämpligt|Hyper-V-kluster, Hyper-V-värd|Ej tillämpligt|
Antalet servrar som kan profileras per körningsinstans av Distributionshanteraren för Site Recovery |En enda (virtuella datorer som hör till en vCenter-server eller en ESXi-server kan profileras samtidigt)|Flera (virtuella datorer över flera värdar eller värdkluster kan profileras samtidigt)| Ej tillämpligt |Flera (virtuella datorer över flera värdar eller värdkluster kan profileras samtidigt)| Ej tillämpligt

* Verktyget är främst avsett för haveriberedskapsscenariot Hyper-V till Azure. För haveriberedskap från Hyper-V till sekundär plats kan det bara användas till att förstå rekommendationer för källsidan, till exempel nätverksbandbredd som krävs, ledigt lagringsutrymme som krävs på varje Hyper-V-källserver samt inledande batchnummer för replikering och batchdefinitioner. Ignorera Azure-rekommendationer och kostnader i rapporten. Åtgärden för att hämta dataflödet gäller dessutom inte för haveriberedskapsscenarion från Hyper-V till sekundär plats.

## <a name="prerequisites"></a>Nödvändiga komponenter
Verktyget har två huvudfaser: profilering och rapportgenerering. Det finns också ett tredje alternativ som endast beräknar dataflödet. Kraven för servern som profilering och dataflödesmätning initieras från visas i följande tabell.

| Serverkrav | Beskrivning|
|---|---|
|Profilering och mätning av dataflöde| <ul><li>Operativsystem: Windows Server 2016 eller Windows Server 2012 R2<br>(matchar helst åtminstone [storleksrekommendationerna för konfigurationsservern](https://aka.ms/asr-v2a-on-prem-components))</li><li>Datorkonfiguration: 8 virtuella processorer, 16 GB RAM, 300 GB Hårddisk</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Visual C++ Redistributable for Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Internetåtkomst till Azure från den här servern</li><li>Azure Storage-konto</li><li>Administratörsbehörighet till servern</li><li>Minst 100 GB ledigt diskutrymme (förutsätter 1 000 virtuella datorer med ett medeltal av tre diskar vardera, profilerade för 30 dagar)</li><li>Nivåinställningarna för VMware vCenter-statistik kan vara 1 eller högre nivå</li><li>Tillåt vCenter-porten (standard 443): Site Recovery Deployment Planner använder den här porten för att ansluta till vCenter-servern/ESXi-värden</ul></ul>|
| Rapportgenerering | En Windows-dator eller Windows Server med Excel 2013 eller senare.<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Visual C++ Redistributable for Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli) krävs bara när du skickar - alternativet för användare i rapporten-kommandot för projektgenerering att hämta den senaste VM-konfigurationsinformationen för de virtuella datorerna. Distributionshanteraren ansluter till vCenter-servern. Tillåt vCenter-porten (standard 443) port att ansluta till vCenter-servern.</li>|
| Användarbehörigheter | Läsbehörighet för det användarkonto som ska användas för åtkomst till VMware vCenter-servern/VMware vSphere ESXi-värden under profilering |

> [!NOTE]
>
>Verktyget kan enbart profilera virtuella datorer med VMDK- och RDM-diskar. Den kan inte profilera virtuella datorer med iSCSI- eller NFS-diskar. Site Recovery har stöd för iSCSI- och NFS-diskar för VMware-servrar. Eftersom kapacitetsplaneraren inte körs i gästen och profileringen bara utförs med prestandaräknare för vCenter, kan verktyget inte se dessa disktyper.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Ladda ned och extrahera distributionshanteraren
1. Ladda ned den senaste versionen av [Distributionshanteraren för Site Recovery](https://aka.ms/asr-deployment-planner).
Verktyget är paketerat i en komprimerad mapp. Den aktuella versionen av verktyget har endast stöd för scenariot med VMware till Azure.

2. Kopiera den komprimerade mappen till den Windows Server som du vill köra verktyget från.
Du kan köra verktyget från Windows Server 2012 R2 om servern har nätverksåtkomst för att ansluta till vCenter-servern/vSphere ESXi-värden som innehåller de virtuella datorer som ska profileras. Vi rekommenderar dock att du kör verktyget på en server vars maskinvarukonfiguration uppfyller [riktlinjerna för förändring av storleken på konfigurationsservrar](https://aka.ms/asr-v2a-on-prem-components). Om du redan har distribuerat Site Recovery-komponenter lokalt bör du köra verktyget från konfigurationsservern.

    Vi rekommenderar att du har samma maskinvarukonfiguration som konfigurationsservern (som har en inbyggd processerver) på den server där du kör verktyget. En sådan konfiguration garanterar att det uppnådda genomflödet som verktyget rapporterar matchar det faktiska dataflöde som Site Recovery kan uppnå under replikeringen. Dataflödesberäkningen är beroende av den tillgängliga nätverksbandbredden och maskinvarukonfigurationen (t.ex. processor och lagringsutrymme) på servern. Om du kör verktyget från andra servrar beräknas dataflödet från den här servern till Azure. Också eftersom maskinvarukonfigurationen på servern kan skilja sig från konfigurationsservern kan det dataflöde som verktyget rapporterar vara felaktigt.

3. Extrahera .zip-filen.
Mappen innehåller flera filer och undermappar. Den körbara filen är ASRDeploymentPlanner.exe i den överordnade mappen.

    Exempel: Kopiera .zip-filen till enhet E:\ och packa upp den.
    E:\ASR Deployment Planner_v2.3.zip

    E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>Uppdatera till den senaste versionen av distributionshanteraren
Om du har en tidigare version av distributionshanteraren gör du något av följande:
 * Om den senaste versionen innehåller inte en profileringskorrigering och profileringen pågår redan på den aktuella versionen av planeringsverktyget fortsätter du profileringen.
 * Om den senaste versionen innehåller en profileringskorrigering rekommenderar vi att du stoppar profileringen av din aktuella version och startar om profileringen med den nya versionen.


 >[!NOTE]
 >
 >När du startar profilering med den nya versionen skickar du samma sökväg för utdatakatalogen så att verktyget lägger till profildata i de befintliga filerna. En fullständig uppsättning profilerade data används för att generera rapporten. Om du skickar en annan utdatakatalog kommer nya filer att skapas och gamla profildata används inte för att skapa rapporten.
 >
 >Varje ny distributionshanterare är en ackumulerad uppdatering av .zip-filen. Du behöver inte kopiera de senaste filerna till föregående mapp. Du kan skapa och använda en ny mapp.


## <a name="version-history"></a>Versionshistorik
Den senaste versionen av Distributionshanteraren för Site Recovery är 2.4.
Läs sidan med [versionshistorik för Distributionshanteraren för Site Recovery](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) för information om korrigeringarna som har lagts till i varje uppdatering.

## <a name="next-steps"></a>Nästa steg
[Kör Distributionshanteraren för Site Recovery](site-recovery-vmware-deployment-planner-run.md)
