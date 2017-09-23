---
title: "Azure Mobile Engagement – Komma igång med bästa praxis"
description: "Komma igång med Azure Mobile Engagement och bästa praxis för integrering"
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: dfce1183-6398-466e-aa7e-ed702fb52818
ms.service: mobile-engagement
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: wesmc;ricksal
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0cb54f6c4b84597fbc21fa691f88f6f7a37beedd
ms.contentlocale: sv-se
ms.lasthandoff: 12/07/2016

---
# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure Mobile Engagement – Komma igång med bästa praxis
## <a name="overview"></a>Översikt
**Det är många som är med och slåss om en plats på mobilskärmen:** Enligt en undersökning från 2013 fanns det i genomsnitt 27 appar installerade på varje mobil enhet. Användarna lade i snitt ned 30 timmar i månaden på apparna. Största delen av tiden tillbringades på sociala nätverk och med spel (cirka 20 timmar). År 2014 fanns det runt 1,5 miljoner appar på Android-marknaden. Apples App Store innehöll då runt 1,2 miljoner appar. Användningen av mobilappar bara fortsätter att öka och konkurrensen mellan de olika apputvecklarna är stenhård. 

En genomsnittlig mobilanvändare installerar och avinstallerar appar i takt med att personliga intressen skiftar och beroende på hur man upplever appen. För att kunna avgöra hur framgångsrik en app är måste man ta med mer än antalet installationer i beräkningen. Det är viktigt att känna till hur användbar din app är och att kunna se trendbrott i användningsmönstret. Följande frågor blir viktiga:

* Börjar användarna bli uttråkade eller har appen spelat ut sin roll? 
* Hur många användare har helt slutat använda appen? 
* Ökar eller minskar antalet köp i appen?
* Är det få användare som slutför arbetsflöden, och beror det på fel i appen eller på att användarna har tappat intresset? 
* Är det möjligt att upprätthålla appens användbarhet och relevans genom att erbjuda användarna nytt innehåll? 
* Ska alla användare få se samma nya innehåll eller ska du endast fokusera på användarsegment baserat på deras beteende i appen? 

Med hjälp av sådana här frågor kan du förlänga appens livslängd och maximera intäkterna. De kan också vara till god hjälp när du ska definiera och behålla användarbasen. 

Medierelaterade appar har ofta den högsta kvarhållningsfrekvensen bland användarna. En anledning till detta är att de hela tiden förser användarna med nytt och fräscht innehåll. Ju tidigare du börjar skicka push-meddelanden till ett användarsegment, desto större effekt får det på kvarhållningen. 

Programmet Azure Mobile Engagement är utformat för att hjälpa dig att förlänga appens livslängd och hålla kvar användarna. Det ger dig en metod för att samla in och analysera detaljerad information om appanvändningen. Det hjälper dig att klassificera din användarbas efter beteende och skapa riktade kampanjer så att du kan skicka push-meddelanden och meddelanden i appen till utvalda användarsegment. Med KPI:er (Key Performance Indicators) mäter du hur aktiva användarna är inom olika områden i appen. Azure Mobile Engagement tillhandahåller metoderna du behöver för att fastställa KPI:er. Programmet ökar avkastningen på investeringen (ROI) genom att tillhandahålla den infrastruktur du behöver för att öka användarnas interaktion med din mobilapp. 

Börja med att ta fram en väl avvägd plan för användarinteraktion, så att du kan få ut mesta möjliga av Azure Mobile Engagement. Med planen kan du identifiera de detaljer du behöver för att kunna segmentera användarbasen. Ofta baseras detta på användarnas beteende och upplevelser i appen. För att försäkra sig om framgång är det en bra idé att tydligt definiera KPI:er som mäter hur väl målen för appen uppfylls. Med tydliga nyckeltal är det enkelt att bädda in den nödvändiga logiken i appen och samla in detaljerade data som du sedan kan använda för att analysera och utvärdera dina KPI:er. Det här avsnittet tipsar om bästa praxis för att definiera de KPI:er som ska ingå i planen för användarinteraktion. 

## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Steg 1: Definiera KPI:er så att de passar BET-modellen
Det kan vara svårt att definiera KPI:er på rätt sätt. Appar för olika branscher har sina egna krav och mål. Tyvärr kan detta försvåra ditt arbete. För att underlätta bör mål och KPI:er klassificeras enligt tre huvudkategorier: **företag**, **användarinteraktion** och **teknik**. Den här metoden kallas **BET-modellen** (efter engelskans Business, Engagement, Technical).

En bra plan har vanligtvis tydliga mål med KPI:er som mäter framgången i följande kategorier enligt BET-modellen.

#### <a name="business-kpis"></a>KPI:er för företag
KPI:er för företag är troligen enklast att ta fram. Du har förmodligen redan definierat sådana i någon form när du planerade din mobilapp. Dessa KPI:er hjälper dig att mäta intäkter och avkastning för appen. Följande lista innehåller några exempel på KPI:er för affärsverksamhet som du kan utgå från när du definierar dina nyckeltal:

* KPI:er för medieföretag
  * Antal annonsklickningar
  * Antal sidbesök per användare
  * Antal prenumeranter för tillfället
* KPI:er för spelföretag
  * Antal köp i app
  * Genomsnittliga intäkter per användare (ARPU)
  * Tid per session
  * Antal speldagar och aktuell nivå i spelet
* KPI:er för e-handel
  * Antal dagar som appen har använts
  * Genomsnittliga intäkter per användare (ARPU)
  * Genomsnittligt belopp per kundvagn i kassan
  * Produktkategori med flest visningar och köp
* KPI:er för banker och försäkringsbolag
  * Antal konton
  * Funktioner som aktiverats
  * Besökta erbjudandesidor
  * Klickade eller aktiverade aviseringar/meddelanden       

#### <a name="engagement-kpis"></a>KPI:er för användarinteraktion
KPI:er för användarinteraktion mäter hur engagerade dina användare är. Trender på det här området hjälper dig att fastställa kvarhållning för din app. Här är några exempel på nyckeltal för den här typen av KPI:

* Aktiva användare under de senaste sju dagarna
* Inaktiva användare under de senaste sju dagarna
* Antal användare som inte har använt appen på 30 dagar  

Vissa uppenbara externa faktorer kan så klart påverka indikationerna inom det här området. Du kanske tar för givet att alla användare alltid har med sig sin mobila enhet. Detta kan dock variera. Spelappar används till exempel mer kring helger och i semestertider när spelarna är lediga från jobb och skola.   

Med väldefinierade KPI:er i den här kategorin blir det enklare för dig att utvärdera hur kunderna förhåller sig till appen.

#### <a name="technical-kpis"></a>KPI:er för teknik
Nyckeltal i den här kategorin kan hjälpa dig att avgöra om din app fungerar som den ska eller om den hänger sig eller kraschar. Dessa indikatorer visar hälsotillståndet för din app och identifierar användbarhetsproblem som hindrar användare från att ha nytta av appen. Information som samlas in i den här kategorin kan också innehålla nyckeltal som är relevanta för marknadsföringsavdelningen. Data av den här typen kan också vara till användning när IT-avdelningen ska köra felsökningar eller när supportavdelningen behöver rapportera okända programfel. 

Här följer några exempel på KPI:er för teknik:

* Antal ohanterade eller hanterade undantag och information om dem 
* Tidsstämpel för senaste krasch
* Senast klickade knapp eller senast besökta sida
* Appens minnesanvändning
* Appens bildfrekvens
* OS-version som appen körs på
* Appversion

Definiera KPI:er av den här typen för att mäta appens prestanda och identifiera potentiella programfel. De här nyckeltalen underlättar felkorrigeringar och hjälper dig att få ut dem till dina kunder snabbare. De kan också hjälpa dig att definiera ett användarsegment som har stött på ett specifikt problem. Använd detta användarsegment till att skapa kampanjer som skickar meddelanden om nya korrigeringsfiler och eventuella specialerbjudanden som plåster på såren. 

#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Strategibok – övning 1: Skapa en KPI-instrumentpanel
När du definierar en marknadsföringsstrategi ska KPI:erna resultera i en vy för vart och ett av huvudmålen. De ska bestå av tydligt definierade datapunkter som gör det möjligt att samla in viktig information för att kunna övervaka din app och slutanvändarens beteende.

Skapa en KPI-instrumentpanel som innehåller informationen nedan

1. Vad har appen för KPI:er?
2. Vilka datapunkter ska jag använda för att representera varje KPI?
3. Var finns dessa data i appen (skärm, inställningar, system o.s.v.)?
4. Kan jag köra en interaktionssekvens för denna KPI?

Exempel och riktlinjer finns i kalkylbladet **KPI Builder** i vår [Media Playbook-mall][Media Playbook link].

## <a name="step-2-your-engagement-program"></a>Steg 2: Ett program för användarinteraktion
Ett bra Mobile Engagement-program bör betraktas som en viktig del av din app. Det måste absolut innehålla ett bra välkomstprogram som körs mot en användare under de första dagarna av appanvändningen. Sådant har ofta en mycket positiv inverkan på användarinteraktionen och kvarhållningen i din app. Studier har visat att de flesta användarna slutar använda en app några dagar efter installationen. Sträva efter att uppfylla eller överträffa kundens förväntningar med intresseväckande innehåll medan kunden fortfarande lägger tid på att bekanta sig med appen. Se till att framhålla de värdefullaste funktionerna och fördelarna när du kommunicerar med kunderna. 

![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Push-meddelanden är det bästa sättet att få till interaktion med mobilanvändarna redan på ett tidigt stadium. Dock måste du vara försiktig när du segmenterar användare för mottagande av push-meddelanden. Om användarna tycker att de får skräppost eller ointressanta meddelanden kan det få allvarliga följder. Med några få klickningar kan en användare radera appen och aldrig mer komma tillbaka. Användaren bör istället få personanpassat mervärde från appen – inte allmänt hållna massutskick.

När användarna väl har aktiverats och blivit engagerade i appen kan ditt program för användarinteraktion även driva på utvecklingen av andra områden i appen.

Du kan till exempel konfigurera en kampanj som ber aktiva användare att betygsätta appen. Eftersom du kommunicerar med det mest aktiva och erfarna användarsegmentet kan du då förvänta dig att få relevanta betyg. Om du får höga betyg i appbutiken leder detta i regel till fler nedladdningar som också minskar kostnaderna för att förvärva nya kunder.

#### <a name="engagement-sequence"></a>Interaktionssekvenser
I varje globalt program för användaraktion ska det finnas interaktionssekvenser. Varje sekvens syftar till att nå flera mål.

###### <a name="life-push-sequence"></a>Sekvens med push-meddelanden vid en viss tidpunkt i livscykeln
Målen för den här typen av sekvens skiljer sig åt beroende på var ni befinner er i livscykeln för användarens interaktion med appen. En användare kan vara ny, inaktiv eller mycket aktiv. Under de olika faserna i livscykeln kan användarna ha användning av nytt innehåll i form av tips eller länkar till dokumentationen. 

Till exempel kan en ny användare behöva hjälp med att lära sig använda den nya appen, eller bli positivt överraskad om han/hon får ett erbjudande speciellt för nya kunder första gången appen startas (se nedan):

*”Vad kul att du registrerat dig! Kom ihåg att logga in för att få en månad utan kostnad!”*

###### <a name="behavioral-push-sequence"></a>Sekvens med beteendebaserade push-meddelanden
En sekvens med beteendebaserade push-meddelanden syftar till att öka användningen och bygger på användarbeteende som samlats in i appen.  

En mycket aktiv användare av en fotbollsapp kan till exempel ha nytta av att få följande push-meddelande:

*”Niklas, du är verkligen en seriös fotbollssupporter! Logga in på vår allsvenska sida och vinn en biljett till finalmatchen!”*

###### <a name="alerting-push-sequence"></a>Sekvens med push-meddelanden i form av aviseringar eller meddelanden
Användarna uppskattar normalt att få nyheter som har med deras intressen att göra. En sekvens med push-meddelanden i form av aviseringar eller meddelanden ökar i regel användarinteraktionen om de bygger på verkliga intressen. Intressena kan uttryckligen ha angetts av användaren direkt i appen. Men det kan också handla om underförstådda intressen baserade på data som samlats in i samband med användarens interaktion med appen.

Om en användare av en e-handelsapp köper ett visst kaffemärke med jämna mellanrum kan du registrera detta med ett KPI för företag. Om användaren får ett meddelande i stil med följande är sannolikheten stor för att interaktionen ökar:

*”Hej Max! Vi ger dig 25 % rabatt på ett av dina favoritkaffemärken under första veckan i september 2015. Eftersom du är en trogen kund ville vi bara se till så att du inte missar detta finfina erbjudande.”*

###### <a name="rentention-push-sequence"></a>Sekvens med push-meddelanden för ökad kvarhållning
Syftet med den här sekvensen är att få användarna att stanna kvar längre i appen. Kör kampanjer med upprepade push-meddelanden för att etablera en rutinmässig användning av appen. Om användaren gillar interaktionen kan det leda till en ökad kvarhållning. 

Användaren av en sportapp kan till exempel ha nytta av följande push-meddelande varje vecka (med fokus på användarens favoritlag):

*”Ta chansen att vinna 200 poäng genom att tippa vilket lag som vinner Stockholmsderbyt i helgen!”*

#### <a name="the-3w-approach"></a>3W-metoden
Lär dig interagera med slutanvändarna genom att först bemästra de olika sekvenserna med push-meddelanden. Men för att kunna anpassa dina meddelanden behöver du också lära dig 3W-metoden. Med hjälp av 3W-metoden kan du svara på frågorna Vem? Vad? och När? (Who, What, When) för varje meddelande du skickar. Om du kan ge tillfredsställande svar på dessa tre frågor är du redo för interaktion med rätt fokus.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)

###### <a name="who-the-user-segment-that-will-receive-messages"></a>Vem? Användarsegmentet som kommer att få meddelandena
Push-meddelanden är en mycket känslig kommunikationskanal. Se noga till att alla meddelanden som du riktar mot ett visst användarsegment verkligen matchar deras intressen. Felaktiga eller irrelevanta utskick har troligen en negativ inverkan på användarens tålamod. Om användaren tycker att du skickar skräppost kan det leda till att han/hon avinstallerar appen. 

Använd en kombination av specifika tekniska och beteendebaserade kriterier när du definierar det användarsegment som ska ta emot meddelandet. Här följer ett enkelt exempel på hur du kan definiera ett användarsegment:

”Alla användare som startade mobilappen för första gången för mindre än tre dagar sedan och som har besökt inloggningssidan två gånger utan att faktiskt logga in.”

Denna instruktion hjälper dig att identifiera vilka data du vill samla in i ett visst scenario.

###### <a name="what-the-message-that-you-will-send"></a>Vad? Meddelandet som ska skickas
**Lämplig ton**

Använd en lämplig ton för interaktionen och användarsegmentet i fråga. Det är i regel ett mycket bra sätt att etablera kontakt med dina slutanvändare och få dem mer intresserade av appen. 

**Omdirigering**

Ett push-meddelande kan användas till mer än att bara öppna appen. Om meddelandet har samband med en nyhet eller ett produkterbjudande kan du lägga in en djuplänk till rätt innehåll i appen. För att göra detta måste du först skapa ett URL-schema så att appen kan hantera omdirigeringen. Det här är ett mycket viktigt steg som inte får glömmas bort när du utarbetar interaktionssekvenser.

Omdirigering kan också hanteras för andra system. Med hjälp av åtgärdens URL-adress kan du omdirigera slutanvändare till ett flertal andra system, inklusive följande:

* En webbplats
* En e-postlåda som redan har konfigurerats
* En SMS-inkorg
* En fjärrtjänst
* Omdirigering till appbutiken för att betygsätta appen. 

Allt detta skapar många tillfällen till interaktion med slutanvändarna och skapar automatiska regler som förbättrar resultaten.

**Format/innehåll**

Olika typer och format för push-meddelanden: 

1. **Meddelanden**: När du vill göra reklamutskick vid olika tidpunkter (när användaren befinner sig i appen, utanför appen eller när som helst).
2. **Omröstningar**: När du vill samla in information från slutanvändarna genom att ställa frågor till dem. Svaren kan sedan användas när du ska skapa kriterier för att rikta in dig på slutanvändare.
3. **Data-push**: När du vill skicka ett push-meddelande med en binär fil eller base64-datafil som uppdaterar appen. Informationen i en data-push skickas till appen och personanpassar användarupplevelsen. Appen måste vara utformad för att ta emot data från en data-push.
4. **Paneler (endast Windows Phone)**: Använd Microsoft Push Notification Service (MPNS) för att skicka systemspecifika push-meddelanden med XML-data (stöds sedan SDK version 0.9.0. – slutlig nyttolast för panelerna får inte överstiga 32 kB). Meddelandet visas direkt på panelen.
5. **Webbvy**: En webbvy är ett popup-fönster med webbinnehåll. Det här popup-fönstret visas när slutanvändaren har klickat på push-meddelandet. En webbvy möjliggör mer interaktion med slutanvändaren.

> [!NOTE]
> Se till att det innehåll som du skickar som push-meddelanden följer respektive plattforms (iOS, Android, Windows) riktlinjer för apputveckling och sändning av push-meddelanden.
> 
> 

###### <a name="when-the-timing-of-your-campaign"></a>När? Tidpunkten för kampanjen
När är bästa tidpunkten för att aktivera en kampanj som utlöser push-meddelanden? Ska detta ske manuellt eller automatiskt? Ska det vara återkommande? Att hitta rätt tidpunkt eller frekvens är mycket viktigt för att kunna interagera med användarna och uppnå bästa möjliga resultat. För varje interaktionssekvens och scenario måste du ange den bästa tidpunkten för att skicka push-meddelanden. Här är några exempel:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Om du skickar många meddelanden varje dag måste du ta en allvarlig funderare på om det kan finnas användare som ser dina utskick som skräppost. 

Azure Mobile Engagement tillhandahåller två metoder för att undvika att få meddelanden klassade som skräppost. Använd en extremt detaljerad segmentering så att du inte riktar dig till samma användare hela tiden. Azure Mobile Engagement innehåller dessutom en ”kvotfunktion”. Med den funktionen kan du begränsa antalet meddelanden som skickas inom ramen för en och samma kampanj. Om du ställer in en standardkvot på till exempel fem meddelanden i veckan kommer ingen av de användare som ingår i kampanjens användarsegment att få fler än fem meddelanden den veckan.

#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Strategibok, övning 2: Skapa ett program för användarinteraktion
Lägg ned lite tid på att sammanfatta dina mål och på att definiera de kampanjer som du förväntar dig att köra med specifika sekvenser. Se noga till att tillämpa 3W-metoden på alla meddelanden i kampanjerna. 

Exempel och riktlinjer finns i kalkylbladet **Engagement Program** i [Media Playbook-mallen][Media Playbook link].

## <a name="step-3-app-integration"></a>Steg 3: Appintegrering
#### <a name="create-a-tag-plan"></a>Skapa en taggplan
Om du vill integrera Azure Mobile Engagement i din app måste du först skapa en taggplan. Taggplanen är själva hörnstenen i projektet. Den definierar relationen mellan marknadsföringsspecifikationer, appens arbetsflöde och verkliga taggdata som samlats in i appen. Det blir även möjligt att avläsa KPI:er. Den anger också vilka analyser som du kan se i portalen. Taggplanen är en god hjälp när du ska definiera användarsegment och skicka riktade push-meddelanden för att öka interaktionen med dina slutanvändare. När du har definierat taggplanen är det enkelt att lägga till och integrera koden i appen med Azure Mobile Engagement SDK.

En taggplan bör inte tagga precis allt i en app. Den ska bara innehålla taggdata som är del av din Mobile Engagement-strategi. Strategierna kan dock skifta mellan de olika apparna. [Media Playbook-mallen][Media Playbook link] tillhandahålls av Azure Mobile Engagement och hjälper dig att skapa en egen taggplan med önskad metod. Använd kalkylbladet **Tag Plan** (Taggplan) som utgångspunkt.

När du definierar ett taggavsnitt i kalkylbladet måste du vara mycket specifik. Det här är viktigt för att undvika onödig förvirring. Ange detaljerad information om de scenarier som ska utlösa en taggsändning. Kom ihåg att inkludera namnet på de aktiviteter där de olika taggarna är inbäddade. Detta måste inkluderas i **informationsdelen** i kalkylbladet. Kalkylbladet med taggplanen ska utgöra den huvudsakliga referensen under testverifieringen. 

I avsnittet **Data to collect** (Data som ska samlas in) ska utvecklarna kunna hitta de typer, namn, värden och extra information för nyckel-/värdeparen som de behöver för varje tagg som ska bäddas in i appen.

Vi rekommenderar att taggplanen granskas av alla team som är kopplade till projektet. Gör alla nödvändiga ändringar och skicka en bekräftelse på att allt är klart till marknadsförings- och utvecklingsavdelningarna.

Kalkylbladet **Statement of work** (Arbetsorder) används för att guida alla inblandade genom projektet.

#### <a name="data-types"></a>Datatyper
Här följer några vanliga datatyper som stöds av Azure Mobile Engagement.

###### <a name="devices-and-users"></a>Enheter och användare
Azure Mobile Engagement identifierar användare genom att generera ett unikt ID för varje enhet. Detta ID kallas enhets-ID (eller deviceid). Alla appar som körs på samma enhet delar samma enhets-ID.

###### <a name="sessions-and-activities"></a>Sessioner och aktiviteter
En session är en instans av appen som körs av en användare. Sessionen sträcker sig från den tidpunkt då användaren startar appen och fram till den stängs.

En aktivitet är en logisk gruppering av saker som appen kan göra under en session. Vanligtvis handlar det om en skärm i appen, men det kan vara i princip vad som helst som definieras av logiken i appen. Du bör åtminstone tagga alla skärmar eller aktiviteter i din app. Detta gör det lättare att förstå hur användarsökvägen ser ut.

###### <a name="events"></a>Händelser
Händelser används för att rapportera användarinteraktion med appen. Det kan till exempel röra sig om omedelbara åtgärder, som att dela innehåll eller spela upp en video. Genom att tagga händelser kan du skapa datasamlingar som visar hur användarna samverkar med appen. 

###### <a name="jobs"></a>Jobb
Jobb används för att rapportera åtgärder som har en varaktighet. Några exempel:

* Körning av API-anrop
* Visningstid för annonser
* Varaktighet för uppgifter som körs i bakgrunden 
* Varaktighet för köpprocesser
* Visa en video

###### <a name="errors"></a>Fel
Fel används för att rapportera problem som identifieras av appen. Exempel på detta är felaktiga användaråtgärder eller misslyckade API-anrop.

###### <a name="application-information"></a>Programinformation
Programinformationen (appinfon) används för att tagga data som rör användarupplevelsen i ett program. Informationen genereras när användaren interagerar med appen. 

Azure Mobile Engagement håller bara reda på den senaste värdeuppsättningen för en viss nyckel (ingen historik). Appinfon visar statusen för din app eller dina slutanvändare. Exempel på detta är inloggningsstatus eller en användares favoritprodukter.

###### <a name="crash-data"></a>Kraschdata
Kraschdata samlas in automatiskt av Mobile Engagement SDK och anger programfel som inte kan hanteras av programmet. Exempel på detta kan vara ett ohanterat undantag.

###### <a name="extra-data"></a>Extra data
Händelser, fel, aktiviteter och jobb kan göras mer avancerade med parametrar. En utvecklare kan lägga till extra information i appen. Det här är viktigt för att möjliggöra detaljerad segmentering. 

Till exempel möjliggör värdet från taggen ”artikel” normalt sett segmentering av slutanvändare baserat på vilka som läst just den artikeln. Men det räcker kanske inte för att uppnå önskat resultat. I det fallet kan det vara bra om taggen ”artikel” också är försedd med extra information som ”nyhetskategori” inom en viss aktivitet. Detta kan vara användbart när du vill fastställa användarens favoritkategorier på ett dynamiskt sätt. 

Extra information rapporteras i form av ett nyckel-/värdepar. Om du har en medieapp kan den extra informationen för ”nyhetskategori” utgöras av värdet för den kategorin. Exempel på detta är ”sport”, ”ekonomi” och ”politik”.

#### <a name="tag-and-sdk-integration"></a>Taggning och SDK-integration
Stegvisa instruktioner för att integrera Azure Mobile Engagement SDK i din app finns i dokumentet [Engagement SDK Integration](mobile-engagement-windows-store-integrate-engagement.md) (Integrering av Engagement SDK) på Azure-webbplatsen. Välj målplattformen med länkarna överst på sidan.

Vi rekommenderar att du skapar projekt för två appar som är byggda på Azure Mobile Engagement. En för utveckling och testning, och en för produktion. IT-avdelningen kan då gå från test till produktion när acceptanstestet för användare har genomförts.

#### <a name="user-acceptance-testing-uat"></a>Acceptanstest för användare (UAT)
Acceptanstest för användare (UAT) går ut på att kontrollera så att allt fungerar som avsett. Arbetsflöden ska slutföras och samla in alla nödvändiga data som anges i taggplanen:

* Informationstaggningen ska genomföras i enlighet med de dokumenterade AZME-koncepten
* All information du behöver samlas in (inklusive värden med extra information och appinfo)
* Nomenklaturen följer taggplanen
* Inga dubblettaggar får skickas

Testa alla typer av meddelandebeteenden som finns inbäddade i appen

* Meddelanden, undersökningar, data-push både i och utanför appen
* Text-/webbvisningar
* Uppdatering av aktivitetsikon, kategorier

#### <a name="setup"></a>Konfiguration
Det är mycket enkelt att konfigurera Azure Mobile Engagement. All dokumentation som rör användargränssnittet finns på webbplatsen för Azure Mobile Engagement i avsnittet [How to navigate the user interface](mobile-engagement-user-interface-home.md).

Vi rekommenderar att du börjar med att konfigurera rätt roller och rollmedlemskap för användarna i ditt projekt. Detta hjälper dig att hantera åtkomst till plattformen för alla användare. Rollerna kan vara:

* Administratörer
* Utvecklare
* Läsare 

Därefter:

* Registrera ditt enhets-ID för att kunna testa på din egen enhet.
* Gå till inställningarna för ditt konto och konfigurera tidszonen för att ange leveranstider för diagram och meddelanden i din tidszon.
* Gå till inställningarna för din app och registrera den appinfo som du behöver för att rikta in dig på slutanvändaren inom räckvidden.

Mer information om hur du kör din första kampanj med push-meddelanden finns i [How to get started using and managing pushes to reach out to your end users](mobile-engagement-how-tos.md).

## <a name="conclusion"></a>Sammanfattning
Program för användarinteraktion bör köras upprepade gånger. Prova dig fram och förbättra appen allt eftersom. 

Börja aldrig med att ta fram en global strategi, utan skaffa dig erfarenheter i mindre skala först. Använd stegvisa metoder för att identifiera KPI:er och lära dig utnyttja dem. Strategin är unik för varje app.

När du har fått en viss erfarenhet bör du överväga att lägga till följande i dina program för användarinteraktion:

* Spårning: Du kan få nya användare och troligtvis även definiera källor för datainsamling. Azure Mobile Engagement kan länkas till källor för datainsamling. På så sätt kan du övervaka resultaten från de olika källorna. Den här informationen blir intressant när du försöker hitta sätt att få största möjliga avkastning på investeringen. 
* A/B-testning: Det här är en viktig del av programmet för användarinteraktion. Varje app har sina egna specifikationer. Med A/B-testning kan du förbättra programmet för användarinteraktion.
* Geografisk placering: Här finns det stora affärsmöjligheter. Tack vare den här funktionen kan du nå användarna på rätt plats och vid rätt tidpunkt. Kontrollera att du har samlat in tillräckligt med beteendedata för slutanvändarna innan du börjar titta närmare på deras geografiska placeringar.
* Data-push: En data-push är ett osynligt push-meddelande. Med en data-push kan du anpassa appen baserat på slutanvändarnas beteende. Om ett användarsegment ofta tittar på högteknologiska produkter kan användaren skicka en data-push som personanpassar hemsidan och lägger till innehåll om just högteknologiska produkter.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett Azure Mobile Engagement-konto](mobile-engagement-create.md).
* Gå till [Definiera en strategi för Mobile Engagement](mobile-engagement-define-your-mobile-engagement-strategy.md) om du vill läsa mer om hur man skapar en strategi.

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

