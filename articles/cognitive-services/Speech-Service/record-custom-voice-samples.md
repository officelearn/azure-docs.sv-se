---
title: Registrera anpassade voice-exempel – Speech Services
titleSuffix: Azure Cognitive Services
description: Gör en anpassad röst hög kvalitet genom att förbereda ett robust skript, anställa bra röst personal och spela in professionellt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b4360150064d2f42cc2dc2cc3ef9f7783a5cb175
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866550"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Post voice-exempel för att skapa en anpassad röst

Skapa en anpassad ton för produktion av hög kvalitet från grunden är inte ett avslappnat företag. Den centrala komponenten i en anpassad röst är ett stort antal ljudsamplingar mänskliga tal. Det är viktigt att dessa ljudinspelningar är av hög kvalitet. Välj en röst-personal som har erfarenhet av att göra dessa typer av inspelningar och låta dem registreras av en behöriga inspelning tekniker med professionella utrustning.

Innan du kan göra dessa inspelningar, måste du dock ett skript: ord som uttalas av din röst-personal för att skapa inläsningar. För bästa resultat måste du ha bra fonetiska täckning och tillräcklig mängd att träna modellen anpassade röst ditt skript.

Många små, men viktiga information ingår i att skapa en professionell röstinspelning. Den här guiden är en karta för en process som hjälper dig att få bra, enhetligt resultat.

> [!TIP]
> Överväg att spännande Microsoft för att utveckla din egen röst för högsta kvalitet resultaten. Microsoft har stor erfarenhet som producerar högkvalitativa röster för egna produkter, inklusive Cortana och Office.

## <a name="voice-recording-roles"></a>Röstinspelning roller

Det finns fyra grundläggande roller i ett projekt för inspelning av anpassade Röst:

Roll|Syfte
-|-
Röst personal        |Den här personens röst utgör grunden för anpassade röst.
Spela in-tekniker  |Övervakar de tekniska aspekterna av inspelningen och fungerar färdskrivare.
Director            |Förbereder skriptet och coaches voice-personal prestanda.
Redigeringsprogram              |Slutför ljudfilerna och förbereder dem för överföring till den anpassade Voice-portalen.

En person kan fylla mer än en roll. Den här guiden förutsätter att du ska främst fylla rollen director och anställa både en röst-personal och en inspelning tekniker. Om du vill göra inspelningarna själv, den här artikeln innehåller information om rollen inspelning tekniker. Rollen Redigerare behövs inte förrän efter sessionen, så kan utföras av director eller spela in-tekniker.

## <a name="choose-your-voice-talent"></a>Välj din röst personal

Aktörer med erfarenhet av voiceover eller röst tecken arbete se bra anpassade röst personal. Du hittar också ofta lämpligt personal bland Speakers och läsare för diskussionsgrupper.

Välj röst personal vars naturligt röst du gillar. Det är möjligt att skapa unika ”tecken” röster, men det är mycket svårare för de flesta personal att utföra dem konsekvent och arbetet som kan orsaka röst stam.

> [!TIP]
> I allmänhet Undvik att använda identifierbara röster för att skapa en anpassad röst, såvida inte naturligtvis målet är att skapa en kändisar röst. Mindre kända röster är vanligtvis mindre störande för användarna.

Den enda viktigaste faktorn för att välja röst personal är konsekvens. Inspelningar bör alla ljud som de har gjorts på samma dag i samma rum. Du kan itu med den här perfekt via bra inspelning metoder och tekniker.

Din röst personal är den andra halvan av formeln. Han eller hon att prata med konstanta, volymnivå, försäljningsargument och ton. Rensa diction är ett måste. Personal måste också kunna ha en strikt kontroll hans eller hennes försäljningsargument variation känslomässig påverkar och maner tal, ovanor.

Spela in anpassade röst exempel kan vara mer tröttande än andra typer av röst arbete. De flesta röst personal registrera för två eller tre timmar per dag. Begränsa sessioner till tre eller fyra en vecka, med en dag av emellan om möjligt.

Inspelningar görs för en röst-modellen ska vara emotionella neutral. Det vill säga läsas ett sad uttryck inte på ett sad sätt. Stämning kan läggas till syntetiskt tal senare via prosody kontroller. Arbeta med din röst-personal att utveckla en ”person” som definierar övergripande ljud och känslomässig tonen hos anpassade röst. I processen, ska du identifiera hur ”neutral” låter för den person.

En person kan exempelvis ha en naturligt upbeat personlighet. ”” Rösten kan så ha ner optimism även om de pratar neutralt. Sådana en personlighet egenskaper bör dock diskret och konsekvent. Lyssna på avläsningar av befintlig för att få en uppfattning om vad du sträva efter att få.

> [!TIP]
> Vanligtvis bör du äger röstinspelningar som du gör. Din röst-personal bör vara lämpar sig för ett arbete för nyanställda kontrakt för projektet.

## <a name="create-a-script"></a>Skapa ett skript

Startpunkten för alla anpassade röstinspelning sessionen är skript som innehåller uttryck som ska talas av din röst-personal. (Termen ”yttranden” omfattar både fullständiga meningar och kortare fraser.)

Yttranden i skriptet kan komma från var som helst: fiction, icke-fiction, avskrifter av tal, nyheter och allt annat som finns i formulär för utskrift. Om du vill kontrollera att din röst när du har bra på specifika typer av ord (till exempel medicinsk terminologi eller programming jargong) kanske du vill inkludera meningar från skolrelaterad undersökningar eller tekniska dokumentationen. En kort beskrivning av eventuella juridiska problem finns i den [”Legalities”](#legalities) avsnittet. Du kan också skriva din egen text.

Din yttranden behöver inte kommer från samma källa eller samma typ av datakälla. De behöver inte ens ha något att göra med varandra. Men om du ska använda Ange fraser (till exempel ”du har loggat in”) i ditt tal-program, se till att inkludera dem i ditt skript. Detta ger din anpassade röst större möjlighet att uttala dessa fraser bra. Och om du vill ska använda en inspelning i stället för syntetiskt tal kan du har redan det i samma röst.

Konsekvens är nyckeln när de väljer röst personal, är olika hallmark på ett bra skript. Skriptet bör innehålla många olika ord och meningar med en rad olika mening längder, strukturer och sinnesstämningar. Varje ljud på språket som ska vara representeras flera gånger och i flera sammanhang (kallas *fonetiska täckning*).

Texten bör dessutom införliva alla metoder för att ett visst ljud kan representeras skriftligen och placera varje ljud på olika platser i meningarna. Både deklarativa meningar och frågor bör ingår och läsa med lämpliga intonation.

Det är svårt att skriva ett skript som innehåller *enough* data för att tillåta Custom Speech-portalen för att skapa en bra röst. I praktiken är det enklaste sättet att göra ett skript som uppnår robust fonetiska täckning att inkludera ett stort antal prover. Standard röster som Microsoft tillhandahåller har skapats från tiotusentals yttranden. Du bör vara beredd på att registrera ett par till flera tusen yttranden minimikrav för att skapa en anpassad röst hög kvalitet.

Kontrollera skriptet noggrant för fel. Om det är möjligt att någon annan kolla för. När du kör igenom skriptet med din personal, kommer du antagligen se några fler fel.

### <a name="script-format"></a>Skriptformat

Du kan skriva ditt skript i Microsoft Word. Skriptet är för användning under inspelning sessionen, så du kan konfigurera det något sätt som du hittar enkelt att arbeta med. Skapa textfilen som krävs av den anpassade Voice-portalen separat.

Ett skript för grundläggande format innehåller tre kolumner:

* Antalet uttryck, börjar på 1. Numrering gör det enkelt för alla i studio för att referera till en viss uttryck (”vi försöker tal 356 igen”). Du kan använda Word-punkt numrering funktionen för att numrera rader i tabellen automatiskt.
* En tom kolumn där du ska skriva take-nummer eller tid för varje uttryck för att hitta i klar inspelningen.
* Texten för uttryck själva.

![Exempelskript](media/custom-voice/script.png)

> [!NOTE]
> De flesta studios registrera kortfattat segment som kallas *tar*. Varje take innehåller normalt 10 till 24 yttranden. Notera bara take-nummer är tillräcklig för att hitta ett uttryck senare. Om du spelar i en studio som helst vill längre inspelningar vill du Observera tid-kod i stället. Studio har en framstående tid visning.

Lämna tillräckligt med utrymme efter varje rad att skriva anteckningar. Var noga med att inga uttryck delas mellan sidor. Sidorna och Skriv ut ditt skript på ena sidan av dokumentet.

Skriva ut tre kopior av skriptet: en för personal, en för teknikern och en för director (du). Använda ett gem i stället för staples: en erfaren röst artist separeras sidor för att undvika att göra bruset som sidorna är aktiverade.

### <a name="legalities"></a>Legalities

Enligt upphovsrätten, kan en aktör läsning av upphovsrättsskyddade text vara en prestanda som författare av arbetet ska ersättas. Den här prestanda kommer inte att känna igen i den slutgiltiga produkten, anpassade röst. Men det är möjligt, är laglighet med att använda upphovsrättsskyddade verk för detta ändamål inte väletablerad. Microsoft kan inte tillhandahålla juridisk rådgivning på det här problemet; Läs dina egna ombud.

Som tur är kan går det att undvika de här problemen helt och hållet. Det finns många källor med text som du kan använda utan behörighet eller licens.

|Text-källa|Beskrivning|
|-|-|
|[CMU Arctic Kristi](http://festvox.org/cmu_arctic/)|Om 1100 meningar som valts ut av upphovsrätt som ska användas i tal syntes projekt. En utmärkt utgångspunkt.|
|Fungerar inte längre<br>upphovsrätten|Vanligtvis publicerade fungerar innan 1923. För engelska, [projekt Gutenberg](https://www.gutenberg.org/) erbjuder tiotusentals sådana verk. Du kanske vill fokusera på nyare fungerar som språket är närmare moderna engelska.|
|Government&nbsp;fungerar|Fungerar som skapats av USA: S regering är inte upphovsrättsskyddade i USA, även om den offentliga sektorn kan göra anspråk på copyright i andra länder/regioner.|
|Allmän egendom|Fungerar för vilka copyright har uttryckligen anges eller som har varit dedikerad till den offentliga domänen. Det kanske inte går att avstå från copyright helt i vissa jurisdiktioner.|
|Permissively licensierad fungerar|Fungerar som distribueras under en licens som Creative Commons eller GNU kostnadsfri dokumentation licens (GFDL). Wikipedia använder GFDL. Vissa licenser, men kan införa begränsningar för prestanda för licensierade innehåll som kan påverka skapandet av en anpassad voice-modell, så Läs noggrant.|

## <a name="recording-your-script"></a>Spela in skriptet

Registrera din skriptet i en professionell inspelning studio som specialiserar sig i röst arbete. De har en inspelning monter rätt utrustning och rätt personer att driva den. Det lönar sig inte för att spela in ändå.

Diskutera dina projekt med i studio inspelning tekniker och lyssna på hans eller hennes råd. Inspelningen bör ha lite eller ingen dynamiskt omfång komprimering (högst 4:1). Det är viktigt att ljudet har konsekventa volym och signal brus högt förhållande, samtidigt som det är kostnadsfritt oönskade ljud.

### <a name="do-it-yourself"></a>Göra det själv

Om du vill göra inspelningen själv, istället för att gå till en inspelning studio, här är en kort introduktion. Tack vare röstfunktion home inspelning och nya podcastmöjligheter är det enklare än någonsin att hitta bra inspelning råd och resurserna.

Din ”spela in monter” ska vara ett litet rum utan märkbar echo eller ”rummet tonen”. Det bör vara så tyst och soundproof som möjligt. Draperier på väggarna kan användas för att minska echo och neutralisera eller ”deaden” ljud i rummet.

Använda en högkvalitativ studio kylaren mikrofon (”mic” för kort) avsedd för röstinspelning. Sennheiser AKG och även senare zoomning mikrofoner kan ge bra resultat. Du kan köpa en mic eller hyra en från en lokal audiovisuellt hyres-företag. Leta efter en med en USB-gränssnitt. Den här typen av mic kombinerar bekvämt mikrofon element, preamp och analog till digital konverterare till ett paket, vilket förenklar koll.

Du kan också använda en analog mikrofon. Många hyra datahus erbjuder ”vintage” mikrofoner världskänd för sin röst-tecken. Observera att professionella analog gear använder belastningsutjämnad XLR kopplingar, snarare än 1/4-tums-plugin som används i konsumentutrustning. Om du går analog, måste du också en preamp och ett ljud datorgränssnittet med dessa anslutningar.

Installera mikrofonen på en fristående eller sådär och installera ett pop filter framför mikrofonen att eliminera brus från ”plosive” konsonanter som ”p” och ”b”. En del mikrofoner levereras med en inaktivering montering som isolerar dem från vibrationer i den fristående, vilket är användbart.

Röst-personal måste hålla sig i ett konsekvent avstånd från mikrofonen. Använd bandet på våning för att markera var han eller hon bör stå. Om personal föredrar att vara, var särskilt noga med att övervaka mic avståndet och undvika chair bruset.

Använd en FRIS för skriptet. Undvik nöjesfiske fristående så att den kan återspegla ljud till mikrofonen.

Den person som driver färdskrivare – teknikern – ska vara i ett separat rum från personal med något sätt att kommunicera med personal i inspelning monter (en *talkback krets).*

Inspelningen ska innehålla som lite störningar som möjligt, med målet för en signal brus 80-db-förhållande eller mindre.

Lyssna nära på en inspelning av tystnad i din ”monter”, ta reda på där alla bruset kommer från, och eliminera orsaken. Vanliga källor för brus är ventilation, fluorescerande ljus förkopplingsdon, trafik på Närliggande vägar och utrustning fans (även bärbara datorer kan ha fans). Mikrofoner och kablar kan avläsa elektriska störningar från i närheten AC koaxialkabel, vanligtvis en surrandet eller buzz. En marknadsföringsplan kan också orsakas av en *grunden loop*, som beror på att utrustning som är ansluten till mer än en elnätet.

> [!TIP]
> I vissa fall kanske du kan använda en equalizer eller en bruset minskning programvara plugin-programmet för att ta bort brus från din inspelningar, även om det alltid är bäst att stoppa den vid dess källa.

Ange nivåer så att det mesta av digital registrering tillgängliga dynamiska omfång används utan overdriving. Innebär det att ange ljudet llåt, men inte så högt som den förvrängs. Ett exempel på Wave för en bra inspelning visas i följande bild:

![En bra inspelning Wave](media/custom-voice/good-recording.png)

Här kan de flesta av intervallet (höjd) används, men de högsta topparna av signalen når inte upp eller ned i fönstret. Du kan också se att tystnad i inspelningen tillhandahåller en tunn vågrät linje, som anger en låg bruset våning. Den här inspelningen har dynamiska intervallet och signal brus förhållande.

Post direkt till datorn via en högkvalitativ ljud-gränssnittet eller en USB-port, beroende på mic som du använder. Analog, håller ljud kedjan enkla: mic, preamp, ljud-gränssnittet, datorn. Du kan licensiera både [Avid Pro verktyg](https://www.avid.com/en/pro-tools) och [Adobe Audition](https://www.adobe.com/products/audition.html) per månad till en rimlig kostnad. Om din budget är mycket nära, prova det kostnadsfritt [Audacity](https://www.audacityteam.org/).

Registrera med 44,1 kHz 16-bitars monophonic (CD kvalitet) eller snabbare. Aktuella-för-avancerade är 48 kHz 24-bitars om utrustningen stöder den. Du kommer nedåtsampla dina ljud till 16 kHz 16-bitars innan du skickar den till den anpassade Voice-portalen. Ändå har lönar det sig att en högkvalitativ ursprungliga inspelning i den händelse att ändringar behövs.

Vi rekommenderar har olika personer som hanterar i rollerna för director, tekniker och personal. Försök inte att göra det. alla själv. I en Zooma kan en person ha både director och teknikern.

### <a name="before-the-session"></a>Innan sessionen

Undvik att ha lagt tid studio, kör du via skript med din röst personal före inspelning sessionen. Medan voice-personal blir van texten kan kan han eller hon tydliggöra uttal för alla okända ord.

> [!NOTE]
> De flesta inspelning studios erbjuder elektronisk visning av skript i monter för inspelning. I det här fallet Skriv anteckningarna snabbgenomgång direkt i skriptets dokumentet. Papper för anteckningar under sessionen, men fortfarande vill. De flesta tekniker kan en utskrift för. Och du fortfarande vill kopiera tredje ut som en säkerhetskopiering för personal om datorn inte är igång.

Din röst-personal kan be vilka ord som du vill att framhållit i ett uttryck (”avgörande ordet”). Be honom eller henne att du vill att en naturlig läsning med ingen särskild vikt. Fokus kan läggas till när tal syntetiskt; Det får inte vara en del av den ursprungliga inspelningen.

Dirigera personal att uttala orden tydligt. Varje ord i skriptet bör vara markant som skrivits. Ljud bör inte utelämnas eller slurred tillsammans, som är vanligt i avslappnat tal, *om de har skrivits på så sätt i skriptet*.

|Texten|Oönskade avslappnat uttal|
|-|-|
|aldrig kommer för att ge dig|aldrig kommer för att ge dig|
|Det finns fyra ljus|Det är fyra ljus|
|Vad är vädret idag|hur är th ”väder idag|
|Säg hej till min lite vän|Säg hej till min lil ”vän|

Personal bör *inte* lägga till distinkta pauser mellan ord. Meningen ska fortfarande att flöda naturligt, även under sounding lite formella. Den här bra skillnaden kan ta idé att ha rätt.

### <a name="the-recording-session"></a>Spela in sessionen

Skapar en referens spela in, eller *matchar filen* i en typisk uttryck i början av sessionen. Be personal Upprepa den här raden varje sida eller så. Varje gång jämför ny inspelning referensen. Den här metoden hjälper till att personal förblir konsekventa i volymen, tempo, försäljningsargument och intonation. Teknikern kan under tiden kan använda filen matchning som referens för nivåer och konsekventa ljud.

Filen matchar är särskilt viktigt när du fortsätter inspelning efter ett avbrott eller en annan dag. Du vill spela upp den några gånger under personal och de Upprepa det varje gång tills de kommer att matcha bra.

Träna din personal för att dra en djupgående hårt och pausa under en kort stund innan varje uttryck. Registrera ett par sekunder tystnad mellan yttranden. Ord bör vara uttalas på samma sätt varje gång som de visas, med tanke på kontexten. Till exempel ”spela in” som ett verb uttalas på olika sätt från ”post” som ett substantiv.

Registrera en bra fem sekunder tystnad innan den första inspelningen för att samla in ”rummet tonen”. Den här metoden hjälper anpassad röst portalen kompensera för eventuella återstående bruset i inspelningen.

> [!TIP]
> Allt du behöver för att avbilda är voice-personal så att du kan göra en monophonic (enskild kanal) inspelning bara sina rader. Men om du spelar in i stereo, du kan använda den andra kanalen för att registrera chatter i kontrollrummet för att samla in beskrivning av särskilda raderna eller tar. Ta bort den här spår från den version som har överförts till den anpassade Voice-portalen.

Lyssna noggrant, använda hörlurar voice-personal-prestanda. Du letar efter bra men naturlig diction och uttalas brist på oönskade ljud. Tveka inte att ställa din personal nytt spela in ett uttryck som inte uppfyller dessa standarder.

> [!TIP]
> Om du använder ett stort antal yttranden, kanske ett enda uttryck inte har en märkbar effekt på den resulterande anpassade rösten. Det kan vara mer lämpligt att helt enkelt tänka yttranden med problem, exkluderar dem från din datauppsättning och se hur din anpassade röst har visat sig. Du kan alltid gå tillbaka till studio och registrera missade exemplen senare.

Observera take-nummer eller tid-kod på ditt skript för varje uttryck. Be tekniker för att markera varje uttryck i den inspelningen metadata eller låt dig ledas-blad.

Ta regelbundna radbrytningar och ger en dryck för att hjälpa din röst personal behålla sin röst i god form.

### <a name="after-the-session"></a>Efter sessionen

Moderna inspelning studios köras på datorer. I slutet av sessionen får du en eller flera ljud filer, inte ett band. De här filerna kommer antagligen vara WAV eller AIFF format i CD kvalitet (44.1 kHz 16-bitars) eller snabbare. 48 kHz 24-bitars är vanliga och önskvärt. Högre avgifter för sampling, till exempel 96 kHz, vanligtvis behövs inte.

Anpassad Voice-portalen kräver varje angivet uttryck i en egen fil. Varje ljudfil som levereras av studio innehåller flera yttranden. Så är den primära släppts aktiviteten att dela upp inspelningarna och förbereda dem för bidrag. Spela in-tekniker kan placeras markörer i filen (eller tillhandahålls ett separat stack-blad) att ange där varje uttryck startar.

Använd dina anteckningar för att hitta den exakta tar du vill och sedan använda ett ljud redigera verktyg, till exempel [Avid Pro verktyg](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html), eller den kostnadsfria [Audacity](https://www.audacityteam.org/), för att kopiera alla uttryck i en ny fil.

Lämna normalt bara 0,2 sekunders tystnad i början och slutet av varje klipp, förutom först. Filen ska inledas med en fullständig fem sekunder tystnad. Använd inte en ljud-redigerare till ”noll out” tyst delar av filen. Inklusive ”rummet tonen” kan anpassade röst algoritmer kompensera för eventuella återstående bakgrundsljud.

Lyssna på varje fil noggrant. I det här skedet kan du redigera ut små oönskade ljud som du har missat under inspelningen som en liten lip smack innan en rad, men vara noga med att inte ta bort alla faktiska tal. Om du inte kan åtgärda en fil, ta bort den från din datauppsättning och Observera att har du gjort det.

Konvertera varje fil till 16 bitar och en frekvens på 16 kHz innan du sparar och, om du har spelat in studio chatter tar du bort den andra kanalen. Spara varje fil i WAV-format, namnge filer med antalet uttryck i skriptet.

Skapa slutligen den *avskrift* som associerar varje WAV-fil med en Textversion av motsvarande uttryck. [Skapa anpassade rösttyper](how-to-customize-voice-font.md) innehåller information om formatet som krävs. Du kan kopiera texten direkt från ditt skript. Skapa sedan en Zip-fil för WAV-filer och textavskrift.

Arkivera ursprungliga inspelningar på en säker plats om du behöver dem senare. Bevara dina skript och anteckningar för.

## <a name="next-steps"></a>Nästa steg

Nu kan du ladda upp din inspelningar och skapa din egen röst.

> [!div class="nextstepaction"]
> [Skapa anpassade rösttyper](how-to-customize-voice-font.md)
