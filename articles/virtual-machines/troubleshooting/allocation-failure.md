---
title: Felsöka allokeringsfel för Azure VM | Microsoft-dokument
description: Felsöka allokeringsfel när du skapar, startar om eller ändrar storlek på en virtuell dator i Azure
services: virtual-machines
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 04/13/2018
ms.author: cjiang
ms.openlocfilehash: b4750ad9fdfa214aa4d7b6a0355c319e7eb1d9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484408"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Felsöka allokeringsfel när du skapar, startar om eller ändrar storlek på virtuella datorer i Azure

När du skapar en virtuell dator (VM), omstart stoppas (deallocated) virtuella datorer, eller ändra storlek på en virtuell dator, microsoft Azure allokerar beräkningsresurser till din prenumeration. Vi investerar kontinuerligt i ytterligare infrastruktur och funktioner för att se till att vi alltid har alla VM-typer tillgängliga för att stödja kundernas efterfrågan. Du kan dock ibland uppleva resursallokeringsfel på grund av aldrig tidigare skådad tillväxt i efterfrågan på Azure-tjänster i specifika regioner. Det här problemet kan uppstå när du försöker skapa eller starta virtuella datorer i en region medan de virtuella datorerna visar följande felkod och meddelande:

**Felkod**: AllokeringMissala eller ZonalAllocationFailed

**Felmeddelande:**"Allokeringen misslyckades. Vi har inte tillräcklig kapacitet för den begärda vm-storleken i den här regionen. Läs mer om att öka sannolikheten för allokeringsframgång på https:\//aka.ms/allocation-guidance"

I den här artikeln förklaras orsakerna till några av de vanliga allokeringsfelen och föreslår möjliga åtgärder.

Om ditt Azure-problem inte tas upp i den här artikeln besöker du [Azure-forumen på MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan skicka ditt problem på @AzureSupport dessa forum eller på Twitter. Du kan också lämna in en Azure-supportbegäran genom att välja Få support på [Azure-supportwebbplatsen.](https://azure.microsoft.com/support/options/)

Tills önskad VM-typ är tillgänglig i önskad region rekommenderar vi kunder som stöter på distributionsproblem att betrakta vägledningen i följande tabell som en tillfällig lösning. 

Identifiera det scenario som bäst matchar ditt ärende och försök sedan igen allokeringsbegäran med hjälp av motsvarande föreslagna lösning för att öka sannolikheten för att allokeringen ska lyckas. Du kan också försöka igen senare. Detta beror på att tillräckligt med resurser kan ha frigjorts i klustret, regionen eller zonen för att tillgodose din begäran. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Ändra storlek på en virtuell dator eller lägga till virtuella datorer i en befintlig tillgänglighetsuppsättning

### <a name="cause"></a>Orsak

En begäran om att ändra storlek på en virtuell dator eller lägga till en virtuell dator i en befintlig tillgänglighetsuppsättning måste provas i det ursprungliga klustret som är värd för den befintliga tillgänglighetsuppsättningen. Den begärda vm-storleken stöds av klustret, men klustret kanske inte har tillräcklig kapacitet. 

### <a name="workaround"></a>Lösning

Om den virtuella datorn kan ingå i en annan tillgänglighetsuppsättning skapar du en virtuell dator i en annan tillgänglighetsuppsättning (i samma region). Den här nya virtuella datorn kan sedan läggas till i samma virtuella nätverk.

Stoppa (deallocate) alla virtuella datorer i samma tillgänglighetsuppsättning och starta sedan om var och en.
Så här stoppar du: Klicka på Resursgrupper > [din resursgrupp] > resurser > [din tillgänglighetsuppsättning] > virtuella datorer > [din virtuella dator] > Stopp.
När alla virtuella datorer har stannat markerar du den första virtuella datorn och klickar sedan på Start.
Det här steget säkerställer att ett nytt allokeringsförsök körs och att ett nytt kluster kan väljas som har tillräcklig kapacitet.

## <a name="restart-partially-stopped-deallocated-vms"></a>Starta om delvis stoppade (frigjorda) virtuella datorer

### <a name="cause"></a>Orsak

Partiell deallocation innebär att du har stoppat (deallocated) en eller flera, men inte alla, virtuella datorer i en tillgänglighetsuppsättning. När du frigör en virtuell dator frigörs de associerade resurserna. Att starta om virtuella datorer i en delvis avtalad tillgänglighetsuppsättning är samma sak som att lägga till virtuella datorer i en befintlig tillgänglighetsuppsättning. Därför måste allokeringsbegäran prövas i det ursprungliga klustret som är värd för den befintliga tillgänglighetsuppsättningen som kanske inte har tillräcklig kapacitet.

### <a name="workaround"></a>Lösning

Stoppa (deallocate) alla virtuella datorer i samma tillgänglighetsuppsättning och starta sedan om var och en.
Så här stoppar du: Klicka på Resursgrupper > [din resursgrupp] > resurser > [din tillgänglighetsuppsättning] > virtuella datorer > [din virtuella dator] > Stopp.
När alla virtuella datorer har stannat markerar du den första virtuella datorn och klickar sedan på Start.
Detta kommer att se till att ett nytt allokeringsförsök körs och att ett nytt kluster kan väljas som har tillräcklig kapacitet.

## <a name="restart-fully-stopped-deallocated-vms"></a>Starta om fullständigt stoppade (frigjorda) virtuella datorer

### <a name="cause"></a>Orsak

Fullständig deallocation innebär att du har stoppat (deallocated) alla virtuella datorer i en tillgänglighetsuppsättning. Allokeringsbegäran för att starta om dessa virtuella datorer kommer att inriktas på alla kluster som stöder önskad storlek inom regionen eller zonen. Ändra allokeringsbegäran enligt förslagen i den här artikeln och försök igen för att förbättra risken för att allokeringen lyckas. 

### <a name="workaround"></a>Lösning

Om du använder äldre VM-serier eller storlekar, till exempel Dv1, DSv1, Av1, D15v2 eller DS15v2, kan du överväga att flytta till nyare versioner. Se dessa rekommendationer för specifika vm-storlekar.
Om du inte har möjlighet att använda en annan vm-storlek kan du prova att distribuera till en annan region inom samma geo. Mer information om tillgängliga vm-storlekar i varje region finns påhttps://aka.ms/azure-regions

Om du använder tillgänglighetszoner kan du prova en annan zon inom regionen som kan ha tillgänglig kapacitet för den begärda vm-storleken.

Om din allokeringsbegäran är stor (mer än 500 kärnor) läser du vägledningen i följande avsnitt för att dela upp begäran i mindre distributioner.

Prova [att distribuera om den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/redeploy-to-new-node-windows). Omfördelning av den virtuella datorn allokerar den virtuella datorn till ett nytt kluster inom regionen.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Allokeringsfel för äldre VM-storlekar (Av1, Dv1, DSv1, D15v2, DS15v2 osv.)

När vi utökar Azure-infrastrukturen distribuerar vi nygenereringsmaskinvara som är utformad för att stödja de senaste typerna för virtuella datorer. Några av de äldre serie virtuella datorerna körs inte på vår senaste generations infrastruktur. Därför kan kunder ibland uppleva allokeringsfel för dessa äldre SKU:er. För att undvika detta problem uppmuntrar vi kunder som använder virtuella datorer i äldre serier att överväga att flytta till motsvarande nyare virtuella datorer enligt följande rekommendationer: Dessa virtuella datorer är optimerade för den senaste maskinvaran och låter dig dra nytta av bättre prissättning och prestanda. 

|Äldre VM-serie/storlek|Rekommenderade nyare VM-serie/storlek|Mer information|
|----------------------|----------------------------|--------------------|
|Av1-serien|[Av2-serien](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1- eller DSv1-serien (D1 till D5)|[Dv3- eller DSv3-serien](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1- eller DSv1-serien (D11 till D14)|[Ev3- eller ESv3-serien](../ev3-esv3-series.md)|
|D15v2 eller DS15v2|Om du använder distributionsmodellen För distribution av Distributionshanteraren för att dra nytta av de större vm-storlekarna kan du överväga att flytta till D16v3/DS16v3 eller D32v3/DS32v3. Dessa är utformade för att köras på den senaste generationens maskinvara. Om du använder Resurshanterarens distributionsmodell för att se till att din VM-instans är isolerad till maskinvara som är dedikerad till en enskild kund kan du överväga att flytta till de nya isolerade vm-storlekarna, E64i_v3 eller E64is_v3, som är utformade för att köras på den senaste generationens maskinvara. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Allokeringsfel för stora distributioner (fler än 500 kärnor)

Minska antalet instanser av den begärda vm-storleken och försök sedan igen distributionsåtgärden. Dessutom, för större distributioner, kanske du vill utvärdera [Azure virtuella dator skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Antalet VM-instanser kan automatiskt öka eller minska som svar på efterfrågan eller ett definierat schema, och du har större chans att allokeringen lyckas eftersom distributionerna kan spridas över flera kluster. 

## <a name="background-information"></a>Bakgrundsinformation
### <a name="how-allocation-works"></a>Så här fungerar allokeringen
Servrarna i Azure-datacenter partitioneras i kluster. Normalt utförs en begäran om minnesallokering i flera kluster, men det är möjligt att vissa restriktioner från begäran om minnesallokering tvingar Azure-plattformen att försöka genomföra begäran i endast ett kluster. I den här artikeln refererar vi till detta som "fäst i ett kluster". Diagram 1 nedan illustrerar fallet med en normal allokering som görs i flera kluster. Diagram 2 illustrerar fallet med en allokering som är fäst vid Kluster 2 eftersom det är där den befintliga molntjänsten CS_1 eller tillgänglighetsuppsättningen finns.
![Fördelningsdiagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Varför allokeringsfel inträffar
När en allokeringsbegäran är fäst i ett kluster finns det större risk att det inte går att hitta lediga resurser eftersom den tillgängliga resurspoolen är mindre. Om allokeringsbegäran är fäst i ett kluster men den typ av resurs som du har begärt inte stöds av klustret, misslyckas begäran även om klustret har lediga resurser. Följande diagram 3 illustrerar det fall där en fäst allokering misslyckas eftersom det enda kandidatklustret inte har lediga resurser. Diagram 4 illustrerar det fall där en fäst allokering misslyckas eftersom det enda kandidatklustret inte stöder den begärda vm-storleken, även om klustret har lediga resurser.

![Fel vid fäst allokering](./media/virtual-machines-common-allocation-failure/Allocation2.png)


