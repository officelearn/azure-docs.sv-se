---
title: Fördefinierad domän referens – LUIS
titleSuffix: Azure Cognitive Services
description: Referens för de fördefinierade domäner som är färdiga samlingar av avsikter och entiteter från Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 7e2b5c5c5d2ca4c0d6ab820866341c5f30082c5f
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672778"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Fördefinierad domän referens för din LUIS-app
Den här referensen innehåller information om den [fördefinierade domäner](luis-how-to-use-prebuilt-domains.md), som är färdiga samlingar med avsikter och entiteter som LUIS erbjuder.

[Anpassade domäner](luis-how-to-start-new-app.md), däremot starta utan avsikter och modeller. Du kan lägga till några fördefinierade domän avsikter och entiteter till en anpassad modell.

## <a name="supported-domains-across-cultures"></a>Domäner som stöds över kulturer

Tabellen nedan sammanfattar de domäner som stöds för närvarande. Stöd för engelska är vanligt vis mer komplett än andra. 

| Entitetstyp       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [Månad](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Uppgifter](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [E-post](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Anteckningar](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Platser](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [RestaurantReservation](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Att göra](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Verktyg](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Baserad](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Webb](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Fördefinierade domäner **stöds inte** i:

* Franska, kanadensisk
* Hindi
* Spanska mexikanska

## <a name="description-for-luis-prebuilt-domains"></a>Beskrivning av LUIS fördefinierade domäner
## <a name="calendar"></a>**Månad**
Kalender är allt om personliga möten och möten, inte offentlig händelse (till exempel världs omfattande scheman, händelse kalender i Seattle) eller en allmän kalender (till exempel vilken dag är den idag, vad som är dags att börja, när är arbets dag).
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------------
 AcceptEventEntry | Acceptera en (n) avtalad tid/ett möte/en händelse i kalendern. | Acceptera en avtalad tid. <br> Acceptera händelsen <br> acceptera dagens möte.
 Avbryt | Avbryt den pågående åtgärden från virtuell assistent, till exempel att avbryta processen med att skapa ett möte. <br> _**Meddelande**: I detta syfte ingår huvudsakligen åtgärden "Avbryt" i kalender scenariot. Om du behöver ett allmänt uttryck på "Avbryt" kan du utnyttja avsikten "Cancel" i **verktyg** -domänen._ | Det är OK, du behöver bara avbryta händelsen. <br> Nej, jag avbryter bara den avtalade tiden.
 ChangeCalendarEntry | Ändra eller ändra schemat för kalender posten. | Schemalägg om min förmiddag avtalad tid imorgon till 17.00 <br> Tidsplanera om läkares avtalade tider i 5 PM <br> Planera lunch med Jenny Olson till fredag. <br> Ändra händelse tid.
 CheckAvailability | Hitta tillgänglighet för en avtalad tid eller ett möte på användarens kalender eller en annan persons kalender. | När är tillgängliga för att möta Jim? <br> Visa när Carol är tillgängligt imorgon. <br> Är Chris kostnadsfritt på lördag?
 Bekräfta | Bekräfta om en åtgärd/åtgärd ska utföras baserat på tidigare avsikt. <br> _**Meddelande**: I detta syfte ingår huvudsakligen åtgärden "bekräfta" för kalender scenariot. Om du behöver fler allmänna uttryck på "bekräfta", kan du använda "bekräfta" avsikt i **verktyg** -domänen._| Det stämmer. skapa mötet <br> Ja, tack, Anslut till mötet.
 ConnectToMeeting | Anslut till ett möte. | Anslut mig till 11:00 konferens samtal med Andy. <br> Godkänn budget Mötes anropet.
 ContactMeetingAttendees | Kontakta Mötes deltagarna. | Berätta för mötet som jag kör sent till 3:00-möte. <br> Meddela kollegor om 8: a möte som det behöver starta vid 8:30.
 CreateCalendarEntry | Lägga till ett nytt enstaka objekt i kalendern. | Skapa ett möte om att diskutera problem. <br> skapa ett möte medabc@microsoft.com
 DeleteCalendarEntry | Begäran att ta bort en kalenderpost.| Avbryta min avtalad tid med Carol. <br> Ta bort min 09.00 uppfylla. <br> Ta bort min händelse.
  FindCalendarEntry | Öppna kalender programmet eller Sök i kalender posten. | Hitta tandläkaren granska avtalad tid. <br> Visa min kalender. <br> Vad finns i min kalender på torsdag?
 FindCalendarWhen | Kontrol lera tiden då schemat äger rum. | När uppfyller jag med gul och Susan? <br> När har jag en brunch schemalagd? 
 FindCalendarWhere | Kontrol lera platsen där schemat äger rum. | Var finns mina möten imorgon? <br>Vart är jag möte med Stacy och Michael imorgon för middag?
  FindCalendarWho | Markera de deltagare som ska närvara vid mål planen. | Jag vill ha bekräftat Attendants för morgon dagens möte vid 2. <br> Kommer Jim att vara vid nästa möte?
 FindCalendarDetail | Kontrol lera och Visa information om schemat. | Jag behöver lämna information om det möte jag har schemalagt med min kollega i Paul.
 FindDuration | Kontrol lera varaktigheten. | Hur lång tid behöver jag för att hämta inköps varor? <br> Hur lång tid har jag för lunch?
 FindMeetingRoom | Hitta de tillgängliga Mötes rummen. | Vad uppfyller rum? <br> En ny Mötes plats, hitta en.
 GoBack | Gå tillbaka till det sista steget eller objektet.  <br> _**Meddelande**: Mer GoBack-yttranden finns i avsnittet om **verktyg** ._ | Föregående en <br> Tillbaka till senaste e-post.
 Avvisa | Användaren avvisar vilken virtuell assistent som föreslagits. <br> _**Meddelande**: Mer information om allmänna yttranden finns i domänen för innehålls Förtecknings **verktyg** ._ | Du behöver inte ange händelsen. <br> Jag har andra saker att göra vid den tiden.
ShowNext | Kontrol lera nästa händelse. <br> _**Meddelande**: Se **verktyg** -domänen för mer ShowNext General yttranden._ | Lämna nästa händelse. <br> Vad är intill i kalendern?
 ShowPrevious | Kontrol lera föregående händelse. <br> _**Meddelande**: Se **verktyg** -domänen för mer ShowPrevious General yttranden._ | Hur ser schemat före?
 TimeRemaining | Kontrol lera den återstående tiden till nästa händelse. | Visar hur lång tid det tar innan mina möten visas. <br> Visa hur lång tid det får innan nästa möte börjar.
 
### <a name="entities"></a>**Poster**
Entitetsnamn | Entitetstyp | Beskrivning | Exempel | Platser
-------|-----------------------|-------------|---------|--------
Kontaktnamn | personName | Namnet på en kontakt eller en Mötes deltagare. | Uppfyller **Betsy**. <br>  Möte med **Aubrey** den tredje juli på 7 p.m. | Betsy <br> Aubrey <br> Amy 
DestinationCalendar | gång | Mål kalenderns namn. | lunch med MOM tisdag 12 **personal** <br> Använd min **Google** Calendar som min standard kalender. <br> Uppdatera yoga-klassen till Mon ons på 14:00 lista i **personlig** kalender. | Google <br> Egna <br> verksamheten <br> Huvudtillg
Duration | datetime | Varaktigheten för ett möte, en avtalad tid eller en återstående tid. | Lägg till i arbets kalender mötet med Gary för att diskutera Scholarship-information imorgon vid 11 am i **20 minuter**. <br> Lägg till i kalendern en händelse på Subway på fredag jag ska äta med Thomas i **en timme** kl. 9 p.m. | en timme <br> 2 dagar <br> 20 minuter 
EndDate | datetime | Slutdatum för ett möte eller en avtalad tid. | Kalender Lägg till konsert i bas Hall Maria tredje till **Mary 5** | Mary 5  
EndTime | datetime | Slut tid för ett möte eller en avtalad tid. | kan du göra det 2 30 till **tre** | tre 
Location | gång | Platsen för kalenderobjekt, möte eller avtalad tid.  Adresser, orter och regioner är bra exempel på platser. | Lägg ett möte i **Fremont** för att publicera surfplattan i Burma <br> Pro Bono-möte i **Edina** | 209 Nashville gymmet <br> 897 pannkaka house <br> Garage 
MeetingRoom | gång | Utrymme för ett möte eller en avtalad tid. | Lägg till i arbets kalender mötet med Johan vid 17.00 på **kontoret** i denna fredag | sitt kontor <br> konferens rum <br> Rum 2
MoveEarlierTimeSpan | datetime | Tiden då användaren vill flytta ett möte eller en avtalad tid tidigare. | Flytta min lunch dag framåt med **30 minuter**. | 30 minuter <br> två timmar 
MoveLaterTimeSpan |  datetime | Tiden då användaren vill flytta ett möte eller en avtalad tid senare. | Skicka mitt möte med Orkidélila Box tillbaka **4 timmar**. | 4 timmar <br> 15 minuter 
OrderReference | gång | Ordningstalet eller relativ position i en lista, identifiera ett objekt som ska hämtas. | Vad är min nästa möte i morgon? | nästa
OriginalEndDate | datetime | Ursprungligt slutdatum för ett möte eller en avtalad tid. | Ändra min semester från att sluta på **fredag** till måndag | Fredag 
OriginalEndTime | datetime | Ursprunglig slut tid för ett möte eller en avtalad tid. | Gör så att den ena slutar vid **3** går till 4 | 3
OriginalStartDate | datetime | Ursprungligt start datum för ett möte eller en avtalad tid. | Ändra den avtalade tiden för **morgon dagen**från 10 a.m. till onsdag kl. 09:00  | imorgon 
OriginalStartTime | datetime | Ursprunglig start tid för ett möte eller en avtalad tid. | Ändra den avtalade tiden för morgon dagen från **10 a.m.** till onsdag kl. 09:00 | 10 a.m.
PositionReference | numret | Den absoluta positionen i en lista som identifierar ett objekt som ska hämtas. | Den **andra** | sekund <br> Nej. 3 <br> nummer 5
RelationshipName | list | Relations namnet för en kontakt. | Lägg till lunch vid 1:00 P.M. med min **fru** | maka <br> make <br> Syster 
SlotAttribute | gång | Attributet användare vill fråga eller redigera. | Ändra händelse **plats** <br> ändra **tiden** till sju klockan | location <br> time 
/SD | datetime | Start datum för ett möte eller en avtalad tid. | Skapa ett möte på **onsdag** vid 4 p.m. | Onsdag 
StartTime | datetime | Start tid för ett möte eller en avtalad tid. | skapa ett möte på onsdag vid **4 p.m.** | 4 p.m.
Subject | enkelt, mönster. Helst | Ämne, till exempel rubrik för ett möte eller en avtalad tid. | Vilken tid är **parten som förberedelse** möte? | Tandläkarens <br> Lunch med Julia 
Message | enkelt, mönster. Helst | Meddelandet som ska skickas till deltagarna. | Meddela deltagarna om middags möte som **Jag är försenad**. | Jag är sen

## <a name="communication"></a>**Uppgifter**
Förfrågningar om att ringa, skicka texter/IMs, hitta/lägga till kontakter och andra kommunikations-relaterade begär Anden (vanligt vis utgående). _Endast kontakt namnet_ yttranden tillhör inte kommunikations domänen.

### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
AddContact | Lägga till en ny kontakt i användarens lista över kontakter. | Lägg till ny kontakt.  <br>   Spara det här talet och ange namnet som Jane.
AddFlag | Lägg till flagga i ett e-postmeddelande | Flagga det här e-postmeddelandet <br> Lägg till en flagga i det här e-postmeddelandet.
AddMore | Lägg till mer i ett e-post eller text, som en del av en stegvis e-post eller SMS-sammansättning. | Lägg till mer i text.  <br>   Lägg till mer i e-postmeddelandet.
Svar | Svara på ett inkommande telefonsamtal. | Besvara anropet.  <br>   Välj det.
AssignContactNickname | Tilldela ett smeknamn till en kontakt. | Ändra Isak till pappa.  <br>   Redigera Jims smek namn. <br>   Lägg till smek namn i Patti Owens.
Motringningsalternativ | Returnera ett telefonsamtal. | Ring upp igen.
CallVoiceMail | Anslut till användarens röstbrevlåda. | Anslut mig till min röst ruta. <br>Röst meddelande tjänst. <br>   Ring röst samtal.
Avbryt | Avbryt en åtgärd. | Avbryt. <br>   Avsluta.
CheckIMStatus | Kontrol lera status för en kontakt i IM. | Jims onlinestatus anges till direkt? 
CheckMessages | Sök efter nya meddelanden eller e-postmeddelanden. | Kontrol lera Inkorgen <br>  Har jag någon ny e-post?
Bekräfta | Bekräfta en åtgärd. | Ja, skicka det. <br> Rättighet, jag bekräftar att jag vill skicka det här e-postmeddelandet.
EndCall | Avsluta ett telefonsamtal. | Lägg på samtalet. <br> Slut.
FindContact | Hitta kontaktuppgifter efter namn. | Hitta MUM-numret. <br>   Visa telefonnummer till mig Carol.
FinishTask | Slutför den aktuella aktiviteten. | Jag är färdig <br> Det är allt.
TurnForwardingOff | Inaktivera vidarekoppling. | Stoppa vidarebefordran av mina samtal. <br> Inaktivera vidarekoppling av samtal.
TurnForwardingOn | Aktivera vidarebefordran av samtal. | Vidarebefordran av min anrop till 3333 <br>  Växla vid vidarekoppling till 3333.
GetForwardingsStatus | Hämta den aktuella statusen för vidarekoppling. | Är Mina vidarekoppling aktiverad? <br>   Berätta om min samtals status är på eller av.
GetNotifications | Hämta meddelanden. | Öppna mina meddelanden <br>   kan du kontrol lera telefon Facebook-meddelanden
GoBack | Gå tillbaka till föregående steg. | Gå tillbaka till twitter <br>   Gå tillbaka ett steg <br>   Gå tillbaka
Ignorera | Ignorera ett inkommande samtal. | Inte svara <br>   Ignorera anrop
IgnoreWithMessage | Ignorera ett inkommande samtal och svara med text i stället. | Inte besvara anropet men skicka ett meddelande i stället. <br>   Ignorera och skicka ett SMS igen.
Uppringning | Ringa ett telefonsamtal. | Anropa Jim <br>   Ring upp 311.
FindSpeedDial | Hitta den speedial nummer ett telefonnummer anges till och vice versa. | Vad är mitt telefonnummer 5? <br>   Måste jag hastighet reglera set? <br>   Vad är telefonnummer för 941-5555-333?
Vidarebefordra | Vidarebefordra ett e-postmeddelande | Vidarebefordra det här e-postmeddelandet till Greg.
ReadAloud | Läsa ett meddelande eller e-post till användaren. | Läs text. <br>   Vad hon säga i meddelandet?
PressKey | Tryck på en knapp eller en siffra på tangentbordet. | Uppringning star. <br>   Tryck på 1 2-3.
QueryLastText | Fråga efter den sista texten eller meddelandet. | Vem har loggat mig? <br>   Vem har nyligen meddelandet mig?
Återuppringning | Ringa upp igen eller anropa en siffra igen. | Ring upp igen. <br>   Ring upp igen min senaste anropet.
Avvisa | Avvisa ett inkommande samtal. | Avvisa samtal <br>   Det går inte att besvara nu <br>   Inte tillgänglig just nu och anropar tillbaka senare.
Svara | Svara på ett meddelande. | svara på Lorem Hound <br>   svara genom att skriva på mitt sätt
SearchMessages | Sök i meddelandena efter vissa omständigheter. | Kan du söka i mina e-postmeddelanden som skickats av Jane?
SendEmail | Skicka ett e-postmeddelande. Den här gäller för e-post men inte textmeddelanden. | E-posta till Mike-vatten: Mike, att middag förra veckan var Splendid. <br>   Skicka ett e-postmeddelande till Bob.
SendMessage | Skicka ett SMS eller ett snabbmeddelande. | Skicka SMS till Chris och Carol <br>   Facebook-meddelande Greg <br>   
SetSpeedDial | Ange en hastighet dial genväg för en telefonnumret. | Ange kortnummer en för Carol. <br>   Ställ in kortnummer för mom.
ShowCurrentNotification | Visa aktuella meddelanden. | Finns det några nya meddelanden? <br> Visa ett meddelande.
ShowNext | Se nästa objekt, till exempel i en lista över textmeddelanden eller e-postmeddelanden. | Visa nästa. <br>   Gå till nästa sida.
ShowPrevious | Se föregående objekt, till exempel i en lista över textmeddelanden eller e-postmeddelanden. | Visa föregående. <br>   Ursprungliga. <br>   Gå till föregående.
TurnSpeakerOff | Inaktivera talare telefonen. | Ta mig av talare. <br>   Inaktivera högtalartelefon.
TurnSpeakerOn | Aktivera talare telefonen. | Högtalartelefon läge. <br>   Placera högtalartelefon på.

### <a name="entities"></a>**Poster**
Entitetsnamn | Entitetstyp | Beskrivning | Exempel | Platser
------|-------|----------|--------------|---------------
Attachment | gång | Bilagan som användaren vill skicka med text eller e-post. | Skicka en **fil** via e-post från OneNote. <br> Skicka mitt underhåll- **dokument** till Katie. | file <br> inköpsdok
AudioDeviceType | gång | Typ av ljuden het (talare, headset, mikrofon osv.). | Svar med hjälp av **praktiska**. <br> Ring upp på **högtalar telefon**. | högtalar <br> hands fria <br> Seriell
Category | gång | Kategorin för ett meddelande eller e-postmeddelande. kategorin måste ha en tydlig definition i e-postsystemet, t. ex. "oläst", "flagga". Beskrivning med Rensa definition, till exempel "ny" och "senaste", är inte kategorier. | Markera alla e-postmeddelanden som **lästa**  <br> Nytt e-postmeddelande med **hög prioritet** för Paul | viktigt <br> hög prioritet <br> läsa
ContactAttribute | gång | Ett attribut för att kontakta användaren med frågor om.| Alla **födelsedagar** nästa månad jag bör känna till? | födelsedagar <br> Adress <br> Telefonnummer
Kontaktnamn | personName  | Namnet på en kontakt eller ett meddelande mottagare. | Skicka e-postmeddelandet till **Stevens** | Stevens
Date/Time | datetime | Datetime för ett e-postmeddelande som mottagits. | Läs **dagens**e-post <br> Vem har e-posta mig **idag**? <br> Vem har ringt på **7**? | i dag <br> imorgon
DestinationPhone | gång | Mål användaren vill anropa eller skicka en text till. | Ring till **hus** <br> Skicka ett textmeddelande till **Start sidan** | egen <br> hem
EmailAddress | email | E-postadressen som användaren vill skicka eller fråga. | Skicka e-post tillMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | enkelt, mönster. Helst | Den text som används som ämnesraden för ett e-postmeddelande. | Skriv e-post till David med ämnet **Hej**  | RE: intressant berättelse
Nyckel | gång | Den nyckel användare vill trycka på. | Tryck på **blank steg** -tangenten. <br> Tryck på **9** | pund <br> udd <br> 8
Rad | gång | Linje användaren vill använda för att skicka ett e-postmeddelande eller en text från. | Läs min senaste **Hotmail** -e-post. <br> Ring Peter per **mobil**. <br> Ring pappa med min **arbets** linje.| Inkorgen <br> Skype <br> British cell
SenderName | personName | Namnet på avsändaren. | Läs e-postmeddelandet från **David** <br> E-postmeddelanden från Chanda | David <br> Chanda
FromRelationshipName | gång | Avsändarens Relations namn. | Läs meddelande från **pappa**. <br> Kan du läsa alla textmeddelanden från **MOM**? | Far <br> Modulen 
Message | enkelt, mönster. Helst |  Meddelandet som ska skickas som ett e-post eller SMS.  | Skicka e-post som säger "**Jag är upptagen**". | Jag är upptagen
OrderReference | gång | Ordningstalet eller relativ position i en lista, identifiera ett objekt som ska hämtas. | Vad var det **senaste** meddelandet jag skickade? <br> Läs **senaste** Nokia-e-post. <br> Läs **nya** textmeddelanden. | pågå <br> latest <br> öppnade <br> Nyaste
PositionReference | enkelt, ordnings tal | Ordningstalet eller relativ position i en lista, identifiera ett objekt som ska hämtas.| Vad var det **första** meddelandet jag skickade? <br> Den **tredje** .| Första <br> tredjeparts
phoneNumber | phoneNumber | Telefonnumret som användaren vill anropa eller skicka en text till. | Skicka en text till **4 1 5 6 8 4 5 2 8 4** | 3525214446
RelationshipName | gång | Relations namnet för en kontakt eller meddelande mottagare. | Skicka e-post till min **fru** | maka
SearchTexts | enkelt, mönster. alla | De texter som används för att filtrera e-postmeddelanden eller meddelanden | Sök i alla e-postmeddelanden som innehåller "**Surface Pro**" | Surface Pro
SpeedDial | gång | Kort numret. | Ring **3 4 5**. <br> Ange **kort nummer för**kort nummer. | 345 <br> 5

## <a name="email"></a>**E-post**
E-post är en under domän till *kommunikations* domänen. Det innehåller främst förfrågningar om att skicka och ta emot meddelanden via e-post.
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
AddMore | Lägg till mer i ett e-post eller text, som en del av en stegvis e-post eller SMS-sammansättning. | Lägg till mer i e-postmeddelandet.
Avbryt | Avbryt en åtgärd. <br> ***Meddelande**: Mer information om hur du avbryter allmän yttranden finns i avsnittet om **verktyg** . | Avbryt. Skicka det inte. <br> Avsluta.
CheckMessages | Sök efter nya meddelanden/e-postmeddelanden utan villkorlig förfrågan. Om det finns något villkor hör yttranden till *SearchMessage* -avsikten. | Kontrol lera Inkorgen. <br> Har jag några nya e-postmeddelanden? 
Bekräfta | Bekräfta en pågående åtgärd som rör bearbetning av e-post. <br> ***Meddelande**: Läs mer i avsnittet om **verktyg** för att bekräfta allmän yttranden. * | Ja, skicka det. <br> Jag bekräftar att jag vill skicka den här e-post.
Ta bort | Ta bort ett e-postmeddelande eller e-postmeddelandena som filtrerats efter vissa villkor. | Sätt e-postmeddelandet i pappers korgen <br> Töm min inkorg. <br> Ta bort Marys e-post.
ReadAloud | Läsa ett meddelande eller e-post till användaren. <br> ***Meddelande**: Se **verktyg** -domänen för mer ReadAloud General yttranden. *  | Läs e-postmeddelandet som skickas av Mary.
Svara | Svara på ett meddelande till det aktuella e-postmeddelandet. | Skapa ett svar på e-postmeddelandet. <br> Svara genom att skriva "tack mycket mycket, det bästa, John".
SearchMessages | Sök i meddelandena efter vissa omständigheter, inklusive avsändarens namn, tid och ämne. | Visa meddelanden från Nisheen. <br> Kan du söka i mina e-postmeddelanden med rubriken "ABC"?
SendEmail | Skicka ett e-postmeddelande. | E-posta till Mike: Mike, att middag förra veckan var Splendid. <br> Skicka ett e-postmeddelande till Bob.
ShowNext | Se nästa objekt i en lista med SMS eller e-postmeddelanden. <br> ***Meddelande**: Se **verktyg** -domänen för mer ShowNext General yttranden. * | Visa nästa. <br> Gå till nästa sida.
ShowPrevious | Visa föregående objekt i en lista med SMS eller e-postmeddelanden. <br> ***Meddelande**: Se **verktyg** -domänen för mer ShowPrevious General yttranden. * | Visa föregående. <br> Ursprungliga. <br> Gå till föregående.
Vidarebefordra | Vidarebefordra ett e-postmeddelande. | Vidarebefordra det här e-postmeddelandet till Greg.
AddFlag | Lägg till flagga i ett e-postmeddelande. | Flagga det här e-postmeddelandet <br> Lägg till en flagga i det här e-postmeddelandet.
QueryLastText | Fråga efter senaste e-post. | Vem skickade e-post till mig? <br> Vem har nyligen e-posta mig?


### <a name="entities"></a>**Poster**
Entitetsnamn | Entitetstyp | Beskrivning | Exempel | Platser
------|-------|----------|--------------|---------------
Attachment | gång | Bilagan som användaren vill skicka med text eller e-post. | Skicka en **fil** via e-post från OneNote. <br> Skicka mitt underhåll- **dokument** till Katie. | file <br> inköpsdok
Kontaktnamn | personName  | Namnet på en kontakt eller ett meddelande mottagare. | Skicka e-postmeddelandet till **Stevens** | Stevens
Date | datetime | Datum för det mottagna e-postmeddelandet. | Läs **dagens**e-post <br> Vem har e-posta mig **idag**? | i dag
EmailAddress | email | E-postadressen som användaren vill skicka eller fråga. | Skicka e-post tillMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | enkelt, mönster. Helst | Den text som används som ämnesraden för ett e-postmeddelande. | Skriv e-post till David med ämnet **Hej**  | RE: intressant berättelse
SenderName | personName | Namnet på avsändaren. | Läs e-postmeddelandet från **David** <br> E-postmeddelanden från Chanda | David <br> Chanda
FromRelationshipName | gång | Avsändarens Relations namn. | Läs meddelande från **pappa**. <br> Kan du läsa alla textmeddelanden från **MOM**? | Far <br> Modulen 
Message | enkelt, mönster. Helst |  Meddelandet som ska skickas som ett e-post eller SMS.  | Skicka e-post som säger "**Jag är upptagen**". | Jag är upptagen
Category | gång | Kategorin för ett meddelande eller e-postmeddelande. kategorin måste ha en tydlig definition i e-postsystemet, t. ex. "oläst", "flagga". Beskrivning med Rensa definition, till exempel "ny" och "senaste", är inte kategorier. | Markera alla e-postmeddelanden som **lästa**  <br> Nytt e-postmeddelande med **hög prioritet** för Paul | viktigt <br> hög prioritet <br> läsa
OrderReference | gång | Ordningstalet eller relativ position i en lista, identifiera ett objekt som ska hämtas. | Vad var det **senaste** meddelandet jag skickade? <br> Läs **senaste** Nokia-e-post. <br> Läs **nya** textmeddelanden. | pågå <br> latest <br> öppnade <br> Nyaste
PositionReference | enkelt, ordnings tal | Ordningstalet eller relativ position i en lista, identifiera ett objekt som ska hämtas.| Vad var det **första** meddelandet jag skickade? <br> Den **tredje** .| Första <br> tredjeparts
RelationshipName | gång | Relations namnet för en kontakt eller meddelande mottagare. | Skicka e-post till min **fru** | maka
Time | datetime | Time | Skicka e- **ikväll**. | ikväll
SearchTexts | enkelt, mönster. alla | De texter som används för att filtrera e-postmeddelanden eller meddelanden | Sök i alla e-postmeddelanden som innehåller "**Surface Pro**" | Surface Pro 
Rad | gång | Linje användaren vill använda för att skicka ett e-postmeddelande från. | Läs min senaste **Hotmail** -e-post. <br> Skicka ett e-postmeddelande från mitt **arbets konto**.| Inkorgen <br> arbets konto 

## <a name="homeautomation"></a>**HomeAutomation**
HomeAutomation-domänen innehåller avsikter och entiteter som är relaterade till att kontrol lera smarta hem enheter. Vi stöder huvudsakligen kontroll kommandot som rör lampor och luft konditionering. Men den har vissa generalize-förmågor på andra elektriska apparater.
### <a name="supported-devices-and-properties"></a>**Enheter och egenskaper som stöds**
Enhet | properties
-------|---------
Temperatur sensor | Temperatur
Ljus, lampa | På av, ljus styrka, färg
TV, radio | Av, volym
Luft konditionering (A/C), termostat | På av, temperatur, kraft
Drivning | På av, energi
Uttag, DVD-spelare, Ice Maker osv. | Av

### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
 Avstängning | Användaren vill inaktivera enhet eller inställningar.  | Stäng av indikeringarna. <br> Stäng av något. <br> Något av.
 Aktivera | Aktivera en enhet eller aktivera och ange enheten till en viss inställning eller läge. | Sätt på ljuset till 50%. <br> Aktivera mitt kaffe Maker <br> Kan du aktivera mina kaffe Maker?
 SetDevice | Användaren vill ställa in enheten till en viss inställning eller typ.  | Ställ in luft konditioneringen på 26 grader. <br> Vänd ljuset blå. <br> Anropa det här ljuset som NightLight.
 QueryState | Användaren ber om status för en enhet eller inställning.  | Vad är termostat inställt på? <br> Är A/C på? <br> Vad är sovrums temperatur?
 TurnUp | Aktivera inställningar eller ljus styrka för enheter. | Ljusare ljuset med 75 procent. <br> Öka ljus styrkan här med 10 procent.  <br> Gör det varmt i vardags rummet.
 TurnDown | Att stänga av men inte av en enhet, antingen genom att dimma, temperaturen eller ljus styrkan hos lamporna. | Stäng av biblioteket med 44%. <br> Tona ljuset. <br> Gör rummets kylare.
### <a name="entities"></a>**Poster**
Entitetsnamn | Entitetstyp | Beskrivning | Exempel
-------|----------|--------------|---------------
Enhetsnamn | List | Användardefinierad text för sina enheter. | Blå<br> Fira <br> Reception
DeviceType | List | Enheter som stöds. | Tänt <br> Luft konditionering <br> nightlight
Location | gång | Plats eller rum där enheten finns. | Artiklar<br> Downstairs <br> Sovrum
NumericalChange | gång | Värdet som en inställning höjs eller minskas med. <br> <br> _Platsen visas bara med turn_up-och turn_down-avsikter._ | 3<br> 50%<br>
OrderReference | numret | Syftet med det här facket är att avbilda urvalet av objekten. Det anger objektets position i en lista. | Första<br> Andra
Kvantifieraren | List | Kvantifieraren anger hur många instanser av en viss entitet som refereras till. Till exempel "alla", "alla" osv. | Alla<br> Var<br> Allt
Inställning | Enkel | Inställningen som användaren vill ställa in enheten på, bland annat scen, nivå, intensitet, färg, läge, temperatur, status för enheten. | Blå<br> 72 <br> Termisk 
SettingType | List | Enhets inställningen som användaren är intresse rad av. | Temperatur<br> 
Tid/datum |  datetime | Tid och varaktighet för att driva en enhet| 5 minuter <br> 14:00
Enhet | List | För att tagga ord som "grader", "procent", "Fahrenheit", "Celsius" måste varje enhets period taggas separat. | Rotation<br> Percent


## <a name="notes"></a>**Anteckningar**
Observera domän underlättar skapandet av anteckningar och skrivning av objekt för användare.
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
AddToNote | Lägg till information till en öppen anteckning. | Lägg till i min planerings anteckning för att kontakta min chef om projektet.
Rensa | Ta bort alla objekt från en befintlig anteckning. | Ta bort alla objekt från min semester anteckning. <br>Rensa alla från min Läs anteckning.
Bekräfta | Bekräfta en åtgärd som är relaterad till en anteckning. <br> ***Meddelande**: I detta syfte ingår huvudsakligen åtgärden "bekräfta" för antecknings scenario. Om du behöver fler allmänna uttryck på "bekräfta" kan du använda alternativet "bekräfta" i **verktyg** -domänen. * | Det är dags för den här anteckningen. <br>Jag bekräftar att alla objekt ska behållas på listor.
Skapa | Skapa en ny anteckning. | Skapa en anteckning. <br>Observera att du får en påminnelse om att Jason är i staden första veckan på maj. 
Ta bort | Ta bort en hel anteckning. | Ta bort min semester anteckning. <br>Ta bort mina inköps kommentarer.
ReadAloud | Läs en anteckning. | Läs mig den första anteckningen. <br>Läs mer i informationen.
Stäng | Stäng den aktuella anteckningen. | Stäng anteckningen. <br>Stäng den aktuella anteckningen.
Öppet | Öppna en redan befintlig anteckning. | Öppna min anslags anteckning. <br>Öppna anteckningen "planering".
RemoveSentence | Ta bort en mening för en anteckning. | Ta bort den sista meningen. <br>Ta bort chips från min inköps anteckning. <br>Ta bort pennor från min skola-anteckning
ChangeTitle | Ändra anteckningens rubrik. | Kallas för "planering".

### <a name="entities"></a>**Poster**
Entitetsnamn | Entitetstyp | Beskrivning | Exempel 
------- | ------- | ------- | -------
Text | enkelt, mönster. Helst | Texten för en kommentar eller påminnelse. | Sträck ut innan walking <br> lång sikt imorgon
Titel | enkelt, mönster. Helst | Rubrik för en anteckning. | mat <br> personer att anropa <br> att göra
CreationDate | datetimeV2 | Det här facket är för när användaren ber om anteckningar som skapats inom ett visst datum/tid-fönster. | 
Kvantifieraren | List | När en användare ber om att utföra en åtgärd för ' alla ', ' varje ' eller ' any ' objekt eller all text i en anteckning. | all <br> Helst <br> återskapning
OrderReference | numret | Användaren vill utföra åtgärder med "First", "föregående", "Next" osv. | förstagångskörningen <br> pågå


## <a name="places"></a>**Platser**
Platser innefattar företag, institutioner, restauranger, offentliga utrymmen och adresser. Domänen har stöd för att hitta och ställa frågor mot informationen på ett offentligt ställe, till exempel plats, drift timmar och avstånd. 
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
MakeCall | Ringa ett telefonsamtal till en plats. | Applebees på 1000/200 Rojas St och Call.
FindPlace | Sök efter en plats (business, institution, restaurang, offentligt adressutrymme, adress). Men inte: <li> Endast företags namn: Starbucks <li> Endast plats namn: Seattle/Norge <li> Cuisine, livsmedel eller endast produkt: Pizza/guacamole/italienska | Var finns det närmaste biblioteket? <br> Starbucks i Seattle. <br> Var finns det närmaste biblioteket? <br> 
GetAddress | Be om adressen till en plats. | Visa adressen till Guu på Robson gatan. <br> Vad är adressen till den närmaste Starbucks?
GetDistance | Fråga om avståndet från den aktuella platsen till en angiven plats. | Hur långt bort är helgdag Inn?<br> Hur långt är det att Bellevue rutan? <br> Vad är avståndet till Tahoe?
GetPhoneNumber | Be om telefonnumret till en plats. | Vad är telefonnumret till den närmaste Starbucks?<br> Ange numret för hem Depå. <br> Ett telefonnummer för Disneyland.
GetPriceRange | Fråga efter förbruknings intervallet per capita för en plats. | Genomsnittligt pris för J. sushi i Seattle. <br> Är Cineplex halva priset på onsdagar? <br> Hur mycket kostar en hela lobster middag på Sizzler?
GetStarRating | Be om stjärnor för en plats. | Hur klassificeras Zucca?<br> Hur många stjärnor har franska tvätt?<br> Är aquarium i Monterrey bra?
GetHours | Fråga om drifttimmar en plats. | Vid vilken tidpunkt Safeway Stäng?<br> Vilka är timmarna för start Depot?<br> Starbucks fortfarande är öppen?
GetReviews | Be om granskningar av en plats. | Visa recensioner för cheesecake Factory. <br> Läs Cineplex-granskningar. <br> Finns det några nya granskningar för Humperdinks?
GetMenu | Be om menyalternativ för en restaurang. | Hanterar Zucca några något vegan? <br> Vad finns på menyn på Sizzler? <br> Visa Applebee-menyn.
RatePlace | Betygs ätt en plats. | 4 stjärn betyg för Maxi-pizza i Kimberly. <br> Ge 4-stjärnor till Bonefish på TripAdvisor. <br> Skapa en 4-stjärn granskning för La Hacienda.
AddFavoritePlace | Användaren vill lägga till en plats i sina favoriter eller MVP-listor. | Spara den här platsen i mina favoriter. <br> Lägg till bästa köp i mina favoriter.

### <a name="entities"></a>**Poster**
LUIS-enheter | Entitetstyp | Beskrivning | Exempel | Uttryck-exempel
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | gång | Platsen eller adress för en plats. | Palo Alto <br> 300 112th para SE <br> Seattle | **1020 Middlefield Rd.** i **Palo-** <br> Dirigerings lager för fågel utrymmen i **Seattle** <br> Hämta Avståndet härifrån till **300 112Th Ara se**.
Bekvämligheterna | gång | Egenskaperna och fördelarna med en offentlig plats. | Hamnområde. <br> kostnadsfria på parkeringssida | Kirkland **Waterfront** skaldjur-restauranger. <br> Finns det någon **kostnads fri parkering** i närheten?
Köket med alla dess | gång | En typ av mat, köket med alla dess eller köket med alla dess medborgarskap. | Kinesiska <br> Italienska <br> Sushi <br> Noodle <br> | Hjälp mig att hitta en **kinesisk** restaurang. <br> Vilka är öppet tiderna för **sushi** -restaurangen? <br> Var är närmaste **Steak** hus?
Date | datetime | datetime eller varaktighet för datum för den aktuella platsen. | imorgon <br> i dag <br> 18.00 | Vilken tid stänger Aquarium **imorgon**? <br> den närmaste cykel Shop som är öppen efter **0,6**
Avstånd | dimension | Avståndet till en offentlig plats från användarens currenct position. | 15 mil <br> 10 mil | ett lager för kläder inom **15 mil** <br> En barns restaurang som bara är **10 sjömil** borta
MealType | List | Typ av Måltid som bra eller lunch. | Bra <br> Middag | Sök **frukost** Greenwood Seattle <br> Hitta mig för min **lunch**.
Ande | List | Beskriv en närliggande plats utan absolut plats eller adress. | nära <br> i det här avsnittet <br> runt mig | Hitta **närmaste** indiska restaurang. <br> Var finns min **lokala** Wetherspoon? <br> Är du nöjd med **dina restauranger?**
OpenStatus | List | Anger om en plats är öppna eller stängda. | open (öppen) <br> Stängd | Vilken tid har yogurt mark **stängt** idag? <br> Vilka är **öppet** tider för Costco?
PlaceName | gång | Namnet på ett mål som är ett företag, restaurang, offentliga finns eller institution. Plats namnet kan innehålla en placetype om det används gemensamt. | Central Park <br> Safeway <br> Walmart| Vilken tid öppnar **Safeway** -apoteket? <br> Är **Walmart** öppet?
PlaceType | gång | Typ av ett mål som är en lokal företag, restaurang, offentliga finns eller institution. | Restaurang <br> Opera <br> biografer | **biografer** i Cambridge <br> Finns det en **restaurang** nära mig?
PriceRange | gång | Pris intervallet för produkter eller tjänster på platsen. | bostäder <br> kostnads effektiv <br> Mycket | Hitta en reparation av **pris värda** apparater <br> Vad är en **billig** pizza-plats som är öppen nu?
Produkt | gång | Produkten som erbjuds av en plats. | tvätt <br> apparater | Var är den bästa platsen för att hämta **livsmedel**? <br> Hitta en Kilbride som letar efter **TV-apparater**.
Klassificering | gång | Klassificering av en plats. | 5 stjärnor <br> översta <br> Bra | Finns det några **lämpliga** platser där jag kan gå ut och äta imorgon <br> **bästa** Amsterdam restauranger <br> Lista **de** tre pizza-butikerna.


## <a name="restaurantreservation"></a>**RestaurantReservation**
Restaurang reservations domänen stöder avsikt att hantera reservationer för restauranger.
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
Reservera | Begär en reservation för en restaurang. | Reservera på zucca för två för ikväll. <br> Boka en tabell i morgon. <br> Tabell för 3 i Palo-på 7. <br> Gör en reservation för 3 vid röd kräfta.
DeleteReservation | Avbryta eller ta bort en reservation för en restaurang. | Avbryt reservationen vid zucca imorgon på natten. <br> Glöm inte min reservation för den röda kräfta vid 7:00 nästa fredag. <br> Jag behöver inte reservationen för zucca, avbryta den.
ChangeReservation | Ändra tid, plats eller antal personer för en befintlig restaurang reservation. | Ändra min reservation till 9 PM <br> Ändra min reservation från zucca till röd kräfta. <br> 7 personer i stället för 6 för reservationen på zucca.
Bekräfta | Bekräfta en åtgärd som rör reservationen. <br> ***Meddelande**: I detta syfte ingår huvudsakligen åtgärden "bekräfta" för restaurang reservations scenariot. Om du behöver fler allmänna uttryck på "bekräfta" kan du använda alternativet "bekräfta" i **verktyg** -domänen. * | Ja, jag har gjort reservationerna för ikväll vid 8 kl. <br> Ja, bara att boka det. <br> Bekräfta reservationen på sushi bar.
FindReservationDetail | Visa detaljerad information om en befintlig reservation. | Hitta min reservation på Renaissance San-Diego <br> Visa reservationen imorgon. <br> Visa information om min reservation för zucca.
FindReservationWhere | Kontrol lera den absoluta platsen för reservationen. | Var finns platsen för zucca i min reservation? <br> Visa nationella inställningar för min reservation imorgon.
FindReservationWhen | Kontrol lera den exakta tiden för reservationen | När är reservationen av zucca i morgon? <br> Tiden för min reservation vid röd kräfta.
Avvisa | Användaren avvisar vilka virtuella assistenter som föreslagits för att hantera en reservation. <br> ***Observera**:P-lån se **till att det** är en domän för att avvisa allmänna yttranden. * | Du behöver inte ange händelsen. | Nej, jag vill inte ändra reservationen. <br> Nej, boka inte, jag gjorde ett misstag.

### <a name="entities"></a>**Poster**
LUIS-entitet | Entitetstyp | Beskrivning | Exempel
-------|------|---------|-------------------
Adress | gång | En händelse plats eller en adress för en reservation. | Palo Alto<br>300 112th para SE<br>Seattle
Atmosfär | List | En beskrivning av en restaurangs atmosfär. | Romantiska<br>avslappnat<br>bra för grupper<br>Trevligt
Köket med alla dess | gång | En typ av mat, köket med alla dess eller köket med alla dess medborgarskap. | Kinesiska<br>Italienska<br>Mexikanska<br>Sushi<br>Noodle<br>steak
MealType | List | En måltid typ som är associerade med en reservation. | Bra<br>Middag<br>Lunch<br>Supper
PlaceName | gång | Namnet på en restaurang | Zucca<br>Tårtbitar Factory<br>röd kräfta
Klassificering | gång | Klassificering av en plats eller en restaurang. | 5 stjärnor<br>3 stjärnor<br>4-stjärnor
NumberPeople | gång | Antal personer för reservation | 3<br>sex
Time | datetime| Tiden för restaurang reservation | imorgon<br>ikväll<br>7:00 p.m.<br>Jul afton


## <a name="todo"></a>**Att göra**
Att göra _-domän tillhandahåller_ typer av uppgifts listor för användare att lägga till, markera och ta bort deras att göra-objekt.
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
AddToDo | Användaren vill lägga till uppgifts objekt för någon av list typerna. |  Påminn mig att köpa mjölk. <br> Lägg till ett objekt med namnet "Köp mjölk" i min att göra-lista
Bekräfta | Användaren vill bekräfta en pågående åtgärd. Uttryck innehåller en explicit signal som "Ja" för att bekräfta en åtgärd. <br> <br> ***Meddelande**: Syftet med detta är huvudsakligen att "bekräfta"-åtgärden för att göra-scenariot. Om du behöver fler allmänna uttryck på "bekräfta" kan du använda alternativet "bekräfta" i **verktyg** -domänen. * | Ta bort uppgiften. <br> Jag är säker på att jag vill lägga till den här uppgiften. <br> Ja, jag vill göra det.
Avbryt | Användaren vill avbryta pågående åtgärd.  <br> <br> ***Meddelande**: I detta syfte ingår huvudsakligen åtgärden "bekräfta" för restaurang reservations scenariot. Om du behöver fler allmänna uttryck på "bekräfta" kan du använda alternativet "bekräfta" i **verktyg** -domänen. * | Nej, glöm inte att det. <br> du behöver bara avbryta uppgiften. <br> Nej, Lägg inte till den.
DeleteToDo | Ta bort ett objekt i uppgifts listan genom att referera till dess ordning eller ta bort alla att göra-objekt. | Ta bort alla uppgifter. <br> Hjälp mig att ta bort den andra.
MarkToDo | Markera en aktivitet som slutförd eller utförd genom att referera till dess order-eller uppgifts innehåll. | Markera uppgiften "Köp mjölk" som färdig. <br> Slutför uppgiftens läsning. <br> Slutför alla.
ShowNextPage | En lista delas upp på flera sidor som visas. Visa List objekt på nästa sida för användaren. | Kan du Visa nästa sida i shopping listan? <br> Vad händer härnäst? <br> Nästa steg
ShowPreviousPage | Visa List objekt på föregående sida för användaren. | Visa föregående. <br> Jag måste kontrol lera tidigare aktiviteter.
ShowToDo | Visa alla objekt i att göra-listan. | Visa min shopping-lista. <br> Visa min inköps lista.

### <a name="entities"></a>**Poster**
LUIS-entitet | Entitetstyp | Beskrivning | Exempel
-------|------|---------|-------------------
ContainsAll | gång | Representera alla eller alla objekt i uppgifts listan | kan du hjälpa till att ta bort **alla** uppgifter. <br> Slutför **allt**.
numret | numret | Ett ordnings tal eller en numerisk referens till ett objekt. | Markera den **tredje** som slutförd. <br> Ta bort den **första** aktiviteten.
ListType | gång | Typ av uppgifts lista.  | Lägg till skor i min **shopping** lista.
FoodOfGrocery | List | Identifiera en lista över livsmedels objekt | Påminn mig att köpa **mjölk**. <br> Lägg till **nötkött** till min inköps lista.
TaskContent | enkelt, mönster. alla | Identifiera innehållet i en uppgift. | Påminn mig om att **kalla min mamma** . <br> Lägg till **hylla John födelsedag** i min att göra-lista


## <a name="utilities"></a>**Verktyg**
\- _Domänen är en allmän domän bland_ alla _Luis_ -fördefinierade modeller som innehåller gemensamma avsikter och yttranden i skillnads scenarier.
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
 Avbryt | Avbryt en åtgärd/begäran/uppgift/tjänst. | Avbryt det. <br> Kom aldrig ihåg att glömma bort det.
 Bekräfta | Bekräfta en åtgärd. | Visst <br> Ja, Använd <br> Confirm.
 Avvisa | Användaren avvisar vilken virtuell assistent som föreslagits. | Nej
 FinishTask | Slut en aktivitet som användaren har börjat. | Jag är färdig. <br> Det är allt. <br> Behandlar.
 GoBack | Gå tillbaka till föregående steg eller gå tillbaka till föregående steg. | Gå tillbaka ett steg. <br> Gå tillbaka.
 Hjälp | Begäran om hjälp. | Hjälp. <br> Öppna hjälpen.
 Upprepa | Upprepa en åtgärd. | Säg det igen.
 ShowNext | Visa eller berätta nästa objekt. | Visa nästa.
 ShowPrevious | Visa föregående objekt i en serie. | Visa föregående ett.
 StartOver | Starta om appen eller starta en ny session. | Börja.
 Stoppa | Berätta för den virtuella assistenten att sluta prata.  | Sluta att säga detta.
 SelectAny | Användaren uppmanas att välja ett objekt eller resultat som visas på skärmen. | Vilken som helst. <br> Välj en.
 SelectNone | Användaren väljer inget av de befintliga objekten och frågar efter fler alternativ. | Ge mig andra förslag. <br> Ingen av dem.
  SelectItem | Användaren uppmanas att välja ett objekt eller ett resultat som visas på skärmen. | Välj den tredje. <br> Välj det övre högra hörnet.
 Eskalera | Be om en kund tjänst att hantera problemen. | Kan jag prata med en person?
 ReadAloud | Läs något högt för användaren. | Läs den här sidan. <br> Läs det högt.

### <a name="entities"></a>**Poster**
LUIS-entitet | Entitetstyp | Beskrivning | Exempel
------------|-------------|-------------|---------
numret | numret | Ett ordnings tal eller en numerisk referens till ett objekt. | Den **andra** . <br> **Nästa** .
nummer | nummer | Antal objekt som användaren vill ha | De följande **3** objekten
DirectionalReference | gång | En referens punkt för var på skärmen ett objekt finns. | Höger en <br> Produktion

## <a name="weather"></a>**Baserad**
Väder domänen fokuserar på att kontrol lera väder förhållanden och rekommendationer med plats och tid eller kontroll tid efter väder förhållanden.
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
 CheckWeatherValue | Be om väder-eller väderbaserade faktorer för en plats på prognos eller tidigare information. <br> Väder relaterade faktorer är: <li> temperatur </li> <li> regn/snö/nederbörd </li> <li> torr/våt/fukt </li> <li> strålkastar </li> <li> UV-index </li> <li> tum regn/snö </li> | Vad är luft kvalitets index i Beijing? <br> Hur mycket regn förväntas i Seattle i mars? <br> Registrera högsta temperatur på Hawaii.
 CheckWeatherTime | Fråga efter tiden för prognostiserade eller historiska väder-relaterade faktorer för en plats. | När förväntas det vara regn? <br> En lämplig tid att gå till Kanada <br> När är den hetaste månaden i Minnesota?
 QueryWeather | Fråga om väder förhållanden eller väder relaterade faktorer för en speciell plats för vilken svaret "Ja, no eller Response" förväntas eller be om aktivitets aviseringar som är beroende av väder villkoret. | Blir det regn imorgon? <br> Är IT-solig idag? <br> Går det för tidigt att gå till Alaska?
 ChangeTemperatureUnit | Ändra enheten för väder, inklusive Celsius, Kelvin och Fahrenheit. | Omvandla i Celsius. <br> Kan jag få det i Kelvin?
 GetWeatherAdvisory | Få väder rekommendationer eller aviseringar från en bestämd plats. | Finns det väder rekommendationer i Memphis? <br> Finns det några väder varningar för mitt områden?

### <a name="entities"></a>**Poster**
LUIS-entitet | Entitetstyp | Beskrivning | Exempel
------------|-------------|-------------|---------
Location | Placering | Den absoluta eller implicita platsen för en väder förfrågan. | Palo Alto<br>Shanghai<br>Seattle<br>Delvina<br>
Date/Time   | datetime | Datetime eller varaktighet för att skicka frågor till väderet. | November<br>per timme<br>morgon<br>Den här helgen<br>10 dagar<br>
AdditionalWeatherCondition | list | Ytterligare beskrivning Word för väder, till exempel hastigheten eller riktningen på vinden. | direction<br>Snabb<br>Hastighet
Historiska | gång | Beskrivning av ord för historisk väder status, inklusive Genomsnittligt antal guide Joomla-gränser under den senaste tids perioden. | sedan<br>historik/historik<br>säsongs<br>bästa tid<br>registreras någonsin
PrecipitationUnit | dimension | Fällningen för snö eller regn. | 5 tum<br>6 cm
SuitableFor | gång | Beskrivningen av en mänsklig aktivitet under ett väderleks tillstånd, som är vanligt när användarna frågar aktivitets råd som är beroende av väder villkoret. | mantel<br>avses<br>simmar
TemperatureUnit |temperatur | temperatur | 18 Celsius<br>7 Kelvin-grader
WeatherRange | gång | Det speciella villkoret för temperatur, vind och andra väder förhållanden mellan en viss tids period | max<br>Högt<br>låg<br>Genomsnittligt högt<br>Högsta
WeatherCondition | gång | Beskrivning av väder villkor | solig<br>lilla<br>regn<br>temperatur<br>snö<br>hot
WindDirectionUnit | list | Riktnings orden för vind | rakt<br>ifrån<br>östasiatisk<br>rubriken<br>nordöstra


## <a name="web"></a>**Webb**
Webb domänen ger avsikt och entiteter för att söka efter en webbplats.
### <a name="intents"></a>**Avsikter**
Namn på avsikt | Beskrivning | Exempel
---------|----------|---------
 Websearch | En begäran om att navigera till en angiven webbplats eller Sök i en sökmotor. | Sök yta i google.com. <br> Hitta Grattis på födelsedagen på webben <br> Gå till www.twitter.com.

### <a name="entities"></a>**Poster**
LUIS-entitet | Entitetstyp | Beskrivning | Exempel
------------|-------------|-------------|---------
SearchEngine | List | Den sökmotor användare vill använda. | Bing <br> Google
SearchText | enkelt, mönster. Helst | Text användaren vill söka. <br> _Tagga "vänner i Facebook" som SearchText om webbplatsen efter "i" inte är en sökmotor. URL: en ska också tagga som SearchText._ | Film <br> Deep learning <br> Tom kryssnings
Länka | url | Webbplats länken. | www.twitter.com

