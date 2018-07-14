---
title: Så här spela in röst-exempel för att skapa en anpassad röst | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Gör en hög kvalitet costum röst genom att förbereda ett robust skript, anställa bra röst personal och spela in professionellt.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 11d96ce0c92916e1975e0cb403aabf057ab8b825
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036837"
---
# <a name="how-to-record-voice-samples-for-a-custom-voice"></a>Hur du registrerar voice-exempel för en anpassad röst

Skapa en anpassad ton för produktion av hög kvalitet från grunden är inte ett avslappnat företag. Den centrala komponenten i en anpassad röst är ett stort antal ljudsamplingar mänskliga tal. Det är viktigt att dessa ljudinspelningar är av hög kvalitet. Välj voice-personal som har erfarenhet av att göra dessa typer av inspelningar och låta dem registreras av en behöriga inspelning tekniker med professionella utrustning.

Innan du kan göra dessa inspelningar, måste du dock ett skript: ord som uttalas av din röst-personal för att skapa inläsningar. För bästa resultat måste du ha bra fonetiska täckning och tillräcklig mängd att träna modellen anpassade röst ditt skript.

Många små, men viktiga information ingår i att skapa en professionell röstinspelning. Den här guiden är en karta för en process som hjälper dig att få bra, enhetligt resultat.

> [!TIP]
> Överväg att spännande Microsoft för att utveckla din egen röst för högsta kvalitet resultaten. Microsoft har stor erfarenhet som producerar högkvalitativa röster för egna produkter, inklusive Cortana och Office.

## <a name="voice-recording-roles"></a>Röstinspelning roller

Det finns fyra grundläggande roller i ett projekt för inspelning av anpassade röst.

Roll|Syfte
-|-
Röst personal        |Den person vars röst utgör grunden för anpassade röst.
Spela in-tekniker  |Den person som övervakar de tekniska aspekterna av inspelningen och fungerar färdskrivare.
Director            |Förbereder skriptet och coaches voice-personal prestanda.
Redigeringsprogram              |Slutför ljudfilerna och förbereder dem för överföring till den anpassade Voice-portalen.

En person kan fylla mer än en roll. Den här guiden förutsätter att du kommer att fylla rollen director främst och anställa röst personal och en inspelning tekniker. Det finns lite information om inspelning engineer roll om du vill göra inspelningarna själv.

## <a name="choosing-voice-talent"></a>Välja röst personal

Aktörer med erfarenhet av voiceover eller röst tecken arbete se bra anpassade röst personal. Du hittar också ofta lämpligt personal bland Speakers och läsare för diskussionsgrupper.

Välj röst personal vars naturligt röst du gillar. Det är möjligt att skapa unika ”tecken” röster, men det är mycket svårare för de flesta personal att utföra dem konsekvent och arbetet som kan orsaka röst stam.

> [!TIP]
> I allmänhet Undvik att använda identifierbara röster för att skapa en anpassad röst, såvida inte naturligtvis målet är att skapa en kändisar röst. Mindre kända röster är vanligtvis mindre störande för användarna.

Den enda viktigaste faktorn för att välja röst personal är konsekvens. Helst bör alla inspelningar ljud som de har gjorts på samma dag i samma rum. Du kan itu med den här perfekt via bra inspelning metoder och tekniker. 

Din röst personal är den andra halvan av formeln. Han eller hon att prata med konstanta, volymnivå, försäljningsargument och ton. Rensa diction är ett måste. Din personal måste också kunna ha en strikt kontroll hans eller hennes försäljningsargument variation känslomässig påverkar och maner tal, ovanor.

Spela in anpassade röst exempel kan vara mer tröttande än andra typer av röst arbete. De flesta röst personal registrera för två eller tre timmar per dag. Begränsa sessioner till tre eller fyra en vecka, med en dag av mellan om möjligt.

Inspelningar görs för en röst-modellen ska vara emotionella neutral. Det vill säga läsas ett sad uttryck inte på ett sad sätt. Stämning kan läggas till syntetiskt tal senare. Arbeta med din röst-personal att utveckla en ”person” som definierar övergripande ljud och känslomässig tonen hos anpassade röst. I processen, ska du identifiera hur ”neutral” låter för den person.

En person kan exempelvis ha en naturligt upbeat personlighet. Så får röst utföra ner optimism även när vi talar neutralt. Sådana en personlighet egenskaper bör dock diskret och konsekvent. Lyssna på vissa befintliga röster för att få en uppfattning om vad du sträva efter att.

### <a name="legalities"></a>Legalities

Vanligtvis bör du äger röstinspelningar som du gör. Din röst-personal bör vara lämpar sig för ett arbete för nyanställda kontrakt för projektet.

## <a name="creating-a-script"></a>Skapa ett skript

Startpunkten för alla anpassade röstinspelning sessionen är skript som innehåller uttryck som ska talas av din röst-personal. (Termen ”yttranden” omfattar både fullständiga meningar och kortare fraser.)

Yttranden i skriptet kan komma från var som helst: fiction, icke-fiction, avskrifter av tal, nyheter och allt annat som finns i formulär för utskrift. Om du vill kontrollera att din röst när du har bra på specifika typer av ord (till exempel medicinsk terminologi eller programming jargong) kan du inkludera meningar från skolrelaterad undersökningar eller tekniska dokumentationen. (Men se [Legalities](#legalities) nedan.) Du kan också skriva din egen text.

Din yttranden behöver inte komma från samma källa eller samma typ av datakälla. De behöver inte ens ha något att göra med varandra. Men om du ska använda Ange fraser (till exempel ”du har loggat in”) i ditt tal-program, se till att inkludera dem i ditt skript. Detta ger din anpassade röst större möjlighet att uttala dessa fraser bra. Och om du ska vill använda en inspelning i stället för syntetiskt tal du har redan det i samma form som din syntetiskt tal.

Konsekvens är nyckeln när de väljer röst personal, är olika hallmark på ett bra skript. Skriptet bör innehålla många olika ord och meningar med en rad olika mening längder, strukturer och sinnesstämningar. Varje ljud på språket som ska vara representeras flera gånger och i flera sammanhang (kallas *fonetiska täckning).* 

Texten bör dessutom införliva alla metoder för att ett visst ljud kan representeras skriftligen och placera varje ljud på olika platser i meningarna. Både deklarativa meningar och frågor bör ingår och läsa med lämpliga intonation.

Det är svårt att skriva ett skript som innehåller *enough* data för att tillåta Custom Speech-portalen för att skapa en bra röst. I praktiken är det enklaste sättet att göra ett skript som uppnår robust fonetiska täckning att inkludera ett stort antal prover. Microsofts standard röster har skapats från tiotusentals yttranden. Du bör vara beredd på att registrera ett par till flera tusen yttranden om du vill skapa en anpassad röst hög kvalitet.

Kontrollera skriptet noggrant för fel. Om det är möjligt att någon annan kolla för. När du kör igenom skriptet med din personal, kommer du antagligen se några fler fel.

### <a name="script-format"></a>Skriptformat

Du kan skriva ditt skript i Microsoft Word. Skriptet är för användning under inspelning sessionen, så du kan konfigurera det något sätt som du hittar enkelt att arbeta med. Skapa textfil som krävs av den anpassade Voice-portalen separat.

Ett skript för grundläggande format innehåller tre kolumner:

* Antalet uttryck, börjar på 1. Numrering gör det lättare för alla i studio för att referera till en viss uttryck (”vi försöker tal 356 igen”). Du kan använda Word punkt numrering funktionen för att numrera rader i tabellen automatiskt.
* En tom kolumn där du ska skriva i take-nummer eller tid för varje uttryck för att hitta i klar inspelningen.
* Texten för uttryck själva.

![Exempelskript](media/custom-voice/script.png)

> [!NOTE]
> De flesta studios registrera kortfattat segment som kallas ”äger” varje tar vanligtvis innehåller tio till 24 yttranden. Bara noteras take-nummer är tillräckliga för att hitta en viss uttryck senare. Vissa studios kanske föredrar att göra längre inspelningar. I det här fallet bör du Observera tidsförskjutningen i filen (tid-kod). Studio har en framstående tid visning.

Lämna tillräckligt med utrymme efter varje rad att skriva anteckningar. Var noga med att inga uttryck delas mellan sidor. Sidorna och Skriv ut ditt skript på ena sidan av dokumentet.

Skriva ut tre kopior av skriptet: en för personal, en för teknikern och en för director (du). Använda ett gem i stället för staples: en erfaren röst artist separeras sidor för att undvika att göra bruset som sidorna är aktiverade.

### <a name="legalities"></a>Legalities

Enligt upphovsrätten, kan en aktör läsning av upphovsrättsskyddade text vara en prestanda som författare av arbetet ska ersättas. Den här prestanda kommer inte att känna igen i den slutgiltiga produkten, anpassade röst. Men det är möjligt, är laglighet med att använda upphovsrättsskyddade verk för detta ändamål inte välkänt. Microsoft kan inte tillhandahålla juridisk rådgivning på det här problemet; Läs dina egna ombud.

Som tur är kan går det att undvika de här problemen helt och hållet. Det finns många källor med text som du kan använda utan behörighet eller licens.

|Text-källa|Beskrivning|
|-|-|
|[CMU Arctic Kristi](http://festvox.org/cmu_arctic/)|Om 1100 meningar som valts ut av upphovsrätt som ska användas i tal syntes projekt. En utmärkt utgångspunkt.|
|Fungerar inte längre<br>upphovsrätten|Vanligtvis publicerade fungerar innan 1923. Engelska erbjuder projekt Gutenberg tiotusentals sådana verk. Du kanske vill fokusera på nyare fungerar som språket är närmare moderna engelska.|
|Government&nbsp;fungerar|Fungerar som skapats av USA: S regering är inte upphovsrättsskyddade i USA, även om den offentliga sektorn kan göra anspråk på copyright i andra länder.|
|Allmän egendom|Fungerar för vilka copyright har uttryckligen anges eller som har varit dedikerad till den offentliga domänen. (Det kan inte vara möjligt att avstå från copyright helt i vissa jurisdiktioner.)|
|Permissively licensierad fungerar|Fungerar som distribueras under en licens som Creative Commons eller licensen GNU kostnadsfri dokumentation. Wikipedia använder GFDL. Vissa licenser, men kan införa begränsningar för prestanda för licensierade innehåll som kan påverka skapandet av en anpassad voice-modell, så Läs noggrant.|

## <a name="recording-your-script"></a>Spela in skriptet

Helst bör du anteckna dina skript vid professionella inspelning verktyg som specialiserar sig i voiceover arbete. De har en inspelning monter, rätt utrustning för jobbet och rätt personer att driva den. Det lönar sig inte för att spela in ändå.

Diskutera dina projekt med i studio inspelning tekniker och lyssna på hans eller hennes råd. Inspelningen bör ha lite eller ingen dynamiskt omfång komprimering (högst 4:1). Det är viktigt att ljudet har konsekventa volym och signal brus högt förhållande, samtidigt som det är kostnadsfritt oönskade ljud.

### <a name="doing-it-yourself"></a>Göra det själv

Om du vill göra inspelningen själv, istället för att gå till en inspelning studio, här är en kort introduktion. Tack vare röstfunktion home inspelning och nya podcastmöjligheter är det enklare än någonsin att hitta bra inspelning råd och resurserna.

Din ”spela in monter” ska vara ett litet rum utan märkbar echo eller ”rummet ton” och som är så tyst och soundproof som möjligt. Draperier på väggarna kan användas för att minska echo och neutralisera eller ”deaden” ljud i rummet.

Använda en högkvalitativ studio kylaren mikrofon (”mic”) är avsedda för röstinspelning. Sennheiser AKG och även senare zoomning mikrofoner kan ge bra resultat. Du kan köpa din mic eller hyra en från en lokal audiovisuellt hyres-företag. Leta efter en med en USB-gränssnitt. Den här typen av mic kombinerar bekvämt mikrofon element, preamp och analog till digital konverterare till ett paket, vilket förenklar koll.

Du kan också använda en analog mikrofon. Många hyra datahus erbjuder ”vintage” mikrofoner världskänd för sin röst-tecken. Observera att professionella analog gear använder belastningsutjämnade XLR kopplingar i stället för 1/4 ”Anslut används i konsumentutrustning. Om du går analog, måste du också en preamp och ett ljud datorgränssnittet med dessa anslutningar.

Installera mikrofonen på en fristående eller sådär och använda ett pop filter framför mikrofonen för att eliminera brus från ”plosive” låter som ”p” och ”b”. En del mikrofoner levereras med en inaktivering montering som isolerar dem från vibrationer i den fristående, vilket är användbart.

Röst-personal måste hålla sig i ett konsekvent avstånd från mikrofonen. Använd bandet på våning för att markera där de ska använda. Om personal föredrar att vara, var särskilt noga med att övervaka mic avståndet och undvika chair bruset.

Använd en FRIS för skriptet. Undvik nöjesfiske fristående så att den kan återspegla ljud till mikrofonen.

Den person som faktiskt gör inspelningen – teknikern – ska vara i ett separat rum från personal med något sätt att kommunicera med personal i inspelning monter (en ”talkback krets”).

Inspelningen ska innehålla som lite störningar som möjligt, med målet för en 80 db signal-brus förhållande eller mindre.

Lyssna nära på en inspelning av tystnad i din ”monter”, ta reda på där alla bruset kommer från, och eliminera orsaken. Vanliga källor för brus är ventilation, fluorescerande ljus förkopplingsdon, trafik på Närliggande vägar och utrustning fans (även bärbara datorer kan ha fans). Mikrofoner och kablar kan avläsa elektriska störningar från i närheten AC koaxialkabel, vanligtvis en surrandet eller buzz.

> [!TIP]
> I vissa fall kan du använda en equalizer eller en bruset minskning programvara plugin-programmet för att ta bort brus från din inspelningar, även om det alltid är bäst att stoppa den vid dess källa när det är möjligt.

Nivåer ska ställas in så att det mesta av digital registrering tillgängliga dynamiska omfång används utan overdriving. Det innebär att högt, men inte så högt som förvränger ljudet. Nedan visas ett exempel på Wave för en bra inspelning.

![bra inspelning Wave](media/custom-voice/good-recording.png)

Du kan se att de flesta av intervallet (höjd) används, men de högsta topparna av signalen inte når upp eller ned på skärmen. Du kan också se att tystnad i inspelningen tillhandahåller en tunn vågrät linje, som anger en låg bruset våning. Den här inspelningen har dynamiska intervallet och signal brus förhållande.

Post direkt till datorn med hjälp av ett ljud högkvalitativa-gränssnitt eller en USB-port, beroende på vilken typ av mic du använder. Håll ljud kedjan enkla: mic, preamp, ljud-gränssnittet, datorn. Båda [Avid Pro verktyg](http://www.avid.com/en/pro-tools) och [Adobe Audition](https://www.adobe.com/products/audition.html) kan licensieras per månad till rimlig kostnad. Om din budget är mycket nära, prova det kostnadsfritt [Audacity](https://www.audacityteam.org/).

Registrera med 44,1 KHz 16-bitars monophonic (CD kvalitet) eller snabbare. Aktuella-för-avancerade är 48 KHz 24-bitars, om utrustningen stöder den. Du kommer nedsampla dina ljud till 16 KHz 16-bitars innan du skickar den till den anpassade Voice-portalen. Ändå har lönar det sig att en högkvalitativ ursprungliga inspelning i den händelse att ändringar behövs.

Vi rekommenderar har olika personer som hanterar i rollerna för director, tekniker och personal. Försök inte att göra det. alla själv! I en Zooma kan director och -tekniker vara en enskild person.

### <a name="before-the-session"></a>Innan sessionen

Undvik att ha lagt tid studio, kör du via skript med din röst personal före inspelning sessionen. Som röst-personal blir van texten kan kan han eller hon tydliggöra uttal för alla okända ord.

> [!NOTE]
> De flesta inspelning studios erbjuder elektronisk visning av skript i monter för inspelning. I det här fallet Skriv anteckningarna snabbgenomgång direkt i skriptets dokumentet. Papper för anteckningar under sessionen, men fortfarande vill. De flesta tekniker kan en papperskopia för. Och du fortfarande vill kopiera tredje ut som en säkerhetskopiering för personal om datorn inte är igång!

Din röst-personal kan be vilka ord som du vill att framhållit i ett uttryck. Aktörer kallas detta ofta ”avgörande ordet”. Berätta för dem du vill att en naturlig läsning med ingen särskild vikt. Fokus kan läggas till när tal syntetiskt; Det får inte vara en del av den ursprungliga inspelningen.

Dirigera personal att uttala orden tydligt. Varje ord i skriptet bör vara markant som skrivits. Ljud bör inte utelämnas eller slurred tillsammans, som är vanligt i avslappnat tal, *om de har skrivits på så sätt i skriptet.*

|Texten|Oönskade avslappnat uttal|
|-|-|
|aldrig kommer för att ge dig|aldrig kommer för att ge dig|
|Det finns fyra ljus|Det är fyra ljus|
|Vad är vädret idag|hur är th ”väder idag|
|Säg hej till min lite vän|Säg hej till min lil ”vän|

Personal bör *inte* lägga till distinkta pauser mellan ord. Meningen ska fortfarande att flöda naturligt, även under sounding lite formella. Den här bra skillnaden kan ta några idé att ha rätt.

### <a name="the-recording-session"></a>Spela in sessionen

Skapar en referens spela in, eller *matchar filen* i en typisk uttryck i början av sessionen. Be personal Upprepa den här raden alla sidor eller sidan och en halv. Varje gång jämför ny inspelning referensen. Den här tekniken kan personal förblir konsekventa i volymen, tempo, försäljningsargument och intonation. Teknikern kan under tiden kan använda filen matchning som referens för nivåer och konsekventa ljud.

Filen matchar är särskilt viktigt när återupptar inspelning efter ett avbrott eller en annan dag. Du vill spela upp den några gånger under personal och de Upprepa det varje gång tills de kommer att matcha bra.

Träna din personal för att dra en djupgående hårt och pausa under en kort stund innan varje uttryck. Registrera ett par sekunder tystnad mellan yttranden. Ord bör vara uttalas samma sätt varje gång som de visas, med tanke på kontexten: ”spela in” som ett verb uttalas på olika sätt från ”post” som ett substantiv.

Registrera en bra fem sekunder tystnad innan den första inspelningen för att samla in ”rummet tonen”. På så sätt kan anpassade röst portalen kompensera för eventuella återstående bruset i inspelningen.

> [!TIP]
> Allt du behöver för att registrera är voice-personal så att det är bra att göra en monophonic (enskild kanal) inspelning bara sina rader. Om du registrerar i stereo kan använda du den andra kanalen för att registrera chatter kontrollrummet. Det är ofta bra att ta hänsyn till detta senare. Ta bort den här spår från den version som överförts till den anpassade Voice-portalen.

Lyssna noggrant, använda hörlurar voice-personal-prestanda. Du letar efter bra men naturlig diction och uttalas brist på oönskade ljud. Tveka inte att ställa din personal nytt spela in ett uttryck som inte uppfyller dessa standarder. 

> [!TIP] 
> När du registrerar ett stort antal yttranden kan att förlora en enda uttryck inte märkbart påverka resulterande anpassade röst. Därför kan det vara mer lämpligt att helt enkelt tänka yttranden som har problem, exkluderar dem från din datauppsättning och se hur din anpassade röst har visat sig. Du kan alltid gå tillbaka till studio och registrera missade exemplen senare.

Anteckna take-nummer eller tid-kod på ditt skript för varje uttryck. Be teknikern om de kan markera varje uttryck i den inspelningen metadata eller låt dig ledas samt.

Ta regelbundna radbrytningar så att din röst personal behålla sin röst i god form. Ge personal något att dryck att hålla sina strupe från att få torr.

### <a name="after-the-session"></a>Efter sessionen

Moderna inspelning studios köras på datorer. I slutet av sessionen får, du en eller flera ljud filer, inte ett band. De här filerna kommer antagligen vara WAV eller AIFF format i CD kvalitet (44,1 KHz 16-bitars) eller snabbare. 48 kHz 24-bitars är vanliga och önskvärt. Högre avgifter för sampling, till exempel 96 KHz är vanligtvis inte behövs.

Studio levererar en eller flera ljudfiler som innehåller flera yttranden. Varje uttryck måste vara i en egen fil för att ladda upp inspelningarna anpassad Voice-portalen. Spela in-tekniker har placerats en markör i filen (eller tillhandahålls en separat stack-lista) som anger där varje uttryck startar.

Du behöver gå igenom ljudinspelningar och göra en WAV-fil för varje uttryck. Använd dina anteckningar för att hitta exakt yttranden som du vill ha och sedan använda ett ljud redigera verktyget som [Avid Pro verktyg](http://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html), eller den kostnadsfria [Audacity](https://www.audacityteam.org/) att kopiera var och en till en ny fil.

Lämna normalt bara 0,2 sekunders tystnad i början och slutet av varje klipp utom först. Filen ska inledas med en fullständig fem sekunder tystnad. Använd inte redigeraren ljud till ”noll out” tyst delar av filen. Inklusive ”rummet tonen” kan anpassade röst algoritmer kompensera för eventuella återstående bakgrundsljud.

Lyssna på varje fil noggrant. I det här skedet kan du redigera ut små oönskade ljud som du har missat under spela in som en liten lip smack innan en rad, så länge de inte överlappar några tal. Om du inte kan åtgärda en fil, bort från datauppsättningen, vilket gör noterat att du har gjort det.

Nedsampla var och en fil till 16 KHz och 16 bitar innan du sparar och, om du har registrerat i stereo, tar du bort den andra kanalen. Spara varje fil i WAV-format.

Arkivera ursprungliga inspelningen på en säker plats om du behöver senare gå tillbaka till den. Bevara dina skript och anteckningar för.

## <a name="next-steps"></a>Nästa steg

Nu kan du ladda upp din inspelningar och skapa din egen röst!

> [!div class="nextstepaction"]
> [Skapa anpassade rösttyper](how-to-customize-voice-font.md)
