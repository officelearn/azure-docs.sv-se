---
title: Designmönster för avslöjanden
titleSuffix: Azure Cognitive Services
description: Designmönster och bästa praxis för offentliggörande.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776630"
---
# <a name="disclosure-design-patterns"></a>Utlämnande av designmönster
Nu när du&#39;har fastställt rätt [nivå av utlämnande](concepts-disclosure-guidelines.md#disclosure-assessment) för din syntetiska röst upplevelse, det&#39;är en bra tid att utforska potentiella designmönster.
## <a name="overview"></a>Översikt
Det finns ett spektrum av designmönster för avslöjande som du kan använda för din syntetiska röstupplevelse. Om resultatet av din bedömning av meddelandet av uppgifter var "High Disclosure", rekommenderar vi [**explicit utlämnande**](#explicit-disclosure), vilket innebär att meddela ursprunget till den syntetiska rösten direkt. [**Implicit avslöjande**](#implicit-disclosure) innehåller ledtrådar och interaktionsmönster som gynnar röstupplevelser oavsett om de krävs är högt eller lågt.
![Spektrum av informationsmönster](media/responsible-ai/disclosure-patterns/affordances.png)






| Explicita informationsmönster                                                                                                                                                                                    | Implicita informationsmönster                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Transparent introduktion](#transparent-introduction)<br> [Verbal transparent introduktion](#verbal-transparent-introduction)<br>  [Explicit Byline](#explicit-byline)<br>  [Anpassning och kalibrering](#customization-and-calibration)<br> [Föräldrarnas avslöjande](#parental-disclosure)<br> [Ge dig möjlighet att lära sig mer om hur rösten gjordes](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Avslöjande av kapacitet](#capability-disclosure)<br>[Implicita tips och feedback](#implicit-cues--feedback)<br> [Genomskinlighet för konversationer](#conversational-transparency) |



Använd följande diagram för att referera direkt till de mönster som gäller för din syntetiska röst. Några av de andra villkoren i det här diagrammet kan också gälla för ditt scenario:<br/>



| Om din syntetiska röstupplevelse... | Rekommendationer | Designmönster |
| --- | --- | --- |
| Kräver hög information  | Använd minst ett explicit mönster och implicita tips på framsidan för att hjälpa användare att skapa associationer. |[Uttryckligt utlämnande](#explicit-disclosure)<br>[Implicit offentliggörande](#implicit-disclosure)  |
| Kräver lågt offentliggörande | Utlämnande kan vara minimal eller onödig, men kan dra nytta av vissa implicita mönster. | [Avslöjande av kapacitet](#capability-disclosure)<br>[Genomskinlighet för konversationer](#conversational-transparency)  |
| Har ett stort engagemang | Bygg långsiktigt och erbjuda flera startpunkter till avslöjande längs användarens färd. Det rekommenderas starkt att ha en introduktionsupplevelse. | [Transparent introduktion](#transparent-introduction)<br>[Anpassning och kalibrering](#customization-and-calibration)<br>[Avslöjande av kapacitet](#capability-disclosure) |
| Inkluderar barn som den primära avsedda målgruppen | Rikta in föräldrar som den primära målgruppen och se till att de effektivt kan kommunicera utlämnande till barn.  | [Föräldrarnas avslöjande](#parental-disclosure)<br>[Verbal transparent introduktion](#verbal-transparent-introduction)<br> [Implicit offentliggörande](#implicit-disclosure)<br> [Genomskinlighet för konversationer](#conversational-transparency)  |
| Inkluderar blinda användare eller personer med nedsatt syn som den primära avsedda målgruppen  | Var inkluderande för alla användare och se till att alla former av visuellt avslöjande har tillhörande alternativ text eller ljudeffekter. Följ tillgänglighetsstandarderna för kontrastförhållande och visningsstorlek. Använd hörselsignaler för att kommunicera avslöjande.  | [Verbal transparent introduktion](#verbal-transparent-introduction) <br>[Auditiva cues](#implicit-cues--feedback)<br>[Haptiska ledtrådar](#implicit-cues--feedback)<br>[Genomskinlighet för konversationer](#conversational-transparency)<br>[Tillgänglighetsstandarder](https://www.microsoft.com/accessibility) |
| Är skärmlös, enhetslös eller använder röst som primärt eller enda läge för interaktion | Använd hörselsignaler för att kommunicera avslöjande. | [Verbal transparent introduktion](#verbal-transparent-introduction) <br> [Auditiva cues](#implicit-cues--feedback)  |
| Potentiellt inkluderar flera användare/lyssnare (t.ex. personlig assistent i flera hushåll)  | Var uppmärksam på olika användarkontexter och nivåer av förståelse och erbjuder flera möjligheter till utlämnande i användarens resa.  | [Transparent introduktion (returanvändare)](#transparent-introduction)<br> [Ge dig möjlighet att lära sig mer om hur rösten gjordes](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Genomskinlighet för konversationer](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Uttryckligt offentliggörande
Om din syntetiska röstupplevelse kräver hög information, är det bäst att använda minst ett av följande explicita mönster för att tydligt ange den syntetiska naturen.
### <a name="transparent-introduction"></a>Transparent introduktion

Innan röstupplevelsen börjar, introducera den digitala assistenten genom att vara helt transparent om ursprunget till sin röst och dess förmåga. Det optimala tillfället att använda det här mönstret är när du onboarding en ny användare eller när du introducerar nya funktioner till en återvändande användare. Genomföra implicita ledtrådar under en introduktion hjälper användare att bilda en mental modell om den syntetiska karaktären hos den digitala agenten.

#### <a name="first-time-user-experience"></a>Första gången användarupplevelse

![Transparent introduktion under första körningen erfarenhet](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Den syntetiska rösten introduceras medan onboarding en ny användare.*

Rekommendationer
- Beskriv att rösten är artificiell &quot;(t.ex. digital&quot;)
- Beskriv vad agenten kan göra
- Ange uttryckligen rösten&#39;ursprung
- Erbjud en ingång för att lära dig mer om den syntetiska rösten

#### <a name="returning-user-experience"></a>Återkommande användarupplevelse

Om en användare hoppar över introduktionsupplevelsen fortsätter du att erbjuda startpunkter till transparent introduktion tills användaren utlöser rösten för första gången.
<br/>

![Transparent introduktion under returanvändarupplevelsen](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Ge en konsekvent startpunkt till den syntetiska röstupplevelsen. Tillåt användaren att återgå till introduktionsupplevelsen när de utlöser rösten för första gången när som helst under användarens färd.*


### <a name="verbal-transparent-introduction"></a>Verbal transparent introduktion

En talad snabb som anger ursprunget till den digitala assistenten&#39;röst är tillräckligt tydlig på egen hand för att uppnå utlämnande. Det här mönstret är bäst för scenarier med hög information där röst är det enda tillgängliga interaktionssättet.
<br/>

![Muntligt talat transparent introduktion](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Använd en transparent introduktion när det finns ögonblick i användarupplevelsen där du kanske redan introducerar eller tillskriver en person&#39;röst.*


![Verbalt talat transparent introduktion i första person](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*För ytterligare öppenhet, kan röstskådespelaren avslöja ursprunget till den syntetiska rösten i den första personen.*

### <a name="explicit-byline"></a>Explicit Byline

Använd det här mönstret om användaren kommer att interagera med en ljudspelare eller interaktiv komponent för att utlösa rösten.


![Explicit byline i ett nyhetsmediescenario](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*En tydlig byline är tilldelningen av var rösten kom ifrån.*

Rekommendationer

- Erbjud startpunkt för att lära dig mer om den syntetiserade rösten

### <a name="customization-and-calibration"></a>Anpassning och kalibrering

Ge användarna kontroll över hur den digitala assistenten svarar på dem (d.v.s. hur rösten ljuder).  När en användare interagerar med ett system på sina egna villkor och med specifika mål i åtanke, då per definition, har de redan förstått att det inte&#39;är inte en verklig person.

#### <a name="user-control"></a>Användarkontroll

Erbjud val som har en meningsfull och märkbar inverkan på den syntetiska röstupplevelsen.

![Användarinställningar](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Med användarinställningar kan användarna anpassa och förbättra sina erfarenheter.*

Rekommendationer

- Tillåt användare att anpassa rösten (t.ex. välja språk och rösttyp)
- Ge användarna ett sätt att lära systemet att svara på sin unika röst (t.ex. röstkalibrering, anpassade kommandon)
- Optimera för användargenererade eller kontextuella interaktioner (t.ex. påminnelser)

#### <a name="persona-customization"></a>Anpassning av Persona

Erbjud sätt att anpassa den digitala assistenten&#39;röst. Om rösten är baserad på en kändis eller en allmänt igenkännlig person kan du använda både visuella och talade introduktioner när användare förhandsgranskar rösten.

![Röstanpassning](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Att erbjuda möjligheten att välja från en uppsättning röster bidrar till att förmedla den konstgjorda naturen.*

Rekommendationer
- Tillåt användare att förhandsgranska ljudet för varje röst
- Använd en autentisk introduktion för varje röst
- Erbjud ingångar för att lära dig mer om den syntetiserade rösten

### <a name="parental-disclosure"></a>Föräldrarnas avslöjande

Förutom att följa COPPA-reglerna, lämna information till föräldrar om din primära avsedda målgrupp är små barn och din exponeringsnivå är hög. För känsliga användningsområden, överväga att ge upplevelsen tills en vuxen har erkänt användningen av den syntetiska rösten. Uppmuntra föräldrar att förmedla budskapet till sina barn.

![Utlämnande för föräldrar](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*En transparent introduktion optimerad för föräldrar säkerställer att en vuxen blev medveten om den syntetiska karaktären av rösten innan ett barn interagerar med den.*

Rekommendationer

- Rikta in föräldrar som den primära målgruppen för avslöjande
- Uppmuntra föräldrar att informera sina barn
- Erbjud ingångar för att lära dig mer om den syntetiserade rösten
- Gate upplevelsen genom att &quot;ställa&quot; föräldrar en enkel skydd fråga för att visa att de har läst utlämnandet

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Ge dig möjlighet att lära sig mer om hur rösten gjordes

Erbjud sammanhangsberoende startpunkter till en sida, popup-sida eller extern webbplats som ger mer information om den syntetiska rösttekniken. Du kan till exempel visa en länk för att lära dig mer under introduktionen eller när användaren ber om mer information under konversationen.

![Startpunkt för att lära dig mer](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Exempel på en startpunkt för att ge möjlighet att lära sig mer om den syntetiserade rösten.*

När en användare begär mer information om den syntetiska rösten är det primära målet att utbilda dem om den syntetiska röstens ursprung och att vara transparenta om tekniken.

![Ge användarna mer information om syntetisk röst](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Mer information kan erbjudas på en extern webbplats.*

Rekommendationer

- Förenkla komplexa koncept och undvika att använda legalese och teknisk jargong
- Begrava inte detta innehåll i sekretess och användningsförklaringar
- Håll innehållet koncist och använd bilder när det är tillgängligt

## <a name="implicit-disclosure"></a>Implicit offentliggörande

Konsekvens är nyckeln till att uppnå utlämnande implicit under hela användarresan. Konsekvent användning av visuella och auditiva signaler över enheter och interaktionssätt kan hjälpa till att skapa associationer mellan implicita mönster och explicit avslöjande.

![Konsekvens av implicita signaler](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Implicita tips & feedback

Antropomorfism kan manifesteras på olika sätt, från den faktiska visuella representationen av medlet, till rösten, ljud, ljusmönster, studsande former eller till och med vibrationerna hos en enhet. När du definierar din persona, utnyttja implicita ledtrådar och mönster feedback snarare än att sträva efter en mycket mänsklig-liknande avatar. Detta är ett sätt att minimera behovet av mer explicit utlämnande.

![Visuella tips och feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Dessa ledtrådar hjälpa antropomorfisera agenten utan att vara alltför mänsklig-liknande. De kan också bli effektiva informationsmekanismer på egen hand när de används konsekvent över tiden.*

Consider the different modes of interactions of your experience when incorporating the following types of cues:

| Visuella tips                                                                                                                                                               | Auditiva cues                                                      | Haptiska ledtrådar |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Responsiva realtidssignaler (t.ex. animeringar)<br> Tips som inte är skärm (t.ex. lampor och mönster på en enhet)<br>  | Sonicon (t.ex. ett kort distinkt ljud, serie noter) | Vibrationer   |

### <a name="capability-disclosure"></a>Avslöjande av kapacitet

Avslöjandet kan uppnås implicit genom att ställa in korrekta förväntningar på vad den digitala assistenten är kapabel till. Ge exempelkommandon så att användarna kan lära sig att interagera med den digitala assistenten och erbjuda kontextuell hjälp för att lära sig mer om den syntetiska rösten under de tidiga stadierna av upplevelsen.

![Visuella tips och feedback](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Genomskinlighet för konversationer

När konversationer hamnar i oväntade sökvägar bör du överväga att skapa standardsvar som kan hjälpa till att återställa förväntningar, öka transparensen och styra användarna mot lyckade vägar. Det finns möjligheter att använda explicit utlämnande i samtal också.

![Hantera oväntade banor](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Off-task &quot;eller&quot; personliga frågor riktade till agenten är ett bra tillfälle att påminna användarna om den syntetiska karaktären av agenten och styra dem att engagera sig i det på lämpligt sätt eller att omdirigera dem till en verklig person.

![Hantera frågor om uppgifter](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>När du ska avslöja

Det finns många möjligheter till utlämnande under hela användarresan. Design för första användningen, andra användningen, nth användning &quot;..., men också omfamna stunder av underlåtenhet&quot; att markera öppenhet, som när systemet gör ett misstag eller när användaren upptäcker en begränsning av agenten&#39;kapacitet.

![Möjligheter till offentliggörande under hela en användarresa](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Exempel på en vanlig användare av digitala assistenter som belyser olika informationsmöjligheter.

### <a name="up-front"></a>Up-front

Det optimala ögonblicket för avslöjande är första gången en person interagerar med den syntetiska rösten.I ett personligt röstassistentscenario skulle detta vara under introduktion, eller första gången användaren praktiskt taget unboxes upplevelsen. I andra scenarier kan det vara första gången en syntetisk röst läser innehåll på en webbplats eller första gången en användare interagerar med ett virtuellt tecken.

- [Transparent introduktion](#transparent-introduction)
- [Avslöjande av kapacitet](#capability-disclosure)
- [Anpassning och kalibrering](#customization-and-calibration)
- [Implicita stack- och](#implicit-cues--feedback)

### <a name="upon-request"></a>På begäran

Användare bör enkelt kunna komma åt ytterligare information, kontrollera preferenser och få transparent kommunikation när som helst under användarens resa på begäran.

- [Ge dig möjlighet att lära sig mer om hur rösten gjordes](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Anpassning och kalibrering](#customization-and-calibration)
- [Genomskinlighet för konversationer](#conversational-transparency)

### <a name="continuously"></a>Kontinuerligt

Använd de implicita designmönster som förbättrar användarupplevelsen kontinuerligt.

- [Avslöjande av kapacitet](#capability-disclosure)
- [Implicita stack- och](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>När systemet misslyckas

Använd avslöjandet som en möjlighet att misslyckas graciöst.

- [Genomskinlighet för konversationer](#conversational-transparency)
- [Ge dig möjlighet att lära sig mer om hur rösten gjordes](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Handoff till människa](#conversational-transparency)



## <a name="additional-resources"></a>Ytterligare resurser
- [Riktlinjer för Microsoft-robotar](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Riktlinjer för Cortana-design](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Riktlinjer för innehållsdesign för Microsoft Windows UWP](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Riktlinjer för kommandot över Microsoft Windows-meddelanden om röstkommando](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Referensdokument

* [Avslöjande för Voice Talent](https://aka.ms/disclosure-voice-talent)
* [Riktlinjer för ansvarsfull användning av syntetisk röstteknik](concepts-guidelines-responsible-deployment-synthetic.md)
* [Gating Översikt](concepts-gating-overview.md)
* [Hur avslöja](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Nästa steg

* [Avslöjande för Voice Talent](https://aka.ms/disclosure-voice-talent)
