---
title: Definiera en strategi för Mobile Engagement | Microsoft Docs
description: Lär dig hur du integrerar och optimerar Azure Mobile Engagement med analyser och push-meddelanden.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: dwrede
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo

---
# Definiera en strategi för Mobile Engagement
*Alla som skapar appar vill att folk ska använda dem.*

Vi tror att du har lagt ned mycket tid och möda på att skapa en riktigt bra app som alla gillar. Förmodligen har du också satsat en hel del pengar på att marknadsföra den och få fler användare. I början är det otroligt spännande att se hur antalet användare bara växer och växer, men så plötsligt börjar trenden ge vika och du märker av en tydlig nedgång i användningen. *Det är här som Azure Mobile Engagement kommer in!* Vi hjälper dig att behålla användarna och att förbättra din app genom testning och nya insikter.

Vår metod för att förbättra kvarhållning och användning bygger på interaktion med appanvändare via push-meddelanden och meddelanden i appen, men på ett speciellt sätt. Alla meddelanden och annan kommunikation skräddarsys efter användarna i fråga med hänsyn tagen till deras beteende i din app. Vårt mål är att du ska kunna nå ut till rätt målgrupp vid rätt tidpunkt och på rätt plats.

Men för att kunna göra det måste du börja med att *försöka förstå dina användare*, skapa grupper utifrån deras handlingar eller deras karaktär (vi kallar detta för segmentering) och sedan kommunicera med varje segment på ett relevant sätt.

## Mobile Engagement hjälper dig att uppnå dina mål
*Vi har nämnt kvarhållning och användning, men vad spelar det egentligen för roll?*

När du skapar en Mobile Engagement-strategi måste du först se över appens målsättning och nyckeltal (KPI:er).

Börja med att definiera de mål och KPI:er som ger användningsfallen rätt fokus.

Användningsfall är helt enkelt en lista med kampanjer som du vill köra mot användarna. Det kan vara allt från ett välkomstmeddelande till mycket avancerade meddelanden som utlöses av IT-systemet. Ett väl avvägt användningsfall måste kunna svara på de tre frågorna: *Vad? Vem? När?*

1. En kort beteckning (till exempel ”Välkomstkampanj”).
2. **Vad?** Ett exempel på meddelande (till exempel ”Vad kul att du registrerat dig! Kom ihåg att logga in för att få en månad utan kostnad!”). Det här är inte menat att vara det slutliga meddelandet, utan du kan när som helst ändra texten. Dock kan det vara nyttigt att skriva ned något för att få igång tankeprocesserna.
3. **Vem?** Det segment som ska få meddelandet (till exempel ”alla användare som startade appen för första gången för tre dagar sedan, som har besökt inloggningssidan men inte loggat in”).
   * Ja, det är inga problem med Azure Mobile Engagement :)
   * Inte heller här behöver du bestämma dig på en gång, utan du kan när som helst definiera om segmenten. Dock är det viktigt att på ett så tidigt stadium som möjligt definiera segmenteringskriterierna för att se till att du samlar in rätt data.
4. **När?** Tidpunkten för kampanjen. Det kan vara på ett speciellt datum eller efter en viss åtgärd baserad på en utlösare. Mobile Engagement erbjuder mängder av alternativ för att ställa in tidpunkten för kommunikationen.

När användningsfall och segment har definierats har du de riktlinjer du behöver för att definiera vilka data som ska samlas in i appen. Det är här som *”taggplanen”* kommer in. Med en taggplan kan du se till att utvecklarna får rätt specifikationer för datainsamlingen. På så sätt kan utvecklarna bädda in Mobile Engagement med rätt inställningar, och du kan arbeta med kampanjer som använder rätt data. Det är också viktigt att köra tester som kontrollerar att integrationen har gjorts på korrekt sätt och att appen samlar in vad du behöver.

Tack vare integrationen kan du som marknadsförare få tillgång till analyser i realtid i de publicerade apparna, segmentera målgruppen och börja skicka smarta riktade push-meddelanden för att öka interaktionen med slutanvändare både i och utanför appen.

### Användningsfall för att komma igång
1. Välkomststrategi: Skapa flera kampanjer med push-meddelanden baserat på slutanvändarens beteende vid start av appen för att få mer interaktion D+2/5/10/15 efter den första sessionen och utöka kvarhållningen redan under första omgången.
2. Gör reklam för nytt innehåll (funktion, artikel/video eller produkt) baserat på slutanvändarens beteende och skicka endast information till slutanvändare som med stor sannolikhet kommer att interagera.
3. Betygsätt appen: Rikta in dig på mindre än 1 procent av användarbasen som med stor sannolikhet kommer att ge appen betyget 5 stjärnor i appbutiken.
4. Öka antalet prenumerationer: Rikta in reklam för värdefullt innehåll mot slutanvändare som ännu inte har sett det för att öka antalet prenumeranter.
5. Självstudier: Inga obligatoriska självstudier för alla. Varför inte att skapa fina självstudiekurser i appen och utlösa dem via meddelanden i appen bara om användaren inte verkar använda appen eller har svårt att använda en funktion?

## Varför behöver du analyser för att skapa interaktion?
Vid det här laget har du kanske förstått att det inte räcker med att skicka push-meddelanden. Ett av huvudsyftena med Mobile Engagement är att göra det enklare för marknadsförare och utvecklare att interagera med rätt slutanvändare vid rätt tidpunkt och på rätt plats. För att verkligen få kontroll över de tre huvudkoncepten måste du samla in analysdata från appen och använda informationen till att segmentera målgruppen. Funktionerna blir ännu mera kraftfulla om du kan kombinera beteendesegment med data från andra databaser, ett CRM-system eller en annan kanal. Med Mobile Engagement kan du hämta data från vilken källa som helst och använda informationen till att rikta in dig på rätt målgrupp.

För att kunna göra din kommunikation så sammanhangskänslig som möjligt måste du känna till slutanvändarnas beteenden och statusar i realtid. Datainsamling gör det möjligt för marknadsförare att fokusera på det som verkligen är viktigt när de går igenom användningsfall och skapar strategier och mål för mobilt engagemang. Bästa sättet att uppnå målen är att avstå från att samla in precis allt under analyserna. Koncentrera dig på de data som behövs för att fokusera på det du vill veta och dina användningsfall. Nu har du det du behöver för att köra igång, prova dig fram och lära dig använda lösningen till att skicka smarta push-meddelanden, öka kvarhållningen av användare och göra din app till en succé!

> [!NOTE]
> Och kom ihåg att välja data med omsorg, för man kan faktiskt få för mycket av det goda!
> 
> 

### Användningsfall och bästa praxis
Här nedan följer kortfattade beskrivningar av några av de viktigaste användningsfallen som vi har stött på bland våra kunder. Förhoppningsvis kan de hjälpa dig att komma igång.

#### Media
Samla in den typ av innehåll som slutanvändaren helst vill konsumera och segmentera målgruppen utifrån det här beteendet, så att du kan rikta specifika typer av innehåll till enbart den målgrupp som med största sannolikhet kommer att konsumera det. På så sätt undviker du massutskick till hela användarbasen och säkerställer en bättre kvarhållning.

#### E-handel (mobila enheter)
Samla in de mest besökta produktkategorierna i appen och rikta in dig på en målgrupp med reklam om rabatt eller en ny produkt i en populär kategori, eftersom det då är mer sannolikt att slutanvändarna kommer att handla. Sikta på att öka intäkterna. Men kom ihåg att aldrig spamma mottagarna!

#### Spel
Samla in spelnivån för slutanvändare och den tid som de lägger ned under en viss period, så att du kan rikta in dig på den målgrupp som kanske har kört fast och kan tänka sig att betala för att hoppa till nästa nivå om det rätta erbjudandet kommer.

Skicka meddelanden om särskilda evenemang som en morot för användare som inte har spelat på länge och som du vill försöka vinna tillbaka.

#### Detaljhandeln
Samla in produkter eller märken som målgrupp kan tänka sig att konsumera baserat på favoriter eller beteende. Försök driva trafiken mot din webbutik för att öka intäkterna.

#### Banker
Samla in data från slutanvändare som skapar ett konto när produkten först lanseras. Syftet är att utveckla en välkomststrategi med riktade push-meddelanden och att öka antalet prenumerantkonton.

### Hur skapar jag en taggplan?
En taggplan är en beskrivning av användarsökvägen eller ett arbetsflöde i appen. Alla nödvändiga taggar (data) måste samlas in för att du få tillräckligt med analysdata för att förstå beteendet hos dina användare och få en korrekt segmentering av användarbasen. Detta är inte en teknisk process. Därför kan du med fördel sätta en marknadsförare på att ange vilka data som ska samlas in baserat på er Mobile Engagement-strategi.

Som minst behöver du tagga alla skärmar i appen (kallas *aktiviteter* i Mobile Engagement). Utifrån detta kan man fastställa en användarsökväg.

En aktivitet kan ha inbäddade *händelser* som samlar in information om olika åtgärder, till exempel vilka knappar användaren klickar på. Detta möjliggör insamling av interaktioner i appen. På så sätt kan marknadsförarna ta reda på vilka skärmar användarna besöker och vad de gör.

`Jobs` är åtgärder med en varaktighet. Detta är användbart för marknadsförare som vill förstå hur lång tid det tar för en användare att till exempel skapa ett konto eller logga in. Det kan även vara användbar information för utvecklare som vill mäta hur lång tid det tar att anropa en webbtjänst.

`Errors` kan också övervakas för att se om användare har problem i din app. Ofta återkommande anslutningsproblem, till exempel.

Data av den här typen kan förstärkas med parametrar (*extra information* i Mobile Engagement) som möjliggör insamling av dynamiska data från appen. Det här är viktigt för att kunna möjliggöra detaljerad segmentering. Till exempel kan marknadsförare segmentera användare baserat på vilken typ av innehåll som de har tagit del av. Typ av innehåll är dynamisk information förknippad med en aktivitet eller en händelse.

*Programinformation* består av data som gör det möjligt att kontrollera statusen för appen eller användaren i realtid. Det här gör det också möjligt att kategorisera en målgrupp och rikta in sig på den. Informationen kan till exempel använda statusar av typen SANT/FALSKT för att ange om användaren loggat in eller inte, eller för att ange om hans/hennes prenumeration har upphört att gälla.

#### Exempel på taggar
*Användningsfall: Segmentera målgruppens beteende för att rikta push-meddelanden med rätt innehåll till rätt slutanvändare*

1. Skicka push-meddelanden som gör reklam för en produktkategori: Samla in data om användarbeteende och segmentera målgruppen utifrån en produktkategori som de har besökt ett visst antal gånger (x) under en viss tidsperiod, eller utifrån en artikel som de lagt i kundvagnen. Med hjälp av insamlade data kan du segmentera och sedan skicka ett push-meddelande till rätt målgrupp.
2. Betygsätt appen: Samla in data baserat på det innehåll som delas av målgruppen på sociala nätverk. Syftet är att segmentera målgruppen och utse *ambassadörer* för din app. Om du vill be användare ge appen betyget 5 stjärnor i appbutiken med hjälp av ett push-meddelande, är ambassadörerna den målgrupp du först och främst ska vända dig till.
   
   ![][1]

*Användningsfall: Deklarativa data*

1. Segmentera nyhetsaviseringar: Samla in deklarativa data för att kunna segmentera målgruppen utifrån deras preferenser. På så sätt kan du skicka push-meddelanden om ett visst ämne som verkligen intresserar den specifika målgruppen.
2. Segmentera målgruppen baserat på inloggningsstatus. Samla in data för att ta reda på om en användare är ansluten eller om han/hon har skapat ett konto. Slutanvändare som ännu inte har loggat in kan få den sista lilla knuffen som behövs för att omvandlas till kund med ett enkelt push-meddelande.
   ![][2]

### Nästa steg
* Gå till [Koncept i Mobile Engagement] om du vill läsa mer om de grundläggande koncepten bakom mobilt engagemang.
* Gå till [Skapa en ny Mobile Engagement-app](mobile-engagement-create.md) för att lära dig hur man gör för att skapa en ny Mobile Engagement-app i Azure och hur du kan börja hantera dina appar via Mobile Engagement-portalen.
* Gå till [Bästa praxis](mobile-engagement-getting-started-best-practices.md) om du vill få mer information.
* Gå till [scenariot med spelappen](mobile-engagement-gaming-scenario.md) om du vill läsa mer om hur du implementerar Mobile Engagement med en spelapp. 
* Gå till [scenariot med medieappen](mobile-engagement-media-scenario.md) om du vill läsa mer om hur du implementerar Mobile Engagement med en medieapp. 
* Gå till [självstudiekurserna] om du vill läsa mer om implementering.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Koncept i Mobile Engagement]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[självstudiekurserna]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/




<!--HONumber=sep16_HO1-->


