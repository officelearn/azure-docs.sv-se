---
title: Spela in anpassade röstexempel - Taltjänst
titleSuffix: Azure Cognitive Services
description: Gör en anpassad röst av produktionskvalitet genom att förbereda ett robust skript, anställa bra rösttalang och spela in professionellt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b076c642f72e45f58be61d67e887e11b6ccb0aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74975864"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Spela in röstexempel för att skapa en anpassad röst

Skapa en högkvalitativ produktion anpassad röst från grunden är inte en tillfällig företag. Den centrala komponenten i en anpassad röst är en stor samling av ljudprover av mänskligt tal. Det är viktigt att dessa ljudinspelningar vara av hög kvalitet. Välj en röst talang som har erfarenhet av att göra denna typ av inspelningar, och få dem inspelade av en kompetent inspelningsingenjör med hjälp av professionell utrustning.

Innan du kan göra dessa inspelningar behöver du dock ett skript: de ord som kommer att talas av din röst talang för att skapa ljudprover. För bästa resultat måste skriptet ha bra fonetisk täckning och tillräcklig variation för att träna den anpassade röstmodellen.

Många små men viktiga detaljer går till att skapa en professionell röstinspelning. Den här guiden är en färdplan för en process som hjälper dig att få bra och konsekventa resultat.

> [!TIP]
> Om du vill ha resultat av högsta kvalitet bör du anlita Microsoft för att utveckla din anpassade röst. Microsoft har lång erfarenhet av att producera högkvalitativa röster för sina egna produkter, inklusive Cortana och Office.

## <a name="voice-recording-roles"></a>Röstinspelningsroller

Det finns fyra grundläggande roller i ett anpassat röstinspelningsprojekt:

Roll|Syfte
-|-
Röst talang        |Denna persons röst kommer att ligga till grund för den anpassade rösten.
Inspelningsingenjör  |Övervakar de tekniska aspekterna av inspelningen och driver färdskrivaren.
Direktör            |Förbereder manuset och coachar rösttalangens prestation.
Redigerare              |Slutför ljudfilerna och förbereder dem för uppladdning till Custom Voice-portalen.

En person kan fylla mer än en roll. Denna guide förutsätter att du främst kommer att fylla regissören roll och anställa både en röst talang och en inspelning ingenjör. Om du vill göra inspelningarna själv innehåller den här artikeln viss information om inspelningsingenjörrollen. Redigeringsrollen behövs inte förrän efter sessionen, så kan utföras av regissören eller inspelningsteknikern.

## <a name="choose-your-voice-talent"></a>Välj din röst talang

Skådespelare med erfarenhet av berättarröst eller röstkaraktärsarbete gör bra anpassade rösttalanger. Du kan också ofta hitta lämpliga talanger bland announcers och nyhetsläsare.

Välj rösttalang vars naturliga röst du gillar. Det är möjligt att skapa unika "karaktär" röster, men det är mycket svårare för de flesta talanger att utföra dem konsekvent, och ansträngningen kan orsaka röst stam.

> [!TIP]
> Undvik i allmänhet att använda igenkännbara röster för att skapa en anpassad röst – såvida inte ditt mål naturligtvis är att skapa en kändisröst. Mindre kända röster är oftast mindre störande för användarna.

Den enskilt viktigaste faktorn för att välja röst talang är konsekvens. Dina inspelningar ska alla låta som om de gjordes samma dag i samma rum. Du kan närma dig detta ideal genom god inspelningspraxis och teknik.

Din röst talang är den andra halvan av ekvationen. De måste kunna tala med jämn hastighet, volymnivå, tonhöjd och ton. Tydlig diktion är ett måste. Talangen måste också kunna strikt kontrollera sin tonhöjd variation, känslomässiga påverka, och tal manér.

Inspelning av anpassade röstexempel kan vara mer tröttande än andra typer av röstarbete. De flesta röst talang kan spela in för två eller tre timmar om dagen. Begränsa sessioner till tre eller fyra i veckan, med en ledig dag däremellan om möjligt.

Inspelningar gjorda för en röstmodell bör vara känslomässigt neutrala. Det vill säga, ett sorgligt yttrande bör inte läsas på ett sorgligt sätt. Stämningen kan läggas till det syntetiserade talet senare genom prosodikontroller. Arbeta med din röst talang för att utveckla en "persona" som definierar den övergripande ljud och känslomässiga tonen i den anpassade rösten. I processen ska du precisera hur "neutral" låter för den persona.

En persona kan ha, till exempel, en naturligt optimistisk personlighet. Så "deras" röst kan bära en anteckning av optimism även när de talar neutralt. Emellertid, en sådan personlighet drag bör vara subtila och konsekvent. Lyssna på läsningar av befintliga röster för att få en uppfattning om vad du siktar på.

> [!TIP]
> Vanligtvis vill du äga de röstinspelningar du gör. Din röst talang bör vara mottagliga för en work-for-hire kontrakt för projektet.

## <a name="create-a-script"></a>Skapa ett skript

Utgångspunkten för en anpassad röstinspelningssession är skriptet, som innehåller de yttranden som ska uttalas av din rösttalang. (Termen "yttranden" omfattar både fullständiga meningar och kortare fraser.)

Yttrandena i ditt manus kan komma från var som helst: fiktion, facklitteratur, utskrifter av tal, nyhetsrapporter och allt annat som finns i tryckt form. Om du vill vara säker på att din röst klarar sig bra på specifika typer av ord (till exempel medicinsk terminologi eller programmeringsjargong), kanske du vill inkludera meningar från vetenskapliga artiklar eller tekniska dokument. En kort diskussion om potentiella juridiska frågor finns i avsnittet ["Legalities".](#legalities) Du kan också skriva din egen text.

Dina yttranden behöver inte komma från samma källa, eller samma typ av källa. De behöver inte ens ha med varandra att göra. Men om du använder ställa in fraser (till exempel "Du har loggat in") i ditt talprogram, se till att inkludera dem i skriptet. Detta kommer att ge din anpassade röst en bättre chans att uttala dessa fraser väl. Och om du skulle välja att använda en inspelning i stället för syntetiserade tal, har du redan det i samma röst.

Även konsekvens är nyckeln till att välja röst talang, är variation kännetecknet för ett bra manus. Ditt manus bör innehålla många olika ord och meningar med en mängd olika mening längder, strukturer och stämningar. Varje ljud i språket bör representeras flera gånger och i många sammanhang (kallas *fonetiska täckning).*

Dessutom bör texten innehålla alla de sätt som ett visst ljud kan representeras skriftligen, och placera varje ljud på olika platser i meningarna. Både deklarativa meningar och frågor bör inkluderas och läsas med lämplig intonation.

Det är svårt att skriva ett skript som ger *precis tillräckligt med* data för att custom speech-portalen ska kunna skapa en bra röst. I praktiken är det enklaste sättet att göra ett skript som uppnår robust fonetisk täckning att inkludera ett stort antal prover. De standardröster som Microsoft tillhandahåller byggdes av tiotusentals yttranden. Du bör vara beredd att spela in några till flera tusen yttranden som minst för att bygga en produktionskvalitet anpassad röst.

Kontrollera skriptet noggrant för fel. Om möjligt, har någon annan kontrollera det också. När du kör igenom manuset med din talang, kommer du förmodligen fånga några fler misstag.

### <a name="script-format"></a>Skriptformat

Du kan skriva skriptet i Microsoft Word. Skriptet är till för användning under inspelningssessionen, så att du kan ställa in det på alla sätt du tycker är lätt att arbeta med. Skapa textfilen som krävs av Custom Voice-portalen separat.

Ett grundläggande skriptformat innehåller tre kolumner:

* Numret på uttrycket, från 1. Numrering gör det enkelt för alla i studion att hänvisa till ett visst uttryck ("låt oss försöka nummer 356 igen"). Du kan använda styckenumreringsfunktionen i Word för att numrera raderna i tabellen automatiskt.
* En tom kolumn där du skriver take number eller tidskod för varje uttryck för att hjälpa dig att hitta den i den färdiga inspelningen.
* Själva uttryckstexten.

![Exempelskript](media/custom-voice/script.png)

> [!NOTE]
> De flesta studior rekord i korta segment som kallas *tar*. Varje tagning innehåller vanligtvis 10 till 24 yttranden. Bara notera ta nummer är tillräckligt för att hitta ett uttryck senare. Om du spelar in i en studio som föredrar att göra längre inspelningar, bör du notera tidskoden i stället. Studion kommer att ha en framträdande tidsvisning.

Lämna tillräckligt med utrymme efter varje rad för att skriva anteckningar. Se till att inget uttryck delas mellan sidor. Numrera sidorna och skriv ut skriptet på ena sidan av papperet.

Skriv ut tre kopior av manuset: en för talang, en för ingenjören och en för regissören (du). Använd ett gem i stället för häftklamrar: en erfaren röstkonstnär separerar sidorna för att undvika att göra ljud när sidorna vrids.

### <a name="legalities"></a>Lagligheten

Enligt upphovsrättslagen kan en aktörs läsning av upphovsrättsskyddad text vara en föreställning som författaren till verket bör kompenseras för. Denna prestanda kommer inte att kännas igen i den slutliga produkten, den anpassade rösten. Trots detta är lagligheten i att använda ett upphovsrättsskyddat verk för detta ändamål inte väl etablerad. Microsoft kan inte ge juridisk rådgivning i det här problemet. kontakta ditt eget råd.

Lyckligtvis är det möjligt att undvika dessa frågor helt och hållet. Det finns många textkällor som du kan använda utan tillstånd eller licens.

|Textkälla|Beskrivning|
|-|-|
|[CMU Arktisk korpus](http://festvox.org/cmu_arctic/)|Omkring 1100 meningar som valts ut från upphovsrättsliga verk som är särskilt avsedda för talsyntesprojekt. En utmärkt utgångspunkt.|
|Fungerar inte längre<br>upphovsrättsskyddade|Typiskt verk publicerade före 1923. För engelska erbjuder [Project Gutenberg](https://www.gutenberg.org/) tiotusentals sådana verk. Du kanske vill fokusera på nyare verk, eftersom språket kommer att vara närmare modern engelska.|
|Regeringen&nbsp;fungerar|Verk som skapats av USA:s regering är inte upphovsrättsskyddade i USA, även om regeringen kan göra anspråk på upphovsrätt i andra länder/regioner.|
|Offentligt område|Verk för vilka upphovsrätt uttryckligen har fråntagits eller som har ägnats åt det offentliga rummet. Det kanske inte är möjligt att helt avstå från upphovsrätt i vissa jurisdiktioner.|
|Tillåtande licensierade verk|Verk som distribueras under en licens som Creative Commons eller GNU Free Documentation License (GFDL). Wikipedia använder GFDL. Vissa licenser kan dock införa begränsningar för prestanda för det licensierade innehållet som kan påverka skapandet av en anpassad röstmodell, så läs licensen noggrant.|

## <a name="recording-your-script"></a>Spela in skriptet

Spela in ditt manus på en professionell inspelningsstudio som specialiserat sig på röstarbete. De kommer att ha en inspelningsmonter, rätt utrustning och rätt personer att använda den. Det lönar sig att inte snåla på inspelningen.

Diskutera ditt projekt med studions inspelningsingenjör och lyssna på deras råd. Inspelningen bör ha liten eller ingen dynamisk omfångskomprimering (högst 4:1). Det är viktigt att ljudet har konsekvent volym och en hög signal-brus-förhållande, samtidigt som det är fritt från oönskade ljud.

### <a name="do-it-yourself"></a>Gör det själv

Om du vill göra inspelningen själv, snarare än att gå in i en inspelningsstudio, här är en kort primer. Tack vare ökningen av hem inspelning och podcasting, det är lättare än någonsin att hitta bra inspelning råd och resurser på nätet.

Din "inspelning monter" bör vara ett litet rum utan märkbar eko eller "rumston." Det bör vara så tyst och ljudisolerat som möjligt. Draperier på väggarna kan användas för att minska eko och neutralisera eller "deaden" ljudet av rummet.

Använd en högkvalitativ studiokondensatormikrofon ("mikrofon" för kort) avsedd för inspelning av röst. Sennheiser, AKG och ännu nyare Zoom mikrofoner kan ge goda resultat. Du kan köpa en mikrofon, eller hyra en från en lokal audiovisuell uthyrningsfirma. Leta efter en med ett USB-gränssnitt. Denna typ av mikrofon kombinerar bekvämt mikrofonelement, förförstärkare och analog-till-digital omvandlare till ett paket, vilket förenklar hookup.

Du kan också använda en analog mikrofon. Många hyreshus erbjuder "vintage" mikrofoner kända för sin röst karaktär. Observera att professionell analog utrustning använder balanserade XLR-kontakter, snarare än 1/4-tums kontakten som används i konsumentutrustning. Om du går analog, behöver du också en förförstärkare och en dator ljudgränssnitt med dessa kontakter.

Installera mikrofonen på ett stativ eller bom, och installera ett popfilter framför mikrofonen för att eliminera buller från "plosive" konsonanter som "p" och "b". Vissa mikrofoner levereras med ett fjädringsfäste som isolerar dem från vibrationer i stativet, vilket är till hjälp.

Rösttalangen måste hålla sig på ett konsekvent avstånd från mikrofonen. Använd tejp på golvet för att markera var de ska stå. Om talangen föredrar att sitta, var särskilt noga med att övervaka mikrofonavstånd och undvika stolsljud.

Använd ett stativ för att hålla skriptet. Undvik att mete stativet så att det kan reflektera ljudet mot mikrofonen.

Den person som använder färdskrivaren - ingenjören-bör vara i ett separat rum från talang, med något sätt att prata med talang i inspelningen monter (en *talkback krets).*

Inspelningen bör innehålla så lite buller som möjligt, med ett mål om en 80-db signal-brus förhållande eller bättre.

Lyssna noga på en inspelning av tystnad i din "monter", räkna ut var något ljud kommer ifrån, och eliminera orsaken. Vanliga bullerkällor är luftventiler, lysrörsballaster, trafik på närliggande vägar och utrustningsfläktar (även bärbara datorer kan ha fläktar). Mikrofoner och kablar kan fånga upp elektriskt brus från närliggande AC-ledningar, vanligtvis ett surr eller surr. En buzz kan också orsakas av en *jordslinga*, som orsakas av att ha utrustning ansluten till mer än en elektrisk krets.

> [!TIP]
> I vissa fall kanske du kan använda en equalizer eller en brusreducering programvara plug-in för att ta bort buller från dina inspelningar, även om det är alltid bäst att stoppa den vid källan.

Ställ in nivåer så att de flesta av de tillgängliga dynamiska omfånget för digital inspelning används utan överkörning. Det innebär att ställa in ljudet högt, men inte så högt att det blir förvrängd. Ett exempel på vågformen av en bra inspelning visas i följande bild:

![En bra inspelning vågform](media/custom-voice/good-recording.png)

Här används de flesta av intervallet (höjd), men de högsta topparna av signalen når inte toppen eller botten av fönstret. Du kan också se att tystnaden i inspelningen approximerar en tunn horisontell linje, vilket indikerar ett lågt brus golv. Denna inspelning har godtagbart dynamiskt omfång och signal-brusförhållande.

Spela in direkt i datorn via ett högkvalitativt ljudgränssnitt eller en USB-port, beroende på vilken mikrofon du använder. För analog, hålla ljudkedjan enkel: mikrofon, förförstärkare, ljudgränssnitt, dator. Du kan licensiera både [Avid Pro Tools](https://www.avid.com/en/pro-tools) och [Adobe Audition](https://www.adobe.com/products/audition.html) varje månad till en rimlig kostnad. Om din budget är extremt tight, prova den fria [Audacity](https://www.audacityteam.org/).

Spela in på 44,1 kHz 16 bitars monofonisk (CD-kvalitet) eller bättre. Nuvarande state-of-the-art är 48 kHz 24-bitars, om din utrustning stöder det. Du kommer att lägga ner ljudet till 16 kHz 16-bitars innan du skickar det till Custom Voice-portalen. Ändå lönar det sig att ha en högkvalitativ originalinspelning i evenemanget redigeringar behövs.

Helst har olika människor tjäna i roller som regissör, ingenjör och talang. Försök inte göra allt själv. I en knipa kan en person vara både regissören och ingenjören.

### <a name="before-the-session"></a>Före sessionen

För att undvika att slösa studiotid, kör igenom skriptet med din rösttalang före inspelningssessionen. Medan rösttalangen bekantar sig med texten, kan de klargöra uttalet av alla okända ord.

> [!NOTE]
> De flesta inspelningsstudior erbjuder elektronisk visning av skript i inspelningsbåset. I så fall skriver du dina genomkörningsanteckningar direkt i skriptets dokument. Du vill dock fortfarande att en papperskopia ska göra anteckningar under sessionen. De flesta ingenjörer kommer att vilja ha en papperskopia också. Och du vill fortfarande ha en tredje tryckt kopia som en säkerhetskopia för talang om datorn är nere.

Din röst talang kan fråga vilket ord du vill betonas i ett yttrande (den "avgörande ordet"). Berätta för dem att du vill ha en naturlig läsning utan särskild betoning. Betoning kan läggas till när tal syntetiseras; Det bör inte vara en del av den ursprungliga inspelningen.

Rikta talangen att uttala ord tydligt. Varje ord i manuset bör uttalas som skrivet. Ljud bör inte utelämnas eller sluddras tillsammans, vilket är vanligt i casual tal, *om de inte har skrivits på det sättet i skriptet*.

|Skriftlig text|Oönskade casual uttal|
|-|-|
|aldrig kommer att ge upp dig|aldrig kommer att ge upp dig|
|det finns fyra lampor|det finns fyra lampor|
|Hur är vädret idag|Hur är vädret idag|
|säga hej till min lilla vän|säg hej till min lil vän|

Talangen bör *inte* lägga till distinkta pauser mellan ord. Meningen bör fortfarande flöda naturligt, även samtidigt som det låter lite formellt. Denna fina distinktion kan krävas för att få rätt.

### <a name="the-recording-session"></a>Inspelningssessionen

Skapa en referensinspelning, eller *matchningsfil,* av ett typiskt uttryck i början av sessionen. Be talangen att upprepa denna rad varje sida eller så. Jämför den nya inspelningen med referensen varje gång. Denna praxis hjälper talangen att förbli konsekvent i volym, tempo, pitch och intonation. Under tiden kan teknikern använda matchningsfilen som referens för nivåer och övergripande konsekvens av ljud.

Matchningsfilen är särskilt viktig när du återupptar inspelningen efter en paus eller en annan dag. Du vill spela det några gånger för talang och få dem att upprepa det varje gång tills de matchar bra.

Coacha din talang för att ta ett djupt andetag och paus en stund före varje yttrande. Spela in ett par sekunders tystnad mellan yttranden. Ord bör uttalas på samma sätt varje gång de visas, med tanke på sammanhanget. Till exempel uttalas "post" som ett verb annorlunda än "post" som ett substantiv.

Spela in drygt fem sekunders tystnad innan den första inspelningen för att fånga "rumstonen". Den här metoden hjälper Custom Voice-portalen att kompensera för eventuellt återstående brus i inspelningarna.

> [!TIP]
> Allt du verkligen behöver för att fånga är rösttalangen, så att du kan göra en monofonisk (single-channel) inspelning av bara sina linjer. Men om du spelar in i stereo kan du använda den andra kanalen för att spela in chatten i kontrollrummet för att fånga diskussion om vissa rader eller tar. Ta bort det här spåret från den version som har överförts till Custom Voice-portalen.

Lyssna noga, med hörlurar, på rösttalangens prestanda. Du letar efter bra men naturlig diktion, korrekt uttal, och en brist på oönskade ljud. Tveka inte att be din talang att spela in ett yttrande som inte uppfyller dessa normer.

> [!TIP]
> Om du använder ett stort antal yttranden kanske ett enda uttryck inte har någon märkbar effekt på den resulterande anpassade rösten. Det kan vara mer lämpligt att bara notera eventuella yttranden med problem, utesluta dem från din datauppsättning och se hur din anpassade röst blir. Du kan alltid gå tillbaka till studion och spela in missade prover senare.

Observera ta nummer eller tidskod på skriptet för varje uttryck. Be teknikern att markera varje uttryck i inspelningens metadata eller köblad också.

Ta regelbundna pauser och ge en dryck för att hjälpa din röst talang hålla sin röst i god form.

### <a name="after-the-session"></a>Efter sessionen

Moderna inspelningsstudior körs på datorer. I slutet av sessionen får du en eller flera ljudfiler, inte ett band. Dessa filer kommer förmodligen att WAV eller AIFF-format i CD-kvalitet (44,1 kHz 16-bitars) eller bättre. 48 kHz 24-bitars är vanligt och önskvärt. Högre provtagningshastigheter, såsom 96 kHz, behövs i allmänhet inte.

Custom Voice-portalen kräver att varje förutsatt uttryck finns i sin egen fil. Varje ljudfil som levereras av studion innehåller flera yttranden. Så den primära efterproduktion uppgift är att dela upp inspelningarna och förbereda dem för inlämning. Inspelningsteknikern kan ha placerat markörer i filen (eller tillhandahållit ett separat referensblad) för att ange var varje uttryck börjar.

Använd dina anteckningar för att hitta exakt tar du vill, och sedan använda ett ljudredigeringsverktyg, till exempel [Avid Pro Tools](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html), eller den fria [Audacity](https://www.audacityteam.org/), för att kopiera varje uttryck i en ny fil.

Lämna bara cirka 0,2 sekunders tystnad i början och slutet av varje klipp, med undantag för det första. Filen ska börja med hela fem sekunders tystnad. Använd inte en ljudredigerare för att "noll ut" tysta delar av filen. Inklusive "rumstonen" hjälper Custom Voice-algoritmerna att kompensera för eventuellt kvarvarande bakgrundsljud.

Lyssna noga på varje fil. I detta skede kan du redigera ut små oönskade ljud som du missade under inspelningen, som en liten läpp smack före en linje, men var noga med att inte ta bort några faktiska tal. Om du inte kan åtgärda en fil tar du bort den från datauppsättningen och noterar att du har gjort det.

Konvertera varje fil till 16 bitar och en samplingsfrekvens på 16 kHz innan du sparar och, om du spelade in studiochatten, ta bort den andra kanalen. Spara varje fil i WAV-format och namnge filerna med uttrycksnumret från skriptet.

Skapa slutligen *avskriften* som associerar varje WAV-fil med en textversion av motsvarande uttryck. [Att skapa anpassade röstteckensnitt](how-to-customize-voice-font.md) innehåller information om det format som krävs. Du kan kopiera texten direkt från skriptet. Skapa sedan en Zip-fil av WAV-filerna och textavskriften.

Arkivera de ursprungliga inspelningarna på ett säkert ställe om du behöver dem senare. Bevara skriptet och anteckningarna också.

## <a name="next-steps"></a>Nästa steg

Du är redo att ladda upp dina inspelningar och skapa din anpassade röst.

> [!div class="nextstepaction"]
> [Skapa anpassade röstteckensnitt](how-to-customize-voice-font.md)
