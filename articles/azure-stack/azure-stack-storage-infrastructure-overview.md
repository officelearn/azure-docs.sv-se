---
title: Hantera lagringsinfrastrukturen för Azure Stack | Microsoft Docs
description: Hantera lagringsinfrastrukturen för Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: ''
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: 416d75b254d0fbe14a0b39e5ae77d09a48e548f6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271296"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Hantera lagringsinfrastrukturen för Azure Stack

*Gäller för Integrerade Azure Stack-system och Azure Stack Development Kit*

Den här artikeln beskriver hälsotillstånd och driftstatus för Azure Stack-infrastruktur lagringsresurser. Dessa resurser inkluderar lagringsenheter och volymer. Informationen i det här avsnittet kan vara ovärderliga vid felsökning av olika problem som en enhet inte kan läggas till i en pool.

## <a name="understand-drives-and-volumes"></a>Förstå enheter och volymer

### <a name="drives"></a>Enheter

Azure Stack definierar bygger på Windows Server-programvara och funktioner för lagring med en kombination av Lagringsdirigering (S2D) och Windows Server Failover Clustering att tillhandahålla en högpresterande, skalbar och flexibel lagring-tjänsten.

Azure Stack-integrerat system-partner erbjuder flera variationer i lösningen, inklusive en mängd olika Lagringsflexibilitet. Du kan för närvarande välja en kombination av tre olika typer: NVMe (beständigt minne uttryckliga), SATA/SAS SSD (Solid-State Drive) HDD (hårddisk).

Lagringsdirigering har en cache för att maximera lagringsprestanda. I Azure Stack-installation med en eller flera typer av enheter, Lagringsdirigering använder automatiskt alla enheter av den ”snabbaste” (NVMe &gt; SSD &gt; HDD) typen för cachelagring. De återstående enheterna används för kapacitet. Enheterna kan grupperas i en ”all-flash” eller ”hybrid” distribution:

![Azure Stack-lagringsinfrastruktur](media/azure-stack-storage-infrastructure-overview/image1.png)

All-flash distributioner syfte att maximera lagringsprestanda och omfattar inte rotational hårddiskar (HDD).

![Azure Stack-lagringsinfrastruktur](media/azure-stack-storage-infrastructure-overview/image2.png)

Hybriddistributioner sträva att balansera prestanda och kapacitet eller maximera kapaciteten och inkluderar rotational hårddiskar (HDD).

Beteendet för cachen bestäms automatiskt baserat på typerna av enheter som cachelagras i. När cachelagring för SSD-enheter (till exempel NVMe cachelagring för SSD: er), cachelagras endast skrivningar. Detta minskar slitage på kapacitet enheter minska kumulativa trafiken till enheterna som kapacitet och utöka deras livstid. Under tiden kan cachelagras inte läsningar eftersom läsningar inte påverkar avsevärt livslängden för flash, och eftersom SSD-enheter erbjuder universellt Läs svarstider. När cachelagring för-hårddiskar (till exempel SSD: er cachelagring för HDD), både läsningar och skrivningar finns i cacheminnet för att ge flash-liknande svarstid (ofta ~ 10 x bättre) för både.

![Azure Stack-lagringsinfrastruktur](media/azure-stack-storage-infrastructure-overview/image3.png)

Tillgängliga konfiguration av lagring, kan du kontrollera Azure Stack OEM-partner (https://azure.microsoft.com/overview/azure-stack/partners/) för detaljerad specifikation.

> [!Note]  
> Azure Stack-installation kan tillhandahållas i en hybriddistribution med både HDD och SSD (eller NVMe)-enheter. Men enheter av snabbare typ som ska användas som cacheenheter och alla återstående enheterna används som kapacitet enheter som en pool. Klientdata (blobs, tabeller, köer och diskar) placeras på kapacitet enheter. Så etablering premium-diskar eller att välja premium storage-konto typ innebär inte att objekten är garanterade att allokeras på SSD eller NVMe-enheter och få bättre prestanda.

### <a name="volumes"></a>Volymer

Den *lagringstjänst* partitionerar tillgängligt lagringsutrymme i separata volymer som har allokerats för att lagra data för system- och klienttrafik. Volymer kombinera enheter i lagringspoolen att introducera feltolerans, skalbarhet och prestandafördelarna med hjälp av Lagringsdirigering.

![Azure Stack-lagringsinfrastruktur](media/azure-stack-storage-infrastructure-overview/image4.png)

Det finns tre typer av volymer som har skapats på Azure Stack-lagringspool:

-   Infrastruktur: värdfiler som används av Azure Stack-infrastrukturens virtuella datorer och kärntjänsterna.

-   VM Temp: värden temporära diskar anslutna till virtuella klientdatorer och data lagras i de här diskarna.

-   Objektet Store: värden klientdata servicing blobar, tabeller, köer och VM-diskar.

I en distribution med flera noder ser du tre infrastruktur volymer, medan antalet VM Temp volymer och objektet Store volymer är lika med antalet noder i Azure Stack-distributionen:

-   På en distribution med fyra noder finns fyra lika VM Temp-volymer och fyra lika objektet Store-volymer.

-   Om du lägger till en ny nod i klustret blir en ny volym för båda typerna skapas.

-   Antalet volymer förblir samma även om en nod fungerar inte eller har tagits bort.

-   Om du använder Azure Stack Development Kit, finns det en enskild volym med flera filresurser.

Volymer i Lagringsdirigering ger flexibilitet att skydda mot maskinvaruproblem, t.ex enhets- eller fel, och aktivera kontinuerlig tillgänglighet i hela serverunderhåll, till exempel programuppdateringar. Azure Stack-distributionen använder trevägsspegling för att säkerställa dataåterhämtning. Tre kopior av klientdata skrivs till olika servrar, där de hamnar i cacheminnet:

![Azure Stack-lagringsinfrastruktur](media/azure-stack-storage-infrastructure-overview/image5.png)

Spegling ger feltolerans genom att ha flera kopior av alla data. Hur dessa data stripe-volymer och placeras är icke-trivialt (se den här bloggen och lär dig mer), men det är absolut sant för att säga att alla data som lagras med spegling är skriven i sin helhet flera gånger. Varje kopia skrivs till olika fysiska maskinvara (olika enheter på olika servrar) som antas misslyckas oberoende av varandra. 3-vägsspegling klarar minst två maskinvaruproblem (enhet eller server) på ett säkert sätt i taget. Till exempel om en servern när plötsligt startas om misslyckas en annan enhet eller server, alla data förblir säkra och kontinuerligt tillgängliga.

## <a name="volume-states"></a>Volymen tillstånd

Om du vill ta reda på vilka tillståndet volumess som kan använda följande PowerShell-kommandon:

\$scaleunit\_namn = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

Get-AzsVolume - ScaleUnit \$scaleunit\_namn - StorageSubSystem \$undersystem\_namn | Select-Object VolumeLabel, anges, OperationalStatus, RepairStatus, beskrivning, åtgärd, TotalCapacityGB, RemainingCapacityGB

Här är ett exempel på utdata som visar en frånkopplad volym och en försämrad/ofullständig volym:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Inte tillgängligt | Är oberoende av |
| ObjStore_2 | Varning | {Försämrad, ofullständig} |

I följande avsnitt listas hälsotillstånd och driftstatus.

### <a name="volume-health-state-healthy"></a>Hälsotillståndet för volymen: Felfri

| Driftstatus | Beskrivning |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ok | Volymen är felfri. |
| Icke-optimal | Inte att skriva data jämnt över enheter.<br> <br>**Åtgärd:** Kontakta supporten för att optimera diskanvändning i lagringspoolen. Innan du, börjar filen logginsamlingsprocessen med hjälp av vägledningen från https://aka.ms/azurestacklogfiles. Du kan behöva återställa från säkerhetskopia när den felande anslutningen har återställts. |


### <a name="volume-health-state-warning"></a>Hälsotillståndet för volymen: Varning

När volymen är i ett varningshälsotillstånd, innebär det att en eller flera kopior av dina data är inte tillgängliga, men Azure Stack kan ändå läsa minst en kopia av dina data.

| Driftstatus | Beskrivning |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| I tjänsten | Azure Stack reparerar volymen, till exempel när du lägger till eller ta bort en enhet. När reparationen är klar ska returnera volymen till OK hälsotillstånd.<br> <br>**Åtgärd:** Vänta tills Azure Stack för att reparera volymen och kontrollera status efteråt. |
| Ofullständig | Återhämtning av volymen minskar eftersom en eller flera enheter misslyckades eller saknas. Dock innehålla saknas enheterna uppdaterade kopior av dina data.<br> <br>**Åtgärd:** Återansluta saknas enheter, Ersätt misslyckade enheter och ansluta alla servrar som är offline. |
| Degraderad | Återhämtning av volymen minskar eftersom en eller flera enheter misslyckades eller saknas och det är inaktuell kopior av dina data på dessa enheter.<br> <br>**Åtgärd:** Återansluta saknas enheter, Ersätt misslyckade enheter och ansluta alla servrar som är offline. |

 

### <a name="volume-health-state-unhealthy"></a>Hälsotillståndet för volymen: Skadad

Vissa eller alla data på volymen är tillfälligt otillgänglig när en volym är i ett feltillstånd hälsotillstånd.

| Driftstatus | Beskrivning |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ingen redundans | Volymen har förlorat data eftersom för många enheter misslyckades.<br> <br>**Åtgärd:** Kontakta supporten. Innan du, börjar filen logginsamlingsprocessen med hjälp av vägledningen från https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Hälsotillståndet för volymen: Inte tillgängligt

Volymen kan också vara i okänt hälsotillståndet om den virtuella disken har frigöras.

| Driftstatus | Beskrivning |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Är oberoende av | Det uppstod fel på lagringsenheten som kan orsaka att volymen inte tillgänglig. Vissa data kan gå förlorade.<br> <br>**Åtgärd:** <br>1. Kontrollera fysiskt och nätverksanslutningar av alla lagringsenheter.<br>2. Kontakta supporten om alla enheter är korrekt anslutna. Innan du, börjar filen logginsamlingsprocessen med hjälp av vägledningen från https://aka.ms/azurestacklogfiles. Du kan behöva återställa från säkerhetskopia när den felande anslutningen har återställts. |

## <a name="drive-states"></a>Enhetsstatus

Du kan använda följande PowerShell-kommandon för att övervaka status för enheter:

 

\$scaleunit\_namn = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

, SerialNumber

 

Get-AzsDrive - ScaleUnit \$scaleunit\_namn - StorageSubSystem \$undersystem\_namn | Select-Object StorageNode, PhysicalLocation, anges, OperationalStatus, beskrivning, åtgärd, användning, CanPool, CannotPoolReason, serienummer, modell, MediaType, CapacityGB

I följande avsnitt beskrivs hälsotillstånd som en enhet kan vara i.

### <a name="drive-health-state-healthy"></a>Hälsotillståndet för enheten: Felfri

| Driftstatus | Beskrivning |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Ok | Volymen är felfri. |
| I tjänsten | Enheten utför vissa åtgärder för internt underhåll. När åtgärden är klar bör enheten tillbaka till OK hälsotillståndet. |

### <a name="drive-health-state-healthy"></a>Hälsotillståndet för enheten: Felfri

En enhet i varningen tillstånd kan läsa och skriva data har men har problem.

| Driftstatus | Beskrivning |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Förlorad kommunikation | Anslutningen har kopplats till enheten.<br> <br>**Åtgärd:** Ta med alla servrar online igen. Om detta inte löser den återansluta enheten. Om detta bevarar händer, ersätter du enheten för att säkerställa fullständig återhämtning. |
| Förutsägbart fel | Ett fel på enheten förväntas ske snart.<br> <br>**Åtgärd:** Byt ut enheten så snart som möjligt för att säkerställa fullständig återhämtning. |
| IO-fel | Det uppstod ett tillfälligt fel vid åtkomst till enheten.<br> <br>**Åtgärd:** Om detta bevarar händer, ersätter du enheten för att säkerställa fullständig återhämtning. |
| Tillfälligt fel | Det uppstod ett tillfälligt fel med enheten. Detta innebär vanligen enheten svarade inte, men det kan också innebära som Lagringsdirigering en partition på ett olämpligt sätt har tagits bort från enheten. <br> <br>**Åtgärd:** Om detta bevarar händer, ersätter du enheten för att säkerställa fullständig återhämtning. |
| Onormalt svarstid | Enheten svarar ibland och visar tecken på fel.<br> <br>**Åtgärd:** Om detta bevarar händer, ersätter du enheten för att säkerställa fullständig återhämtning. |
| Tar bort från poolen | Azure Stack håller på att ta bort enheten från dess lagringspoolen.<br> <br>**Åtgärd:** Vänta tills Azure Stack för att slutföra borttagningen av enheten och kontrollera status efteråt.<br>Om statusen, kontakta supporten. Innan du, börjar filen logginsamlingsprocessen med hjälp av vägledningen från https://aka.ms/azurestacklogfiles. |
|  |  |
| Från underhållsläge | Azure Stack håller på att placera enheten i underhållsläge. Detta är ett tillfälligt tillstånd - enheten bör snart vara i underhållslägesstatus för Indien.<br> <br>**Åtgärd:** Vänta tills Azure Stack och slutföra processen och kontrollera status efteråt. |
| I underhållsläge | Enheten är i underhållsläge, stoppat läsningar och skrivningar från enheten. Detta innebär vanligen Azure Stack-administrativa uppgifter som PNU eller FRU fungerar på enheten. Men administratören kan också placera enheten i underhållsläge.<br> <br>**Åtgärd:** Vänta tills Azure Stack att slutföra uppgiften administration och kontrollera status efteråt.<br>Om statusen, kontakta supporten. Innan du, börjar filen logginsamlingsprocessen med hjälp av vägledningen från https://aka.ms/azurestacklogfiles. |
|  |  |
| Stoppa underhållsläge | Azure Stack håller på att enheten igen. Detta är ett tillfälligt tillstånd - enheten bör snart vara i ett annat tillstånd - helst felfritt.<br> <br>**Åtgärd:** Vänta tills Azure Stack och slutföra processen och kontrollera status efteråt. |

 

### <a name="drive-health-state-unhealthy"></a>Hälsotillståndet för enheten: Skadad

En enhet i ett ohälsosamt tillstånd kan inte för närvarande skrivs till eller komma åt.

| Driftstatus | Beskrivning |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Delat | Enheten har blivit avgränsade från poolen.<br> <br>**Åtgärd:** Ersätt enheten med en ny disk. Om du måste använda den här disken, ta bort disken från systemet, kontrollera att det finns inga användbara data på disken, radera disken och sedan placera disken. |
| Kan inte användas | Den fysiska disken är i karantän eftersom det inte stöds av din lösningsleverantör. Diskar som är godkända för lösningen och har rätt disk-programvaran stöds.<br> <br>**Åtgärd:** Ersätt enheten med en disk som har en godkänd tillverkare och modellnummer för lösningen. |
| Inaktuella metadata | Den nya disken tidigare har använts och kan innehålla data från en okänd lagringssystemet. Disken är i karantän.        <br> <br>**Åtgärd:** Ersätt enheten med en ny disk. Om du måste använda den här disken, ta bort disken från systemet, kontrollera att det finns inga användbara data på disken, radera disken och sedan placera disken. |
| Okända metadata | Okända metadata hittades på enheten, vilket vanligtvis innebär att enheten har metadata från en annan pool på den.<br> <br>**Åtgärd:** Ersätt enheten med en ny disk. Om du måste använda den här disken, ta bort disken från systemet, kontrollera att det finns inga användbara data på disken, radera disken och sedan placera disken. |
| Medier med fel | Enheten misslyckades och kommer inte längre användas av lagringsutrymmen.<br> <br>**Åtgärd:** Byt ut enheten så snart som möjligt för att säkerställa fullständig återhämtning. |
| Maskinvarufel i enheten | Det uppstod ett maskinvarufel på den här enheten. <br> <br>**Åtgärd:** Byt ut enheten så snart som möjligt för att säkerställa fullständig återhämtning. |
| Uppdatering av inbyggd programvara | Azure Stack uppdaterar den inbyggda programvaran på enheten. Det här är ett tillfälligt tillstånd som varar mindre än en minut och under vilken tid som andra enheter i poolen kan hantera alla läsningar och skrivningar.<br> <br>**Åtgärd:** Vänta tills Azure Stack att slutföra uppdateringen och kontrollera status efteråt. |
| Startar | Enheten är nästan klar för åtgärden. Det bör vara ett tillfälligt tillstånd - när du är klar enheten ska övergå till en annan Användningsstatus.<br> <br>**Åtgärd:** Vänta tills Azure Stack att slutföra åtgärden och kontrollera status efteråt. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Orsaker till en enhet inte kan grupperas

Vissa enheter inte bara är redo att vara i Azure Stack-lagringspoolen. Du kan se varför en enhet inte är berättigade till poolning genom att titta på CannotPoolReason-egenskapen för en enhet. I följande tabell ger lite mer detaljerad information om var och en av anledningarna.

| Orsak | Beskrivning |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Maskinvara som inte är kompatibla | Enheten är inte i listan över godkända lagringsmodellerna anges med hjälp av tjänsten för hälsotillstånd.<br> <br>**Åtgärd:** Ersätt enheten med en ny disk. |
| Inbyggd programvara som inte är kompatibla | Den inbyggda programvaran på den fysiska enheten finns inte i listan över godkända inbyggd programvara revisioner genom att använda tjänsten för hälsotillstånd.<br> <br>**Åtgärd:** Ersätt enheten med en ny disk. |
| Används av klustret | Enheten är för närvarande används av ett redundanskluster.<br> <br>**Åtgärd:** Ersätt enheten med en ny disk. |
| Flyttbart medium | Enheten klassificeras som en flyttbar enhet. <br> <br>**Åtgärd:** Ersätt enheten med en ny disk. |
| Ej hälsosam | Enheten är inte i felfritt tillstånd och kan behöva ersättas.<br> <br>**Åtgärd:** Ersätt enheten med en ny disk. |
| Inte tillräckligt med kapacitet | Det finns partitioner tar upp det lediga utrymmet på enheten.<br> <br>**Åtgärd:** Ersätt enheten med en ny disk. Om du måste använda den här disken, ta bort disken från systemet, kontrollera att det finns inga användbara data på disken, radera disken och sedan placera disken. |
| Verifiering pågår | Tjänsten kontrollerar om du vill se om enheten eller inbyggda programvaran är godkänd för användning.<br> <br>**Åtgärd:** Vänta tills Azure Stack och slutföra processen och kontrollera status efteråt. |
| Verifieringen misslyckades | Tjänsten för hälsotillstånd kunde inte kontrollera om enheten eller inbyggda programvaran är godkänd för användning.<br> <br>**Åtgärd:** Kontakta supporten. Innan du, börjar filen logginsamlingsprocessen med hjälp av vägledningen från https://aka.ms/azurestacklogfiles. |
| Offline | Enheten är offline. <br> <br>**Åtgärd:** Kontakta supporten. Innan du, börjar filen logginsamlingsprocessen med hjälp av vägledningen från https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Nästa steg

[Hantera kapacitet](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-storage-shares) 