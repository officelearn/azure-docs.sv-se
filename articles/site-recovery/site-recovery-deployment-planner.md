---
title: "Azure Site Recovery-kapacitetsplaneraren för VMware till Azure| Microsoft Docs"
description: "Det här är användarhandboken för Azure Site Recovery-kapacitetsplaneraren."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/04/2017
ms.author: nisoneji
ms.openlocfilehash: 665bb65d17e9abec98262b92afffba008ed6c891
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Distributionshanteraren för Azure Site Recovery för VMware till Azure
Den här artikeln utgör användarhandboken för Azure Site Recovery Deployment Planner för produktionsdistribution av VMware till Azure.

## <a name="overview"></a>Översikt

Innan du börjar skyddar virtuella VMware-datorer med hjälp av Site Recovery måste du allokera tillräckligt mycket bandbredd sett till din dagliga dataändringshastighet så att du når önskat mål för återställningspunkt. Du måste distribuera tillräckligt många konfigurationsservrar och processervrar lokalt.

Du måste också skapa rätt typ och antal Azure-mållagringskonton. Du skapar antingen Standard Storage- eller Premium Storage-konton, och väger in tillväxt på källproduktionsservrarna på grund av ökad användning under en viss tid. Du väljer lagringstyp per virtuell dator baserat på arbetsbelastningens egenskaper (exempelvis läs- och skrivbehörighet, i/o-åtgärder per sekund [IOPS] eller dataomsättningen) och Site Recovery-begränsningarna.

Distributionshanteraren för Azure Site Recovery (version 2) är ett kommandoradsverktyg som är tillgängligt för både haveriberedskapsscenarier för Hyper-V till Azure och VMware till Azure. Du kan profilera dina virtuella VMware-datorer via fjärranslutning med det här verktyget (utan att produktionen påverkas alls) så att du får en uppfattning om vilken bandbredd och hur stort Azure Storage-lagringsutrymme som kommer att behövas för replikering och redundansväxling. Du kan köra verktyget utan att installera alla Site Recovery-komponenter lokalt. Du får dock bättre dataflödesresultat om du kör planeringsverktyget på en Windows-server som uppfyller minimikraven för den konfigurationsserver för Site Recovery som du kommer att ta i drift som ett av de första stegen i produktionsdistributionen.

Du kan se följande information i verktyget:

**Utvärdering av kompatibilitet**

* En utvärdering av om den virtuella datorn stöds baserat på antal diskar, diskstorlek, IOPS, dataomsättning, starttyp (EFI/BIOS) och OS-version
 
**Nätverkets bandbreddsbehov kontra utvärdering av återställningspunktmål**

* Beräknad nätverksbandbredd som krävs för deltareplikering
* Dataflödet som Site Recovery kan få från lokala datorer till Azure
* Antalet virtuella datorer att bearbeta per batch sett till den uppskattade bandbredd som krävs för att slutföra den inledande replikeringen inom viss tid

**Krav på infrastruktur för Azure**

* Typ av lagringsutrymme (Standard Storage- eller Premium Storage-konto) som krävs för varje virtuell dator
* Totalt antal Standard Storage- och Premium Storage-konton som ska konfigureras för replikering
* Namnförslag för lagringskonton baserade på riktlinjer för Azure Storage
* Lagringskontots placering för alla virtuella datorer
* Antalet Azure-kärnor som ska etableras innan redundanstest/redundansväxling för prenumerationen
* Den rekommenderade storleken på den virtuella Azure-datorn för varje lokal virtuell dator

**Krav på lokal infrastruktur**
* Antalet konfigurationsservrar och processervrar som måste distribueras lokalt

**Beräknad DR-kostnad för Azure** 
* Uppskattad total DR-kostnad för Azure: databearbetning, lagring, nätverk och Azure Site Recovery-licenskostnader
* Detaljerad analys av faktiska kostnader per virtuell dator


>[!IMPORTANT]
>
>Eftersom användningen troligtvis ökar med tiden utförs alla föregående verktygsberäkningar med förutsättningen att en 30-procentig tillväxtfaktor för arbetsbelastningen egenskaper och ett 95-procentigt percentilvärde används för alla profileringsmått (skrivbar IOPS, omsättning och så vidare). Båda parametrarna (tillväxtfaktorn och percentilberäkningen) kan konfigureras. Om du vill veta mer om tillväxtfaktor, se avsnittet ”Överväganden för tillväxtfaktorer”. Mer information om percentilvärdet finns i avsnittet ”Percentilvärdet som används för beräkningen”.
>

## <a name="support-matrix"></a>Supportmatris

| | **VMware till Azure** |**Hyper-V till Azure**|**Azure till Azure**|**Hyper-V till sekundär plats**|**VMware till sekundär plats**
--|--|--|--|--|--
Scenarier som stöds |Ja|Ja|Nej|Ja*|Nej
Version som stöds | vCenter 6.5, 6.0 eller 5.5| Windows Server 2016, Windows Server 2012 R2 | Ej tillämpligt |Windows Server 2016, Windows Server 2012 R2|Ej tillämpligt
Konfiguration som stöds|vCenter, ESXi| Hyper-V-kluster, Hyper-V-värd|Ej tillämpligt|Hyper-V-kluster, Hyper-V-värd|Ej tillämpligt|
Antalet servrar som kan profileras per körningsinstans av distributionshanteraren för Azure Site Recovery |En enda (virtuella datorer som hör till en vCenter-server eller en ESXi-server kan profileras samtidigt)|Flera (virtuella datorer över flera värdar eller värdkluster kan vara profil samtidigt)| Ej tillämpligt |Flera (virtuella datorer över flera värdar eller värdkluster kan vara profil samtidigt)| Ej tillämpligt

* Verktyget är främst för haveriberedskapsscenariot Hyper-V till Azure. För haveriberedskap från Hyper-V till sekundär plats. Det kan endast användas för att förstå rekommendationer från källan, till exempel nätverksbandbredd som krävs, ledigt lagringsutrymme som krävs på varje Hyper-V-server som agerar som en källa samt inledande siffror för replikering och batchbearbetning och batch-definitioner.  Ignorera Azure-rekommendationer och kostnader i rapporten. Åtgärden för att hämta dataflödet gäller dessutom inte för haveriberedskapsscenarion från Hyper-V till sekundär plats.

## <a name="prerequisites"></a>Krav
Verktyget har två huvudfaser: profilering och rapportgenerering. Det finns också ett tredje alternativ som endast beräknar dataflödet. Kraven för servern som profilering och dataflödesmätning initieras från visas i följande tabell:

| Serverkrav | Beskrivning|
|---|---|
|Profilering och mätning av dataflöde| <ul><li>Operativsystem: Microsoft Windows Server 2016 eller Microsoft Windows Server 2012 R2<br>(matchar helst åtminstone [storleksrekommendationerna för konfigurationsservern](https://aka.ms/asr-v2a-on-prem-components))</li><li>Datorkonfiguration: 8 virtuella processorer, 16 GB RAM-minne, 300 GB hårddisk</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Microsoft Visual C++ Redistributable for Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Internetåtkomst till Azure från den här servern</li><li>Azure Storage-konto</li><li>Administratörsbehörighet till servern</li><li>Minst 100 GB ledigt diskutrymme (förutsatt 1 000 virtuella datorer med ett medeltal av de tre diskar vardera, profilerade under 30 dagar)</li><li>Nivåinställningarna för VMware vCenter-statistik bör anges till 2 eller hög nivå</li><li>Tillåt port 443: ASR Deployment Planner använder den här porten för att ansluta till vCenter-servern/ESXi-värden</ul></ul>|
| Rapportgenerering | En Windows-dator eller Windows Server med Microsoft Excel 2013 eller senare |
| Användarbehörigheter | Läsbehörighet för det användarkonto som ska användas för åtkomst till VMware vCenter-servern/VMware vSphere ESXi-värden under profilering |

> [!NOTE]
>
>Verktyget kan enbart profilera virtuella datorer med VMDK- och RDM-diskar. Den kan inte profilera virtuella datorer med iSCSI- eller NFS-diskar. Även om Site Recovery har stöd för iSCSI- och NFS-diskar för VMware-servrar kan inte verktyget se dessa disktyper eftersom kapacitetsplaneraren inte körs i gästen och profileringen bara utförs med prestandaräknare för vCenter.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Ladda ned och extrahera distributionshanteraren
1. Ladda ned den senaste versionen av [Azure Site Recovery-kapacitetsplaneraren](https://aka.ms/asr-deployment-planner).  
Verktyget är paketerat i en komprimerad mapp. Den aktuella versionen av verktyget har endast stöd för scenariot med VMware till Azure.

2. Kopiera den komprimerade mappen till den Windows Server som du vill köra verktyget från.  
Du kan köra verktyget från Windows Server 2012 R2 om servern har nätverksåtkomst för att ansluta till vCenter-servern/vSphere ESXi-värden som innehåller de virtuella datorer som ska profileras. Vi rekommenderar dock att du kör verktyget på en server vars maskinvarukonfiguration uppfyller [riktlinjen för förändring av storleken på konfigurationsservrar](https://aka.ms/asr-v2a-on-prem-components). Om du redan har distribuerat Site Recovery-komponenter lokalt bör du köra verktyget från konfigurationsservern.

 Vi rekommenderar att du har samma maskinvarukonfiguration som konfigurationsservern (som har en inbyggd processerver) på den server där du kör verktyget. En sådan konfiguration garanterar att det uppnådda genomflödet som verktyget rapporterar matchar det faktiska dataflöde som Site Recovery kan uppnå under replikeringen. Dataflödesberäkningen är beroende av den tillgängliga nätverksbandbredden och maskinvarukonfigurationen (processor, lagringsutrymme och så vidare) på servern. Om du kör verktyget från andra servrar beräknas dataflödet från den här servern till Microsoft Azure. Också eftersom maskinvarukonfigurationen på servern kan skilja sig från konfigurationsservern kan det dataflöde som verktyget rapporterar vara felaktigt.

3. Extrahera .zip-filen.  
Mappen innehåller flera filer och undermappar. Den körbara filen är ASRDeploymentPlanner.exe i den överordnade mappen.

    Exempel:  
    Kopiera .zip-filen till enhet E:\ och packa upp den.
   E:\ASR Deployment Planner_v2.0zip

    E:\ASR Deployment Planner_v2.0\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Uppdatera till den senaste versionen av distributionshanteraren
Om du har en tidigare version av distributionshanteraren gör du något av följande:
 * Om den senaste versionen innehåller inte en profileringskorrigering och profileringen pågår redan på den aktuella versionen av planeringsverktyget fortsätter du profileringen.
 * Om den senaste versionen innehåller en profileringskorrigering rekommenderar vi att du stoppar profileringen av din aktuella version och startar om profileringen med den nya versionen.


 >[!NOTE]
 >
 >När du startar profilering med den nya versionen skickar du samma sökväg för utdatakatalogen så att verktyget lägger till profildata i de befintliga filerna. En fullständig uppsättning profilerade data används för att generera rapporten. Om du skickar en annan utdatakatalog kommer nya filer att skapas och gamla profildata använda inte för att skapa rapporten.
 >
 >Varje ny kapacitetsplanerare är en ackumulerad uppdatering av .zip-filen. Du behöver inte kopiera de senaste filerna till föregående mapp. Du kan skapa och använda en ny mapp.

## <a name="next-steps"></a>Nästa steg
* [Kör distributionshanteraren](site-recovery-vmware-deployment-planner-run.md).
