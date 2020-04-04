---
title: Metodtips – Azure Batch
description: Lär dig metodtips och användbara tips för att utveckla din Azure Batch-lösning.
author: LauraBrenner
ms.author: labrenne
ms.date: 04/03/2020
ms.service: batch
ms.topic: article
manager: evansma
ms.openlocfilehash: 94483f8e15b0cd90f76e369034e987bec6da127c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655834"
---
# <a name="azure-batch-best-practices"></a>Metodtips för Azure Batch

I den här artikeln beskrivs en samling metodtips för att använda Azure Batch-tjänsten på ett effektivt och ändamålsenligt sätt. Dessa bästa metoder härleds från vår erfarenhet av Batch och erfarenheter av Batch-kunder. Det är viktigt att förstå den här artikeln för att undvika designfallgropar, potentiella prestandaproblem och anti-mönster samtidigt som du utvecklar för och använder Batch.

I den här artikeln får du lära dig:

> [!div class="checklist"]
> - Vilka är de bästa metoderna
> - Varför du bör använda metodtips
> - Vad kan hända om du inte följer de bästa metoderna
> - Så här följer du de bästa metoderna

## <a name="pools"></a>Pooler

Batchpooler är beräkningsresurser för att köra jobb på batch-tjänsten. Följande avsnitt innehåller vägledning om de bästa metoderna för bästa möjliga information när du arbetar med batchpooler.

### <a name="pool-configuration-and-naming"></a>Poolkonfiguration och namngivning

- **Allokeringsläge för pool** När du skapar ett batchkonto kan du välja mellan två poolallokeringslägen: **Batch-tjänst** eller **användarprenumeration**. I de flesta fall bör du använda standardtjänstläget för batch, där pooler allokeras bakom kulisserna i Batch-hanterade prenumerationer. I det alternativa användarprenumerationsläget skapas virtuella Batch-datorer och andra resurser direkt i din prenumeration när en pool skapas. Användarkonton används främst för att aktivera en viktig men liten delmängd av scenarier. Du kan läsa mer om användarprenumerationsläge vid [Ytterligare konfiguration för användarprenumerationsläge](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Överväg jobb och aktivitetskörningstid när du bestämmer jobb till poolmappning.**
    Om du har jobb som huvudsakligen består av kortgående aktiviteter och antalet förväntade totala aktiviteter är små, så att den totala förväntade körningstiden för jobbet inte är lång, ska du inte fördela en ny pool för varje jobb. Fördelningstiden för noderna minskar jobbets körtid.

- **Pooler bör ha mer än en beräkningsnod.**
    Enskilda noder är inte garanterade att alltid vara tillgängliga. Maskinvarufel, uppdateringar av operativsystem och en mängd andra problem kan göra att enskilda noder är offline. Om din batch-arbetsbelastning kräver deterministiska, garanterade förlopp bör du allokera pooler med flera noder.

- **Återanvänd inte resursnamn.**
    Batch resurser (jobb, pooler, etc.) kommer ofta och går över tiden. Du kan till exempel skapa en pool på måndag, ta bort den på tisdag och sedan skapa en annan pool på torsdag. Varje ny resurs som du skapar bör ges ett unikt namn som du inte har använt tidigare. Detta kan göras med hjälp av ett GUID (antingen som hela resursnamnet eller som en del av det) eller bädda in den tid då resursen skapades i resursnamnet. Batch stöder [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), som kan användas för att ge en resurs ett läsbart namn även om det faktiska resurs-ID är något som inte är så mänskligt vänligt. Genom att använda unika namn blir det enklare för dig att skilja på vilken resurs som gjorde något i loggar och mått. Det tar också bort tvetydighet om du någonsin måste lämna in ett supportärende för en resurs.

- **Kontinuitet vid underhåll och fel vid pool.**
    Det är bäst att ha dina jobb använda pooler dynamiskt. Om dina jobb använder samma pool för allt, det finns en chans att dina jobb inte kommer att köras om något går fel med poolen. Detta är särskilt viktigt för tidskänsliga arbetsbelastningar. Om du vill åtgärda detta markerar eller skapar du en pool dynamiskt när du schemalägger varje jobb, eller har ett sätt att åsidosätta poolnamnet så att du kan kringgå en felaktig pool.

- **Kontinuitet i verksamheten vid underhåll och fel vid pool** Det finns många möjliga orsaker som kan hindra en pool från att växa till önskad storlek du önskar, såsom interna fel, kapacitetsbegränsningar, etc. Därför bör du vara redo att rikta om jobb vid en annan pool (eventuellt med en annan VM-storlek - Batch stöder detta via [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)) om det behövs. Undvik att använda ett statiskt pool-ID med förväntningen att det aldrig kommer att tas bort och aldrig ändras.

### <a name="pool-lifetime-and-billing"></a>Poolens livslängd och fakturering

Poolens livslängd kan variera beroende på vilken metod för allokering och alternativ som tillämpas på poolkonfigurationen. Pooler kan ha en godtycklig livstid och ett varierande antal beräkningsnoder i poolen när som helst. Det är ditt ansvar att hantera beräkningsnoderna i poolen antingen uttryckligen eller genom funktioner som tillhandahålls av tjänsten (automatisk skalning eller automatisk buffring).

- **Håll poolerna fräscha.**
    Du bör ändra storlek på dina pooler till noll med några månaders mellanrum för att säkerställa att du får de senaste nodagentuppdateringarna och buggfixarna. Poolen får inga nodagentuppdateringar om den inte återskapas eller ändra storlek på 0 beräkningsnoder. Innan du återskapar eller ändrar storlek på poolen rekommenderar vi att du hämtar alla nodagentloggar för felsökning, som beskrivs i avsnittet [Noder.](#nodes)

- **Återskapa poolen** På en liknande anteckning rekommenderas det inte att ta bort och återskapa dina pooler dagligen. Skapa i stället en ny pool och uppdatera dina befintliga jobb så att de pekar på den nya poolen. När alla aktiviteter har flyttats till den nya poolen tar du bort den gamla poolen.

- **Pool effektivitet och fakturering** Batch själv medför inga extra avgifter, men du ådrar dig avgifter för de beräkningsresurser som används. Du faktureras för varje beräkningsnod i poolen, oavsett vilket tillstånd den är i. Detta inkluderar eventuella avgifter som krävs för att noden ska köras, till exempel lagrings- och nätverkskostnader. Mer information finns i [Kostnadsanalys och budgetar för Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Fel vid poolallokering

Poolallokeringsfel kan inträffa när som helst under den första allokeringen eller efterföljande storleksanningar. Detta kan bero på tillfällig kapacitetsutmattning i en region eller fel i andra Azure-tjänster som Batch är beroende av. Din kärnkvot är inte en garanti utan snarare en gräns.

### <a name="unplanned-downtime"></a>Oplanerat stillestånd

Det är möjligt för batchpooler att uppleva driftstoppshändelser i Azure. Detta är viktigt att tänka på när du planerar och utvecklar ditt scenario eller arbetsflöde för Batch.

Om en nod misslyckas försöker Batch automatiskt att återställa dessa beräkningsnoder för din räkning. Detta kan utlösa omplanering av alla aktiviteter som körs på noden som återställs. Se [Designa för återförsök](#designing-for-retries-and-re-execution) om du vill veta mer om avbrutna uppgifter.

- **Beroende av Azure-region** Det rekommenderas att inte vara beroende av en enda Azure-region om du har en tidskänslig eller produktionsarbetsbelastning. Även sällsynta, det finns problem som kan påverka en hel region. Om din bearbetning till exempel måste börja vid en viss tidpunkt kan du överväga att skala upp poolen i den primära regionen *i god tid före starttiden.* Om poolskalan misslyckas kan du återgå till att skala upp en pool i en säkerhetskopieringsregion (eller regioner). Pooler över flera konton i olika regioner ger en färdig och lättillgänglig säkerhetskopiering om något går fel med en annan pool. Mer information finns i [Utforma ditt program för hög tillgänglighet](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Jobb

Ett jobb är en behållare som är utformad för att innehålla hundratals, tusentals eller till och med miljontals uppgifter.

- **Placera många uppgifter i ett jobb** Det är ineffektivt att använda ett jobb för att köra en enskild aktivitet. Det är till exempel mer effektivt att använda ett enda jobb som innehåller 1 000 aktiviteter i stället för att skapa 100 jobb som innehåller 10 aktiviteter vardera. Köra 1000 jobb, var och en med en enda uppgift, skulle vara den minst effektiva, långsammaste och dyraste metoden att ta.

    Utforma inte en Batch-lösning som kräver tusentals aktiva jobb samtidigt. Det finns ingen kvot för aktiviteter, så att utföra så många aktiviteter under så få jobb som möjligt använder effektivt dina [jobb- och jobbschemakvoter](batch-quota-limit.md#resource-quotas).

- **Livslängd för jobbet** Ett batchjobb har en obestämd livslängd tills det tas bort från systemet. Ett jobbs tillstånd anger om det kan acceptera fler aktiviteter för schemaläggning eller inte. Ett jobb flyttas inte automatiskt till slutfört tillstånd om det inte uttryckligen har avslutats. Detta kan utlösas automatiskt via egenskapen [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) eller [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Det finns en [standardaktiv jobb- och jobbschemakvot](batch-quota-limit.md#resource-quotas). Jobb och jobbscheman i slutfört tillstånd räknas inte in i den här kvoten.

## <a name="tasks"></a>Aktiviteter

Uppgifter är enskilda arbetsenheter som består av ett jobb. Aktiviteter skickas av användaren och schemaläggs av Batch på att beräkna noder. Det finns flera designöverväganden att göra när du skapar och kör uppgifter. I följande avsnitt beskrivs vanliga scenarier och hur du utformar dina uppgifter för att hantera problem och utföra effektivt.

- **Spara aktivitetsdata som en del av uppgiften.**
    Beräkningsnoder är till sin natur efemära. Det finns många funktioner i Batch såsom automatisk buffring och automatisk skalning som gör det enkelt för noder försvinna. När noder lämnar poolen (på grund av en storleksstorlek eller en poolborttagning) tas även alla filer på dessa noder bort. På grund av detta rekommenderas att innan en uppgift slutförs, flyttar den utdata från noden den körs på och till en varaktig butik, på samma sätt om en uppgift misslyckas bör flytta loggar som krävs för att diagnostisera felet i en varaktig butik. Batch har integrerat stöd för Azure Storage för att ladda upp data via [OutputFiles](batch-task-output-files.md), samt en mängd delade filsystem, eller så kan du utföra överföringen själv i dina uppgifter.

### <a name="task-lifetime"></a>Livslängd för uppgiften

- **Ta bort uppgifter när de är klara.**
    Ta bort aktiviteter när de inte längre behövs eller ange ett [statusvillkor för retentionTime-aktivitet.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) Om `retentionTime` en har angetts rensar Batch automatiskt det diskutrymme `retentionTime` som används av aktiviteten när den upphör att gälla.

    Om du tar bort uppgifter utförs två saker. Det säkerställer att du inte har en uppbyggnad av uppgifter i jobbet, vilket gör frågor / hitta den uppgift du är intresserad av svårare (eftersom du måste filtrera genom slutförda uppgifter). Det rensar också motsvarande uppgiftsdata på noden (förutsatt att `retentionTime` den inte redan har träffats). Detta säkerställer att noderna inte fyller upp med aktivitetsdata och på diskutrymme.

### <a name="task-submission"></a>Inlämning av uppgift

- **Skicka in ett stort antal uppgifter i en samling.**
    Uppgifter kan lämnas in individuellt eller i samlingar. Skicka uppgifter i [samlingar](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) på upp till 100 åt gången när du gör massinlämning av uppgifter för att minska overhead- och inlämningstiden.

### <a name="task-execution"></a>Körning av uppgift

- **Välja maxuppgifter per nod** Batch stöder överskrivning av aktiviteter på noder (kör fler aktiviteter än en nod har kärnor). Det är upp till dig att se till att dina uppgifter "passar" i noderna i din pool. Du kan till exempel ha en försämrad upplevelse om du försöker schemalägga åtta aktiviteter som var och `maxTasksPerNode = 8`en förbrukar 25 % CPU-användning på en nod (i en pool med ).

### <a name="designing-for-retries-and-re-execution"></a>Designa för återförsök och återutförande

Aktiviteter kan automatiskt göras om av Batch. Det finns två typer av försök: användarstyrda och interna. Användarstyrda återförsök anges av aktivitetens [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet). När ett program som anges i aktiviteten avslutas med en utgångskod som inte är `maxTaskRetryCount`noll görs uppgiften på nytt upp till värdet för .

Även om det är sällsynt kan en aktivitet göras internt på grund av fel på beräkningsnoden, till exempel att inte kunna uppdatera internt tillstånd eller ett fel på noden medan aktiviteten körs. Aktiviteten kommer att göras om på samma beräkningsnod, om möjligt, upp till en intern gräns innan du ger upp uppgiften och skjuter upp aktiviteten som ska schemaläggas om av Batch, eventuellt på en annan beräkningsnod.

- **Skapa varaktiga uppgifter** Uppgifter bör utformas för att tåla fel och rymma återförsök. Detta är särskilt viktigt för långvariga uppgifter. För att göra detta, se till att aktiviteter genererar samma, enda resultat även om de körs mer än en gång. Ett sätt att uppnå detta är att göra dina uppgifter "mål söker". Ett annat sätt är att se till att dina uppgifter är idempotenta (aktiviteter kommer att ha samma resultat oavsett hur många gånger de körs).

    Ett vanligt exempel är en uppgift att kopiera filer till en beräkningsnod. En enkel metod är en uppgift som kopierar alla angivna filer varje gång den körs, vilket är ineffektivt och inte är byggt för att motstå fel. Skapa i stället en uppgift för att säkerställa att filerna finns på beräkningsnoden. en uppgift som inte kopierar om filer som redan finns. På så sätt fortsätter uppgiften där den slutade om den avbröts.

- **Noder med låg prioritet** Det finns inga designskillnader när du utför dina uppgifter på dedikerade noder med låg prioritet eller låg prioritet. Oavsett om en aktivitet föregripas när du kör på en nod med låg prioritet eller avbryts på grund av ett fel på en dedikerad nod, mildras båda situationerna genom att utforma uppgiften för att motstå fel.

- **Tid för körning av uppgift** Undvik uppgifter med kort körningstid. Aktiviteter som bara körs i en till två sekunder är inte idealiska. Du bör försöka göra en betydande mängd arbete i en enskild uppgift (10 sekunders minimum, går upp till timmar eller dagar). Om varje aktivitet körs i en minut (eller mer) är planeringsomkostnaderna som en bråkdel av den totala beräkningstiden liten.

## <a name="nodes"></a>Noder

- **Startuppgifter ska vara idempotenta** I likhet med andra aktiviteter bör nodstartaktiviteten vara idempotent eftersom den körs igen varje gång noden startar. En idempotent uppgift är helt enkelt en som ger ett konsekvent resultat när du kör flera gånger.

- **Hantera tidskrävande tjänster via gränssnittet för operativsystemtjänster.**
    Ibland finns det ett behov av att köra en annan agent tillsammans med Batch-agenten i noden, t.ex. Vi rekommenderar att dessa agenter distribueras som OS-tjänster, `systemd` till exempel en Windows-tjänst eller en Linux-tjänst.

    När du kör dessa tjänster får de inte ta fillås på några filer i Batch-hanterade kataloger på noden, eftersom batch annars inte kan ta bort dessa kataloger på grund av fillåsen. Om du till exempel installerar en Windows-tjänst i en startuppgift kopierar du filerna någon annanstans (om filerna bara hoppar över kopian i stället för att starta tjänsten direkt från arbetskatalogen för startuppgiften). Installera tjänsten från den platsen. När Batch kör din startuppgift igen tas arbetskatalogen för startaktiviteten bort och den skapas igen. Detta fungerar eftersom tjänsten har fillås på den andra katalogen inte startuppgiften arbetskatalogen.

- **Undvik att skapa katalogkorsningar i Windows** Katalog korsningar, ibland kallas katalog hårda länkar, är svåra att hantera under uppgift och jobb rensning. Använd symlinks (soft-links) i stället för hårda länkar.

- **Samla batchagentloggarna om det finns ett problem** Om du upptäcker ett problem med beteendet hos en nod eller uppgifter som körs på en nod, rekommenderas att samla in batchagentloggarna innan du frigör noderna i fråga. Batch-agentloggarna kan samlas in med hjälp av API:et för överföringsbatch-tjänstens loggar. Dessa loggar kan levereras som en del av en supportbiljett till Microsoft och hjälper till med felsökning och lösning.

## <a name="security"></a>Säkerhet

### <a name="security-isolation"></a>Isolering av säkerhet

För isolering, om ditt scenario kräver att isolera jobb från varandra, bör du isolera dessa jobb genom att ha dem i separata pooler. En pool är säkerhetsisoleringsgränsen i Batch, och som standard är två pooler inte synliga eller kan kommunicera med varandra. Undvik att använda separata batchkonton som ett sätt att isolera.

## <a name="moving"></a>Flytta

### <a name="move-batch-account-across-regions"></a>Flytta batchkonto mellan regioner

Det finns olika scenarier där du vill flytta ditt befintliga batchkonto från en region till en annan. Du kanske till exempel vill flytta till en annan region som en del av planeringen för haveriberedskap.

Azure Batch-konton kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen av ditt batchkonto.  Du kan sedan iscensätta resursen i en annan region genom att exportera batchkontot till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallen till den nya regionen. När du har överfört mallen till den nya regionen måste du återskapa certifikat, jobbscheman och programpaket. Kom ihåg att ta bort det ursprungliga batchkontot eller resursgruppen om du vill genomföra ändringarna och slutföra flytten av batchkontot.

Mer information om Resurshanteraren och mallar finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

## <a name="connectivity-to-the-batch-service"></a>Anslutning till batchtjänsten

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Nätverkssäkerhetsgrupper (NSG) och användardefinierade rutter (UDRs)

När du etablerar [batchpooler i ett virtuellt nätverk](batch-virtual-network.md)ska du `BatchNodeManagement` se till att du följer riktlinjerna för användningen av servicetag, portar, protokoll och riktning för regeln.
Användning av servicetag rekommenderas starkt och inte de underliggande batch-tjänstens IP-adresser eftersom de kan ändras med tiden. Om du använder IP-adresserna för batchtjänsten direkt kan visas som instabilitet, avbrott eller avbrott för dina batchpooler när batch-tjänsten uppdaterar IP-adresser som används över tid. Om du för närvarande använder IP-adresser för batchtjänsten i NSG-reglerna rekommenderar vi att du växlar till att använda servicetag.

För användardefinierade vägar kontrollerar du att du har en process på plats för att uppdatera IP-adresser för batchtjänster regelbundet i flödestabellen när dessa ändras med tiden. Mer information om hur du hämtar listan över IP-adresser för batchtjänster finns i [Service-taggar lokalt](../virtual-network/service-tags-overview.md). IP-adresserna för batchtjänsten `BatchNodeManagement` associeras med servicetaggen (eller den regionala varianten som matchar din batchkontoregion).

### <a name="honoring-dns"></a>Hedra DNS

Se till att dina system respekterar DNS Time-to-Live (TTL) för din batch-kontotjänst-URL. Kontrollera dessutom att batch-tjänstklienter och andra anslutningsmekanismer till batch-tjänsten inte är beroende av IP-adresser.

Om dina begäranden får HTTP-svar på 5xx-nivå och det finns ett "Anslutning: stäng"-huvud i svaret, bör batch-tjänstklienten följa rekommendationen genom att stänga den befintliga anslutningen, matcha DNS för batchkontotjänstens URL och försöka följa begäranden om en ny anslutning.

### <a name="retrying-requests-automatically"></a>Försöka igen begäranden automatiskt

Se till att dina Batch-tjänstklienter har lämpliga principer för återförsök för att automatiskt försöka med dina begäranden, även under normal drift och inte enbart under några serviceunderhållsperioder. Dessa principer för återförsök bör sträcka sig över ett intervall på minst 5 minuter. Funktioner för automatiskt återförsök med olika batch-SDK:er, till exempel [klassen .NET RetryPolicyProvider](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet).

