---
title: Tjänst administration i portalen
titleSuffix: Azure Cognitive Search
description: Hantera en Azure Kognitiv sökning-tjänst, en värd för moln Sök tjänst på Microsoft Azure, med hjälp av Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282931"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Tjänst administration för Azure Kognitiv sökning i Azure Portal
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0) -> 

Azure Kognitiv sökning är en fullständigt hanterad, molnbaserad Sök tjänst som används för att skapa en omfattande Sök upplevelse i anpassade appar. Den här artikeln beskriver de tjänst administrations uppgifter som du kan utföra i [Azure Portal](https://portal.azure.com) för en Sök tjänst som du redan har etablerad. Tjänst administration är lätt att utforma, begränsad till följande uppgifter:

> [!div class="checklist"]
> * Hantera åtkomst till de *API-nycklar* som används för Läs-eller skriv åtkomst till din tjänst.
> * Justera tjänst kapaciteten genom att ändra allokeringen av partitioner och repliker.
> * Övervaka resursanvändningen, i förhållande till de maximala gränserna för din tjänst nivå.

Observera att *uppgraderingen* inte visas som en administrativ uppgift. Eftersom resurser allokeras när tjänsten är etablerade kräver en ny tjänst att flytta till en annan nivå. Mer information finns i [skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md).

Du kan övervaka frågans volym och andra mått och använda dessa insikter för att justera tjänsten för snabbare svars tider. Mer information finns i [övervaka användning och fråga mått](search-monitor-usage.md) och [prestanda och optimering](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Administratörs behörighet
Etablering eller inaktive ring av tjänsten kan utföras av en administratör för Azure-prenumeration eller tillsammans med en administratör.

I en tjänst har alla som har åtkomst till tjänst-URL: en och en Admin API-nyckel Läs-och Skriv behörighet till tjänsten. Läs-och skriv åtkomst ger möjlighet att lägga till, ta bort eller ändra Server objekt, inklusive API-nycklar, index, indexerare, data källor, scheman och roll tilldelningar som implementerade via [RBAC-definierade roller](search-security-rbac.md).

All användar interaktion med Azure Kognitiv sökning är inom något av dessa lägen: Läs-och Skriv behörighet till tjänsten (administratörs behörighet) eller skrivskyddad åtkomst till tjänsten (fråge rättigheter). Mer information finns i [Hantera API-nycklar](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Loggning och system information
Azure Kognitiv sökning visar inte loggfiler för en enskild tjänst, antingen via portalen eller programmerings gränssnitt. På Basic-nivån och över, övervakar Microsoft alla Azure Kognitiv sökning Services för 99,9% tillgänglighet per service nivå avtal (SLA). Om tjänsten är långsam eller begär data flöde hamnar under SLA-tröskelvärden granskar support team de loggfiler som är tillgängliga för dem och löser problemet.

När det gäller allmän information om tjänsten kan du hämta information på följande sätt:

* I portalen, på instrument panelen för tjänsten, via aviseringar, egenskaper och status meddelanden.
* Använda [PowerShell](search-manage-powershell.md) eller [hanterings REST API](https://docs.microsoft.com/rest/api/searchmanagement/) för att [Hämta tjänst egenskaper](https://docs.microsoft.com/rest/api/searchmanagement/services)eller status för index resursanvändning.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Övervaka resursanvändning
I instrument panelen är resurs övervakning begränsad till den information som visas i instrument panelen för tjänsten och några mått som du kan hämta genom att fråga tjänsten. På instrument panelen för tjänsten i användnings avsnittet kan du snabbt avgöra om partitionernas resurs nivåer är lämpliga för ditt program. Du kan etablera externa resurser, till exempel Azure-övervakning, om du vill samla in och spara loggade händelser. Mer information finns i [övervakning av Azure-kognitiv sökning](search-monitor-usage.md).

Med hjälp av Sök tjänsten REST API kan du få ett antal dokument och index program mässigt: 

* [Hämta index statistik](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Räkna dokument](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Haveri beredskap och drift avbrott

Även om vi kan återanvända dina data ger Azure Kognitiv sökning inte omedelbar redundansväxling av tjänsten om det uppstår ett avbrott på klustret eller data Center nivån. Om ett kluster Miss lyckas i data centret kommer drift teamet att identifiera och arbeta med återställnings tjänsten. Du får stillestånds tid under tjänst återställningen, men du kan begära service krediter för att kompensera för att tjänsten inte är tillgänglig per [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Om kontinuerlig tjänst krävs vid oåterkalleliga haverier utanför Microsofts kontroll kan du [etablera en ytterligare tjänst](search-create-service-portal.md) i en annan region och implementera en strategi för geo-replikering för att säkerställa att index är helt redundanta för alla tjänster.

Kunder som använder [indexerare](search-indexer-overview.md) för att fylla i och uppdatera index kan hantera haveri beredskap via geo-/regionsspecifika indexerare som använder samma data källa. Två tjänster i olika regioner, som kör en indexerare, kan indexera samma data källa för att uppnå GEO-redundans. Om du indexerar från data källor som också är geo-redundanta bör du vara medveten om att Azure Kognitiv sökning indexerare endast kan utföra stegvis indexering (sammanfoga uppdateringar från nya, ändrade eller borttagna dokument) från primära repliker. I en redundansväxling, se till att du pekar på indexeraren igen till den nya primära repliken. 

Om du inte använder indexerare använder du program koden för att skicka objekt och data till olika Sök tjänster parallellt. Mer information finns i [prestanda och optimering i Azure kognitiv sökning](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Eftersom Azure Kognitiv sökning inte är en primär data lagrings lösning ger vi inte en formell mekanism för säkerhets kopiering och återställning av självbetjäning. Du kan dock använda exempel koden **index-Backup-Restore** i den här [Azure kognitiv sökning .net-exempel lagrings platsen](https://github.com/Azure-Samples/azure-search-dotnet-samples) för att säkerhetskopiera index definitionen och ögonblicks bilden till en serie JSON-filer och sedan använda filerna för att återställa indexet, om det behövs. Det här verktyget kan också flytta index mellan tjänst nivåer.

Annars är din program kod som används för att skapa och fylla i ett index det här alternativet för återställning om du tar bort ett index av misstag. Om du vill återskapa ett index tar du bort det (förutsatt att det finns), återskapar indexet i tjänsten och läser in igen genom att hämta data från det primära data lagret.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Skala upp eller ned
Varje Sök tjänst börjar med minst en replik och en partition. Om du har registrerat dig för en [nivå som tillhandahåller dedikerade resurser](search-limits-quotas-capacity.md), klickar du på **skala** -panelen i instrument panelen för att justera resursanvändningen.

När du lägger till kapacitet via någon av resurserna använder tjänsten dem automatiskt. Ingen ytterligare åtgärd krävs för din del, men det finns en liten fördröjning innan den nya resursens påverkan realiseras. Det kan ta 15 minuter eller mer att etablera ytterligare resurser.

 ![][10]

### <a name="add-replicas"></a>Lägg till repliker
Att öka antalet frågor per sekund (frågor per sekund) eller att uppnå hög tillgänglighet görs genom att lägga till repliker. Varje replik har en kopia av ett index, så du kan lägga till en eller flera repliker översätts till ett mer index som är tillgängligt för att hantera förfrågningar om tjänst frågor. Det krävs minst 3 repliker för hög tillgänglighet (mer information finns i [kapacitets planering](search-capacity-planning.md) ).

En Sök tjänst som har fler repliker kan belastningsutjämna förfrågningar om begär anden över ett större antal index. Med tanke på en nivå av frågans volym kommer frågans data flöde att bli snabbare när det finns fler kopior av det index som är tillgängligt för att betjäna begäran. Om du upplever svars tid för frågor kan du förvänta en positiv inverkan på prestanda när de ytterligare replikerna är online.

Även om frågans data flöde går upp när du lägger till repliker, så är det inte exakt dubbelt eller tredubbla när du lägger till repliker till din tjänst. Alla Sök program omfattas av externa faktorer som kan impinge på frågans prestanda. Komplexa frågor och nätverks fördröjning är två faktorer som bidrar till variationer i svars tider för frågor.

### <a name="add-partitions"></a>Lägg till partitioner
De flesta tjänst program har ett inbyggt behov av fler repliker snarare än partitioner. I de fall där det krävs ett ökat antal dokument kan du lägga till partitioner om du har registrerat dig för standard tjänsten. Basic-nivån tillhandahåller inga ytterligare partitioner.

På standard-nivån läggs partitioner till i multipler av 12 (särskilt, 1, 2, 3, 4, 6 eller 12). Detta är en artefakt av horisontell partitionering. Ett index skapas i 12 Shards, som kan lagras på en partition eller vara jämnt indelat i 2, 3, 4, 6 eller 12 partitioner (en Shard per partition).

### <a name="remove-replicas"></a>Ta bort repliker
Efter perioder med hög fråga-volymer kan du använda skjutreglaget för att minska antalet repliker när Sök frågan läses in har normaliserats (till exempel efter att försäljningen är över). Det finns inga ytterligare steg som krävs för din del. Om du sänker antalet repliker överlämnas virtuella datorer i data centret. Dina frågor och data inmatnings åtgärder kommer nu att köras på färre virtuella datorer än tidigare. Minimi kravet är en replik.

### <a name="remove-partitions"></a>Ta bort partitioner
I motsats till att ta bort repliker, som inte kräver extra ansträngning på din sida, kan du ha lite arbete om du använder mer lagrings utrymme än vad som kan minskas. Om din lösning exempelvis använder tre partitioner genererar downsizing till en eller två partitioner ett fel om det nya lagrings utrymmet är mindre än vad som krävs för att vara värd för ditt index. Som du kan förväntar dig kan du välja att ta bort index eller dokument i ett associerat index för att frigöra utrymme, eller behålla den aktuella konfigurationen.

Det finns ingen identifierings metod som anger vilka index-Shards som lagras på vissa partitioner. Varje partition ger cirka 25 GB lagring, så du måste minska lagrings utrymmet till en storlek som kan hanteras av antalet partitioner som du har. Om du vill återgå till en partition måste alla 12-Shards anpassas.

Om du vill ha hjälp med framtida planeringen kanske du vill kontrol lera lagringen (med hjälp av [Hämta index statistik](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) för att se hur mycket du faktiskt har använt. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Nästa steg
När du förstår begreppen bakom tjänst administration kan du använda [PowerShell](search-manage-powershell.md) för att automatisera uppgifter.

Vi rekommenderar också att du går igenom [artikeln om prestanda och optimering](search-performance-optimization.md).

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



