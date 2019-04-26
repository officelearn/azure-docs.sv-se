---
title: Lär dig mer om Azure SQL Data Warehouse operations | Microsoft Docs
description: 'Elasticiteten i SQL Data Warehouse låter dig öka, minska eller pausa beräkningskraft med hjälp av en glidande skala för informationslagerenheter (DWU:er). Den här artikeln förklarar måtten i informationslager och hur de relaterar till DWU:er. '
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
origin.date: 04/17/2018
ms.date: 11/12/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: f0489d9c018abac380cd0f8b1aa51fa5bb81707a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531190"
---
# <a name="data-warehouse-workload"></a>Arbetsbelastning i informationslager
En arbetsbelastning i ett informationslager innefattar alla åtgärder som utförs mot informationslagret. Arbetsbelastningen i informationslagret innefattar hela processen med att läsa in data i lagret, genomföra analys och rapportering av informationslagret, hantera data i informationslagret och exportera data från informationslagret. Hur djupa och breda de här komponenterna är, står ofta i proportion till åldern på informationslagret.

## <a name="new-to-data-warehousing"></a>Ny användare av informationslager?
Ett informationslager är en samling data som läses in från en eller flera datakällor och används för att utföra olika typer av business intelligence-åtgärder som rapportering och dataanalys.

Informationslager kännetecknas av frågor som genomsöker ett stort antal rader, stora dataintervall och som kan returnera relativt stora resultat för analys och rapportering. Informationslager kännetecknas också av relativt stora databelastningar gentemot små infogningar/uppdateringar/borttagningar på transaktionsnivå.

* Ett informationslager fungerar bäst när data är lagrade på ett sätt som optimerar frågor som behöver genomsöka ett stort antal rader eller stora dataintervall. Den här typen genomsökningar fungerar bäst när data lagras och genomsöks i kolumner istället för i rader.

> [!NOTE]
> Det minnesinterna kolumnlagerindexet, som använder sig av kolumnlagring, ger upp till 10 gånger bättre komprimering och 100 gånger bättre frågeprestanda än traditionella binära träd för rapporterings- och analysfrågor. Vi anser att kolumnlager-index är standarden för lagring och genomsökning av stora mängder data i ett informationslager.
> 
> 

* Ett informationslager har andra krav på sig än ett system som optimerats för online transaktionsbearbetning (OLTP). OLTP-system har många infoga-, uppdatera- och ta bort-åtgärder. De här åtgärderna söker mot specifika rader i tabellen. Tabellsökningar gör bäst ifrån sig när data lagras rad för rad. Datan kan sorteras och snabbt genomsökas med en söndra och erövra metod som kallas binary tree eller btree-sökning.

## <a name="data-loading"></a>Läsa in data
Datainläsning är en stor del av arbetsbelastningen för informationslager. Företag har vanligtvis ett upptaget OLTP-system som spårar ändringar under dagen medan kunderna genererar affärstransaktioner. Med jämna mellanrum, ofta på natten under ett underhållsfönster, flyttas eller kopieras transaktionerna till informationslagret. När den väl finns i informationslagret, kan analytiker utföra analyser och fatta affärsbeslut baserat på datan.

* Processen för inläsning kallas vanligtvis för ETL (Extract, Transform, and Load). Data behöver vanligtvis omvandlas för att vara konsekvent med andra data i informationslagret. Förut använde företag sig av dedikerade ETL-servrar för att genomföra omvandlingarna. Nu med snabb, massivt parallell bearbetning, går det att först läsa in data i SQL Data Warehouse och sedan utföra omvandlingarna. Den här processen kallas för ELT (Extract, Load and Transform) och håller på att bli den nya standarden för arbetsbelastningar för informationslager.

> [!NOTE]
> Med SQL Server 2016 kan du nu utföra analyser i realtid på en OLTP-tabell. Det här ersätter inte behovet av ett informationslager för att lagra och analysera data, men det ger ett sätt att utföra analyser i realtid.
> 
> 

### <a name="reporting-and-analysis-queries"></a>Rapporterings- och analysfrågor
Rapporterings- och analysfrågor indelas ofta i små, medel och stora baserat på ett antal kriterier, men vanligtvis baserat på tidsåtgång. I de flesta informationslager finns det blandade arbetsbelastningar med snabba kontra tidskrävande frågor. I varje fall är det viktigt att fastställa blandningen och fastställa frekvensen (varje timme, dagligen, månadsslut, kvartalsvis och så vidare). Det är viktigt att förstå att arbetsbelastningen med blandade frågor tillsammans med samtidighet, leder till korrekt kapacitetsplanering för ett informationslager.

* Kapacitetsplanering kan vara komplicerat för en arbetsbelastning med blandade frågor, speciellt när man behöver långa ledtider för att lägga till kapacitet till informationslagret. SQL Data Warehouse gör kapacitetsplanering mindre angeläget genom att det låter dig växa eller krympa bearbetningskapaciteten när som helst och genom att lagrings- och bearbetningskapacitet skalas oberoende av varandra.

### <a name="data-management"></a>Datahantering
Datahantering är viktig, speciellt när du vet att du kan få slut på diskutrymme inom en snar framtid. Informationslager delar vanligtvis upp data i meningsfulla intervall som sedan lagras som partitioner i en tabell. Alla SQL Server-baserade produkter låter dig flytta partitioner in och ut ur tabellen. Den här partitionsväxlingen gör att du kan flytta äldre data till billigare lagring och hålla den senaste datan tillgänglig i onlinelagring.

* Kolumnlager-index stöder partitionerade tabeller. Partitionerade tabeller i kolumnlagrings-index används för datahantering och arkivering. För tabeller som lagras rad för rad, har partitioner en viktigare roll i frågeprestanda.  
* PolyBase spelar en viktig roll i datahantering. Med PolyBase har du möjlighet att arkivera äldre data till Hadoop eller Azure blobblagring.  Det ger dig en mängd alternativ, eftersom dina data fortfarande är online.  Det kan ta längre tid att hämta data från Hadoop, men det kan uppvägas av besparingarna i lagringskostnad.

### <a name="exporting-data"></a>Exportera data
Ett sätt att göra data tillgänglig för rapporter och analys är att skicka den från informationslagret till dedikerade rapport- och analysservrar. De här servrarna kallas för dataförråd. Du kan till exempel förbearbeta rapportdata och sedan exportera den från informationslagret till flera servrar runtom i världen, för att göra den tillgänglig för kunder och analytiker.

* För att generera rapporter, skulle du kunna fylla skrivskyddade rapporteringsservrar varje natt med en ögonblicksbild av dagliga data. Det ger högre bandbredd åt kunderna, samtidigt som beräkningsresurserna som behövs i informationslagret minskar. Ur en säkerhetsperspektiv, låter dataförråd dig minska antalet användare som har åtkomst till informationslagret.
* När det gäller analys, kan du antingen bygga en analyskub i informationslagret och köra analys mot det, eller så kan du förbearbeta data och exportera den till analysservern för ytterligare analys.

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse kan du gå vidare och se hur du snabbt [skapar ett SQL Data Warehouse][create a SQL Data Warehouse] och [läsa in exempeldata][load sample data].

<!--Image references-->

<!--Article references-->
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->