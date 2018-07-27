---
title: Flera huvudservrar i global skala med Azure Cosmos DB | Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 4911a302bf9055948827a72f2e631663b8be741e
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266052"
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Multimaster i global skala med Azure Cosmos DB 
 
Utveckla globalt distribuerade program som svarar med lokala fördröjning medan upprätthålla konsekvent vyer av data över hela världen är utmanande problem. Kunder använder globalt distribuerade databaser, eftersom de behöver för att förbättra svarstiden för åtkomst av data, uppnå hög tillgänglighet, se till att garanterad haveriberedskap och för att uppfylla sina affärsbehov. Multimaster i Azure Cosmos DB ger hög tillgänglighet (99,999%), latensvärden svarstid att skriva data och skalbarhet med inbyggda omfattande och flexibel konflikt matchningsstöd. Dessa funktioner kan avsevärt förenkla utvecklingen av globalt distribuerade program. Stöd för flera huvudservrar är avgörande för globalt distribuerade program. 

![Arkitektur för flera huvudservrar](./media/multi-region-writers/multi-master-architecture.png)

Med flera huvudservrar stöd för Azure Cosmos DB kan utföra du skrivåtgärder på behållare av data (till exempel samlingar, diagram, tabeller) distribuerade var som helst i världen. Du kan uppdatera data i valfri region som är associerad med ditt databaskonto. Uppdateringarna data kan spridas asynkront. Förutom att ge snabb åtkomst och skrivfördröjningen till dina data, är multimaster också en praktisk lösning för redundans och belastningsutjämning. Sammanfattningsvis med Azure Cosmos DB får du skrivfördröjningen av < 10 ms i den 99: e percentilen var som helst i världen, 99,999% skrivning och lästillgänglighet var som helst i världen och möjlighet att skala både Skriv- och läsningsdataflöde som var som helst i världen.   

> [!IMPORTANT]
> Stöd för flera huvudservrar finns i privat förhandsgranskning, som använder förhandsversion, [registrera](#sign-up-for-multi-master-support) nu.

## <a name="sign-up-for-multi-master-support"></a>Registrera dig för stöd för flera huvudservrar

Om du redan har en Azure-prenumeration kan registrera du dig att ansluta till flera huvudservrar förhandsvisningsprogrammet i Azure-portalen. Om Azure är nytt för dig, kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free) där du får 12 månaders gratis åtkomst till Azure Cosmos DB. Utför följande steg för att begära åtkomst till flera huvudservrar förhandsvisningsprogrammet.

1. I [Azure-portalen](https://portal.azure.com), klickar du på **Skapa en resurs** > **Databaser** > **Azure Cosmos DB**.  

2. På sidan nytt konto, ange ett namn för ditt Azure Cosmos DB-konto, Välj API, prenumeration, resursgrupp och plats.  

3. Sedan väljer du **registrera dig för förhandsgranskning i dag** under fältet med flera Master-förhandsversion.  

   ![Registrera dig för förhandsversionen av flera huvudservrar](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. I den **registrera dig för förhandsgranskning i dag** fönstret klickar du på **OK**. När du har skickat förfrågan status ändras till **väntar på godkännande** i bladet för att skapa kontot.  

När du skickar din begäran får du ett e-postmeddelande att din begäran har godkänts. På grund av stora mängder begäranden, bör du få ett meddelande inom en vecka. Du behöver inte skapa ett supportärende för att slutföra begäran. Begäranden granskas i den ordning de tas emot.

## <a name="a-simple-multi-master-example--content-publishing"></a>Publicering av ett enkelt flera huvudservrar exempel – innehåll  

Låt oss titta på ett verkligt scenario som beskriver hur du använder flera huvudservrar stöd med Azure Cosmos DB. Överväg en publishing innehållsplattform som bygger på Azure Cosmos DB. Här följer några krav som den här plattformen måste uppfylla för en bättre användarupplevelse för både utgivare och konsumenter. 

* Både redigerare och prenumeranter är fördelade över hela världen.  

* Författare måste publicera artiklar (skriva) till deras lokala (närmaste) region.  

* Författare har läsare-/ prenumeranter av artiklarna som distribueras över hela världen.  

* Prenumeranter bör få ett meddelande när nya artiklar publiceras.  

* Prenumeranter måste kunna läsa artiklarna från deras lokala region. De bör också att kunna lägga till recensioner i dessa artiklar.  

* Vem som helst, inklusive författaren av artiklarna ska kunna visa alla granskningar som är anslutna till artiklar från en lokal region.  

Om vi antar att miljoner konsumenter och utgivare med flera miljarder artiklar, snart måste vi ta itu med problemen med skalas tillsammans, vilket ger ort av åtkomst. Ett användningsfall är en perfekt kandidat för Azure Cosmos DB multimaster. 

## <a name="benefits-of-having-multi-master-support"></a>Fördelarna med att ha stöd för flera huvudservrar 

Stöd för flera huvudservrar är nödvändigt för globalt distribuerade program. Multimaster består av [flera master regioner](distribute-data-globally.md) som lika delta i en skrivning var som helst-modell (aktiv-aktiv mönster) och används för att säkerställa att data är tillgängliga när som helst där du behöver den. Uppdateringar som görs till en enskild region sprids asynkront till alla andra regioner (som i sin tur är master regioner i sina egna). Azure Cosmos DB-regioner som fungerar som master regioner i en konfiguration med flera huvudservrar automatiskt fungerar för att Konvergera data över alla repliker och se till att [global konsekvens och dataintegritet](consistency-levels.md). Följande bild visar Läs/Skriv-replikering för en enskild och Multi-Master.

![Enskild och flera huvudservrar](./media/multi-region-writers/single-vs-multi-master.png)

Implementerar multimaster på egen hand lägger till belastningen på utvecklare. Storskaliga kunder som försöker implementera multimaster på egen hand tillbringar hundratals timmar konfigurera och testa en världen över flera huvudservrar konfiguration, och många har en dedikerad uppsättning tekniker som arbetar med enda är att övervaka och underhålla flera master replikering. Skapa och hantera konfiguration med flera huvudservrar på egen hand tar tid, resurser från utveckling i själva programmet och resulterar i mycket högre kostnader. Azure Cosmos DB har stöd för flera huvudservrar ”out-of the box” och tar bort det här arbetet utvecklare.  

Sammanfattningsvis ger multimaster följande fördelar:

* **Bättre haveriberedskap, skriva tillgänglighet och redundans**-multimaster kan användas för att bevara hög tillgänglighet för en verksamhetskritiska databas i större utsträckning. Till exempel kan flera master-databasen replikera data från en region till en region för växling vid fel när den primära regionen blir otillgänglig på grund av ett avbrott eller ett regionalt haveri. Sådana redundansregion kommer att fungera som en helt funktionella master region programmet. Multimaster ger större ”överlevnads” skydd för naturkatastrofer, strömavbrott eller sabotage, eftersom återstående regioner kan använder geografiskt olika flera huvudservrar med ett ha garanterad tillgänglighet > 99,999%. 

* **Förbättrad skrivfördröjningen för slutanvändare** – ju närmare dina data (som du hanterar) är för slutanvändarna, desto bättre blir upplevelsen. Till exempel om du har användare i Europa, men databasen är i USA eller Australien, har lagts till svarstiden är cirka 140 ms och 300 ms för respektive regioner. Fördröjningar är acceptabel att börja med för många populära spel, bank krav eller interaktiva program (webb- eller mobile). Svarstid spelar en stor del i kundens uppfattning av en upplevelse av hög kvalitet och har visat sig påverka beteendet för användare i några märkbara utsträckning. Som förbättrar teknik och särskilt med ankomsten av AR, VR och MR måste kräver ännu mer uppslukande och verklighetstrogna upplevelser utvecklare nu du skapa programvarusystem med strikta svarstidskrav. Därför är med lokalt tillgängliga program och data (innehåll för appar) viktigare. Prestanda är lika snabbt som vanlig lokal läsningar och skrivningar och förbättrad globalt med geo-replikering med Multi-Master i Azure Cosmos DB.  

* **Förbättrad skalbarhet för skrivning och genomströmning för skrivning** – multimaster ger högre dataflöde och större användning och erbjuder flera konsekvensmodeller med är korrekt garanterar och backas upp av serviceavtal. 

  ![Skalning av dataflöden i skrivning med Multi-Master](./media/multi-region-writers/scale-write-throughput.png)

* **Bättre stöd för frånkopplade miljöer (till exempel edge-enheter)** -multimaster gör det möjligt för användarna att replikera alla eller en delmängd av data från en edge-enhet till en närmaste region i en frånkopplad miljö. Det här scenariot är typiska för säljarna automatiseringssystem, där en enskild persons bärbar dator (en frånkopplad enhet) lagrar en delmängd av data som är relaterade till enskilda säljare. Master-regioner i molnet som är placerade var som helst i världen kan fungera som mål för kopiering från fjärranslutna edge-enheter.  

* **Belastningsutjämning** -med multimaster, belastning över programmet kan genomförs genom att flytta användare/arbetsbelastningar från en tungt belastad region till regioner där belastningen jämnt fördelad. Skriva kapacitet enkelt kan utökas genom att lägga till en ny region och sedan växla vissa skrivningar till det nya området. 

* **Bättre användning av etablerad kapacitet** – med multimaster, för hög och blandade arbetsbelastningar, kan du fylla etablerad kapacitet i flera regioner...  I vissa fall som du kan distribuera om läsningar och skrivningar mer lika, så att det kräver mindre dataflöde som ska etableras och leder till fler besparingar för kunder.  

* **Enklare och mer robust apparkitekturer** -program flyttar till flera huvudservrar configuration garanterad dataåterhämtning.  Med Azure Cosmos DB dölja alla komplexiteten, kan den avsevärt förenkla programdesignen och arkitektur. 

* **Riskfritt testning av redundans** -testning av redundans inte har någon prestandaförsämring på genomströmning för skrivning. Med multimaster är alla andra regioner fullständig huvudservrar så redundans inte har mycket inverkan på genomströmning för skrivning.  

* **Lägre Total kostnad Ownership(TCO) och DevOps** -uppfyller skalbarhet, prestanda, global distribution, mål för återställningstid är ofta dyra på grund av dyra tillägg eller underhålla en infrastruktur för säkerhetskopiering som är vilande tills haveriberedskap under katastrofsituationer. Med Azure Cosmos DB multimaster backas upp av branschledande serviceavtal, utvecklare längre kräver att bygga och underhålla den ”sammanlänkande serverdelslogik” själva och blir en lugn och ro sina verksamhetskritiska arbetsbelastningar. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Användningsfall där flera huvudservrar support behövs

Det finns många användningsområden för flera master i Azure Cosmos DB: 

* **IoT** – Azure Cosmos DB multimaster möjliggör förenklad distribuerade implementeringen av IoT databearbetning. GEO-distribuerad edge-distributioner som använder CRDT konflikt kostnadsfria replikerade data typer ofta behöver spåra time series-data från flera platser. Varje enhet kan vara homed till närmaste regioner och en enhet kan reser (till exempel en bil) och kan dynamiskt rehomed för att skriva till en annan region.  

* **E-handel** -avveckla fantastiska användarupplevelse i e-handel scenarier behöver hög tillgänglighet och flexibilitet till felscenarier. Om det inte går att en region, vill användarsessioner shopping kundvagnar, aktiva listor behöver sömlöst hämtas av en annan region utan att förlora tillstånd. Under tiden kan uppdateringar som görs av användaren måste hanteras på rätt sätt (till exempel lägger till och tar bort från kundvagnen måste överför via). Med multimaster, kan Azure Cosmos DB hantera sådana scenarier ett smidigt sätt, med en smidig övergång mellan aktiva regioner samtidigt som en enhetlig vy baserat på användarens av. 

* **Identifiering av bedrägerier/avvikelser** -ofta program som övervakar användaraktivitet eller kontoaktivitet distribueras globalt och måste hålla reda på flera händelser samtidigt. När du skapar och underhålla poäng för en användare, uppdatera åtgärder från olika geografiska regioner samtidigt resultat för att hålla risken mått direkt. Azure Cosmos DB kan garantera att utvecklare inte behöver hantera konflikt scenarier på programnivå. 

* **Samarbete** – för program som rangordning utifrån popularitet artiklar, till exempel varor på försäljning eller media som ska konsumeras osv. Spåra popularitet i olika geografiska områden kan bli komplicerade, särskilt om royalty måste vara betald eller verklig tid reklam beslut om. Rangordning, sortering och rapportering i många regioner över hela världen, i realtid med Azure Cosmos DB gör att utvecklare kan leverera funktioner utan besvär och utan att kompromissa med svarstider. 

* **Avläsning av** – inventering och reglerar användning (till exempel API-anrop, transaktioner/sekund, minut används) kan implementeras globalt enkelt med Azure Cosmos DB multimaster. Inbyggda konfliktlösning säkerställer båda noggrannhet för förordning i realtid. 

* **Anpassning** – om du hanterar geografiskt distribuerade räknare som utlösa åtgärder, till exempel lojalitet pekar awards eller implementera anpassade användarsession vyer, hög tillgänglighet och förenklad geo-distribuerad Räkna tillhandahålls av Azure Cosmos DB kan program ger hög prestanda med enkelhet. 

## <a name="conflict-resolution-with-multi-master"></a>Konfliktlösning med Multi-Master 

Med multimaster är den stora utmaningen ofta att två (eller fler) repliker av samma post kan uppdateras samtidigt av olika skrivare i minst två olika regioner. Samtidiga skrivningar kan leda till två olika versioner av samma post och utan inbyggda konfliktlösning och själva programmet måste utföra konfliktlösning för att lösa det här inkonsekvens.  

**Exempel** -antar vi att du använder Azure Cosmos DB som det beständiga arkivet för ett kundvagnsprogram och det här programmet distribueras i två regioner: östra USA och västra USA.  Om ungefär på samma gång en användare i San Francisco lägger till ett objekt till hans kundvagn (till exempel en bok) när en process för hantering av inventering i östra USA upphäver ett annat i kundvagnen objekt (till exempel en ny telefon) för användaren som svar på en s meddelande om upplier som lanseringsdatumet har fördröjts. Vid tiden T1 skiljer i kundvagnen posterna i två regioner. Databasen använder dess replikering och mekanism för lösning av konflikt för att lösa det här inkonsekvens och slutligen en av de två versionerna av kundvagnen kommer att markeras. Med hjälp av konflikt upplösning heuristik som oftast används av flera huvudservrar databaser (till exempel senaste skrivning wins), är det omöjligt för användaren eller programmet att förutsäga vilken version som ska väljas. I båda fallen data förloras eller oväntade problem kan uppstå. Om East region version väljs, sedan användarens val av ett nytt köp-objekt (det vill säga en bok) går förlorad och väljer region Väst, sedan det tidigare valda objektet (det vill säga phone) är fortfarande i i kundvagnen. I båda fallen information tas bort. En annan process som granskar den shopping kundvagn slutligen mellan T1 och T2 kommer att se samt icke-deterministisk beteende. En bakgrundsprocess som markeras betjäna lagret och uppdateras i kundvagnen leveranskostnader skulle till exempel ge resultat som står i konflikt med eventuell innehållet i i kundvagnen. Om processen körs i region Väst och alternativ 1 blir verklighet, skulle den compute leveranskostnader för två objekt, även om i kundvagnen snart har bara ett objekt, boken. 

Azure Cosmos DB implementerar logiken för hantering av motstridiga skrivningar i databasmotorn själva. Azure Cosmos DB erbjuder **omfattande och flexibel konflikt matchningsstöd** genom att erbjuda flera konflikt upplösning modeller, inklusive automatisk (CRDT konflikt kostnadsfria replikerad datatyper), senaste skriva Wins (LWW) och anpassade ( Lagrad procedur) för automatisk lösning. Konflikt upplösning modeller ger garantier är korrekt och konsekvens och tar bort belastningen från utvecklare att bekymra dig om konsekvens, tillgänglighet, prestanda, replikeringsfördröjning och komplexa kombinationer av händelser under geo-redundans och skrivkonflikter i över flera regioner.  

  ![Mult-master konfliktlösning](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Har du 3 typer av konflikt upplösning modeller som erbjuds av Azure Cosmos DB. Semantiken för konflikt upplösning modeller är följande: 

**Automatisk** – det här är standardprincipen för konfliktlösning. Att välja den här principen gör Azure Cosmos DB att automatiskt lösa Motstridiga uppdateringar på serversidan och ge garanterar stark--konsekvens. Internt, implementerar Azure Cosmos DB automatisk konfliktlösning genom att använda konflikt-kostnadsfri-replikerade-datatyper (CRDTs) i databasmotorn.  

**Senaste-Write-Wins (LWW)** – välja den här principen gör att du kan lösa konflikter baserat på något som definieras synkroniseras tidsstämpelsegenskapen eller en anpassad egenskap definierats på konfliktversionen poster. Konfliktlösningen sker på serversidan och version med den senaste tidsstämpeln är markerad som vinnare.  

**Anpassad** – du kan registrera ett definierat konflikt upplösning programlogiken genom att registrera en lagrad procedur. Den lagrade proceduren kommer få anropas vid identifiering av Uppdateringskonflikter inom ramen för en databastransaktion på serversidan. Om du väljer alternativet men inte det gick att registrera en lagrad procedur (eller om den lagrade proceduren genererar ett undantag vid körning), du kan använda alla filversioner via konflikter Feed och Lös dem individuellt.  

## <a name="next-steps"></a>Nästa steg  

I den här artikeln lärt dig hur du använder globalt distribuerade multimaster med Azure Cosmos DB. Nu ska ta en titt på följande resurser: 

* [Lär dig mer om hur Azure Cosmos DB stöder global distribution](distribute-data-globally.md)  

* [Lär dig mer om automatisk redundans i Azure Cosmos DB](regional-failover.md)  

* [Lär dig mer om global konsekvens med Azure Cosmos DB](consistency-levels.md)  

* Utveckla med flera regioner med Azure Cosmos DB - [SQL API](tutorial-global-distribution-sql-api.md), [MongoDB API](tutorial-global-distribution-mongodb.md), eller [tabell-API](tutorial-global-distribution-table.md)  
