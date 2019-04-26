---
title: Felsöka Allokeringsfel för virtuella Azure-datorer | Microsoft Docs
description: Felsök Allokeringsfel när du skapar, startar om eller ändra storlek på en virtuell dator i Azure
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
ms.openlocfilehash: 72fbdbcfcd94dd41a67bb81314802dd7314ae463
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505827"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Felsök Allokeringsfel när du skapar, startar om eller ändra storlek på virtuella datorer i Azure

När du skapar en virtuell dator (VM), starta om Stoppad (frigjord) virtuella datorer eller ändra storlek på en virtuell dator, allokerar Microsoft Azure beräkningsresurser till din prenumeration. Vi kontinuerligt investera i ytterligare infrastruktur och vilka funktioner du vill se till att vi alltid har alla VM-typer för att stödja kundernas efterfrågan. Det kan ibland uppstå Allokeringsfel för resursen på grund av en oöverträffad ökad efterfrågan på Azure-tjänster i vissa regioner. Det här problemet kan uppstå när du försöker skapa eller starta virtuella datorer i en region medan de virtuella datorerna visas följande felkod och meddelandet:

**Felkod**: AllocationFailed eller ZonalAllocationFailed

**Felmeddelande**: ”Tilldelningen misslyckades. Vi har inte tillräckligt med kapacitet för den begärda VM-storleken i den här regionen. Läs mer om att förbättra sannolikheten för en lyckad allokering på https:\//aka.ms/allocation-guidance ”

Den här artikeln förklarar orsakerna till några av de vanliga Allokeringsfel och föreslår åtgärder.

Om din Azure-problem inte behandlas i den här artikeln, gå till den [Azure-forum på MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera problemet på dessa forum eller @AzureSupport på Twitter. Även, du kan skicka en supportförfrågan för Azure genom att välja Get-stöd på den [Azure-supporten](https://azure.microsoft.com/support/options/) plats.

Vi rekommenderar att kunder som stöter på problem med distribution att överväga riktlinjerna i tabellen nedan som en tillfällig lösning tills din önskade typ av virtuell dator är tillgänglig i din önskade region. 

Identifiera scenario som bäst passar ditt ärende och försök sedan begäran om minnesallokering med motsvarande Föreslagen lösning för att öka sannolikheten för allokering. Du kan också du alltid försöka igen senare. Det beror på att tillräckligt med resurser kan ha frigjorts i klustret, region eller zon för din begäran. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Ändra storlek på en virtuell dator eller lägga till virtuella datorer i en befintlig tillgänglighetsuppsättning

### <a name="cause"></a>Orsak

Ange en begäran om att ändra storlek på en virtuell dator eller lägga till en virtuell dator till en befintlig tillgänglighetsuppsättning måste provas på det ursprungliga klustret som är värd för den befintliga tillgängligheten. Den begärda storleken som stöds av klustret, men klustret kan för närvarande inte har tillräckligt med kapacitet. 

### <a name="workaround"></a>Lösning

Om den virtuella datorn kan vara en del av en annan tillgänglighetsuppsättning, kan du skapa en virtuell dator i en annan tillgänglighetsuppsättning (i samma region). Den här nya virtuella datorn kan sedan läggas till samma virtuella nätverk.

Stoppa (frigöra) alla virtuella datorer i samma tillgänglighetsuppsättning ange och sedan starta om var och en.
Sluta: Klicka på resursgrupper > [resursgruppen] > resurser > [tillgänglighetsuppsättningen] > VM > [din virtuella dator] > Stoppa.
När alla virtuella datorer har slutat, Välj den första virtuella datorn och klicka sedan på Starta.
Det här steget ser till att ett nytt försök för allokering körs och att ett nytt kluster kan väljas som har tillräckligt med kapacitet.

## <a name="restart-partially-stopped-deallocated-vms"></a>Starta om delvis stoppade (frigjorda) virtuella datorer

### <a name="cause"></a>Orsak

Flyttningen är delvis innebär att du stoppat (frigjort) en eller flera, men inte alla, virtuella datorer i en tillgänglighetsuppsättning har angetts. När du frigöra en virtuell dator, släpps de associerade resurserna. Starta om virtuella datorer i en delvis frigjort tillgänglighetsuppsättning är detsamma som att lägga till virtuella datorer i en befintlig tillgänglighetsuppsättning. Begäran om minnesallokering måste därför provas på det ursprungliga klustret att värdarna som den befintliga tillgänglighetsuppsättning som inte kanske har tillräckligt med kapacitet.

### <a name="workaround"></a>Lösning

Stoppa (frigöra) alla virtuella datorer i samma tillgänglighetsuppsättning ange och sedan starta om var och en.
Sluta: Klicka på resursgrupper > [resursgruppen] > resurser > [tillgänglighetsuppsättningen] > VM > [din virtuella dator] > Stoppa.
När alla virtuella datorer har slutat, Välj den första virtuella datorn och klicka sedan på Starta.
Det säkerställer att ett nytt försök för allokering körs och att ett nytt kluster kan väljas som har tillräckligt med kapacitet.

## <a name="restart-fully-stopped-deallocated-vms"></a>Starta om fullständigt stoppade (frigjorda) virtuella datorer

### <a name="cause"></a>Orsak

Flyttningen är fullständig innebär att du stoppat frigjord () alla virtuella datorer i en tillgänglighetsuppsättning. Begäran om minnesallokering att starta om dessa virtuella datorer gäller för alla kluster som har stöd för önskad storlek inom den region eller zon. Ändra din begäran om minnesallokering per förslagen i den här artikeln och försöka att förbättra risken för allokering. 

### <a name="workaround"></a>Lösning

Om du använder äldre VM-serien eller storlekar, till exempel Dv1, DSv1, Av1, D15v2 eller DS15v2, Överväg att flytta till nyare versioner. Se dessa rekommendationer för specifika VM-storlekar.
Om du inte har alternativet att använda en annan VM-storlek, försök att distribuera till en annan region inom samma geografiska område. Mer information om de tillgängliga storlekarna i varje region på https://aka.ms/azure-regions

Om du använder tillgänglighetszoner, prova en annan zon i den region som kan ha tillgänglig kapacitet för den begärda VM-storleken.

Om din begäran om minnesallokering är stor (fler än 500 kärnor), finns i riktlinjerna i följande avsnitt för att dela upp begäran i mindre distributioner.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Allokeringsfel för äldre VM-storlekar (Av1, Dv1, DSv1, D15v2, DS15v2 osv.)

Eftersom vi har expanderat Azure-infrastrukturen kan distribuera vi nyare maskinvara som har utformats för att stödja de senaste typerna av virtuella datorer. Några av de virtuella datorerna äldre serien körs inte på vår senaste generation infrastruktur. Därför kan kunder ibland uppstå Allokeringsfel för dessa äldre SKU: er. För att undvika det här problemet kan rekommenderar vi att kunder som använder äldre serien virtuella datorer för att flytta till motsvarande nyare virtuella datorer per följande rekommendationer: Dessa virtuella datorer är optimerade för den senaste maskinvaran och kan du dra nytta av bättre pris- och prestanda. 

|Äldre VM-serien eller storlek|Rekommenderade nyare VM-serien eller storlek|Mer information|
|----------------------|----------------------------|--------------------|
|Av1-serien|[Av2-serien](../windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 eller DSv1-serien (D1 till D5)|[Dv3 eller DSv3-serien](../windows/sizes-general.md#dsv3-series-1)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 eller DSv1-serien (D11 till D14)|[Ev3 eller ESv3-serien](../windows/sizes-memory.md#ev3-series)|
|D15v2 eller DS15v2|Om du använder theResource Manager-distributionsmodellen för att kunna dra nytta av större storlekar för Virtuella datorer kan du överväga att flytta till D16v3/DS16v3 eller D32v3/DS32v3. Dessa är avsedda att köras på den senaste generation maskinvaran. Överväg att övergå till de nya isolerade VM-storlekar, E64i_v3 eller E64is_v3, som är avsedd att köras på den senaste generation maskinvaran om du använder Resource Manager-distributionsmodellen för att kontrollera att VM-instansen är isolerad till maskinvara som är dedikerad till en enda kund. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Allokeringsfel för stora distributioner (fler än 500 kärnor)

Minska antalet instanser av den begärda VM-storleken och försök sedan distributionen igen. Dessutom för större distributioner kan du utvärdera [Azure VM-skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Antal VM-instanser kan automatiskt öka eller minska som svar på begäran eller ett definierat schema och du har en större risk för en lyckad allokering eftersom distributioner kan spridas till flera kluster. 

## <a name="background-information"></a>Grundläggande information
### <a name="how-allocation-works"></a>Så här fungerar allokering
Servrarna i Azure-datacenter partitioneras i kluster. Normalt utförs en begäran om minnesallokering i flera kluster, men det är möjligt att vissa restriktioner från begäran om minnesallokering tvingar Azure-plattformen att försöka genomföra begäran i endast ett kluster. I den här artikeln ska ska vi referera till detta som ”Fäst till ett kluster”. Diagram 1 nedan illustrerar i fallet med en normal allokering som görs i flera kluster. Diagram 2 visas i fallet med en allokering som har fästs till klustret 2 eftersom det är där den befintliga uppsättningen Cloud Service CS_1 eller tillgänglighet finns.
![Allokering Diagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Varför Allokeringsfel inträffa
När en begäran om minnesallokering är fäst till ett kluster, finns en högre risk för att hitta kostnadsfria resurser eftersom tillgängliga resurspoolen är mindre inte. Dessutom, om din begäran om minnesallokering fästs till ett kluster, men typ av resurs som du har begärt stöds inte av klustret, din begäran misslyckas även om klustret har kostnadsfria resurser. Följande Diagram 3 illustrerar det fall där en fäst allokering misslyckas eftersom endast kandidat klustret inte har kostnadsfria resurser. Bild 4 visar fall där en fäst allokering misslyckas eftersom endast kandidat klustret inte stöder den begärda storleken trots att klustret har kostnadsfria resurser.

![Fästa Allokeringsfel](./media/virtual-machines-common-allocation-failure/Allocation2.png)


