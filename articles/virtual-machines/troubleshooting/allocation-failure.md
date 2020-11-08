---
title: Felsöka allokeringsfel för Azure VM | Microsoft Docs
description: Felsök allokeringsfel när du skapar, startar om eller ändrar storlek på en virtuell dator i Azure
services: virtual-machines
documentationcenter: ''
author: DavidCBerry13
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: daberry
ms.openlocfilehash: 79bc043a991404a3ee9da954b9639bf1a41f2c51
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365881"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Felsöka allokeringsfel när du skapar, startar om eller ändrar storlek på virtuella datorer i Azure

När du skapar en virtuell dator (VM) måste du starta om stoppade (Frigjord) virtuella datorer eller ändra storlek på en virtuell dator, Microsoft Azure allokerar beräknings resurser till din prenumeration. Vi går ständigt att investera i ytterligare infrastruktur och funktioner för att se till att vi alltid har alla VM-typer som är tillgängliga för att stödja kund behovet. Du kan dock ibland uppleva resurs tilldelnings felen på grund av oöverträffad tillväxt i efter frågan på Azure-tjänster i vissa regioner. Det här problemet kan uppstå när du försöker skapa eller starta virtuella datorer i en region medan de virtuella datorerna visar följande felkod och meddelande:

**Felkod** : AllocationFailed eller ZonalAllocationFailed

**Fel meddelande** : "allokeringen misslyckades. Det finns inte tillräckligt med kapacitet för den begärda virtuella dator storleken i den här regionen. Läs mer om hur du förbättrar sannolikheten för att tilldelningen lyckades vid https: \/ /aka.MS/Allocation-Guidance "

> [!NOTE]
> Om du felsöker en skalnings uppsättning för virtuella datorer (VMSS) är processen densamma som en standard-VM. Lös problemet genom att följa anvisningarna i den här artikeln.
> 
>**Fel meddelande** : "allokeringen misslyckades. Om du försöker lägga till en ny virtuell dator i en skalnings uppsättning för virtuella datorer med en enda placerings grupp eller uppdatera/ändra storlek på en befintlig virtuell dator i en skalnings uppsättning för virtuella datorer med en enda placerings grupp, Observera att en sådan allokering är begränsad till ett enda kluster och att klustret har slut på kapacitet. Läs mer om hur du förbättrar sannolikheten för att tilldelningen lyckades vid http: \/ /aka.MS/Allocation-Guidance. "

I den här artikeln beskrivs orsakerna till några vanliga allokeringsfel och förslag på möjliga lösningar.

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du gå till [Azure-forumen på MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller @AzureSupport på Twitter. Du kan också skicka en support förfrågan till Azure genom att välja få support på [support](https://azure.microsoft.com/support/options/) webbplatsen för Azure.

Tills din önskade VM-typ är tillgänglig i din önskade region, så aviserar vi kunder som har problem med distributions problem för att kunna ta del av rikt linjerna i följande tabell som en tillfällig lösning. 

Identifiera scenariot som bäst matchar ditt ärende och försök sedan att utföra allokeringsprocessen igen med hjälp av motsvarande rekommenderade lösning för att öka sannolikheten för att tilldelningen lyckades. Alternativt kan du alltid försöka igen senare. Det beror på att tillräckligt med resurser kan ha frigjorts i klustret, regionen eller zonen för att kunna hantera din begäran. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Ändra storlek på en virtuell dator eller lägga till virtuella datorer i en befintlig tillgänglighetsuppsättning

### <a name="cause"></a>Orsak

En begäran om att ändra storlek på en virtuell dator eller lägga till en virtuell dator i en befintlig tillgänglighets uppsättning måste göras på det ursprungliga klustret som är värd för den befintliga tillgänglighets uppsättningen. Den begärda virtuella dator storleken stöds av klustret, men klustret har kanske för närvarande inte tillräckligt med kapacitet. 

### <a name="workaround"></a>Lösning

Om den virtuella datorn kan ingå i en annan tillgänglighets uppsättning skapar du en virtuell dator i en annan tillgänglighets uppsättning (i samma region). Den nya virtuella datorn kan sedan läggas till i samma virtuella nätverk.

Stoppa (frigör) alla virtuella datorer i samma tillgänglighets uppsättning och starta sedan om var och en.
Stoppa: Klicka på resurs grupper > [resurs gruppen] > resurser > [din tillgänglighets uppsättning] > Virtual Machines > [den virtuella datorn] > stoppa.
När alla virtuella datorer har stoppats väljer du den första virtuella datorn och klickar sedan på Starta.
Det här steget ser till att ett nytt tilldelnings försök körs och att ett nytt kluster kan väljas som har tillräckligt med kapacitet.

## <a name="restart-partially-stopped-deallocated-vms"></a>Starta om delvis stoppade (frigjorda) virtuella datorer

### <a name="cause"></a>Orsak

Partiell avallokering innebär att du har stoppat (Frigjord) en eller flera, men inte alla, virtuella datorer i en tillgänglighets uppsättning. När du frigör en virtuell dator frigörs de tillhör ande resurserna. Att starta om virtuella datorer i en delvis frigjord tillgänglighets uppsättning är detsamma som att lägga till virtuella datorer i en befintlig tillgänglighets uppsättning. Därför måste tilldelnings förfrågan göras på det ursprungliga klustret som är värd för den befintliga tillgänglighets uppsättningen som kanske inte har tillräckligt med kapacitet.

### <a name="workaround"></a>Lösning

Stoppa (frigör) alla virtuella datorer i samma tillgänglighets uppsättning och starta sedan om var och en.
Stoppa: Klicka på resurs grupper > [resurs gruppen] > resurser > [din tillgänglighets uppsättning] > Virtual Machines > [den virtuella datorn] > stoppa.
När alla virtuella datorer har stoppats väljer du den första virtuella datorn och klickar sedan på Starta.
Detta ser till att ett nytt tilldelnings försök körs och att ett nytt kluster kan väljas som har tillräckligt med kapacitet.

## <a name="restart-fully-stopped-deallocated-vms"></a>Starta om fullständigt stoppade (frigjorda) virtuella datorer

### <a name="cause"></a>Orsak

Fullständig avallokering innebär att du har stoppat (frigjort) alla virtuella datorer i en tillgänglighets uppsättning. Tilldelnings förfrågan för att starta om de virtuella datorerna är riktad mot alla kluster som har stöd för önskad storlek inom regionen eller zonen. Ändra din begäran om tilldelning enligt förslagen i den här artikeln och gör om begäran för att förbättra risken för att tilldelningen lyckades. 

### <a name="workaround"></a>Lösning

Om du använder äldre serien eller storlekar för virtuella datorer, till exempel DV1, DSv1, AV1, D15v2 eller DS15v2, bör du överväga att flytta till nyare versioner. Se de här rekommendationerna för enskilda VM-storlekar.
Om du inte har alternativet att använda en annan VM-storlek kan du prova att distribuera till en annan region inom samma geografiska område. Mer information om tillgängliga VM-storlekar i varje region finns på https://aka.ms/azure-regions

Om du använder tillgänglighets zoner kan du prova en annan zon inom den region som kan ha tillgänglig kapacitet för den begärda virtuella dator storleken.

Om din tilldelnings förfrågan är stor (mer än 500 kärnor) kan du läsa mer i anvisningarna i följande avsnitt för att dela upp begäran i mindre distributioner.

Försök [att distribuera om den virtuella datorn](./redeploy-to-new-node-windows.md). Omdistribution av den virtuella datorn allokerar den virtuella datorn till ett nytt kluster inom regionen.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Allokeringsfel för äldre VM-storlekar (Av1, Dv1, DSv1, D15v2, DS15v2 osv.)

När vi utökar Azure-infrastrukturen distribuerar vi nyare generations maskin vara som är utformad för att stödja de senaste typerna av virtuella datorer. Några av de äldre virtuella datorerna i serien körs inte på vår senaste generations infrastruktur. Därför kan kunder ibland uppleva allokeringsfel för dessa äldre SKU: er. För att undvika det här problemet uppmuntrar vi kunder som använder äldre datorer i serien att överväga att flytta till motsvarande nyare virtuella datorer enligt följande rekommendationer: de virtuella datorerna är optimerade för den senaste maskin varan och gör att du kan dra nytta av bättre priser och prestanda. 

|Äldre VM-serien/storlek|Rekommenderad nyare VM-serien/storlek|Mer information|
|----------------------|----------------------------|--------------------|
|AV1-serien|[Av2-serien](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|DV1 eller DSv1-serien (D1 till D5)|[Dv3 eller DSv3-serien](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|DV1 eller DSv1-serien (D11 till D14)|[Ev3 eller ESv3-serien](../ev3-esv3-series.md)|
|D15v2 eller DS15v2|Om du använder distributions modellen för theResource Manager för att dra nytta av de större VM-storlekarna bör du överväga att flytta till D16v3/DS16v3 eller D32v3/DS32v3. De är utformade för att köras på den senaste generationens maskin vara. Om du använder distributions modellen för Resource Manager för att se till att den virtuella dator instansen är isolerad till maskin vara som är dedikerad till en enda kund, bör du överväga att flytta till de nya isolerade VM-storlekarna, E64i_v3 eller E64is_v3, som är avsedda att köras på den senaste generationen |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Allokeringsfel för stora distributioner (fler än 500 kärnor)

Minska antalet instanser av den begärda VM-storleken och försök sedan att distribuera igen. För större distributioner kan du dessutom vilja utvärdera [skalnings uppsättningar för virtuella Azure-datorer](../../virtual-machine-scale-sets/index.yml). Antalet virtuella dator instanser kan öka eller minska automatiskt som svar på efter frågan eller ett definierat schema, och du har större chans att fördelningen lyckas eftersom distributionerna kan spridas över flera kluster. 

## <a name="background-information"></a>Bakgrunds information
### <a name="how-allocation-works"></a>Så här fungerar allokering
Servrarna i Azure-datacenter partitioneras i kluster. Normalt utförs en begäran om minnesallokering i flera kluster, men det är möjligt att vissa restriktioner från begäran om minnesallokering tvingar Azure-plattformen att försöka genomföra begäran i endast ett kluster. I den här artikeln kommer vi att se detta som "fäst i ett kluster". Diagram 1 nedan illustrerar fallet med en normal allokering som görs i flera kluster. Diagram 2 visar fallet för en allokering som är fäst på kluster 2, eftersom den befintliga moln tjänsten CS_1 eller tillgänglighets uppsättningen finns.
![Tilldelnings diagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Varför allokeringsfel inträffar
När en tilldelnings förfrågan fästs i ett kluster, finns det en högre chans att det inte går att hitta kostnads fria resurser eftersom den tillgängliga resurspoolen är mindre. Om din tilldelnings förfrågan fästs i ett kluster, men den typ av resurs som du har begärt inte stöds av det klustret, kommer begäran att Miss Miss förväntas även om klustret har kostnads fria resurser. Följande diagram 3 visar det fall där en fäst allokering Miss lyckas eftersom det enda kandidat klustret inte har några lediga resurser. Diagram 4 visar det fall där en fäst allokering Miss lyckas eftersom det enda kandidat klustret inte stöder den begärda virtuella dator storleken, även om klustret har kostnads fria resurser.

![Det har fästs ett allokeringsfel](./media/virtual-machines-common-allocation-failure/Allocation2.png)
