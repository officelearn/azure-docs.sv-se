---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 6377b79d986d32fba8f84c670d6b69d5eda98b8a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
När du skapar en virtuell dator (VM), starta om stoppats (frigjorts) virtuella datorer eller ändra storlek på en virtuell dator allokerar beräkningsresurser till din prenumeration på Microsoft Azure. Vi kontinuerligt investera i ytterligare infrastruktur och funktioner för att se till att vi alltid har alla VM-typer för att stödja kundernas behov. Du kan ibland uppstå fel vid tilldelning av resursen på grund av en oöverträffad ökad efterfrågan på Azure-tjänster i vissa områden. Det här problemet kan uppstå när du försöker skapa eller starta virtuella datorer i en region när de virtuella datorerna visas följande felkod och meddelandet:

**Felkod**: AllocationFailed eller ZonalAllocationFailed

**Felmeddelande**: ”gick inte att allokera. Vi har inte tillräckligt med kapacitet för den begärda VM-storleken i den här regionen. Läs mer om hur du förbättrar sannolikhet allokering på http://aka.ms/allocation-guidance”

Den här artikeln förklarar orsakerna till några vanliga Allokeringsfel och föreslår möjliga lösningar.

Om din Azure problemet inte åtgärdas i den här artikeln, finns det [Azure-forum på MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera problemet på dessa forum eller @AzureSupport på Twitter. Du kan också filen en Azure-supportbegäran genom att välja Get support på den [Azure-supporten](https://azure.microsoft.com/support/options/) plats.

Tills din standardtypen VM finns tillgänglig i ditt önskad region, rekommenderar vi kunder som får distributionsproblem att överväga riktlinjerna i följande tabell som en tillfällig lösning. 

Identifiera det scenario som bäst passar ditt ärende och försök sedan begäran om minnesallokering med hjälp av motsvarande föreslagna lösningen för att öka sannolikheten för allokering lyckades. Du kan också alltid försöka igen senare. Det beror på att tillräckligt med resurser kan ha frigjorts i klustret, region eller zon för att hantera din förfrågan. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Ändra storlek på en virtuell dator eller Lägg till virtuella datorer i en befintlig tillgänglighetsuppsättning

### <a name="cause"></a>Orsak

Ange en begäran att ändra storlek på en virtuell dator eller lägga till en virtuell dator till en befintlig tillgänglighetsuppsättning måste försökas på det ursprungliga klustret som är värd för den befintliga tillgängligheten. Den begärda VM-storleken som stöds av klustret, men klustret kan för närvarande inte har tillräcklig kapacitet. 

### <a name="workaround"></a>Lösning

Om den virtuella datorn kan vara en del av en annan tillgänglighetsuppsättning, skapar du en virtuell dator i en annan tillgänglighetsuppsättning (i samma region). Den här nya virtuella datorn kan sedan läggas till samma virtuella nätverk.

Stoppa (frigöra) alla virtuella datorer i samma tillgänglighetsuppsättning ange och sedan starta om var och en.
Stoppa: Klicka på resursgrupper > [resursgruppen] > resurser > [tillgänglighetsuppsättning] > virtuella datorer > [den virtuella datorn] > Stoppa.
När alla virtuella datorer har slutat, väljer den första virtuella datorn och klicka sedan på Starta.
Det här steget ser till att ett nytt försök för allokering körs och att ett nytt kluster kan väljas som har tillräckligt med kapacitet.

## <a name="restart-partially-stopped-deallocated-vms"></a>Starta om delvis stoppats (frigjorts) virtuella datorer

### <a name="cause"></a>Orsak

Flyttningen är delvis innebär att du har stoppats (frigjorts) en eller flera, men inte alla, virtuella datorer i en tillgänglighetsgrupp. När du frigör en virtuell dator släpps associerade resurser. Starta om virtuella datorer i en delvis frigjord tillgänglighetsuppsättning är detsamma som att lägga till virtuella datorer i en befintlig tillgänglighetsuppsättning. Begäran om minnesallokering måste därför försökas på det ursprungliga klustret att värdar befintliga tillgänglighetsuppsättning som inte kanske har tillräcklig kapacitet.

### <a name="workaround"></a>Lösning

Stoppa (frigöra) alla virtuella datorer i samma tillgänglighetsuppsättning ange och sedan starta om var och en.
Stoppa: Klicka på resursgrupper > [resursgruppen] > resurser > [tillgänglighetsuppsättning] > virtuella datorer > [den virtuella datorn] > Stoppa.
När alla virtuella datorer har slutat, väljer den första virtuella datorn och klicka sedan på Starta.
Kontrollerar att att ett nytt försök för allokering körs och att ett nytt kluster kan väljas som har tillräckligt med kapacitet.

## <a name="restart-fully-stopped-deallocated-vms"></a>Starta om fullständigt stoppats (frigjorts) virtuella datorer

### <a name="cause"></a>Orsak

Fullständig flyttningen innebär att du har stoppats frigjorts () alla virtuella datorer i en tillgänglighetsuppsättning. Begäran om minnesallokering att starta om dessa virtuella datorer gäller alla kluster som har stöd för önskad storlek i region eller zonen. Ändra din begäran om minnesallokering per förslag i den här artikeln och försöka att öka sannolikheten att allokering lyckas. 

### <a name="workaround"></a>Lösning

Om du använder äldre virtuell dator-serie eller storlekar, till exempel Dv1, DSv1, Av1, D15v2 eller DS15v2, Överväg att övergå till nyare versioner. Se dessa rekommendationer för specifika VM-storlekar.
Om du inte har alternativet att använda en annan VM-storlek, försök att distribuera till en annan region inom samma geo. Mer information om tillgängliga VM-storlekar i varje region på https://aka.ms/azure-regions

Om du använder tillgänglighet zoner prova en annan zon för regionen som kan ha tillgänglig kapacitet för den begärda VM-storleken.

Om din begäran om minnesallokering är stor (mer än 500 kärnor) finns i följande avsnitt för att dela upp begäran till mindre distributioner.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Allokeringsfel för äldre VM-storlekar (Av1, Dv1, DSv1, D15v2, DS15v2 osv.)

Som vi Expandera Azure-infrastrukturen distribuera vi nyare maskinvara som har utformats för att stödja de senaste typerna av virtuella datorer. Några av de äldre serie virtuella datorerna körs inte på vår senaste generation infrastruktur. Därför kan kunder ibland uppstå Allokeringsfel för dessa äldre SKU: er. För att undvika det här problemet kan vi rekommenderar att kunder som använder äldre serien virtuella datorer ska du överväga att flytta till motsvarande senare virtuella datorer per följande rekommendationer: dessa virtuella datorer som är optimerade för den senaste maskinvaran och kan du dra nytta av bättre priser och prestanda. 

|Äldre VM-serien eller storlek|Rekommenderade nyare VM-serien eller storlek|Mer information|
|----------------------|----------------------------|--------------------|
|Av1-serien|[Av2-serien](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 eller DSv1-serien (D1 till D5)|[Dv3 eller DSv3-serien](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 eller DSv1-serien (D11 till D14)|[Ev3 eller ESv3-serien](../articles/virtual-machines/windows/sizes-memory.md#esv3-series-sup1sup)|
|D15v2 eller DS15v2|Om du använder theResource Manager-distributionsmodellen för att kunna dra nytta av större VM-storlekar, Överväg att övergå till D16v3/DS16v3 eller D32v3/DS32v3. Dessa är avsedd att köras på den senaste generation maskinvaran. Om du använder Resource Manager-distributionsmodellen för att kontrollera din VM-instans är isolerad till maskinvara som är dedikerad till en kund, Överväg att övergå till de nya isolerade storlekar på VM, E64i_v3 eller E64is_v3, som är avsedd att köras på den senaste generation maskinvaran. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Allokeringsfel för stora distributioner (mer än 500 kärnor)

Minska antalet instanser av den begärda VM-storleken och försök sedan distributionen. Dessutom för större distributioner du kanske vill utvärdera [skalningsuppsättningar i virtuella Azure-datorn](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Antal VM-instanser kan automatiskt öka eller minska som svar på begäran eller ett definierat schema och du har en större möjlighet att allokering lyckas eftersom distributioner går att sprida över flera kluster. 

## <a name="background-information"></a>Bakgrundsinformation
### <a name="how-allocation-works"></a>Så här fungerar allokering
Servrarna i Azure-datacenter partitioneras i kluster. Normalt utförs en begäran om minnesallokering i flera kluster, men det är möjligt att vissa restriktioner från begäran om minnesallokering tvingar Azure-plattformen att försöka genomföra begäran i endast ett kluster. I den här artikeln ska vi referera till den här som ”fäst på ett kluster”. Bild 1 nedan visar skiftläget för en normal allokering som görs i flera kluster. Figur 2 visar i fallet med en allokering som har fäst på klustret 2 eftersom det är där den befintliga uppsättningen Cloud Service CS_1 eller tillgänglighet körs.
![Allokering av Diagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Varför Allokeringsfel inträffa
När en begäran om minnesallokering är fäst på ett kluster, finns en högre risk för att hitta lediga resurser eftersom tillgängliga resurspoolen är mindre inte. Dessutom misslyckas din begäran även om klustret har lediga resurser om din begäran om minnesallokering är fäst på ett kluster men typ av resurs som du har begärt stöds inte av klustret. Följande Diagram 3 illustrerar de fall där en fast allokering misslyckas eftersom endast kandidat klustret inte har frigöra resurser. Bild 4 visar de fall där en fast allokering misslyckas eftersom endast kandidat kluster inte stöder den begärda VM-storleken, även om klustret har lediga resurser.

![Fäst Allokeringsfel](./media/virtual-machines-common-allocation-failure/Allocation2.png)


