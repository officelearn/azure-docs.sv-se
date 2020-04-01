---
title: Etik och ansvarsfullt bruk - Personalizer
titleSuffix: Azure Cognitive Services
description: Dessa riktlinjer syftar till att hjälpa dig att implementera personalisering på ett sätt som hjälper dig att bygga förtroende för ditt företag och din tjänst. Var noga med att stanna upp till forskning, lära och överlägga om effekterna av personalisering på människors liv. När du är osäker, sök vägledning.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: e422284b871214dbeca31b5dd17b9177a18ad3c8
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478103"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Riktlinjer för ansvarsfullt genomförande av Personalizer

För att människor och samhälle ska kunna förverkliga ai:s fulla potential måste implementeringar utformas på ett sådant sätt att de får förtroende hos dem som lägger till AI i sina applikationer och användare av applikationer som skapats med AI. Dessa riktlinjer syftar till att hjälpa dig att implementera Personalizer på ett sätt som hjälper dig att bygga förtroende för ditt företag och din tjänst. Var noga med att stanna upp till forskning, lära och överlägga om effekterna av personalisering på människors liv. När du är osäker, sök vägledning.

Dessa riktlinjer är inte avsedda som juridisk rådgivning och du bör separat se till att din ansökan överensstämmer med den snabba utvecklingen i lagen på detta område och inom din sektor.

När du utformar din applikation med Personalizer bör du också ta hänsyn till en bred uppsättning ansvarsområden som du har när du utvecklar alla datacentrerade AI-system, inklusive etik, sekretess, säkerhet, säkerhet, inkludering, öppenhet och ansvarsskyldighet. Du kan läsa mer om dessa i avsnittet [Rekommenderad läsning.](#recommended-reading)

Du kan använda följande innehåll som en checklista för start och anpassa och förfina det till ditt scenario. Det här dokumentet innehåller två huvudavsnitt: Den första är dedikerad till att lyfta fram överväganden om ansvarsfull användning när du väljer scenarier, funktioner och belöningar för Personalizer. Den andra ta en uppsättning värden som Microsoft anser bör beaktas när man bygger AI-system, och ger användbara förslag och risker om hur din användning av Personalizer påverkar dem.


## <a name="your-responsibility"></a>Ditt ansvar

Alla riktlinjer för ansvarsfullt genomförande bygger på den grund som utvecklare och företag som använder Personalizer är ansvariga och ansvariga för effekterna av att använda dessa algoritmer i samhället. Om du utvecklar ett program som din organisation ska distribuera bör du känna igen din roll och ditt ansvar för dess funktion och hur det påverkar människor. Om du utformar ett program som ska distribueras av en tredje part, kommer till en gemensam förståelse med dem om vem som är ytterst ansvarig för beteendet för programmet och dokumentera den förståelsen.

Förtroende bygger på begreppet uppfyllda åtaganden - tänk på dina användare, samhället och den rättsliga ram dina program arbetar i, för att identifiera uttryckliga och implicita åtaganden de kan ha.

Microsoft lägger kontinuerligt kraft vid sina verktyg och dokument för att hjälpa dig att agera på dessa ansvarsområden. [Ge feedback till Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) om du tror att ytterligare verktyg, produktfunktioner och dokument skulle hjälpa dig att implementera dessa riktlinjer för att använda Personalizer.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Faktorer för ansvarsfullt genomförande Personalizer

Implementera Personalizer kan vara av stort värde för dina användare och ditt företag. Om du vill implementera Personalizer på ett ansvarsfullt sätt börjar du med följande riktlinjer när:

* Välja användningsfall för att använda Anpassning.
* Bygga [belöningsfunktioner](concept-rewards.md).
* Välja vilka [funktioner](concepts-features.md) om sammanhanget och möjliga åtgärder som du ska använda för anpassning.


## <a name="choosing-use-cases-for-personalizer"></a>Välja användningsfall för Personalizer

Det är användbart att använda en tjänst som lär sig att anpassa innehåll och användargränssnitt. Det kan också tillämpas felaktigt om hur personalisering skapar negativa biverkningar i den verkliga världen, inklusive om användarna är omedvetna om innehållsanpassning.

Exempel på användning av Personalizer med ökad potential för negativa biverkningar eller brist på öppenhet inkluderar scenarier där "belöning" beror på många långsiktiga komplexa faktorer som, när över-förenklas till en omedelbar belöning kan ha ogynnsamma resultat för individer. Dessa tenderar att betraktas som "följdval" eller val som innebär en risk för skada. Ett exempel:


* **Ekonomi**: Anpassa erbjudanden på lån, finansiella och försäkringsprodukter, där riskfaktorer är baserade på data som individerna inte känner till, inte kan få eller inte kan bestrida.
* **Utbildning**: Anpassa leden för skolkurser och utbildningsinstitutioner där rekommendationer kan sprida fördomar och minska användarnas medvetenhet om andra alternativ.
* **Demokrati och medborgerligt deltagande**: Att anpassa innehåll för användare med målet att påverka åsikter är följdriktigt och manipulativt.
* **Belöningsutvärdering från tredje part**: Anpassa objekt där belöningen baseras på en utvärdering från tredje part av användaren, i stället för att ha en belöning som genereras av användarens eget beteende.
* **Intolerans mot utforskning:** Varje situation där utforskning beteende Personalizer kan orsaka skada.

När du väljer användningsfall för Personalizer:

* Starta designprocessen med tanke på hur anpassningen hjälper användarna.
* Tänk på de negativa konsekvenserna i den verkliga världen om vissa objekt inte rangordnas för användare på grund av anpassningsmönster eller utforskning.
* Fundera över om ditt användningsfall utgör automatiserad behandling som väsentligt påverkar registrerade som regleras enligt [GDPR](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) artikel 22 eller andra lagar.
* Överväg självuppfyllande profetior. Detta kan inträffa om en personaliseringsbelöning tränar en modell så att den senare ytterligare kan utesluta en demografisk grupp från att komma åt relevant innehåll. Till exempel, de flesta människor i en låg inkomst grannskap inte få en premie försäkring erbjudande, och långsamt ingen i grannskapet tenderar att se erbjudandet alls om det inte finns tillräckligt med prospektering.
* Spara kopior av modeller och utbildningsprinciper om det är nödvändigt att reproducera Personalizer i framtiden. Du kan göra detta med jämna mellanrum eller varje modelluppdateringsperiod.
* Tänk på nivån på utforskning tillräcklig för utrymmet och hur man använder det som ett verktyg för att mildra "eko kammare" effekter.


## <a name="selecting-features-for-personalizer"></a>Välja funktioner för Personalizer

Att anpassa innehåll beror på att du har användbar information om innehållet och användaren. Tänk på att vissa användarfunktioner kan betraktas som diskriminerande och potentiellt olagliga för vissa program och branscher.

Tänk på effekten av dessa funktioner:

* **Användardemografi**: Funktioner om kön, kön, ålder, ras, religion: Dessa funktioner kan inte tillåtas i vissa tillämpningar av regulatoriska skäl, och det kanske inte är etiskt att anpassa runt dem eftersom personalisering skulle sprida generaliseringar och partiskhet. Ett exempel på denna bias spridning är en platsannons för teknik som inte visas för äldre eller kön-baserade målgrupper.
* **Lokal information**: På många platser i världen, platsinformation (t.ex. ett postnummer, postnummer, eller grannskap namn) kan vara starkt korrelerade med inkomst, ras och religion.
* **Användaruppfattning av rättvisa:** Även i de fall där ditt program fattar välgrundade beslut, överväga effekten av användare som uppfattar att innehållet som visas i ditt program ändras på ett sätt som verkar vara korrelerade till funktioner som skulle vara diskriminerande.
* **Oavsiktlig bias i funktioner:** Det finns typer av fördomar som kan införas med hjälp av funktioner som bara påverkar en delmängd av befolkningen. Detta kräver extra uppmärksamhet om funktioner genereras algoritmiskt, till exempel när du använder bildanalys för att extrahera objekt i en bild eller textanalys för att identifiera entiteter i text. Gör dig medveten om egenskaperna hos de tjänster du använder för att skapa dessa funktioner.

Använd följande metoder när du väljer funktioner som ska skickas i sammanhang och åtgärder till Personalizer:

* Tänk på lagligheten och etiken i att använda vissa funktioner för vissa applikationer, och om oskyldiga utseende funktioner kan vara proxyservrar för andra du vill eller bör undvika,
* Var transparent för användare att algoritmer och dataanalys används för att anpassa de alternativ de ser.
* Fråga dig själv: Skulle mina användare bry sig och vara glad om jag använde denna information för att anpassa innehållet för dem? Skulle jag känna mig bekväm att visa dem hur beslutet fattades att markera eller dölja vissa objekt?
* Använd beteendemässiga snarare än klassificerings- eller segmenteringsdata baserat på andra egenskaper. Demografisk information användes traditionellt av återförsäljare av historiska skäl - demografiska attribut verkade enkla att samla in och agera på före en digital era, - men fråga hur relevant demografisk information är när du har faktisk interaktion, kontextuella och historiska data som relaterar närmare preferenser och identitet användare.
* Fundera på hur du förhindrar att funktioner blir "falska" av illvilliga användare, vilket om de utnyttjas i stort antal kan leda till utbildning Personalizer på vilseledande sätt att målmedvetet störa, genera och trakassera vissa klasser av användare.
* När det är lämpligt och genomförbart kan du utforma ditt program så att användarna kan välja eller välja bort vissa personliga funktioner. Dessa kan grupperas, till exempel "Platsinformation", "Enhetsinformation", "Tidigare inköpshistorik" etc.


## <a name="computing-rewards-for-personalizer"></a>Datorbelöningar för Personalizer

Personalizer strävar efter att förbättra valet av vilken åtgärd att belöna baserat på belöningspoängen som tillhandahålls av din applikationsaffärslogik.

En välbyggd belöning poäng kommer att fungera som en kortsiktig proxy till ett affärsmål, som är knuten till en organisations uppdrag.

Till exempel, givande på klick kommer att göra Personalizer Service söka klick på bekostnad av allt annat, även om det som klickas på är distraherande eller inte knuten till ett affärsresultat.

Som ett kontrasterande exempel kanske en nyhetswebbplats vill ange belöningar som är knutna till något mer meningsfullt än klick, till exempel "Har användaren spenderat tillräckligt med tid för att läsa innehållet?" "Har de klicka på relevanta artiklar eller referenser?". Med Personalizer är det lätt att knyta mått nära belöningar. Men var noga med att inte blanda ihop kortsiktiga användare engagemang med goda resultat.

### <a name="unintended-consequences-from-reward-scores"></a>Oavsiktliga konsekvenser från belöningspoäng
Belöningspoäng kan byggas med de bästa avsikter, men kan fortfarande skapa oväntade konsekvenser eller oavsiktliga resultat på hur Personalizer rankar innehåll.

Överväg följande exempel:

* Belöna videoinnehåll anpassning på andelen av videolängden såg kommer förmodligen tenderar att rangordna kortare videor.
* Belöna sociala medier aktier, utan sentiment analys av hur det delas eller själva innehållet, kan leda till rangordning offensiv, omodererat eller inflammatoriskt innehåll, som tenderar att hetsa en hel del "engagemang", men tillför lite värde.
* Att belöna åtgärden på element i användargränssnittet som användarna inte förväntar sig att ändra kan störa användargränssnittets användbarhet och förutsägbarhet, där knapparna överraskande ändrar plats eller syfte utan förvarning, vilket gör det svårare för vissa grupper av användare att förbli produktiva.

Implementera följande metodtips:

* Kör offlineexperiment med ditt system med hjälp av olika belöningsmetoder för att förstå påverkan och biverkningar.
* Utvärdera dina belöningsfunktioner och fråga dig själv hur skulle en extremt naiv person böja sin tolkning och nå oönskade resultat med den.


## <a name="responsible-design-considerations"></a>Överväganden om ansvarsfull design

Följande är designområden för ansvarsfulla implementeringar av AI. Läs mer abut detta ramverk i [Framtiden beräknas](https://news.microsoft.com/futurecomputed/).

![AI-värden från framtida beräknad](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Ansvar
*Människor som utformar och distribuerar AI-system måste vara ansvariga för hur deras system fungerar.*

* Skapa interna riktlinjer för hur du implementerar Personalizer, dokumentera och kommunicera dem till ditt team, chefer och leverantörer.
* Utför regelbundna granskningar av hur belöningspoäng beräknas, utför offlineutvärderingar för att se vilka funktioner som påverkar Personalizer och använd resultaten för att eliminera onödiga och onödiga funktioner.
* Kommunicera tydligt med dina användare hur Personalizer används, till vilket syfte och med vilka data.
* Arkivera information och tillgångar – till exempel modeller, utbildningsprinciper och andra data – som Personalizer använder för att fungera för att kunna återge resultat.

### <a name="transparency"></a>Transparens
*AI-system bör vara förståeligt*. Med Personalizer:

* *Ge användarna information om hur innehållet anpassades.* Du kan till exempel visa användarna `Why These Suggestions?` en knapp med etiketten som visar vilka toppfunktioner i användaren och åtgärder som spelat en roll i resultatet av Personalizer.
* Se till att dina användarvillkor nämner att du kommer att använda information om användare och deras beteende för att anpassa upplevelsen.

### <a name="fairness"></a>Rättvisa
*AI Systems bör behandla alla människor rättvist*.

* Använd inte Personalizer för användningsfall där resultaten är långsiktiga, följdskador eller innebär verklig skada.
* Använd inte funktioner som inte är lämpliga att anpassa innehåll med, eller som kan hjälpa till att sprida oönskade fördomar. Till exempel bör alla med liknande ekonomiska omständigheter se samma personliga rekommendationer för finansiella produkter.
* Förstå fördomar som kan finnas i funktioner som kommer från redigerare, algoritmiska verktyg eller användarna själva.

### <a name="reliability-and-safety"></a>Tillförlitlighet och säkerhet
*AI-system bör fungera tillförlitligt och säkert*. För Personalizer:

* *Ge inte åtgärder till Personalizer som inte bör väljas.* Till exempel bör felaktigt filmer filtreras bort från de åtgärder som ska anpassas om du gör en rekommendation för en anonym eller minderårig användare.
* *Hantera din Personalizer-modell som en affärstillgång.*  Fundera över hur ofta du ska spara och säkerhetskopiera modellen och inlärningsprinciperna bakom din Personalizer Loop och på annat sätt behandla den som en viktig affärstillgång. Att reproducera tidigare resultat är viktigt för självrevision och mätning av förbättringar.
* *Ge kanaler för att få direkt feedback från användare*. Förutom kodning säkerhetskontroller för att se till att endast rätt målgrupper se rätt innehåll, ge en feedback mekanism för användare att rapportera innehåll som kan vara överraskande eller störande. Särskilt om ditt innehåll kommer från användare eller tredje part kan du överväga att använda Microsoft Content Moderator eller ytterligare verktyg för att granska och validera innehåll.
* *Utför frekventa offlineutvärderingar*. Detta hjälper dig att övervaka trender och se till att effektiviteten är känd.
* *Upprätta en process för att upptäcka och agera på skadlig manipulation*. Det finns aktörer som kommer att dra nytta av maskininlärning och AI-system förmåga att lära av sin miljö för att flytta resultatet mot sina mål. Om din användning av Personalizer är i stånd att påverka viktiga val, se till att ha lämpliga medel för att upptäcka och mildra dessa klasser av attacker, inklusive mänsklig granskning under lämpliga omständigheter.

### <a name="security-and-privacy"></a>Säkerhet och sekretess
*AI-system bör vara säkra och respektera integriteten.* När du använder Personalizer:

* *Informera användarna på framsidan om de data som samlas in och hur de används och få deras samtycke i förväg,* enligt dina lokala och branschregler.
* *Tillhandahålla användarkontroller som skyddar sekretessen.* För program som lagrar personlig information kan du överväga att tillhandahålla en lättsökningsknapp för funktioner som:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

I vissa fall kan dessa krävas enligt lag. Tänk på kompromisserna i omskolningsmodeller med jämna mellanrum så att de inte innehåller spår av borttagna data.

### <a name="inclusiveness"></a>Delaktighet
*Ta itu med ett brett spektrum av mänskliga behov och erfarenheter*.
* *Tillhandahålla anpassade upplevelser för hjälpmedelsaktiverade gränssnitt.* Den effektivitet som kommer från god personalisering - tillämpas för att minska mängden ansträngning, rörelse, och onödig upprepning i interaktioner- kan vara särskilt fördelaktigt för personer med funktionshinder.
* *Justera programbeteendet till kontext*. Du kan använda Personalizer för att skilja mellan avsikter i en chattrobot, till exempel, eftersom rätt tolkning kan vara kontextuell och en storlek kanske inte passar alla.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktiv beredskap för ökat dataskydd och ökad styrning

Det är svårt att förutse specifika förändringar i regleringssammanhang, men i allmänhet vore det klokt att gå längre än den lagstadgade minimiramen för att säkerställa en respektfull användning av personuppgifter och tillhandahålla insyn och valfrihet i samband med algoritmiskt beslutsfattande.


* Överväg att planera i förväg till en situation där det kan finnas nya restriktioner för data som samlats in från individer, och det finns ett behov av att visa hur det användes för att fatta beslut.
* Överväg extra beredskap där användare kan inkludera marginaliserade sårbara populationer, barn, användare i ekonomisk sårbarhet eller användare som på annat sätt kan påverka från algoritmisk manipulation.
* Tänk på det utbredda missnöjet med hur målgruppsinriktning och målgruppspåverkande program och algoritmer för datainsamling har spelat ut och hur man undviker beprövade strategiska fel.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktiva bedömningar under projektets livscykel

Överväg att skapa metoder för gruppmedlemmar, användare och företagare att rapportera problem med ansvarsfull användning och skapa en process som prioriterar deras lösning och förhindrar repressalier.

Varje person som tänker på biverkningar av användning av någon teknik begränsas av deras perspektiv och livserfarenhet. Utöka antalet tillgängliga åsikter genom att ta in mer olika röster i dina team, användare eller rådgivande nämnder. så att det är möjligt och uppmuntras för dem att säga ifrån. Överväg utbildning och läromedel för att ytterligare utöka gruppkunskaperna i den här domänen och lägga till funktioner för att diskutera komplexa och känsliga ämnen.

Överväg att behandla uppgifter om ansvarsfull användning precis som andra övergripande uppgifter i programmets livscykel, till exempel uppgifter som är relaterade till användarupplevelse, säkerhet eller DevOps. Dessa uppgifter och deras krav kan inte vara en eftertanke. Ansvarsfull användning bör diskuteras och verifieras under hela programmets livscykel.

## <a name="questions-and-feedback"></a>Frågor och feedback

Microsoft lägger kontinuerligt kraft vid verktyg och dokument som hjälper dig att agera utifrån dessa ansvarsområden. Vårt team uppmanar dig att [ge feedback till Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) om du tror att ytterligare verktyg, produktfunktioner och dokument skulle hjälpa dig att implementera dessa riktlinjer för att använda Personalizer.

## <a name="recommended-reading"></a>Rekommenderad läsning

* Se Microsofts sex principer för ansvarsfull utveckling av AI som publiceras i boken [The Future Computed](https://news.microsoft.com/futurecomputed/) i januari 2018
* [Vem äger framtiden?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) av Jaron Lanier.
* [Vapen av Math Förstörelse](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) av - Cathy O'Neil
* [Etik och datavetenskap](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) av DJ Patil, Hilary Mason, Mike Loukides.
* [ACM etiska regler](https://www.acm.org/code-of-ethics)
* [Genetisk information Icke-diskriminering Act - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FATML Principer för ansvariga algoritmer](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Nästa steg

[Funktioner: handling och sammanhang](concepts-features.md).
