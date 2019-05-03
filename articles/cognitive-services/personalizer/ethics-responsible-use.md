---
title: Etik och användning av ansvarig - Personalizer
titleSuffix: Azure Cognitive Services
description: Dessa riktlinjer är avsedda att hjälpa dig att implementera anpassning på ett sätt som hjälper dig att skapa förtroende för företaget och tjänsten. Glöm inte att pausa till research, Lär dig och avsiktligt på effekten av anpassningen på människors liv. Om du tvekar, söker du efter vägledning.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 7b1e972b5516aa79d1754e32e487e17c9e68ac1d
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035417"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Riktlinjer för ansvarar implementering av Personalizer

Implementeringar måste vara utformad så att de uppfyller förtroendet för dem att lägga till AI i sina program och användare av program som skapats med AI för personer och society att utnyttja hela potentialen i AI. Dessa riktlinjer är avsedda att hjälpa dig att implementera Personalizer på ett sätt som hjälper dig att skapa förtroende för företaget och tjänsten. Glöm inte att pausa till research, Lär dig och avsiktligt på effekten av anpassningen på människors liv. Om du tvekar, söker du efter vägledning.

Dessa riktlinjer är inte avsedda som juridisk rådgivning och separat bör du se till att ditt program överensstämmer med många demonstrationer utvecklingen i lag i det här området och i din sektor.

Dessutom bör du överväga en rad olika ansvarsområden som du har när du utvecklar alla datacentrerade AI-system, inklusive ethics, sekretess, säkerhet, säkerhet, inkludering, transparens och redovisning utforma ditt program med Personalizer. Du kan läsa mer om dessa i den [rekommenderad läsning](#recommended-reading) avsnittet.

Du kan använda följande innehåll som en checklista för startprogrammet och anpassa och förfina ditt scenario. Det här dokumentet har två huvudavsnitt: Först är dedikerad till markering ansvarig Använd överväganden när du väljer scenarier, funktioner och poäng för Personalizer. Andra gör en värdeuppsättning som Microsoft anser bör övervägas när du skapar AI-system och ger användbara förslag och risker på hur din användning av Personalizer påverkar dem. 


## <a name="your-responsibility"></a>Ditt ansvar

Alla riktlinjer för ansvarar implementering bygger på grunden att utvecklare och företag som använder Personalizer är ansvarig och hållas ansvariga för följderna av att använda dessa algoritmer i samhället. Om du utvecklar ett program som ska distribueras i organisationen, bör du känner igen din roll och ansvar för dess drift och hur den påverkar personer. Om du utvecklar ett program som ska distribueras av en tredje part, gå till en delad förståelse med dem som är slutgiltigt ansvariga för programmets beteende och dokumentera den förståelse.

Förtroende bygger på begreppet uppfyllda åtaganden, Överväg att dina användare och society och det regelverk som dina program fungerar i, identifiera direkta och indirekta åtaganden som de kan ha.

Microsoft skapar kontinuerligt arbete i dess verktyg och dokument kan du agera utifrån dessa uppgifter. [Lämna feedback till Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) om du tror att ytterligare verktyg produktfunktioner och dokument kan hjälpa dig att implementera dessa riktlinjer för att använda Personalizer.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Faktorer för att implementera ett ansvarsfullt sätt Personalizer

Implementera Personalizer kan vara värdefulla till användarna och din verksamhet. Starta genom att fundera över följande riktlinjer för att implementera Personalizer ansvarsfullt när:

* Om du väljer objekt att tillämpa anpassning.
* Att skapa [belöna funktioner](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md).
* Välja vilket [funktioner](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) om kontext och möjliga åtgärder som ska användas för anpassning.


## <a name="choosing-use-cases-for-personalizer"></a>Välja användningsområden för Personalizer

Det är praktiskt att använda en tjänst som lär sig att anpassa innehåll och användargränssnitt. Det kan också vara misapplied om hur anpassningen skapar negativa sidoeffekter i den verkliga världen, inklusive om användare inte känner till personanpassning av innehåll. 

Exempel på användningsområden för Personalizer med förhöjd potential för negativa sidoeffekter eller brist på transparens är bland annat scenarier där ”utmärkelse” beror på många långsiktig komplex faktorer som, när över förenklad i en omedelbar trafik kan ha negativ resultat för enskilda användare. Detta brukar betraktas som ”följdskador” alternativ eller alternativ som omfattar en risk för. Exempel: 


* **Finans**: Anpassa erbjuder på lån, finansiella institutioner och försäkring produkter, där riskfaktorer baseras på data enskilda användare vet om det går inte att hämta eller kan inte tvist. 
* **Utbildning**: Anpassa rangordningar för skolan kurser och utbildningsinstitutioner där rekommendationer kan spridas eventuella fördomar och minska användarnas medvetenhet om andra alternativ.
* **Demokratin och Civic deltagande**: Personalisering av innehåll för användare med målet att påverkande yttranden är följdskador och manipulative.
* **Tredjeparts-utmärkelse utvärdering**: Anpassa objekt där trafik baserat på en senare 3 part utvärderingen av användaren, i stället för att en trafik som genereras av användarens egna beteende.
* **Intolerans till utforskning**: Alla situationer där Personalizer utforskning beteende kan skada.

När du väljer användningsfall för Personalizer:

* Börja designprocessen överväger hur anpassningen hjälper dina användare.
* Överväg negativa konsekvenser i verkligheten om vissa objekt inte rangordnas för användare på grund av personanpassning mönster eller utforskning.
* Överväg att själva uppfylla prophecy slingor. Detta kan utföras om anpassning ersättning träna en modell så att det kan därefter ytterligare undanta en demografiska grupp från att komma åt relevant innehåll. Till exempel de flesta personer i ett annat stadsdel hämta inte en försäkring premium-erbjudande och långsamt ingen i området tenderar att ser Erbjudandet alls.
* Spara kopior av modeller och learning principer om det är nödvändigt att återskapa Personalizer i framtiden. Du kan göra detta regelbundet eller uppdateringsperioden för varje modell.
* Överväg att andelen utforskning tillräcklig för området och hur du använder det som ett verktyg för att minimera effekterna av ”echo lokaler”.


## <a name="selecting-features-for-personalizer"></a>Välja funktioner för Personalizer

Personalisering av innehåll är beroende av att användbar information om innehållet och användaren. Kom ihåg för vissa program och branscher, vissa funktioner kan direkt eller indirekt anses diskriminerande och potentiellt otillåten användare.

Fundera över hur dessa funktioner:

* **Användaren demografi**: Funktioner om kön, kön, ålder, RAS, religion: Dessa funktioner kan inte tillåtna i vissa program regulatoriska orsaker och det kanske inte etiska att skräddarsy utifrån dem eftersom anpassningen skulle spridas generaliseringar och bias. Ett exempel på den här inneslutning spridning är ett projekt för tekniker som inte visas för äldre eller kön-baserade målgrupper.
* **Språkinformationen**: På många ställen i hela världen kan platsinformation (till exempel ett postnummer, postnummer eller stadsdel namn) mycket korreleras med intäkter, RAS och religion.
* **Användarens uppfattning av rättvisa**: Överväg att effekten av användare perceiving innehållet visas i ditt program ändras på ett sätt som visas korreleras till funktioner som skulle vara diskriminerande även i fall där ditt program är att fatta välgrundade beslut.
* **Oönskade Bias i funktioner**:  Det finns typer av eventuella fördomar som introduceras med hjälp av funktioner som påverkar endast en delmängd av populationen. Detta kräver extra uppmärksamhet om funktioner genereras algoritmiskt, till exempel när du använder avbildningsfilen objekt analys för att extrahera i en bild eller text analytics för att identifiera enheter i text. Göra dig medveten om egenskaperna för de tjänster du använder för att skapa de här funktionerna.

Använd följande metoder när du väljer vilka funktioner du vill skicka i sammanhang och åtgärder till Personalizer:

* Överväg att laglighet och etiska med att använda vissa funktioner för vissa program och om ofarliga försöker söker funktioner kan vara proxyservrar för andra som du vill eller bör undvika
* Vara transparent för användarna att algoritmer och analys av data som används för att anpassa de alternativ som visas.
* Fråga dig själv: Skulle användarna hand och dina om jag har använt den här informationen för att anpassa innehållet för dem? Skulle jag upplev bekvämligheten som visar hur beslutet gjordes att markera eller dölja vissa objekt?
* Använd beteendeanalys i stället för klassificering eller segmentering data baserat på andra egenskaper. Demografisk information traditionellt har använts av återförsäljare historiska skäl – demografiska attribut visat sig enkelt att samla in och vidta åtgärder innan en digital era - men fråga hur relevanta demografisk information är när du har faktiska interaktion sammanhangsberoende och historiska data som rör närmare dina användarinställningar och identiteten för användare.
* Överväg hur du förhindrar att funktioner ”falska” av obehöriga användare, som om de utnyttjas i stort kan leda till utbildning Personalizer i vilseledande sätt att avsiktligt störa, embarrass och trakassera vissa klasser av användare. 
* När lämpligt och genomförbart, designa programmet så att dina användare att anmäla eller avanmäla dig från att ha vissa personliga funktioner som används. De kan grupperas, till exempel ”information” och ”enhetsinformation”, ”senaste inköpshistorik” osv.


## <a name="computing-rewards-for-personalizer"></a>Databehandling poäng för Personalizer

Personalizer strävar efter att förbättra valet av vilken åtgärd som ska trafik baserat på trafik poängen som tillhandahålls av affärslogiken program.

En väl inbyggda utmärkelse poäng fungerar som en kortsiktig proxy till ett mål för företag som är kopplad till en organisations uppdrag.

Till exempel gör nu belönar vi på klick Personalizer Service Målsökning klick på bekostnad av allt annat, även om vad klickas på är störande och inte bundet till ett affärsresultat.

En kontrasterande exempel är vilja en nyhetswebbplats ange belöningar som är kopplad till något mer beskrivande än klickar, till exempel ”gjorde användaren tillräckligt med ägna tid åt att läsa innehållet”? ”De Klicka på relevanta artiklar eller referenser”?. Det är enkelt att knyta mått noga för att kunna fördelarna med Personalizer. Men var noga med att inte confound kortsiktig användarnas engagemang med bra resultat.

### <a name="unintended-consequences-from-reward-scores"></a>Oönskade konsekvenser från utmärkelse poäng
Utmärkelse poäng kan byggas med bäst från avsikter, men kan fortfarande skapa oväntade händelser eller oväntade resultat på hur Personalizer rangordnar innehåll. 

Överväg följande exempel:

* Nu belönar vi video personanpassning av innehåll på procentuella andelen Videolängd som bevakade kommer förmodligen tenderar att rangordnas kortare videor.
* Nu belönar vi resurser för sociala medier, utan känsloanalys av hur de delas eller själva innehållet kan leda till rangordning stötande, utan eller inflammatoriska innehållet, vilket tenderar att stimulera mycket ”engagement”, men lägger till värde.
* Nu belönar vi åtgärd på gränssnittselement som användare inte ska kunna ändra kan störa användbarhet och förutsägbarheten hos användargränssnittet, där knappar överraskande ändrar platsen eller ändamål utan varning, vilket gör det svårare för vissa grupper av användare att förbli produktiva.

Implementera dessa metodtips:

* Kör offline experiment med systemet med hjälp av olika utmärkelse metoder för att förstå effekten och sidoeffekter.
* Utvärdera dina utmärkelse funktioner och fråga dig själv hur skulle en extremt naïve person böja dess tolkning och nå oönskade resultat med den.


## <a name="responsible-design-considerations"></a>Designöverväganden för ansvariga

Här följer delar av design för ansvarar implementeringar av AI. Lär dig mer om datorns ramverket i [i framtiden beräknad](https://news.microsoft.com/futurecomputed/).

![AI-värden från framtida beräknas](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Ansvarstagande
*Personer som utforma och distribuera AI-system måste vara ansvariga för hur systemen fungerar*. 

* Skapa interna riktlinjer för hur du implementerar Personalizer, dokumentera och vidarebefordra dem till ditt team, chefer och leverantörer.
* Utför regelbundna granskningar av hur trafik poängen beräknas, utföra offline utvärderingar för att se vilka funktioner påverkar Personalizer och använda resultatet för att eliminera onödiga och onödiga funktioner.
* Informera tydligt användarna hur Personalizer används, vilket syfte och med vilken data.
* Arkivera information och resurser – till exempel modeller, learning principer och andra data – som Personalizer använder ska fungera, för att kunna återge resultat.

### <a name="transparency"></a>Transparens
*AI-system bör vara Understandable*. Med Personalizer

• Ger användare information om hur innehållet var personlig. Exempel: du kan visa dina användare en knapp med etiketten ”varför förslagen”? Visar vilka översta funktioner för användaren och åtgärder spelat en roll i resultatet av Personalizer.
• Kontrollera att dina villkor för användning gör nämner som du ska använda information om användare och deras beteende för att anpassa upplevelsen.


* *Ger användare information om hur innehållet var personlig.* Exempel: du kan visa dina användare en knapp med etiketten `Why These Suggestions?` som visar vilka översta funktioner för användaren och åtgärder spelat en roll i anpassningen.
* Kontrollera att dina användningsvillkor nämna att information om användare som kommer användas för att anpassa upplevelsen.

### <a name="fairness"></a>Rättvisa
* AI-system bör hantera alla personer ganska.

* Använd inte Personalizer för användningsfall där resultaten är långsiktiga, följdskador eller omfatta verkliga skada.
* Använd inte funktioner som inte är lämpligt att anpassa innehåll med eller som kan hjälpa att sprida oönskad eventuella fördomar. Exempelvis bör alla med liknande finansiella ställning se samma personanpassade rekommendationer för finansiella produkter.
* Förstå eventuella fördomar som kan finnas i funktioner som kommer från redigerare, algoritmiska verktyg eller användarna själva.

### <a name="reliability-and-safety"></a>Tillförlitlighet och säkerhet
*AI-system bör utföra på ett tillförlitligt och säkert*. För Personalizer:

* *Inte ge Personalizer inte bör väljas åtgärder*. Till exempel ska på ett olämpligt sätt filmer filtreras av åtgärder för att anpassa om gör en rekommendation för en anonym eller under ålder användare.
* *Hantera din Personalizer-modell som en affärstillgång*.  Överväg hur ofta du vill spara och säkerhetskopiera modellen och lära dig principer bakom Personalizer loopen och behandlar det annars som en viktig affärstillgång. Det är viktigt för lokal granska och mätning förbättring efterliknar tidigare resultat.
* *Ange kanal för att få direkt feedback från användare*. Förutom att koda säkerhet kontroller för att se till att se bara till rätt målgrupper rätt innehåll, tillhandahåller en mekanism för feedback för användare att rapportinnehåll som är så konstigt eller störa. Särskilt om innehållet kommer från användare eller 3 parter, Överväg att använda Microsoft Content Moderator eller fler verktyg att granska och validera innehåll.
* *Utför ofta offline utvärderingar*. Detta hjälper dig att övervaka trender och se till att effektivitet är känd.
* *Upprätta en process för att upptäcka och agera på manipulering av skadliga*. Det finns aktörer som drar nytta av machine learning och AI systems möjlighet att lära dig från deras miljö ska växlas resultatet mot sina mål. Om din användning av Personalizer är möjlighet att påverka viktiga alternativ, se till att ha lämpliga metoder för att upptäcka och åtgärda de här klasserna av attacker, inklusive mänsklig granskning under vissa omständigheter.

### <a name="security-and-privacy"></a>Säkerhet och sekretess
*AI-system bör vara säkra och värnar om integritet*. När du använder Personalizer:

* *Meddela användare direkt om de data som samlas in och hur de används och få deras medgivande i förväg*, följa dina lokala regler och branschstandarder föreskrifter.
* *Ger skydd av sekretess användarkontroller.* Överväg att tillhandahålla ett enkelt att hitta knappen för funktioner såsom för program som lagrar personuppgifter: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

I vissa fall kan krävas dessa enligt lag. Du överväga kompromisserna i träna modeller regelbundet så att de inte innehåller spår av borttagna data.

### <a name="inclusiveness"></a>Inclusiveness
*Åtgärda ett brett utbud av mänskliga behov och upplevelser*.
* *Ange anpassade upplevelser för hjälpmedel gränssnitt.* Effektiviteten som kommer från bra personanpassning - används för att minska mängden arbete-, rörelse- och onödiga upprepningar i interaktioner kan vara särskilt bra för personer med funktionshinder.
* *Ändra programmets beteende kontext*. Du kan använda Personalizer för att undvika tvetydigheten mellan avsikter i en chattrobot, till exempel som rätt tolkning kan vara sammanhangsberoende och en lösning kanske inte passar alla. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktiv beredskap för ökad dataskydd och styrning

Det är svårt att förutse vissa ändringar i föreskrifter kontexter, men i allmänhet skulle det vara klokt att gå längre än den minsta ram säkerställer respekt användning av personliga data och ge transparens och val som rör algoritmiska beslutsfattande.


* Överväg att planera hur du ska en situation där det nya krav på data som samlas in från enskilda användare och det finns ett behov att visa hur den används för att fatta beslut.
* Överväg att extra beredskap där användarna kan inkludera marginalized sårbara populations, barn, användare i ekonomiska säkerhetsproblem eller användare annars kan påverka från algoritmiska manipulering.
* Överväg att utökas frustrerande program och algoritmer med hur målgrupper och påverkan på målgrupp insamling av data har spelat och hur du undviker beprövade strategiska fel.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktiva utvärderingar under livscykeln projekt

Överväg att skapa metoder för gruppmedlemmar, användare och företag till rapporten frågor angående ansvarig användning och skapa en process som prioriterar sin lösning och förhindrar retaliation.

En person har du funderingar kring sidoeffekter för användning av teknik begränsas av sina perspektiv och livscykelhantering upplevelse. Utöka utbudet av yttranden som är tillgängliga genom att ta in mer diversifierad röster i team, användare och organisationer; så att det är möjligt och rekommenderas att tala. Överväg att utbildning och utbildningsmaterial att ytterligare utöka team-kunskaper i den här domänen och lägga till möjligheten att diskutera komplexa och känslig information.

Överväg att behandla uppgifter om ansvarig användande precis som andra crosscutting uppgifter i programmets hela livscykel, t.ex. uppgifter som rör användare får, säkerhet eller devops. Dessa uppgifter och deras krav får inte vara en eftertanke. Ansvarig användning ska beskrivs och verifieras under hela programmets livscykel.
 
## <a name="questions-and-feedback"></a>Frågor och feedback

Microsoft skapar kontinuerligt arbete i verktyg och dokument kan du agera utifrån dessa uppgifter. Vårt team bjuder in dig till [ger feedback till Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) om du tror att fler verktyg, produktfunktioner, och dokument kan hjälpa dig att implementera dessa riktlinjer för att använda Personalizer.

## <a name="recommended-reading"></a>Rekommenderad läsning

* Se Microsofts sex principer för ansvarar utvecklingen av AI som publicerats i januari 2018-bok [i framtiden beräknas](https://news.microsoft.com/futurecomputed/)
* [Vem äger framtiden? ](https://www.goodreads.com/book/show/15802693-who-owns-the-future) av Jaron Lanier.
* [Vapen av matematiska destruktion](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) genom - Cathy O'Neil
* [Etik och datavetenskap](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) av DJ Patil, Hilary Mason, Mike Loukides.
* [ACM koden för Ethics](https://www.acm.org/code-of-ethics)
* [Genetisk Information Nondiscrimination Act - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FATML principer för att hantera algoritmer](http://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Nästa steg

[Funktioner: åtgärden och kontext](concepts-features.md).
