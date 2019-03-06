---
title: Fördefinierad domänreferens
titleSuffix: Azure
description: Referens för de fördefinierade domäner som är färdiga samlingar av avsikter och entiteter från Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: e1e579233a5ad1af1ef8ee84019cd995959d3b2b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433781"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referens för fördefinierade för LUIS-appen
Den här referensen innehåller information om den [fördefinierade domäner](luis-how-to-use-prebuilt-domains.md), som är färdiga samlingar med avsikter och entiteter som LUIS erbjuder.

[Anpassade domäner](luis-how-to-start-new-app.md), däremot starta utan avsikter och modeller. Du kan lägga till några fördefinierade domän avsikter och entiteter till en anpassad modell.

## <a name="list-of-prebuilt-domains"></a>Lista över fördefinierade domäner
LUIS erbjuder 20 fördefinierade domäner. 

| Fördefinierade domän | Beskrivning | Språk som stöds |
| ---------------- |-----------------------|:------:|
| Kalender | Kalender-domänen innehåller avsikt och entiteter för att lägga till, tar bort, eller redigera en avtalad tid, kontrollerar tillgängligheten för deltagare och hitta information om en kalenderhändelse.| en-US<br/> zh-CN |
| Kamera | Kamera domänen tillhandahåller avsikter och entiteter för att ta bilder, spela in videor och broadcasting video till ett program.| en-US |
| Kommunikation | Skicka meddelanden och telefonsamtal.| en-US <br/> zh-CN |
| underhållning  | Hantering av frågor som rör musik, filmer och TV.| en-US |
| Händelser | Biljetter för konserter, festivaler, Sportspel och Komedi för bokning visas.| en-US |
| Lämplighet | Hantering av begäranden som rör spårning lämplighet aktiviteter.| en-US |
| Spel | Hantering av begäranden som rör en game part i spelet.| en-US |
| HomeAutomation | Styra smart home enheter som tangentsektionen och installationer.| en-US<br/> zh-CN |
| MovieTickets | Ärenden hos filmer på en film theater för bokning.| en-US |
| Musik | Spela musik på en musikspelare.| en-US<br/> zh-CN |
| Obs! | Obs domänen innehåller avsikter och entiteter relaterade till att skapa, redigera och söka efter information.| en-US<br/> zh-CN |
| OnDevice | OnDevice domänen innehåller avsikter och entiteter relaterade till att styra enheten.| en-US<br/> zh-CN |
| Platser  | Hantering av frågor som rör platser som företag, institutioner, restauranger, offentliga blanksteg och adresser.| en-US<br/> zh-CN |
| Påminnelse | Hantering av begäranden som rör skapa, redigera och söka efter påminnelser.| en-US<br/> zh-CN |
| RestaurantReservation | Hantering av begäranden för att hantera restaurang reservationer.| en-US<br/> zh-CN |
| Taxi | Hantering av bokningar för en taxi.| en-US<br/> zh-CN |
| Översätt | Översätter text till ett mål-språk.| en-US<br/> zh-CN |
| TV | Kontrollera TV-apparater.| en-US |
| Samhällsservice  | Hantering av begäranden som är vanliga i många domäner, t.ex. ”hjälp”, ”upprepa”, ”börja om”.| en-US |
| Väder | Få väderprognoser och prognoser.| en-US<br/> zh-CN |
| Webb | Navigera till en webbplats.| en-US<br/> zh-CN |

Mer information om varje domän finns i avsnitten som följer.

## <a name="calendar"></a>Kalender 

Kalender-domänen innehåller avsikter och entiteter relaterade till kalenderposter. Kalender-avsikter omfattar att lägga till, ta bort eller redigera en avtalad tid, kontrollerar tillgängligheten och att hitta information om en kalenderpost eller en avtalad tid.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Lägg till | Lägga till ett nytt enstaka objekt i kalendern.| Skapa en avtalad tid med Lisa klockan 2 på söndag <br/><br/>Jag vill schemalägga ett möte<br/><br/>Jag behöver du ställer in ett möte|
| CheckAvailability | Hitta tillgänglighet för en avtalad tid eller ett möte på användarens kalender eller en annan persons kalender.| När är tillgängliga för att möta Jim? <br/><br/>Visa när Carol är tillgänglig imorgon<br/><br/>Är Chris kostnadsfritt på lördag?|
| Ta bort | Begäran att ta bort en kalenderpost.| Avbryta min avtalad tid med Carol. <br/><br/>Ta bort mitt 9: 00-möte<br/>|
| Redigera | Begäran om för att ändra ett befintligt möte eller kalenderposten.| Flytta min 9: 00-möte till 10 am.<br/><br/>Jag vill uppdatera mitt schema.<br/><br/>Schemalägga om mötet med Ryan.|
| Hitta | Visa kalendern varje vecka.| Hitta tandläkaren granska avtalad tid. <br/><br/>Visa min kalender<br/>|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Plats | Platsen för kalenderobjekt, möte eller avtalad tid. Adresser, orter och regioner är bra exempel på platser.| 209 Nashville gymmet <br/><br/>897 pannkaka house<br/><br/>Garage|
| Subjekt | Rubriken på ett möte eller en avtalad tid.| tandläkartiden <br/><br/>Lunch med Julia<br/><br/>Parti-, avtalad tid|

## <a name="camera"></a>Kamera 
Kamera domänen innehåller avsikter och entiteter relaterade med användning av en kamera. Avsikter täcker ta ett foto, selfie, skärmbild eller video och sänder video till ett program.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| CapturePhoto| Avbilda ett foto.| Ta ett foto<br/><br/>samla in|
| CaptureScreenshot | Ta en skärmbild.| Ta skärmbild.<br/><br/>fånga skärm.|
| CaptureSelfie | Avbilda en selfie.| Ta en selfie <br/><br/>ta en bild av mig |
| CaptureVideo | Börja spela in video.| Starta inspelningen <br/><br/>Börja spela in|
| StartBroadcasting| Starta broadcasting video.| Starta sändning till Facebook|
| StopBroadcasting| Stoppa broadcasting video.| Stoppa broadcasting|
| StopVideoRecording| Sluta spela in en video.| Nu är<br/><br/>stoppa inspelningen|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Programnamn | Namnet på ett program att sända video till.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Kommunikation 
Kommunikation domänen innehåller avsikter och entiteter relaterade till e-post, meddelanden och telefonsamtal.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AddContact| Lägga till en ny kontakt i användarens lista över kontakter.|Lägg till ny kontakt <br/><br/>Spara det här talet och placera namnet som Carol|
| AddMore| Lägg till mer i ett e-post eller text, som en del av en stegvis e-post eller SMS-sammansättning.|Lägga till fler till text <br/><br/>Lägga till fler e-brödtext|
| Svar| Svara på ett inkommande telefonsamtal.|Besvara samtalet <br/><br/>Hämta det|
| AssignContactNickname| Tilldela ett smeknamn till en kontakt.|Ändra Isak till pappa <br/>Redigera Jims smeknamn<br/>Lägga till smeknamn Patti Owens|
| CallVoiceMail| Anslut till användarens röstbrevlåda.|Ansluta mig till min röstmeddelanden box <br/>Röstmeddelanden<br/>Anropa röstmeddelanden|
| CheckIMStatus| Kontrollera status för en kontakt i Skype.|Jims onlinestatus anges till direkt? <br/>Finns Carol chatta med?|
| Bekräfta| Bekräfta en åtgärd.|Ja<br/>Okej<br/>Ok<br/>Jag bekräftar att jag vill skicka den här e-post.<br/>|
| Uppringning| Ringa ett telefonsamtal.|Anropa Jim<br/>Ring 311.<br/>|
| FindContact| Hitta kontaktuppgifter efter namn.|Hitta Carols nummer<br/>Visa mig Carols tal<br/>|
| FindSpeedDial| Hitta hastighet telefonnummer ett telefonnummer anges till och vice versa.|Vad är mitt telefonnummer 5?<br/>Måste jag hastighet reglera set?<br/>Vad är telefonnummer för 941-5555-333?|
| GetForwardingsStatus| Hämta den aktuella statusen för vidarekoppling.|Är Mina vidarekoppling aktiverad?<br/>Berätta om min samtalsstatus har aktiverats eller inaktiverats<br/>|
| GoBack| Gå tillbaka till föregående steg.|Gå tillbaka till twitter<br/>Gå tillbaka ett steg<br/>Gå tillbaka|
| Ignorera| Ignorera ett inkommande samtal.|Inte svara<br/>Ignorera anrop|
| IgnoreWithMessage| Ignorera ett inkommande samtal och svara med text i stället.|Inte besvara anropet men skicka ett meddelande i stället.<br/>Ignorera och skicka ett SMS igen.|
| PressKey| Tryck på en knapp eller en siffra på tangentbordet.|Uppringning star.<br/>Tryck på 1 2-3.|
| ReadAloud| Läsa ett meddelande eller e-post till användaren.|Läs text.<br/>Vad hon säga i meddelandet?|
| TurnForwardingOff| Ringa ett telefonsamtal.|<br/><br/>|
| Återuppringning| Ringa upp igen eller anropa en siffra igen.|Ring upp igen.<br/>Ring upp igen min senaste anropet.|
| Avvisa| Avvisa ett inkommande samtal.|Avvisa samtal<br/>Det går inte att besvara nu<br/>Inte tillgänglig just nu och anropar tillbaka senare.|
| SendEmail| Skicka ett e-postmeddelande. Den här gäller för e-post men inte textmeddelanden.|E-postadress att vatten: Mike att middag förra veckan har splendid.<br/>Skicka ett e-postmeddelande till Bob<br/>|
| SendMessage| Skicka ett SMS eller ett snabbmeddelande.|Skicka SMS till Chris och Carol|
| SetSpeedDial| Ange en hastighet dial genväg för en telefonnumret.|Ange kortnummer en för Carol.<br/>Ställ in kortnummer för mom.|
| ShowNext| Se nästa objekt, till exempel i en lista över textmeddelanden eller e-postmeddelanden.|Visa nästa.<br/>Gå till nästa sida.|
| ShowPrevious| Se föregående objekt, till exempel i en lista över textmeddelanden eller e-postmeddelanden.|Visa föregående.<br/>Föregående<br/>Gå till föregående.|
| StartOver| Systemet börja om från början eller starta en ny session.|Starta om<br/>Ny session<br/>restart|
| TurnForwardingOff| Inaktivera vidarekoppling.|Stoppa vidarebefordran min anrop<br/>Stänga av vidarekoppling|
| TurnForwardingOn| Inaktivera talare telefonen.|Vidarebefordran av min anrop till 3333<br/>Aktivera vidarekoppling till 3333|
| TurnSpeakerOff| Inaktivera talare telefonen.|Ta mig av talare.<br/>Inaktivera högtalartelefon.<br/>|
| TurnSpeakerOn| Aktivera talare telefonen.|Högtalartelefon läge.<br/>Placera högtalartelefon på.<br/>|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AudioDeviceType | Typ av ljudenhet (talare, hörlurar, mikrofon osv.).| Talare<br/>Handsfree-<br/>Bluetooth|
| Kategori | Kategori för ett meddelande eller e-post.| Viktigt<br/>Hög prioritet|
| ContactAttribute | Ett attribut för kontakta användaren förfrågningar om.| Födelsedagar<br/>Adress<br/>Telefonnummer|
| Kontaktnamn | Namnet på en kontakt eller ett meddelande mottagare.| Carol<br/>Jim<br/>Chris|
| EmailSubject | Den text som används som ämnesraden för ett e-postmeddelande.| RE: intressant berättelse|
| Rad | Rad användaren vill använda för att ringa ett samtal eller skicka ett text/e-postmeddelande från.| Arbeta rad<br/>British cell<br/>Skype|
| Meddelande | Meddelandet som ska skickas som ett e-post eller SMS.| Det gick bra möte idag. Se du igen lite senare!|
| MessageType | Namnet på en kontakt eller ett meddelande mottagare.| Text<br/>E-post|
| OrderReference | Ordningstalet eller relativ position i en lista, identifiera ett objekt som ska hämtas. Till exempel ”efternamn” eller ”senaste” i ”vad var det sista meddelandet som jag skickat”?| Sista<br/>Senaste|
| SenderName | Namnet på avsändaren.| Patti Owens|

## <a name="entertainment"></a>underhållning  
Underhållning domän ger avsikter och entiteter relaterade till att söka efter filmer, musik, spel och TV visas.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Search| Sök efter filmer, musik, appar, spel och TV visas.|Sök i butiken efter Halo.<br/>Sök efter Avatar.|

### <a name="entities"></a>Entiteter
| Entitetsnamnet | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| ContentRating | Media innehållsklassificering som G eller R för filmer.|Barn video.<br/>PG görs.|
| Genre | Genre för en film, spel, appar eller låt.|Comedies<br/>Dramas<br/>Rolig|
| Nyckelord| En allmän sökord som du anger ett attribut i finns inte på de mer specifika både media.|Ljudspår<br/>Månfas floden<br/>Amelia Earhart|
| Språk | Språk som används i medier, till exempel film eller låt talat språk.|Franska<br/>Svenska<br/>Koreanska|
| MediaFormat | Ytterligare särskilda tekniska typen där mediet formateras.|HD-filmer<br/>3D-filmer<br/>Nedladdningsbara|
| MediaSource | Store eller marketplace för att hämta media.|Netflix<br/>Prime|
| MediaSubTypes| Medietyper som är mindre än filmer och spel.|Demonstrationer<br/>DLC<br/>Släpfordon|
| Medborgarskap| Det land där en film, visa eller låt skapades.|Franska<br/>Tyska<br/>Koreanska|
| Person| Aktör, director, producenten, musiker eller artist som är associerade med en film, app, spel eller TV-program.|Madonna<br/>Stanley Kubrick|
| Roll| Roll som spelas upp av en person i skapandet av media.|Sings<br/>Fått av<br/>Av|
| Titel| Namnet på en film, app, spel, TV-program eller låt.|Vänner<br/>Minecraft|
| Typ| Typ eller media formatet för en film, app, spel, TV-program eller låt.|Musik<br/>MovieTV <br/>Visar|
| UserRating| Användaren star eller miniatyrer omdöme.|5 stjärnor<br/>3 stjärnor<br/>4 stjärnor|

## <a name="events"></a>Händelser 
Händelser-domänen ger avsikter och entiteter relaterade till bokning biljetter för evenemang som konserter, festivaler, Sportspel och Komedi visas.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Bok| Köp biljetter på en händelse.|Jag skulle vilja köpa en biljett för symphony helgen.|


### <a name="entities"></a>Entiteter
| Entitetsnamnet | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Adress | Händelsens plats eller -adress. |Palo Alto<br/>300 112th para SE <br/> Seattle |
| Namn | Namnet på en händelse.|Shakespeare i parken|
| PlaceName| Händelsens plats namn.|Louvre<br/>Opera House<br/>Broadway|
| PlaceType | Typ av plats händelsen ska lagras på.|Café<br/>THEATRE<br/>Bibliotek|
| Typ | Typ av en händelse.|Evenemang<br/>Sport spel|

## <a name="fitness"></a>Lämplighet 
Lämplighet domänen innehåller avsikter och entiteter som rör spårning lämplighet aktiviteter. Avsikter omfattar sparar information, återstående tid eller distance eller spara Aktivitetsresultat.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AddNote| Lägger till ytterligare information till en spårade aktivitet.|Svårare för den här körningen var 6/10<br/>Terräng jag på som körs på är asfalt<br/>Jag använder en cykel 3 hastighet|
|GetRemaining| Hämtar den återstående tid eller avståndet för en aktivitet.|Hur lång tid till nästa varv?<br/>Hur många mil ligger kvar i min kör? Hur lång tid för delningen?|
| LogActivity| Spara eller loggresultat slutförd aktivitet.|Spara min senaste körningen<br/>Logga in min lördag morgon-genomgång<br/>lagra min tidigare simma|
| LogWeight| Spara eller logga användarens aktuella vikt.|Spara min aktuella vikt<br/>Logga min vikt nu<br/>lagra min aktuella brödtext vikt|

### <a name="entities"></a>Entiteter
| Entitetsnamnet | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| ActivityType | Typ av aktivitet som spårar. |Kör<br/>Gå<br/>Simma<br/>Cykel |
| Livsmedel | En typ av mat ska spåras i en lämplighet-app. |Bananer<br/>Laxrosa<br/>Skaka om protein|
| MealType| Måltid-typ som kan spåras i en hälsotillstånd eller lämplighet app.|Bra<br/>Middag<br/>Lunch<br/>Supper|
| Mått| En typ av mått för tid, avståndet eller vikt för användning i en lämplighet eller health-app.|Kilometer<br/>Miles<br/>Minuter<br/>Kg|
| Tal | Ett numeriskt värde för användning i en lämplighet eller health-app.|19<br/>tre<br/>200<br/>en|
| StatType | En statistik typ på sammanställda data för användning i en lämplighet eller health-app.|Summa<br/>Medel<br/>Maximal<br/>Minimum|

## <a name="gaming"></a>Spel 
Spel-domänen innehåller avsikter och entiteter som rör hantering av en game part i spelet.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| InviteParty| Bjud in en kontakt som ska ansluta till en spel part.|Bjud in den här player till min parten<br/>Gå till min part<br/>Ansluta till min clan|
|LeaveParty| Hämtar den återstående tid eller avståndet för en aktivitet.|Jag ut<br/>Jag lämna den här parten för en annan<br/>Jag avsluta|
| StartParty| Starta en spel part i spelet.|Skojar bara ska vi börja en part<br/>Starta en part<br/>Vi ska börja en clan ikväll|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Kontakt| Ett kontaktnamn ska användas i spelet.|Carol<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
HomeAutomation domänen innehåller avsikter och entiteter relaterade till att styra smart home enheter som tangentsektionen och installationer.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Avstängning| Inaktivera, stänga eller låsa upp en enhet.|Inaktivera lamporna<br/>Stoppa kaffe maker<br/>Stäng garagedörr|
|Aktivera| Aktivera en enhet eller ställa in enheten till en viss inställning eller läge.|Aktivera Mina kaffe maker<br/>kan du aktivera min kaffe maker?<br/>Ange termostat 72 grader.|


### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Enhet | En typ av enhet som kan vara aktiverat eller inte.|Kaffe maker<br/>Termostat<br/>ljus|
| Åtgärd | Tillstånd att ställa in på enheten.|Lås<br/>open (öppen)<br/>på<br/>inaktiverade|
| Utrymme | Plats- eller enheten är i rummet.|Vardagsrum<br/>sovrum<br/>Se|

## <a name="movietickets"></a>MovieTickets 
MovieTickets domänen innehåller avsikter och entiteter relaterade till boka biljetter till filmer på en film theater.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Boka två biljetter för kapten Omar och två musketörer|
|Avbryt biljetter|
|När visar kapten Omar?|

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Bok | Köpa film biljetter.|Boka två biljetter för kapten Omar och två musketörer<br/>Jag vill köpa en biljett för morgondagens film<br/>Jag vill ha en biljett för kapten Omar del 2 nästa onsdag|
|GetShowTime| Hämta showtime av en film.|När visar kapten Omar?|


### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Adress | Adressen till en film theater.|Palo Alto<br/>300 112th para SE<br/>Seattle|
| MovieTitle | Rubrik för en film.|Livslängd för Pi<br/>Svält spel<br/>Start|
| PlaceName | Namnet på en film theater eller biografer.|Biografer Amir<br/>Alexandria Theatre<br/>New York Theater|
| PlaceType | Typ av plats som en film visas.|biografer<br/>Theater<br/>IMAX biografer|

## <a name="music"></a>Musik 
Musik domänen innehåller avsikter och entiteter relaterade till spela musik på en musikspelare.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|spela upp Beethoven|
|öka spåra volym|
|Gå vidare till nästa låt|

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| DecreaseVolume | Minska volymen för enheten.|minska spåra volymen<br/>Sänk volym|
| IncreaseVolume | Öka enheten volymen.|öka spåra volym<br/>volym upp|
| Ljud av |Stäng av spelas upp musik.|Ljud av Låt<br/>Placera spåra på läge<br/>Ljud av musik |
| Pausa | Pausa spelas upp musik.|Pausa<br/>Pausa musik<br/>Pausa spåra|
| PlayMusic | Spela upp musik på en enhet.|spela upp Kevin Durant<br/>spela upp Paradise av Coldplay<br/>spela upp Hello av Adele|
| Upprepa |Upprepa spelas upp musik.|Upprepa Låt<br/>Spela upp Spåra vinst<br/>Upprepa musik|
| Återuppta | Återuppta spelas upp musik.|Återuppta Låt<br/>Starta musik igen<br/>Fortsätt med|
| SkipBack | Hoppa bakåt ett spår.|Gå vidare till nästa låt<br/>Spela upp ett spår|
| SkipForward |Hoppa framåt ett spår.|Spela upp tidigare Låt<br/>Gå tillbaka till föregående spår |
| Stoppa | Stoppa en åtgärd som är relaterad till musikuppspelning. |Stanna detta album.|
| Ljud på | På ljud en enhet för uppspelning av musik.| På ljud.|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| ArtistName | Aktör, director, producenten, skrivare, musiker eller artist som är associerade med media för att spela upp på en enhet.|Elvis Presley<br/>Taylor Swift<br/>Adele<br/>Mozart|
| Genre | Genre för musik som begärts.|Land musik<br/>Broadway klassiker<br/>Spela upp min klassiskt från Baroque period|

## <a name="note"></a>Obs! 
Obs domänen innehåller avsikter och entiteter relaterade till att skapa, redigera och söka efter information.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Lägg till i Mina mat Obs sallat tomat bröd kaffe|
|Kontrollera av bananer från min Inköpslista|
|Ta bort alla objekt från listan semester|

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AddToNote | Lägg till information till en anteckning.|Lägg till i Mina mat Obs sallat tomat bröd kaffe<br/>Lägg till min att göra-listan<br/>Lägg till cupcakes i min Wunderlist|
| CheckOffItem | Markera objekt från en befintlig anteckning.|Kontrollera av bananer från min Inköpslista<br/>Markera ost enkelt på min helgdag Inköpslista som görs|
| Rensa | Ta bort alla objekt från en befintlig anteckning.|Ta bort alla objekt från listan semester<br/>Rensa alla från listan läsning|
| Bekräfta | Bekräfta en åtgärd som är relaterad till en anteckning.|Det är okej av mig<br/>ja<br/>Jag bekräftat att hålla alla objekt på listor|
| Skapa | Skapa en ny anteckning. | Skapa en lista<br/>Observera att Påminn mig att Jason är i stad första veckan i maj|
| Ta bort | Ta bort en hel anteckning. |Ta bort Min semester-lista <br/>ta bort min mat-anteckning|
| DeleteNoteItem | Ta bort objekt från en befintlig anteckning.| Ta bort kretsar från min Inköpslista<br/>Ta bort pennor från min skola Inköpslista|
| ReadAloud | Läs en lista upplästa högt.|Viktigt förstnämnda<br/>Viktigt informationen|
| ShowNext | Se nästa objekt i en lista med anteckningar.|Visa nästa<br/>Nästa sida<br/>Nästa|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Programnamn | Anteckna programnamnet.|Wunderlist<br/>OneNote|
| Kontaktnamn | Namnet på en kontakt i en anteckning.|Carol<br/>Jim<br/>Chris|
| DataSource | Platsen för anteckningar.|OneDrive<br/>Google docs<br/>min dator|
| Datatyp | Typ av filen eller dokumentet, vanligtvis förknippas med specifika program.|Bilder<br/>Kalkylblad<br/>Kalkylblad|
| Text | Texten för en kommentar eller påminnelse.|Sträck ut innan walking<br/>lång sikt imorgon|
| Titel | Rubrik för en anteckning.|mat<br/>personer att anropa<br/>att göra|

## <a name="ondevice"></a>OnDevice 
OnDevice domänen innehåller avsikter och entiteter relaterade till att styra enheten.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Stäng videospelare|
|Avbryt uppspelning|
|Kan du göra skärmen ljusare?|


### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AreYouListening | Be om enheten lyssnar.|är det här på?<br/>du lyssnar?|
|CloseApplication|Stäng programmet på exempelenheten.|Stäng videospelare|
|FileBug|Rapportera ett fel på enheten.|Rapportera ett fel.<br/>Kan du rapportera ett fel för mig?<br/>Låt mig rapportera den här buggen|
|GoBack|Be att gå tillbaka till föregående steg eller gå tillbaka till föregående steg.|Gå tillbaka.<br/>Gå till föregående skärm<br/>Gå tillbaka stoppa lyssnar|
|Hjälp| Be om hjälp.|Hjälp.<br/>Hej<br/>Vad kan du göra?<br/>Jag behöver hjälp| 
|LocateDevice|Leta upp enheten.|Du kan hitta min telefon<br/>Hitta tom's iphone<br/>Hitta min telefon|
|Logga in|Logga in på en tjänst som använder enheten.|Logga in.<br/>Facebook-logga in<br/>Logga in på LinkedIn|
|Logga ut|Logga ut från en tjänst som använder enheten.|Logga ut min telefon<br/>Logga in på twitter<br/>Logga ut|
|MainMenu|Visa huvudmenyn för en enhet.|Visa-menyn.|
|OpenApplication|Öppna ett program på enheten.|Öppna larmet.<br/>Aktivera kameran<br/>Starta kalender|
|OpenSetting|Öppna en inställning på enheten.|Öppna nätverksinställningar.|
|PairDevice|Koppla enheten.|Du kan hjälpa mig i parkoppling Bluetooth-signal till telefon<br/>Aktiverar bluetooth och koppla den bärbara datorer<br/>Par Bluetooth signalen att min bärbara dator|
|Stäng av datorn | Stäng av enheten.|Du kan stänga av datorn<br/>Avstängning<br/>Inaktivera min mobil|
|QueryBattery|Få information om batterilivslängd.|Visa batteritid.<br/>Vad är min batteristatus<br/>Hur mycket batteri kvar nu?<br/>Visa mig batteri|
|QueryWifi|Få information om Wi-Fi.|Hämta Wi-Fi-information.|
|Starta om|Starta om enheten.|Starta om.|
|RingDevice| Be att enheten ring, för att hitta den när den har förlorat.|Ring min telefon.| 
|SetBrightness|Ange ljusstyrka för enheten.|Ställa in ljusstyrka till medium<br/>Ställa in ljusstyrka till hög<br/>Ställa in ljusstyrka till lågt|
|SetupDevice|Initiera installation av enheter.|Jag vill installera OS-installationsprogrammet<br/>Konfigurera.<br/>Göra inställningar för mig|
|ShowAppBar|Visa en app-panelen.|Visa mig programfältet<br/>Program-fältet genom<br/>Låt mig finns i programfältet|
|ShowContextMenu|Visa en snabbmeny.|Låt mig finns i snabbmenyn<br/>Snabbmenyn.<br/>Du kan visa snabbmenyn|
|Strömsparläge|Placera enheten i viloläge.|Gå och lägga sig<br/>Strömsparläge<br/>Min dator i strömsparläge|
|SwitchApplication|Växla programmet till att använda på enheten.|Växla till min media player.|
|TurnDownBrightness|Stänga ned ljusstyrka för enheten.|Dim skärmen.|
|TurnOffSetting|Inaktivera inställningen för en enhet.|Inaktivera Bluetooth<br/>Inaktivera data<br/>Koppla från bluetooth|
|TurnOnSetting|Aktivera inställningen för en enhet.|På <br/> Av|
|TurnUpBrightness|Höj ljusstyrka för enheten.|Kan du göra skärmen ljusare?|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Programnamn | Namnet på ett program på enheten.|SoundCloud<br/>YouTube|
| BrightnessLevel | Ange ljusstyrkan på enheten.|Hundra procent<br/>Femtio<br/>40%|
| Kontaktnamn | Namnet på en kontakt på enheten.|Paul<br/>Marlen Max|
| Enhetstyp | Typ av enhet. |Telefon<br/>Kindle<br/>Bärbar dator|
| Medietyp | Mediatyp som hanteras av enheten.|Musik<br/>Film<br/>TV-program|
| SettingType | En typ av inställning eller inställningspanelen som användaren vill redigera.|Wi-Fi<br/>Trådlöst nätverk<br/>Färgschema<br/>Meddelandecenter|

## <a name="places"></a>Platser  
Platser domänen innehåller avsikter för hantering av frågor som rör platser som företag, institution, restauranger, offentliga blanksteg och adresser.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Spara den här platsen i Mina favoriter|
|Hur långt bort är helgdag Inn?|
|Vid vilken tidpunkt Safeway Stäng?|


### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Lägga till en plats i användarens Favoriter-lista.|Spara den här platsen i Mina favoriter<br/>Lägga till den här adressen i Mina favoriter|
|CheckAccident|Be om det finns en olycka på angivna väg.|Finns det en olycka på 880?<br/>Visa mig misstag information|
|CheckAreaTraffic|Kontrollera trafik för ett allmänt område eller plats, inte på en angiven väg.|Trafik i Seattle<br/>Vad är trafik som i Seattle?|
|CheckIntoPlace|Checka in på en plats med hjälp av sociala medier.|Kontrollera mig i på Foursquare<br/>Checka in här|
|CheckRouteTraffic| Kontrollera trafik för en specifik väg som angetts av användaren.|Vad är trafiken till Mashiko?<br/>Visa mig trafiken till Kirkland<br/>Vad är trafiken till Seattle?| 
|Bekräfta|Bekräfta en åtgärd som är relaterad till en plats.|Bekräfta min restaurang-reservation.|
|Avsluta|Åtgärd att avsluta en uppgift som är relaterad till en plats.|Avsluta.<br/>Avsluta ger mig till anvisningarna|
|FindPlace|Sök efter en plats (business, institution, restaurang, offentligt adressutrymme, adress).|Var finns det närmaste biblioteket?<br/>Hitta en bra italienska restaurang i Mountain View|
|GetAddress| Be om adressen till en plats.|Visa adressen för Guu på Robson gata<br/>Vad är adressen till den närmaste Starbucks?| 
|GetDistance|Fråga om avståndet till en specifik plats.|Hur långt bort är helgdag Inn?<br/>hur långt är det att Bellevue kvadratisk härifrån<br/>Vad är avståndet till Tahoe|
|GetHours|Fråga om drifttimmar en plats.|Vid vilken tidpunkt Safeway Stäng?<br/>Vilka är timmarna för start Depot?<br/>Starbucks fortfarande är öppen?|
|GetMenu|Be om menyalternativ för en restaurang.|Hanterar Zucca några något vegan?<br/>Vad är på menyn på Sizzler<br/>Visa mig Applebees menyn|
|GetPhoneNumber| Be om telefonnumret till en plats.|Vad är telefonnumret till den närmaste Starbucks?<br/>Numrera för start Depot| 
|GetPriceRange| Begär prisintervall i en plats.|Är Zucca billiga?<br/>Är Cineplex halva priset på onsdagar?<br/>Hur mycket kostar en hela lobster middag på Sizzler?|
|GetReviews|Be om granskningar av en plats.|Visa mig granskningar för tårtbitar Factory<br/>Läs Cineplex recensioner i Yelp|
|GetRoute|Fråga efter vägen till en plats.|Hur du går till Bellevue kvadratisk<br/>Visa det kortaste sättet att 8 och 59th härifrån<br/>Få mig en vägbeskrivning till Mountain View CA|
|GetStarRating|Be om stjärnor för en plats.|Hur klassificeras Zucca enligt Yelp?<br/>Hur många stjärnor har franska tvätt?<br/>Är aquarium i Monterrey bra?|
|GetTransportationSchedule|Hämta bus-schemat för en plats.|Vilken tid är nästa bussen för att centralt?<br/>Visa mig bussarna i King County|
|GetTravelTime|Be om restiden till ett angivet mål.|Hur lång tid tar det för att komma till San Francisco härifrån<br/>Vad ska körtid Denver från SF|
|MakeCall|Ringa ett telefonsamtal till en plats.|Anropa mom<br/>Jag vill ringa upp Skype Anna<br/>Anropa Jim|
|MakeReservation|Begär en reservation för en restaurang eller andra företag.|Reservera på Zucca för två för tonight<br/>Boka en tabell för imorgon<br/>Tabell för 3 i Palo Alto på 8|
|MapQuestions|Begära information om riktningar eller om en angiven väg försätts i ett mål.|13 passerar genom centralt?<br/>Kan jag ta 880 till Oakland?|
|Klassificering|Hämta den klassificering beskrivningen av en restaurang eller plats.|Hur många stjärnor har Contoso Inn?|
|ReadAloud|Läs en lista med platser upplästa högt.|Viktigt förstnämnda<br/>Viktigt informationen|
|SelectItem|Välj ett objekt från en lista med alternativ som är relaterade till en plats eller platser.|Välj den andra mallen<br/>Välj först|
|ShowMap|Visa en karta över ett område.|Visa en karta för den andra mallen<br/>Visa karta<br/>Hitta San Francisco på kartan|
|ShowNext|Visa nästa objekt i en serie.|Visa nästa<br/>Gå till nästa sida|
|ShowPrevious|Visa föregående objekt i en serie.|Visa föregående<br/>föregående<br/>Gå till föregående|
|StartOver|Starta om appen eller starta en ny session.|Starta om<br/>Ny session<br/>
restart|
|TakesReservations|Be om en plats tar emot reservationer.|Accepterar konstgalleri reservationer<br/>Är det möjligt att göra en reservation vid oliver Garden

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AbsoluteLocation | Platsen eller adress för en plats.|Palo Alto<br/>300 112th para SE<br/>Seattle|
| Bekvämligheterna | Objektiva egenskaper/fördelarna med en plats.|barn äta utan kostnad<br/>Hamnområde.<br/>kostnadsfria på parkeringssida|
| Atmosfär | Luft på en plats.|barn-vänlig<br/>informell restaurang<br/>Sportig|
| Köket med alla dess | Den köket med alla dess för en plats. |Medelhavet<br/>Italienska<br/>Territoriet i indiska|
| DestinationAddress| En målplats eller adress.|Palo Alto<br/>300 112th para SE<br/>Seattle|
| DestinationPlaceName| Namnet på ett mål som är ett företag, restaurang, offentliga finns eller institution.|Central park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Typ av ett mål som är en lokal företag, restaurang, offentliga finns eller institution. |Restaurang<br/>Opera<br/>biografer|
| Avstånd | Avståndet till en plats.|15 mil<br/>5 mil<br/>10 långt bort|
| MealType | Typ av Måltid som bra eller lunch. |Bra<br/>Middag<br/>Lunch<br/>Supper|
| OpenStatus | Anger om en plats är öppna eller stängda.|Öppet<br/>Stängd<br/>Öppna|
| PlaceName | Namnet på en plats.|Tårtbitar Factory|
| PlaceType | Typ av en plats.|Café<br/>THEATRE<br/>Bibliotek|
| PreferredRoute | Den prioriterade vägen som angetts av användaren. | 101 <br/>202 <br/>Väg 401|
| Produkt | Produkten som erbjuds av en plats. | Kläder<br/>Digitala ASR kameror<br/>Ny fisheye | 
| PublicTransportationRoute | Namnet på den kollektivtrafik väg som du söker efter. | Nordöstra korridoren train<br/>Bus dirigera 3 X |
| Klassificering | Klassificering av en plats. | 5 stjärnor<br/>3 stjärnor<br/>4 stjärnor|
| RouteAvoidanceCriteria | Kriterier för att undvika specifika vägar som undvika olyckor, konstruktioner eller vägtullar | Vägtullar <br/>Konstruktioner<br/>Väg 11|
| ServiceProvided | Det här är den tjänst som tillhandahålls av en företags- eller plats, till exempel klippte dig, åka plowing, trädgård. | klippte dig<br/>verkstaden<br/>rörmokare|
| TransportationCompany | Namnet på en transportprovider.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Transport-typen.|Bus<br/>Träna<br/>Driving (Bil)|

## <a name="reminder"></a>Påminnelse 
Påminnelse domänen innehåller avsikter och entiteter för att skapa, redigera och söka efter påminnelser.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Ändra min intervjun till 9: 00 imorgon|
|Påminn mig att köpa mjölk på vägen tillbaka Start|
|Kan du kontrollera om jag har en påminnelse om Christinas födelsedag?|


### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Ändra| Ändra en påminnelse.|Ändra min intervjun till 9: 00 imorgon<br/>Flytta påminnelsen tilldelning till imorgon|
| Skapa| Skapa en ny påminnelse.|Skapa en påminnelse<br/>Påminn mig att köpa mjölk<br/>Jag vill att komma ihåg att anropa Rebecca när jag hemma|
| Ta bort | Ta bort en påminnelse.|Ta bort påminnelsen bild<br/>Ta bort den här påminnelsen|
| Hitta | Hitta en påminnelse.|Måste jag en påminnelse om min anniversary?<br/>Kan du kontrollera om jag har en påminnelse om Christinas födelsedag?|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Text | Textbeskrivning av en påminnelse.|Hämta Kemtvätt<br/>tar bort min bil på servicecenter|

## <a name="restaurantreservation"></a>RestaurantReservation 
RestaurantReservation domänen innehåller avsikter och entiteter som rör hantering av restaurang reservationer.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Reservera på Zucca för två för tonight|
|Boka en tabell i BJS för imorgon|
|Tabell för 3 i Palo Alto på 7|

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Reservera | Begär en reservation för en restaurang. |Reservera på Zucca för två för tonight<br/>Boka en tabell för imorgon<br/>Tabell för 3 i Palo Alto på 7|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Adress| En händelse plats eller en adress för en reservation.|Palo Alto<br/>300 112th para SE<br/>Seattle|
| Bekvämligheterna | Ett attribut som beskriver bekvämligheterna för en plats.|Visa havet<br/>icke hälsan|
| Programnamn | Namnet på ett program för att göra reservationer.|ÖppnaTabell<br/>Yelp<br/>TripAdvisor|
| Atmosfär | En beskrivning av atmosfär i en restaurang eller på annan plats.|Romantiska<br/>avslappnat<br/>bra för grupper|
| Köket med alla dess | En typ av mat, köket med alla dess eller köket med alla dess medborgarskap. |Kinesiska<br/>Italienska<br/>Mexikanska|
| MealType | En måltid typ som är associerade med en reservation.|Bra<br/>Middag<br/>Lunch<br/>Supper|
| PlaceName | Namnet på en lokal företag, restaurang, offentliga finns eller institution.|IHOP<br/>Tårtbitar Factory<br/>Louvre|
| PlaceType | Typ av en lokal företag, restaurang, offentliga finns eller institution.|Restaurang<br/>Opera<br/>biografer|
| Klassificering | Klassificering av en plats eller en restaurang.|5 stjärnor<br/>3 stjärnor<br/>4 stjärnor|

## <a name="taxi"></a>Taxi 
 
Taxi-domänen innehåller avsikter och entiteter för att skapa och hantera taxi bokningar.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Hämta en CAB-fil klockan 15|
|Hur mycket längre har att vänta på min taxi?|
|Avbryt min Uber|

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Bok | Anropa en taxi. |Hämta mig en CAB-fil<br/>Hitta en taxi<br/>Boka mig en uber x|
| Avbryt | Avbryta en åtgärd som är relaterad till en taxi för bokning.|Avbryt min taxi<br/>Avbryt min Uber|
| Spåra | Spåra en taxi-väg.|Hur mycket längre har att vänta på min taxi?<br/>Var finns min Uber?|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Adress| Adressen som är associerade med en taxi för bokning. |Palo Alto<br/>300 112th para SE<br/>Seattle|
| DestinationAddress| En målplats eller adress. |Palo Alto<br/>300 112th para SE<br/>Seattle|
| DestinationPlaceName | Namnet på ett mål som är en lokal företag, restaurang, offentliga finns eller institution. |Central Park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Typ av ett mål som är en lokal företag, restaurang, offentliga finns eller institution. |Restaurang<br/>Opera<br/>biografer|
| PlaceName | Namnet på lokala företag, restaurang, offentliga finns eller institution. |Central Park<br/>Safeway<br/>Walmart|
| PlaceType| Typ av plats i en begäran om att boka en taxi.|Restaurang<br/>Opera<br/>biografer|
| TransportationCompany | Namnet på en transportprovider.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Transport-typen.|Bus<br/>Träna<br/>Driving (Bil)|

## <a name="translate"></a>Översätt 
Översätt domänen innehåller avsikter och entiteter relaterade till att översätta text till ett mål-språk.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Omvandla till franska|
|Översätt Hej till tyska|
|Översätt den här meningen till engelska|


### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Översätt| Översätta text till ett annat språk.|Omvandla till franska<br/>Översätt Hej till tyska|


### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| TargetLanguage | Målspråk för en översättning.|Franska<br/>Tyska<br/>Koreanska|
| Text | Texten att översätta.|Hello World<br/>God morgon<br/>God kväll|

## <a name="tv"></a>TV 
 
TV-domänen innehåller avsikter och entiteter för att styra TV-apparater.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Växeln kanal till BBC|
|Visa TV-guide|
|Titta på nationella geografiska|

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| ChangeChannel| Ändra en kanal på TV.|Ändra kanal till CNN<br/>Växeln kanal till BBC<br/>Gå till kanal 4|
| ShowGuide| Visa TV-guide.|Visa TV-guide<br/>Vad är på film kanal nu?<br/>Visa mina programlistan|
| WatchTV| Be att titta på en TV-kanal.|Jag vill titta på Disney-kanal<br/>Gå till TV.<br/>Titta på nationella geografiska|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Kanalnamn | Namnet på en TV-kanal.|CNN<br/>BBC<br/>Film kanal|

## <a name="utilities"></a>Samhällsservice  
Domänen verktyg ger avsikter för uppgifter som är gemensamma för många aktiviteter, till exempel hälsningar, avbryta, bekräftelse, hjälp, upprepning, navigering, startas och stoppas.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Gå tillbaka till Twitter|
|Hjälp|
|Upprepa sista frågan.|


### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Avbryt | Avbryta en åtgärd.|Avbryt meddelandet<br/>Jag vill inte skicka e-postmeddelandet längre|
| Bekräfta | Bekräfta en åtgärd.|Ja bekräftar ojsan jag att<br/>Jag bra bekräftar<br/>Jag okej bekräftar|
| FinishTask | Slut en aktivitet som användaren har börjat.|Jag är klar<br/>Jag har slutfört<br/>Det är klart|
| GoBack | Gå tillbaka till föregående steg eller gå tillbaka till föregående steg.|Gå tillbaka till Twitter<br/>Gå tillbaka ett steg<br/>Gå tillbaka|
| Hjälp | Begäran om hjälp.|Hjälp<br/>öppna hjälpen<br/>Hjälp|
| Upprepa | Upprepa en åtgärd.|Upprepa sista frågan.<br/>Upprepa senaste Låt|
| ShowNext | Visa nästa objekt i en serie. |Visa nästa<br/>Gå till nästa sida|
| ShowPrevious | Visa föregående objekt i en serie.|Visa föregående|
| StartOver | Starta om appen eller starta en ny session.|Starta om<br/>Ny session<br/>restart|
| Stoppa | Stoppa en åtgärd.| Stoppa om denna<br/>Håll käft<br/>Stoppa.|

## <a name="weather"></a>Väder 
Väder-domänen innehåller avsikter och entiteter för att få väderprognoser och prognoser.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|väder i London i september|
|Vad? s 10 dagar Prognostisera?|
|Vad är medeltemperaturen i Indien i september?|


### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| GetCondition | Få historiska fakta som rör väder. |väder i London i September<br/>Vad är medeltemperaturen i Indien i September?|
| GetForecast | Hämta aktuellt väder och prognos för de närmaste dagarna. |Vad är vädret idag?<br/>Vad är 10 dagar Prognostisera?<br/>Hur blir vädret helgen?|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Plats| Absoluta plats för en väder-begäran.|Seattle<br/>Paris<br/>Palo Alto|

## <a name="web"></a>Webb 
Web-domänen innehåller en avsikt för att navigera till en webbplats.

### <a name="examples"></a>Exempel

|Exempel|
|--|
|Gå till facebook.com|
|Gå till www.twitter.com|
|Gå till www.bing.com|

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Navigera | En begäran att navigera till en angiven webbplats. |Gå till facebook.com<br/>Gå till www.twitter.com|

