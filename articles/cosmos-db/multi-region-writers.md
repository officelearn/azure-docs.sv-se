---
title: Multimaster på global skala med Azure Cosmos DB | Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: cc66b2f506d81a7ba10b26c3b24287472e890682
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724915"
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Flera master i global skala med Azure Cosmos DB 
 
Utveckla globalt distribuerade program som svarar med lokala fördröjning när det är svårt problem att upprätthålla konsekvent vyer av data över hela världen. Kunder använder globalt distribuerade databaser, eftersom de behöver för att förbättra svarstiden för åtkomst av data, uppnå hög tillgänglighet, kontrollera garanterad katastrofåterställning och för att uppfylla sina affärsbehov. Flera master i Azure Cosmos DB ger hög tillgänglighet (99,999%), en siffra millisekunders latens att skriva data och skalbarhet med stöd för inbyggda omfattande och flexibelt konflikt lösning. Dessa funktioner avsevärt förenklar utvecklingen av globalt distribuerade program. Stöd för flera master är avgörande för globalt distribuerade program. 

![Multimaster-arkitektur](./media/multi-region-writers/multi-master-architecture.png)

Du kan utföra skrivningar med stöd för Azure Cosmos DB multimaster på behållare för data (till exempel samlingar, diagram, tabeller) distribueras var som helst i världen. Du kan uppdatera data i en region som är kopplad till ditt konto. Uppdateringarna data kan spridas asynkront. Förutom att tillhandahålla snabb åtkomst och skrivfördröjningen till dina data är med flera master också en praktisk lösning för redundans och belastningsutjämning problem. Sammanfattningsvis med Azure Cosmos DB får du skrivfördröjningen för < 10 ms vid 99th percentilen var som helst i världen, 99,999% skrivning och Läs tillgänglighet var som helst i världen, och möjligheten att skala både skriva och läsa genomströmning var som helst i världen.   

> [!IMPORTANT]
> Multimaster stöd är i privat förhandsvisning att använda förhandsversionen, [registrering](#sign-up-for-multi-master-support) nu.

## <a name="sign-up-for-multi-master-support"></a>Registrera dig för multimaster-stöd

Om du redan har en Azure-prenumeration kan registrera du dig att ansluta till multimaster förhandsgranskningsprogrammet i Azure-portalen. Om du har använt Azure registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free) där du får 12 månader gratis tillgång till Azure Cosmos DB. Utför följande steg om du vill begära åtkomst till multimaster förhandsgranskningsprogrammet.

1. I den [Azure-portalen](https://portal.azure.com), klickar du på **skapar du en resurs** > **databaser** > **Azure Cosmos DB**.  

2. Ange ett namn för ditt konto i Azure Cosmos DB på sidan nytt konto, Välj API, prenumeration, resursgrupp och plats.  

3. Sedan väljer du **registrera dig för förhandsgranskning i dag** under fältet Multi Mater förhandsgranskning.  

   ![Registrera dig för multimaster förhandsversionen](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. I den **registrera dig för förhandsgranskning i dag** rutan klickar du på **OK**. När du skickar din begäran status ändras till **godkännande** i bladet konto skapas.  

När du skickar din begäran får du ett e-postmeddelande att din begäran har godkänts. På grund av stora mängder begäranden, bör du få meddelande inom en vecka. Du behöver inte skapa ett supportärende för att slutföra begäran. Begäranden granskas i den ordning de mottogs.

## <a name="a-simple-multi-master-example--content-publishing"></a>Publicering av ett enkelt multimaster exempel – innehåll  

Nu ska vi titta på ett verkligt scenario som beskriver hur du använder multimaster support med Azure Cosmos DB. Överväg att en innehåll publishing plattform som bygger på Azure Cosmos DB. Här följer några krav som den här plattformen måste uppfylla för en bra användarupplevelse för både utgivare och konsumenter. 

* Både författare och prenumeranter sprids över hela världen.  

* Författare måste publicera (skriva) artiklar på sin lokala (närmaste) region.  

* Författare har läsare-/ prenumeranter sina artiklar som distribueras över hela världen.  

* Prenumeranter bör få ett meddelande när nya artiklar publiceras.  

* Prenumeranter måste kunna läsa artiklar från sin lokala region. De bör också kunna lägga till granskningar till dessa artiklar.  

* Alla inklusive författaren av artiklarna ska kunna visa alla omdömen anslutna till artiklar från region med en lokal.  

Under förutsättning att miljontals användare och utgivare med miljarder artiklar, snart vi behöver ta itu med problem för skala tillsammans med garanterar ort åtkomst. Ett användningsfall är en perfekt kandidat för Azure Cosmos DB flera master. 

## <a name="benefits-of-having-multi-master-support"></a>Fördelarna med att ha stöd för flera master 

Multimaster-stöd är nödvändigt för globalt distribuerade program. Flera master består av [flera master regioner](distribute-data-globally.md) som lika deltar i en skrivning var som helst modell (aktiv-aktiv mönster) och används för att säkerställa att data är tillgängliga när som helst där det behövs. Uppdateringar som görs i en enskild region sprids asynkront till alla andra regioner (som i sin tur master regioner på sina egna). Azure DB Cosmos-regioner som fungerar som hanterare regioner i en konfiguration för multimaster automatiskt fungerar för att Konvergera alla data för alla repliker och se till att [globala konsekvens och dataintegritet](consistency-levels.md). Följande bild visar läsning och skrivning replikering för ett enda ställe och mult master.

![Single master och multimaster](./media/multi-region-writers/single-vs-multi-master.png)

Implementera flera master på egen hand lägger till belastningen på utvecklare. Storskalig kunder som försöker implementera flera master på egen hand tillbringar hundratals timmar att konfigurera och testa en globalt multimaster-konfiguration, och många har en särskild uppsättning tekniker som arbetar med enda är att övervaka och underhålla flera master replikering. Skapa och hantera multimaster installationen på egen hand tar tid, resurser från införa i programmet och ger mycket högre kostnader. Azure Cosmos-DB multimaster stöder ”out-of-the-box” och tar bort det här arbetet utvecklare.  

Sammanfattningsvis ger flera master följande fördelar:

* **Bättre katastrofåterställning, skriva tillgänglighet och redundans**-flera master kan användas för att bevara hög tillgänglighet för en verksamhetskritiska databas i större utsträckning. Till exempel kan flera master-databasen replikera data från en till en region för växling vid fel när den primära regionen blir otillgänglig på grund av ett avbrott eller en regional katastrofåterställning. Sådan växling vid fel region fungerar som en fullt fungerande master region som stöd för programmet. Flera master ger större ”överlevnads” skydd med avseende på naturkatastrof, strömavbrott, eller sabotage av anläggningen eller båda eftersom återstående regioner kan vara i ett geografiskt olika flera original med en garanterad skrivåtgärder tillgänglighet > 99,999%. 

* **Förbättrad skrivfördröjningen för slutanvändare** - närmare dina data (som du hanterar) är till slutanvändare, desto bättre blir upplevelsen. Till exempel om du har användare i Europa men databasen är i USA eller Australien tillagda svarstiden är cirka 140 ms och 300 ms för respektive regioner. Fördröjningar är acceptabelt att börja med för många populära spel, banktjänster krav eller interaktiva program (web eller mobile). Latens spelar en stor del i kundens uppfattning av en hög kvalitet och har visat för att påverka beteendet för användare i några märkbara utsträckning. Som förbättrar teknik och särskilt med ankomsten av AR, VR och MR, måste som kräver ytterligare djupare och verklighetstrogna upplevelser utvecklare nu du skapa programvarusystem med strikta fördröjning. Därför är har lokalt tillgängliga program och data (innehåll för program) viktigare. Prestanda är lika snabbt som regelbundet lokala läser och skriver och förbättrad globalt av geo-distribution med flera master i Azure Cosmos DB.  

* **Förbättrad skalbarhet för skrivning och genomströmning för skrivning** – flera master ger högre genomströmning och större användning samtidigt som den erbjuder flera konsekvenskontroll modeller med är korrekt garanterar och backas upp av SLA: er. 

  ![Skalning genomströmning för skrivning med flera master](./media/multi-region-writers/scale-write-throughput.png)

* **Bättre stöd för frånkopplade miljöer (till exempel gränsenheterna)** -flera master gör att användarna kan replikera alla eller en delmängd av data från en insticksenhet till en närmaste region i en frånkopplad miljö. Det här scenariot är typiska för säljarna automatiseringssystem, där en person bärbar dator (en frånkopplad enhet) lagrar en delmängd av data som rör enskilda säljare. Master regioner i molnet som finns var som helst i världen kan användas som mål för kopiera från de fjärranslutna enheterna.  

* **Belastningsutjämning** -med flera master belastningen över programmet kan genomförs genom att flytta användare/arbetsbelastningar från en tungt belastad region till regioner där belastningen fördelas jämnt. Skriva kapacitet kan enkelt utökas genom att lägga till en ny region och sedan växla vissa skrivningar till den nya regionen. 

* **Bättre användning av etablerad kapacitet** – med flera master för skrivintensiv och blandade arbetsbelastningar, kan du fylla etablerad kapacitet över flera regioner...  I vissa fall som du kan distribuera läsningar och skrivningar mer lika, så att den kräver färre genomströmning till etableras och leder till en mer besparingar för kunder.  

* **Enklare och mer robust apparkitekturer** -program som flyttar till multimaster configuration få garanterat data återhämtning.  Med Azure Cosmos-DB dölja alla komplexitet, förenkla den avsevärt programdesign och arkitektur. 

* **Risk utan testning av redundans** -testning av redundans kommer inte har någon försämring på genomströmning för skrivning. Med flera master är alla andra regioner fullständig huvudservrar så redundans inte har mycket inverkan på genomströmning för skrivning.  

* **Lägre totala kostnaden Ownership(TCO) och DevOps** -möte skalbarhet, prestanda, global distributionsplatsen återställningstiden kostar ofta på grund av dyra tillägg eller underhålla en infrastruktur för säkerhetskopiering som är vilande tills katastrofåterställning träffar. Med Azure Cosmos DB flera master säkerhetskopierats av branschledande serviceavtal, utvecklare längre kräver skapa och underhålla ”backend ibland sammanlänkande logik” sig själva och blir en sinnesro sina verksamhetskritiska arbetsbelastningar som körs. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Användningsfall där multimaster stöd krävs

Det finns flera användningsområden för flera master i Azure Cosmos-databasen: 

* **IoT** -Azure Cosmos DB flera master tillåter förenklad distribuerade implementering av IoT databearbetning. Fördelade edge-distributioner som använder CRDT konflikt utan replikerade data typer ofta behöver spåra tid series-data från flera platser. Varje enhet kan vara homed till någon av de närmaste regionerna, och en enhet kan förflytta sig (t.ex, en bil) och kan dynamiskt rehomed för att skriva till en annan region.  

* **E-handel** -så bra användarupplevelse i e-handel scenarier kräver hög tillgänglighet och återhämtning för scenarier. Om det inte går att en region, vill användarsessioner shopping Datorvagnar, aktiva listor behöver sömlöst tas upp av en annan region utan tillstånd. Under tiden kan uppdateringar som görs av användaren måste hanteras på rätt sätt (exempelvis lägger till och tar bort från kundvagnen måste överföra). Med flera master kan Azure Cosmos DB hantera dessa scenarier avslutas, med en smidig övergång mellan active regioner samtidigt som en konsekvent vy från användarens synvinkel. 

* **Bedrägeri/Avvikelseidentifiering** -ofta program som övervakar användaraktivitet eller kontoaktivitet distribueras globalt och måste hålla reda på flera händelser samtidigt. När du skapar och underhålla poängen för en användare, uppdatera åtgärder från olika geografiska regioner samtidigt resultat för att hålla risken mått infogad. Azure Cosmos-DB kan garantera utvecklare inte behöver hantera konflikt scenarier på programnivå. 

* **Samarbete** – för program som skolorna utifrån popularitet artiklar, till exempel varor på försäljning eller media som ska konsumeras osv. Spåra popularitet över geografiska regioner kan få komplicerad, särskilt när royalty måste vara betald eller verkliga reklam beslut görs. Rangordning, sortering och rapportering över flera regioner över hela världen, i realtid med Azure Cosmos DB gör att utvecklare kan tillhandahålla funktioner med mycket ansträngning och utan att kompromissa med svarstider. 

* **Avläsning** - inventering och reglerar användningen (till exempel API-anrop, transaktioner per sekund minuter används) kan implementeras globalt med enkelhet med hjälp av Azure Cosmos DB flera master. Inbyggda konfliktlösning säkerställer båda noggrannhet i antal och förordning i realtid. 

* **Anpassning** – om du underhålla geografiskt distribuerade räknare som utlöser åtgärder, till exempel förmåner pekar priser eller implementera anpassade användarsession vyer, hög tillgänglighet och förenklad geodistribuerad inventering som tillhandahålls av Azure Cosmos DB kan program ger hög prestanda med enkelhet. 

## <a name="conflict-resolution-with-multi-master"></a>Konfliktlösning med flera master 

Med flera master är det en utmaningen ofta att två (eller fler) repliker av samma post samtidigt kan uppdateras av olika skrivare i två eller flera olika regioner. Samtidiga skrivningar kan leda till att två olika versioner av samma post och utan inbyggda konfliktlösning och programmet måste utföra konfliktlösning för att lösa det här inkonsekvens.  

**Exempel** -vi antar att du använder Azure Cosmos DB som det beständiga arkivet för ett kundvagnsprogram och det här programmet har distribuerats i två områden: östra USA och västra USA.  Om ungefär på samma gång en användare i San Francisco lägger till ett objekt i sin kundvagn (till exempel en bok) när en process för hantering av inventering i östra USA upphäver ett annat i kundvagn objekt (t.ex, ett nytt telefonnummer) för samma användare som svar på en s meddelande om upplier som lanseringsdatumet har fördröjts. Vid tiden T1 är shopping kundvagn posterna i två regioner olika. Databasen använder dess replikering och konflikt upplösning mekanism för att lösa det här inkonsekvens och slutligen ett av de två versionerna av kundvagnen markeras. Med konflikt upplösning heuristik som oftast används av multimaster databaser (till exempel senaste skrivning wins) är det möjligt för användaren eller programmet att förutsäga vilken version som ska väljas. I båda fallen data förloras eller oväntat kan inträffa. Om den region Öst-versionen är markerad sedan användarens val av ett nytt köp-objekt (det vill säga en bok) går förlorad och väljer region Väst, sedan det tidigare valda objektet (det vill säga phone) är fortfarande i vagnen. Oavsett hur information går förlorad. En annan process undersöks den shopping kundvagn slutligen mellan tid T1 och T2 ska se samt icke-deterministiska beteende. Bakgrunden som väljer automatiserat datalager och uppdaterar kundvagn leverans kostnader skulle exempelvis gav inga resultat som står i konflikt med eventuell innehållet i vagnen. Om processen körs i region Väst och alternativ 1 blir verkligheten, skulle den beräkna leveranskostnader för två objekt även om vagnen snart har bara ett objekt, boken. 

Azure Cosmos-DB implementerar logik för att hantera motstridiga skrivningar i databasmotorn sig själv. Azure Cosmos-DB erbjuder **omfattande och flexibelt konflikt matchningsstöd** genom att erbjuda flera konflikt upplösning modeller, inklusive automatisk (CRDT konflikt utan replikerad datatyper), senaste skriva Wins (LWW) och anpassade ( Lagrade proceduren) för automatisk konfliktlösning. Konflikt upplösning modeller ge garantier är korrekt och konsekvent och ta bort belastningen från utvecklare behöver tänka på konsekvens, tillgänglighet, prestanda, replikeringsfördröjning och komplicerade kombinationer av händelser under geo-redundans och konflikter mellan region skrivåtgärder.  

  ![Mult master konfliktlösning](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Du måste 3 typer av konflikt upplösning modeller som erbjuds av Azure Cosmos DB. Semantiken för konflikt upplösning modeller är följande: 

**Automatisk** -detta är standardprincipen konflikt lösning. Att välja den här principen gör Azure Cosmos-Databsen ska automatiskt lösa Motstridiga uppdateringar på serversidan och ger stark-eventuell-konsekvens garanterar. Internt, implementerar Azure Cosmos DB automatisk konfliktlösning genom att utnyttja konflikt-kostnadsfri-replikerade-datatyper (CRDTs) i databasmotorn.  

**Senaste skrivning Wins (LWW)** – om du väljer den här principen kan du lösa konflikter baserat på något som definieras synkroniseras tidsstämpel-egenskapen eller en anpassad egenskap definierats på poster i konflikt version. Konfliktlösningen sker på serversidan och version med den senaste tidsstämpeln är markerad som vinnaren.  

**Anpassad** -du kan registrera en definierad konflikt upplösning programlogik genom att registrera en lagrad procedur. Den lagrade proceduren kommer hämta anropas vid identifiering av Uppdateringskonflikter av en databastransaktion på serversidan. Om du väljer alternativet men inte det gick att registrera en lagrad procedur (eller om den lagrade proceduren genererar ett undantag vid körning), du kan använda alla filversioner via konflikter Feed och Lös dem individuellt.  

## <a name="next-steps"></a>Nästa steg  

I den här artikeln upptäckt använda globalt distribuerade flera master med Azure Cosmos DB. Ta en titt på följande resurser: 

* [Lär dig mer om hur Azure Cosmos DB stöder global distributionsplatsen](distribute-data-globally.md)  

* [Lär dig mer om automatisk redundans i Azure Cosmos DB](regional-failover.md)  

* [Lär dig mer om globala konsekvens med Azure Cosmos DB](consistency-levels.md)  

* Utveckla med flera områden med Azure Cosmos-DB - [SQL API](tutorial-global-distribution-sql-api.md), [MongoDB API](tutorial-global-distribution-mongodb.md), eller [tabell-API](tutorial-global-distribution-table.md)  
