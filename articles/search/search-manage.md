---
title: Serviceadministration i portalen
titleSuffix: Azure Cognitive Search
description: Hantera en Azure Cognitive Search-tjänst, en värdbaserad molnsöktjänst på Microsoft Azure, med hjälp av Azure-portalen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282931"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Tjänstadministration för Azure Cognitive Search i Azure-portalen
> [!div class="op_single_selector"]
> * [Powershell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Cognitive Search är en fullständigt hanterad, molnbaserad söktjänst som används för att skapa en omfattande sökupplevelse i anpassade appar. Den här artikeln beskriver de tjänstadministrationsuppgifter som du kan utföra i [Azure-portalen](https://portal.azure.com) för en söktjänst som du redan har etablerat. Serviceadministration är lätt till design, begränsad till följande uppgifter:

> [!div class="checklist"]
> * Hantera åtkomst till *api-nycklarna* som används för läs- eller skrivåtkomst till din tjänst.
> * Justera tjänstkapaciteten genom att ändra allokeringen av partitioner och repliker.
> * Övervaka resursanvändningen i förhållande till högsta gränser för din tjänstnivå.

Observera att *uppgraderingen* inte visas som en administrativ uppgift. Eftersom resurser allokeras när tjänsten etableras kräver en ny tjänst att flytta till en annan nivå. Mer information finns i [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md).

Du kan övervaka frågevolymen och andra mått och använda dessa insikter för att justera tjänsten för snabbare svarstider. Mer information finns i [Övervaka användnings- och frågemått](search-monitor-usage.md) samt [Prestanda och optimering](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Administratörsrättigheter
Etablering eller avveckling av själva tjänsten kan göras av en Azure-prenumerationsadministratör eller medadministratör.

Inom en tjänst har alla som har åtkomst till tjänstens URL och en administratörs-api-nyckel läs-skrivåtkomst till tjänsten. Läs-skriv-åtkomst ger möjlighet att lägga till, ta bort eller ändra serverobjekt, inklusive api-nycklar, index, indexerare, datakällor, scheman och rolltilldelningar som implementeras via [RBAC-definierade roller](search-security-rbac.md).

All användarinteraktion med Azure Cognitive Search ligger inom ett av dessa lägen: läs-skriv-åtkomst till tjänsten (administratörsrättigheter) eller skrivskyddad åtkomst till tjänsten (frågerättigheter). Mer information finns i [Hantera api-keys](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Loggning och systeminformation
Azure Cognitive Search exponerar inte loggfiler för en enskild tjänst, varken via portalen eller programmatiska gränssnitt. På basic-nivån och högre övervakar Microsoft alla Azure Cognitive Search-tjänster för 99,9 % tillgänglighet per servicenivåavtal (SLA). Om tjänsten är långsam eller begäran dataflöde faller under SLA tröskelvärden, supportteam granska loggfiler tillgängliga för dem och lösa problemet.

När det gäller allmän information om din tjänst kan du få information på följande sätt:

* I portalen, på tjänstinstrumentpanelen, via meddelanden, egenskaper och statusmeddelanden.
* Använda [PowerShell](search-manage-powershell.md) eller [REST-API:et för hantering](https://docs.microsoft.com/rest/api/searchmanagement/) för att hämta [tjänstegenskaper](https://docs.microsoft.com/rest/api/searchmanagement/services)eller status för indexresursanvändning.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Övervaka resursanvändning
I instrumentpanelen är resursövervakning begränsad till den information som visas i tjänstinstrumentpanelen och några mått som du kan få genom att fråga tjänsten. På instrumentpanelen för tjänsten i avsnittet Användning kan du snabbt avgöra om partitionsresursnivåerna är tillräckliga för ditt program. Du kan etablera externa resurser, till exempel Azure-övervakning, om du vill samla in och bevara loggade händelser. Mer information finns i [Övervaka Azure Cognitive Search](search-monitor-usage.md).

Med rest-API:et för söktjänsten kan du räkna med dokument och index programmässigt: 

* [Hämta indexstatistik](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Räkna dokument](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Avbrott vid katastrofåterställning och service

Även om vi kan återställa dina data ger Azure Cognitive Search inte omedelbar redundans av tjänsten om det finns ett avbrott på kluster- eller datacenternivå. Om ett kluster misslyckas i datacentret identifierar och arbetar driftteamet för att återställa tjänsten. Du kommer att uppleva driftstopp under servicerestaurering, men du kan begära servicekrediter för att kompensera för otillgänglighet för tjänsten enligt [servicenivåavtalet (SLA).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 

Om kontinuerlig service krävs i händelse av katastrofala fel utanför Microsofts kontroll kan du [tillhandahålla ytterligare en tjänst](search-create-service-portal.md) i en annan region och implementera en strategi för georeplikering för att säkerställa att index är helt redundanta för alla tjänster.

Kunder som använder [indexerare](search-indexer-overview.md) för att fylla i och uppdatera index kan hantera haveriberedskap genom geospecifika indexerare som utnyttjar samma datakälla. Två tjänster i olika regioner, som var och en kör en indexerare, kan indexera samma datakälla för att uppnå georedundans. Om du indexerar från datakällor som också är geouppsagbara bör du vara medveten om att Azure Cognitive Search-indexerare bara kan utföra inkrementell indexering (sammanslagning av uppdateringar från nya, ändrade eller borttagna dokument) från primära repliker. I en redundanshändelse måste du peka om indexeraren till den nya primära repliken. 

Om du inte använder indexerare använder du programkoden för att skicka objekt och data till olika söktjänster parallellt. Mer information finns [i Prestanda och optimering i Azure Cognitive Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Eftersom Azure Cognitive Search inte är en primär datalagringslösning tillhandahåller vi inte en formell mekanism för säkerhetskopiering och återställning av självbetjäning. Du kan dock använda exempelkoden **för index-säkerhetskopieringsåterställning** i det här [Azure Cognitive Search .NET-exemplet för](https://github.com/Azure-Samples/azure-search-dotnet-samples) att säkerhetskopiera indexdefinitionen och ögonblicksbilden till en serie JSON-filer och sedan använda dessa filer för att återställa indexet om det behövs. Det här verktyget kan också flytta index mellan tjänstnivåer.

Annars är din programkod som används för att skapa och fylla i ett index de facto-återställningsalternativet om du tar bort ett index av misstag. Om du vill återskapa ett index tar du bort det (förutsatt att det finns), återskapar indexet i tjänsten och laddar om genom att hämta data från ditt primära datalager.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Skala upp eller ned
Varje söktjänst börjar med minst en replik och en partition. Om du har registrerat dig för en [nivå som tillhandahåller dedikerade resurser](search-limits-quotas-capacity.md)klickar du på panelen **SKALA** på instrumentpanelen för tjänsten för att justera resursanvändningen.

När du lägger till kapacitet via någon av resurserna använder tjänsten dem automatiskt. Inga ytterligare åtgärder krävs från din sida, men det finns en liten fördröjning innan effekten av den nya resursen realiseras. Det kan ta 15 minuter eller mer att etablera ytterligare resurser.

 ![][10]

### <a name="add-replicas"></a>Lägga till repliker
Öka frågor per sekund (QPS) eller uppnå hög tillgänglighet görs genom att lägga till repliker. Varje replik har en kopia av ett index, så att lägga till ytterligare en replik översätts till ytterligare ett index som är tillgängligt för hantering av tjänstfrågebegäranden. Minst 3 repliker krävs för hög tillgänglighet (se [Kapacitetsplanering](search-capacity-planning.md) för mer information).

En söktjänst med fler repliker kan läsa in frågebegäranden över ett större antal index. Med tanke på en frågevolymnivå kommer frågedataflödet att gå snabbare när det finns fler kopior av indexet tillgängliga för service av begäran. Om du upplever frågefördröjning kan du förvänta dig en positiv inverkan på prestanda när de ytterligare replikerna är online.

Även om frågegenomströmning går upp när du lägger till repliker, dubblar eller tredubblas det inte exakt när du lägger till repliker till tjänsten. Alla sökprogram är föremål för externa faktorer som kan inkräkta på frågeprestanda. Komplexa frågor och nätverksfördröjning är två faktorer som bidrar till variationer i frågesvarstider.

### <a name="add-partitions"></a>Lägga till partitioner
De flesta tjänstprogram har ett inbyggt behov av fler repliker i stället för partitioner. I de fall där ett ökat antal dokument krävs kan du lägga till partitioner om du har registrerat dig för standardtjänsten. Grundläggande nivå innehåller inte ytterligare partitioner.

På standardnivån läggs partitioner till i multiplar av 12 (specifikt 1, 2, 3, 4, 6 eller 12). Det här är en artefakt av skärva. Ett index skapas i 12 shards, som alla kan lagras på 1 partition eller delas lika in i 2, 3, 4, 6 eller 12 partitioner (en fragment per partition).

### <a name="remove-replicas"></a>Ta bort repliker
Efter perioder med höga frågevolymer kan du använda skjutreglaget för att minska repliker efter att sökfrågebelastningen har normaliserats (till exempel när semesterförsäljningen är över). Det finns inga ytterligare steg som krävs från din sida. Om du änker antalet repliker avstår virtuella datorer i datacentret. Dina fråge- och datainmatningsåtgärder körs nu på färre virtuella datorer än tidigare. Minimikravet är en replik.

### <a name="remove-partitions"></a>Ta bort partitioner
I motsats till att ta bort repliker, vilket inte kräver någon extra ansträngning från din sida, kan du ha en del arbete att göra om du använder mer lagringsutrymme än vad som kan minskas. Om lösningen till exempel använder tre partitioner genererar en nedskärning till en eller två partitioner ett fel om det nya lagringsutrymmet är mindre än vad som krävs för att vara värd för ditt index. Som du kan förvänta dig är dina val att ta bort index eller dokument inom ett associerat index för att frigöra utrymme eller behålla den aktuella konfigurationen.

Det finns ingen identifieringsmetod som talar om vilka indexsvandrar som lagras på specifika partitioner. Varje partition innehåller cirka 25 GB lagringsutrymme, så du måste minska lagringsutrymmet till en storlek som kan tillgodoses med antalet partitioner du har. Om du vill återgå till en partition måste alla 12 shards passa.

För att hjälpa till med framtida planering, kanske du vill kontrollera lagring (med [Hämta indexstatistik)](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)för att se hur mycket du faktiskt använt. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Nästa steg
När du har förstått begreppen bakom tjänstadministration bör du överväga att använda [PowerShell](search-manage-powershell.md) för att automatisera uppgifter.

Vi rekommenderar också att du granskar [prestanda- och optimeringsartikeln](search-performance-optimization.md).

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



