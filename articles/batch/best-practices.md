---
title: Bästa praxis
description: Lär dig metod tips och användbara tips för att utveckla din Azure Batch-lösning.
ms.date: 04/03/2020
ms.topic: article
ms.openlocfilehash: 43a0020953ea44593cf38298a78547194751fc72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117513"
---
# <a name="azure-batch-best-practices"></a>Metod tips för Azure Batch

I den här artikeln beskrivs en samling bästa metoder för att använda tjänsten Azure Batch effektivt och effektivt. De här bästa metoderna är härledda från vår erfarenhet med batch och erfarenheterna av batch-kunder. Det är viktigt att förstå den här artikeln för att undvika design fall GRO par, potentiella prestanda problem och anti-mönster när du utvecklar och använder batch.

I den här artikeln får du lära dig:

> [!div class="checklist"]
> - Vilka är bästa praxis
> - Varför du bör använda bästa praxis
> - Vad kan inträffa om du inte följer de bästa metoderna
> - Så här följer du de bästa metoderna

## <a name="pools"></a>Pooler

Batch-pooler är beräknings resurserna för att köra jobb i batch-tjänsten. I följande avsnitt får du vägledning om bästa praxis att följa när du arbetar med batch-pooler.

### <a name="pool-configuration-and-naming"></a>Konfiguration av pooler och namngivning

- **Poolens fördelnings läge** När du skapar ett batch-konto kan du välja mellan två pool tilldelnings lägen: **Batch-tjänst** eller **användar prenumeration**. I de flesta fall bör du använda standard läget för batch-tjänsten, där pooler allokeras bakom scenerna i batch-hanterade prenumerationer. I det alternativa användarprenumerationsläget skapas virtuella Batch-datorer och andra resurser direkt i din prenumeration när en pool skapas. Användar prenumerations konton används främst för att aktivera en viktig, men liten del av scenarier. Du kan läsa mer om användar prenumerations läge på [ytterligare konfiguration för användar prenumerations läge](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Överväg jobb-och uppgifts körnings tid när du bestämmer jobb till pool-mappning.**
    Om du har jobb som huvudsakligen är kortsiktiga och de förväntade totala antalet aktiviteter är små, så att den förväntade körnings tiden för jobbet inte är lång, allokera inte en ny pool för varje jobb. Tilldelnings tiden för noderna minskar jobbets körnings tid.

- **Pooler bör ha mer än en Compute-nod.**
    Enskilda noder är inte garanterat alltid tillgängliga. Även om det är ovanligt kan maskin varu fel, operativ system uppdateringar och en annan värd för andra problem orsaka att enskilda noder är offline. Om batch-arbetsbelastningen kräver deterministisk, garanterad status, bör du allokera pooler med flera noder.

- **Återanvänd inte resurs namn.**
    Batch-resurser (jobb, pooler osv.) kommer ofta att gå över tid. Du kan till exempel skapa en pool på måndag, ta bort den på tisdag och sedan skapa en annan pool på torsdag. Varje ny resurs som du skapar ska tilldelas ett unikt namn som du inte har använt tidigare. Detta kan göras med hjälp av ett GUID (antingen som hela resurs namnet eller som en del av den) eller bädda in den tid som resursen skapades i resurs namnet. Batch stöder [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), som kan användas för att ge en resurs ett läsligt namn även om det verkliga resurs-ID: t är något som inte är det som är mänskligt. Med hjälp av unika namn blir det enklare för dig att särskilja vilka specifika resurser som något i loggar och mått. Det tar också bort tvetydighet om du skulle behöva ange ett support ärende för en resurs.

- **Kontinuitet under underhåll av pooler och haveri.**
    Det är bäst att låta dina jobb använda pooler dynamiskt. Om dina jobb använder samma pool för allt, finns det en risk att dina jobb inte körs om något går fel med poolen. Detta är särskilt viktigt för tids känsliga arbets belastningar. Du kan åtgärda detta genom att välja eller skapa en pool dynamiskt när du schemalägger varje jobb, eller ha ett sätt att åsidosätta poolnamn så att du kan kringgå en ohälsosam pool.

- **Verksamhets kontinuitet under underhåll och haveri för pooler** Det finns många möjliga orsaker som kan förhindra att en pool växer till önskad storlek som du vill ha, till exempel interna fel, kapacitets begränsningar osv. Därför bör du vara redo att återanvända jobb på en annan pool (eventuellt med en annan VM-storlek – batch stöder detta via [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)) om det behövs. Undvik att använda ett statiskt pool-ID med förväntat att det aldrig kommer att tas bort och aldrig ändras.

### <a name="pool-lifetime-and-billing"></a>Livstid för pool och fakturering

Poolens livs längd kan variera beroende på vilken metod du vill tilldela och vilka alternativ som tillämpas på konfigurationen av poolen. Pooler kan ha en godtycklig livs längd och ett varierande antal data bearbetnings noder i poolen vid varje tidpunkt. Det är ditt ansvar att hantera Compute-noderna i poolen, antingen uttryckligen eller via funktioner som tillhandahålls av tjänsten (autoskalning eller autopool).

- **Se till att pooler är färska.**
    Du bör ändra storlek på dina pooler till noll med några månader för att se till att du får de senaste uppdateringarna för Node agent och fel korrigeringar. Poolen tar inte emot uppdateringar för Node-agenten om den inte återskapas eller ändras till 0 datornoder. Innan du återskapar eller ändrar storlek på poolen rekommenderar vi att du hämtar eventuella noder för fel sökning enligt beskrivningen i avsnittet [Nodes](#nodes) .

- **Skapa ny pool** På en liknande anteckning rekommenderar vi inte att du tar bort och återskapar dina pooler på daglig basis. Skapa i stället en ny pool och uppdatera dina befintliga jobb så att de pekar på den nya poolen. När alla aktiviteter har flyttats till den nya poolen tar du bort den gamla poolen.

- **Pool effektivitet och fakturering** Batch debiterar sig inga extra avgifter, men du debiteras för de beräknings resurser som används. Du debiteras för varje Compute-nod i poolen, oavsett vilken status den har i. Detta inkluderar eventuella avgifter som krävs för att noden ska kunna köras, till exempel lagrings-och nätverks kostnader. Mer information om bästa praxis finns i [kostnads analys och budgetar för Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Allokeringsfel för pool

Allokeringsfel för pooler kan inträffa när som helst under första allokeringen eller efterföljande storleks ändringar. Detta kan bero på temporär kapacitets utbelastning i en region eller andra Azure-tjänster som batchen använder. Kärn kvoten är inte en garanti utan en gräns.

### <a name="unplanned-downtime"></a>Oplanerat stillestånd

Batch-pooler kan uppleva stillestånds händelser i Azure. Detta är viktigt att tänka på när du planerar och utvecklar ditt scenario eller ett arbets flöde för batch.

Om en nod Miss lyckas försöker batch automatiskt återställa de här Compute-noderna åt dig. Detta kan utlösa en omschemaläggningen av alla pågående aktiviteter på den nod som återställs. Se [utforma för nya försök](#designing-for-retries-and-re-execution) för att lära dig mer om avbrutna uppgifter.

- **Azure-region beroende** Vi rekommenderar att du inte är beroende av en enda Azure-region om du har en tids känslig eller produktions belastning. Även om det är sällsynt, finns det problem som kan påverka en hel region. Om din bearbetning till exempel behöver starta vid en angiven tidpunkt, kan du överväga att skala upp poolen i din primära region på ett *bra sätt innan du börjar med start tiden*. Om poolens skalning Miss lyckas kan du återgå till att skala upp en pool i en säkerhets kopierings region (eller regioner). Pooler över flera konton i olika regioner ger en klar och lättillgänglig säkerhets kopia om något går fel med en annan pool. Mer information finns i [utforma ditt program för hög tillgänglighet](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Jobb

Ett jobb är en behållare som är utformad för att innehålla hundratals, tusentals eller till och med miljon tals uppgifter.

- **Publicera många aktiviteter i ett jobb** Att använda ett jobb för att köra en enskild uppgift är ineffektivt. Till exempel är det mer effektivt att använda ett enda jobb som innehåller 1000 uppgifter i stället för att skapa 100-jobb som innehåller 10 aktiviteter. Att köra 1000-jobb, var och en med en enda uppgift, är den minst effektiva, långsammast och mest dyra metoden att ta.

    Skapa inte en batch-lösning som kräver tusentals samtidigt av aktiva jobb. Det finns ingen kvot för aktiviteter, så du kan köra så många uppgifter som möjligt under så få jobb som möjligt, så att du effektivt kan använda [jobbets och jobb schema kvoterna](batch-quota-limit.md#resource-quotas).

- **Jobbets livs längd** Ett batch-jobb har en obegränsad livs längd tills det tas bort från systemet. Ett jobbs tillstånd anger om det kan acceptera fler aktiviteter för schemaläggning eller inte. Ett jobb flyttas inte automatiskt till slutfört tillstånd om det inte uttryckligen avslutas. Detta kan aktive ras automatiskt via egenskapen [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) eller [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Det finns ett [aktivt standard jobb och en kvot för jobb schema](batch-quota-limit.md#resource-quotas). Jobb och jobb scheman i slutfört tillstånd räknas inte över till den här kvoten.

## <a name="tasks"></a>Aktiviteter

Aktiviteter är enskilda enheter av arbete som utgör ett jobb. Aktiviteter skickas av användaren och schemaläggs av batch på för att beräkna noder. Det finns flera design aspekter att fatta när du skapar och kör uppgifter. I följande avsnitt beskrivs vanliga scenarier och hur du utformar dina aktiviteter för att hantera problem och utföra effektiva åtgärder.

- **Spara uppgifts data som en del av uppgiften.**
    Compute-noder är av sin natur. Det finns många funktioner i batch, till exempel autopool och autoskalning som gör det enkelt för noder att försvinna. När noder lämnar poolen (på grund av en storleks ändring eller en pool borttagning) raderas även alla filer på noderna. Därför rekommenderar vi att innan en aktivitet slutförs, flyttas utmatningen från noden som körs på och till ett varaktigt lager, på samma sätt som om en aktivitet Miss lyckas bör det flyttas loggar som krävs för att diagnosticera felet till ett varaktigt lager. Batch har integrerat stöd Azure Storage för att ladda upp data via [OutputFiles](batch-task-output-files.md), samt en rad olika delade fil system, eller så kan du utföra överföringen själv i dina uppgifter.

### <a name="task-lifetime"></a>Aktivitetens livs längd

- **Ta bort uppgifter när de är klara.**
    Ta bort aktiviteter när de inte längre behövs eller ange en [retentionTime](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) . Om en `retentionTime` har angetts rensar batch automatiskt disk utrymmet som används av aktiviteten när det `retentionTime` upphör att gälla.

    Att ta bort uppgifter utför två saker. Det garanterar att du inte har en version av aktiviteterna i jobbet, genom att skicka frågor till/hitta den aktivitet du är intresse rad av (eftersom du måste filtrera igenom de slutförda uppgifterna). Den rensar också motsvarande uppgifts data på noden (anges `retentionTime` har inte redan nåtts). På så sätt ser du till att noderna inte fyller i aktivitets data och slut på disk utrymme.

### <a name="task-submission"></a>Uppgifts överföring

- **Skicka in ett stort antal aktiviteter i en samling.**
    Aktiviteter kan skickas på en enskild basis eller i samlingar. Skicka uppgifter i [samlingar](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) på upp till 100 i taget när du utför Mass inlämning av uppgifter för att minska omkostnader och överförings tider.

### <a name="task-execution"></a>Uppgifts körning

- **Välja Max aktiviteter per nod** Batch stöder oversubscribing-aktiviteter på noder (som kör fler aktiviteter än en nod har kärnor). Det är upp till dig att se till att aktiviteterna anpassas till noderna i poolen. Du kan till exempel ha en försämrad upplevelse om du försöker schemalägga åtta uppgifter som varje använder 25% CPU-användning på en nod (i en pool med `maxTasksPerNode = 8`).

### <a name="designing-for-retries-and-re-execution"></a>Design för nya försök och omkörning

Aktiviteter kan göras om automatiskt med batch. Det finns två typer av återförsök: User-styrd och Internal. Användar kontrollerade återförsök anges av aktivitetens [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet). När ett program som anges i uppgiften avslutas med en slutkod som inte är noll, görs ett nytt försök till värdet för `maxTaskRetryCount`.

Även om det är sällsynt, kan en aktivitet omprövas internt på grund av att det inte går att uppdatera beräknings noden, till exempel att det inte går att uppdatera det interna läget eller ett haveri på noden medan aktiviteten körs. Aktiviteten provas på samma Compute-nod, om möjligt, upp till en intern gräns innan den ger aktiviteten och en uppskjuten aktivitet som ska schemaläggas om av batch, eventuellt på en annan Compute-nod.

- **Bygg tåliga uppgifter** Aktiviteter bör utformas för att motstå fel och försöka hantera försök igen. Detta är särskilt viktigt för tids krävande uppgifter. Det gör du genom att se till att uppgifterna genererar samma resultat även om de körs mer än en gång. Ett sätt att åstadkomma detta är att göra dina uppgifter till "måls sökning". Ett annat sätt är att se till att uppgifterna är idempotenta (uppgifter kommer att ha samma resultat oavsett hur många gånger de körs).

    Ett vanligt exempel är en uppgift för att kopiera filer till en Compute-nod. En enkel metod är en uppgift som kopierar alla angivna filer varje gång den körs, vilket är ineffektivt och inte är konstruerat för att motstå felen. Skapa i stället en aktivitet för att se till att filerna finns på Compute-noden. en uppgift som inte kopierar om filer som redan finns. På det här sättet hämtar uppgiften där den slutade om den avbröts.

- **Noder med låg prioritet** Det finns inga design skillnader när du kör aktiviteter på dedikerade eller låg prioritets noder. Om en aktivitet avbryts vid körning på en nod med låg prioritet eller avbryts på grund av ett haveri på en dedikerad nod, begränsas båda situationerna genom att aktiviteten ändras till att motstå felen.

- **Körnings tid för aktivitet** Undvik uppgifter med kort körnings tid. Aktiviteter som bara körs för en till två sekunder är inte idealiska. Du bör försöka göra en stor mängd arbete i en enskild uppgift (10 sekunders tid på upp till timmar eller dagar). Om varje aktivitet körs i en minut (eller mer) är tids gränsen för schemaläggningen som en bråkdel av den totala beräknings tiden liten.

## <a name="nodes"></a>Noder

- **Start aktiviteter ska vara idempotenta** Precis som andra uppgifter ska nodens start aktivitet vara idempotenta eftersom den kommer att köras igen varje gång noden startas. En idempotenta-uppgift är bara en som ger ett konsekvent resultat vid körning flera gånger.

- **Hantera tids krävande tjänster via gränssnittet för operativ system tjänster.**
    Ibland måste det finnas behov av att köra en annan Agent tillsammans med batch-agenten i noden, t. ex. för att samla in data från noden och rapportera den. Vi rekommenderar att dessa agenter distribueras som OS-tjänster, till exempel en Windows-tjänst eller en `systemd` Linux-tjänst.

    När de här tjänsterna körs får de inte ta fillås på några filer i batch-hanterade kataloger på noden, eftersom annars kan batch inte ta bort dessa kataloger på grund av fillås. Om du till exempel installerar en Windows-tjänst i en start aktivitet, i stället för att starta tjänsten direkt från arbets katalogen starta aktivitet, kopierar du filerna någon annan stans (om filerna bara finns hoppar du över kopian). Installera tjänsten från den platsen. När batch kör om din start uppgift, tar den bort start uppgiftens arbets katalog och skapar den igen. Detta fungerar eftersom tjänsten har fillås på den andra katalogen, inte start katalogen för start uppgiften.

- **Undvik att skapa katalog Knut punkter i Windows** Katalog kopplingar, ibland kallade katalog hårda länkar, är svåra att hantera under aktivitets-och jobb rensning. Använd symlinks (mjuka länkar) i stället för hårda länkar.

- **Samla in batch-agentens loggar om det är problem** Om du upptäcker ett problem som involverar beteendet för en nod eller aktiviteter som körs på en nod, rekommenderar vi att du samlar in batch-agentens loggar innan du avallokerar noderna i fråga. Batch agent-loggarna kan samlas in med hjälp av API: t Ladda upp batch-tjänst loggar. Dessa loggar kan levereras som en del av ett support ärende till Microsoft och hjälper till med problem fel sökning och lösning.

## <a name="security"></a>Säkerhet

### <a name="security-isolation"></a>Säkerhets isolering

Om ditt scenario kräver att du isolerar jobb från varandra i isolerings syfte bör du isolera dessa jobb genom att ha dem i separata pooler. En pool är säkerhets isolerings gränser i batch och som standard är två pooler inte synliga eller kan kommunicera med varandra. Undvik att använda separata batch-konton som ett sätt att isolera.

## <a name="moving"></a>Flyttat

### <a name="move-batch-account-across-regions"></a>Flytta batch-konto mellan regioner

Det finns olika scenarier där du vill flytta ditt befintliga batch-konto från en region till en annan. Till exempel kanske du vill flytta till en annan region som en del av Disaster Recovery-planeringen.

Azure Batch-konton kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager mall för att exportera den befintliga konfigurationen av ditt batch-konto.  Du kan sedan mellanlagra resursen i en annan region genom att exportera batch-kontot till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen. När du har överfört mallen till den nya regionen måste du återskapa certifikat, jobb scheman och programpaket. Kom ihåg att ta bort det ursprungliga batch-kontot eller resurs gruppen för att genomföra ändringarna och slutföra flyttningen av batch-kontot.

Mer information om Resource Manager och mallar finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

## <a name="connectivity-to-the-batch-service"></a>Anslutning till batch-tjänsten

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Nätverks säkerhets grupper (NSG: er) och användardefinierade vägar (UDR)

När du konfigurerar [batch-pooler i ett virtuellt nätverk](batch-virtual-network.md)bör du se till att du noga följer rikt linjerna för användningen av `BatchNodeManagement` service tag-numret, portarna, protokollen och riktningen för regeln.
Användningen av service tag gen rekommenderas och inte de underliggande IP-adresserna för batch-tjänsten eftersom de kan ändras med tiden. Om IP-adresser för batch-tjänsten används direkt kan det vara instabilt, avbrott eller avbrott för batch-pooler när batch-tjänsten uppdaterar IP-adresser som används över tid. Om du för närvarande använder batch-tjänstens IP-adresser i dina NSG-regler, rekommenderar vi att du växlar till att använda tjänst tag gen.

För användardefinierade vägar måste du se till att du har en process på plats för att uppdatera batch-tjänstens IP-adresser regelbundet i routningstabellen som ändringen över tid. Information om hur du hämtar en lista över IP-adresser för batch-tjänsten finns i [tjänst märkning lokalt](../virtual-network/service-tags-overview.md). IP-adresserna för batch-tjänsten kommer att `BatchNodeManagement` associeras med Service Tag-numret (eller den regionala variant som matchar ditt batch-kontoområdet).

### <a name="honoring-dns"></a>DNS-inlöst

Se till att dina system följer DNS-TTL (Time-to-Live) för batch-kontots tjänst-URL. Kontrol lera också att batch-tjänstens klienter och andra anslutnings metoder till batch-tjänsten inte förlitar sig på IP-adresser.

Om dina begär Anden tar emot HTTP-svar på 5xx nivå och det finns ett "anslutning: Stäng"-huvud i svaret, bör batch-tjänstens klient studera rekommendationen genom att stänga den befintliga anslutningen, matcha om DNS för batch-kontots tjänst-URL och försöka utföra följande förfrågningar på en ny anslutning.

### <a name="retrying-requests-automatically"></a>Försöker utföra begär anden igen automatiskt

Se till att dina batch-betjäna klienter har lämpliga principer för återförsök på plats för att automatiskt försöka utföra begär anden igen, även under normal drift och inte enbart under några tids perioder för tjänste underhåll. Dessa principer för återförsök bör omfatta ett intervall på minst 5 minuter. Funktioner för automatisk återförsök tillhandahålls med olika batch-SDK: er, till exempel [.net RetryPolicyProvider-klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet).

