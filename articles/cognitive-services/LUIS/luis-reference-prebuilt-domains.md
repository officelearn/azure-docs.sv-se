---
title: Fördefinierade referens - Azure | Microsoft Docs
titleSuffix: Azure
description: Referens för de fördefinierade domäner som är färdiga samlingar med avsikter och entiteter från språk förstå Intelligent tjänster (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 8e04853e0044e045158642fea51c225378eb3ad6
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319062"
---
# <a name="prebuilt-domain-reference"></a>Fördefinierade referens
Den här referensen innehåller information om de fördefinierade domäner som är färdiga samlingar med avsikter och entiteter som THOMAS erbjuder.

## <a name="list-of-prebuilt-domains"></a>Lista över fördefinierade domäner
THOMAS erbjuder 20 fördefinierade domäner. 

| Fördefinierade domän | Beskrivning | Språk som stöds |
| ---------------- |-----------------------|:------:|
| Kalender | Domänen kalender innehåller avsikt och entiteter för att lägga till, ta bort, eller redigera ett möte, kontrollera deltagare i tillgänglighet och att hitta information om en händelse.| sv-SE<br/> zh-CN |
| Kamera | Domänen kamera innehåller avsikter och entiteter för tar bilder, videofilmer registrering och broadcasting video till ett program.| sv-SE |
| Kommunikation | Skicka meddelanden och ringer.| sv-SE <br/> zh-CN |
| Underhållning  | Hantera frågor som rör musik, filmer och TV.| sv-SE |
| Händelser | Bokningstyp biljetter för konserter, festivaler, Sportspel och Komedi visas.| sv-SE |
| Lämplighet | Begäranden som rör Aktivitetsspårning lämplighet.| sv-SE |
| Spel | Begäranden som rör spel part i spelet.| sv-SE |
| HomeAutomation | Styra smart hem enheter som ljus och installationer.| sv-SE<br/> zh-CN |
| MovieTickets | Bokningstyp biljetter för filmer på en biografen.| sv-SE |
| Musik | Spela upp musik på en musikspelare.| sv-SE<br/> zh-CN |
| Obs! | Obs domänen ger avsikter och enheter som är relaterade till att skapa, redigera och att hitta anteckningar.| sv-SE<br/> zh-CN |
| OnDevice | Domänen OnDevice ger avsikter och enheter som är relaterade till att styra enheten.| sv-SE<br/> zh-CN |
| Platser  | Hantera frågor relaterade till platser som företag, institutioner, hotell, offentliga blanksteg och adresser.| sv-SE<br/> zh-CN |
| Påminnelse | Begäranden som rör skapa, redigera och söka efter påminnelser.| sv-SE<br/> zh-CN |
| RestaurantReservation | Hantera begäranden om att hantera restaurang reservationer.| sv-SE<br/> zh-CN |
| Taxi | Hanterar bokningar för en taxi.| sv-SE<br/> zh-CN |
| Översätta | Översätta text till ett mål-språk.| sv-SE<br/> zh-CN |
| TV | Kontrollera TV-apparater.| sv-SE |
| Samhällsservice  | Begäranden som är vanliga i många domäner som ”hjälp”, ”upprepa”, ”börja om från början”.| sv-SE |
| Väder | Hämtar väderrapporter och prognoser.| sv-SE<br/> zh-CN |
| Webb | Gå till en webbplats.| sv-SE<br/> zh-CN |

Mer information om varje domän i avsnitten som följer.

## <a name="calendar"></a>Kalender 

Domänen kalender innehåller avsikter och enheter som är relaterade till kalenderposter. Kalender avsikter omfattar att lägga till, ta bort eller redigera ett möte, Kontrollera tillgänglighet och hitta information om ett kalenderobjekt eller möte.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Lägg till | Lägga till ett nytt enstaka objekt i kalendern.| Göra en tid med Lisa klockan 2 på söndag <br/><br/>Jag vill schemalägga ett möte<br/><br/>Jag behöver för att skapa ett möte|
| CheckAvailability | Hitta tillgängligheten för ett möte eller på användarens kalender eller en annan person kalender.| När är Jim tillgodoser? <br/><br/>Visa när Carol är tillgängliga i framtiden<br/><br/>Är Chris gratis på lördag?|
| Ta bort | Begäran om att ta bort en kalenderpost.| Avbryt min möte med Carol. <br/><br/>Ta bort min 9: 00-möte<br/>|
| Redigera | Begäran om för att ändra ett befintligt möte eller kalendern.| Flytta mitt möte 9: 00 till 10 kl.<br/><br/>Jag vill uppdatera schemat.<br/><br/>Reschdule mitt möte med Ryan.|
| Hitta | Visa kalendern varje vecka.| Sök efter tandläkaren granska möte. <br/><br/>Visa min kalender<br/>|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Plats | Platsen för kalenderobjekt, möte eller möte. Adresser, orter och regioner är bra exempel på platser.| 209 Nashville gymmet <br/><br/>897 pannkaka house<br/><br/>Garage|
| Ämne | Rubrik på eller mötet.| tandläkartiden <br/><br/>Lunch med Julia<br/><br/>Läkares möte|

## <a name="camera"></a>Kamera 
Domänen kamera ger avsikter och enheter som är relaterade till att använda en kamera. Avsikter omfatta avbildning av ett foto, selfie, skärmbild eller videoklipp och sänder video till ett program.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| CapturePhoto| Avbilda ett foto.| Ta ett foto<br/><br/>Avbilda|
| CaptureScreenshot | Avbilda en skärmbild.| Ta en skärmbild.<br/><br/>Avbilda skärmen.|
| CaptureSelfie | Avbilda en selfie.| Ta en selfie <br/><br/>ta en bild av mig |
| CaptureVideo | Börja spela in video.| Starta inspelningen <br/><br/>Börja spela in|
| StartBroadcasting| Starta broadcasting video.| Starta sändning till Facebook|
| StopBroadcasting| Stoppa broadcasting video.| Stoppa broadcasting|
| StopVideoRecording| Stoppa inspelningen av en video.| Som är tillräckligt<br/><br/>stoppa inspelningen|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Programnamn | Namnet på ett program att sända video till.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Kommunikation 
Kommunikation-domänen ger avsikter och enheter som är relaterade till e-post, meddelanden och samtal.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AddContact| Lägg till en ny kontakt i användarens lista över kontakter.|Lägg till ny kontakt <br/><br/>Spara det här numret och placera namnet som Carol|
| AddMore| Lägga till fler i ett e-post eller text, som en del av en steg för steg så e-post eller text-sammansättning.|Lägga till fler till text <br/><br/>Lägga till fler e-brödtext|
| Svar| Besvara ett inkommande telefonsamtal.|besvara samtalet <br/><br/>Välj|
| AssignContactNickname| Tilldela en kontakt ett smeknamn.|Ändra Isaac till pappa <br/>Redigera Jim smeknamn<br/>Lägg till smeknamn Patti Owens|
| CallVoiceMail| Ansluta till användarens röstmeddelanden.|Anslut till min röstmeddelanden box <br/>röstmeddelanden<br/>anropa röstmeddelanden|
| CheckIMStatus| Kontrollera status för en kontakt i Skype.|Jim's online status anges till direkt? <br/>Finns Carol chatta med?|
| Bekräfta| Bekräfta åtgärden.|Ja<br/>Okej<br/>Ok<br/>Jag bekräftar att jag vill skicka e-postmeddelandet.<br/>|
| fjärråtkomst| Ringa ett telefonsamtal.|Anropa Jim<br/>Kontrollera Ring 311<br/>|
| FindContact| Hitta kontaktuppgifter efter namn.|Hitta Carols nummer<br/>Visa Carols nummer<br/>|
| FindSpeedDial| Hitta antalet speedial ett telefonnummer har angetts till och vice versa.|Vad är Mina telefonnummer 5?<br/>Måste jag hastighet Ring set?<br/>Vad är telefonnummer för 941-5555-333?|
| GetForwardingsStatus| Hämta den aktuella statusen för vidarekoppling.|Är Mina vidarekoppling aktiverad?<br/>Berätta om min samtalsstatus är aktiverad eller inaktiverad<br/>|
| GoBack| Gå tillbaka till föregående steg.|Gå tillbaka till twitter<br/>Gå tillbaka ett steg<br/>Gå tillbaka|
| Ignorera| Ignorera ett inkommande samtal.|Inte svara<br/>Ignorera anrop|
| IgnoreWithMessage| Ignorera ett inkommande samtal och svara med text i stället.|Inte besvara anropet men skicka ett meddelande i stället.<br/>Ignorera och skickar tillbaka en text.|
| PressKey| Tryck på en knapp eller numret på tangentbordet.|Ring stjärna.<br/>Tryck på 1 2 3.|
| ReadAloud| Läsa ett meddelande eller e-post till användaren.|Läs texten.<br/>Vad hon säga i meddelandet?|
| TurnForwardingOff| Ringa ett telefonsamtal.|<br/><br/>|
| Ring upp igen| Ring upp igen eller kontakta en siffra igen.|Ring upp igen.<br/>Ring upp igen min senaste anropet.|
| Avvisa| Avvisa ett inkommande samtal.|Avvisa samtal<br/>Det går inte att besvara nu<br/>Inte tillgänglig för tillfället och ringer tillbaka senare.|
| SendEmail| Skicka ett e-postmeddelande. Den här meningen gäller e-post men inte textmeddelanden.|E-post till Mike Waters: Mike att middag förra veckan var splendid.<br/>Skicka ett e-postmeddelande till Bob<br/>|
| SendMessage| Skicka ett SMS eller snabbmeddelande.|Skicka text till Chris och Carol|
| SetSpeedDial| Ange en genväg för uppringning hastighet för en telefonnumret.|Ange hastighet Ring en för Carol.<br/>Ställ in hastighet uppringning för mom.|
| ShowNext| Se nästa objekt, till exempel i en lista med SMS eller e-postmeddelanden.|Visa nästa.<br/>Gå till nästa sida.|
| ShowPrevious| Se föregående objekt, till exempel i en lista med SMS eller e-postmeddelanden.|Visa föregående.<br/>Föregående<br/>Gå till föregående.|
| StartOver| Starta systemet via eller starta en ny session.|Starta om<br/>Ny session<br/>restart|
| TurnForwardingOff| Inaktivera vidarekoppling.|Stoppa vidarebefordran min anrop<br/>inaktivera vidarekoppling|
| TurnForwardingOn| Stäng av talare telefon.|Vidarebefordran av min anrop till 3333<br/>Aktivera vidarebefordran av anrop till 3333|
| TurnSpeakerOff| Stäng av talare telefon.|Ta me av talare.<br/>Inaktivera högtalartelefon.<br/>|
| TurnSpeakerOn| Aktivera talare telefon.|Högtalartelefon läge.<br/>Spärra högtalartelefon.<br/>|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AudioDeviceType | Typ av ljudenhet (talare, hörlurar, mikrofon och så vidare).| Talare<br/>Handsfree<br/>Bluetooth|
| Kategori | Kategori för ett meddelande eller e-post.| Viktigt<br/>Hög prioritet|
| ContactAttribute | Ett attribut för kontakta användaren förfrågningar om.| Födelsedagar<br/>Adress<br/>Telefonnummer|
| Kontaktperson | Namnet på en kontakt eller ett meddelande mottagare.| Carol<br/>Jim<br/>Chris|
| EmailSubject | Den text som ska användas som ämnesraden för e-post.| RE: intressanta artikeln|
| Rad | Raden användaren vill använda för att ringa ett samtal eller skicka en text/e-post från.| Fungerar rad<br/>Brittiska cell<br/>Skype|
| Meddelande | Meddelandet som ska skickas som ett e-post eller text.| Det var bra möte idag. Se dig igen snart!|
| MessageType | Namnet på en kontakt eller ett meddelande mottagare.| Text<br/>E-post|
| OrderReference | Ordningstalet eller relativ position i en lista som identifierar ett objekt om du vill hämta. Till exempel ”senaste” eller ”senaste” i ”vad var det sista meddelandet som jag skickade”?| Sista<br/>Senaste|
| SenderName | Namnet på avsändaren.| Patti Owens|

## <a name="entertainment"></a>Underhållning  
Underhållning domän ger avsikter och enheter som är relaterade till att söka efter filmer, musik, spel och TV visas.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Search| Sök efter filmer, musik, appar, spel och TV visas.|Sök i butiken för Halo.<br/>Sök efter Avatar.|

### <a name="entities"></a>Entiteter
| Namn på enheter | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| ContentRating | Media innehållsklassificering som G eller R för filmer.|Barnen video.<br/>PG klassificerats.|
| Genre | Genre för en film, spel, appar eller låt.|Comedies<br/>Dramas<br/>Rolig|
| Nyckelordet| En allmän sökord som anger ett attribut i finns inte i mer specifika media-platserna.|Ljudspår<br/>Månen floden<br/>Amelia Earhart|
| Språk | Media innehållsklassificering som G eller R för filmer.|Franska<br/>Svenska<br/>Koreanska|
| MediaFormat | Ytterligare särskilda tekniska typen där mediet formateras.|HD filmer<br/>3D-filmer<br/>Nedladdningsbar|
| MediaSource | Store eller marketplace för hämtning av mediet.|Netflix<br/>Prime|
| MediaSubTypes| Medietyper är mindre än filmer och spel.|Demonstrationer<br/>DLC<br/>Släpfordon|
| Medborgarskap| Det land där en film, visa eller låt skapades.|Franska<br/>Tyska<br/>Koreanska|
| Person| Aktören, chef, producenten, musiker eller artist som är associerade med en film app, spel eller TV-program.|Madonna<br/>Stanley Kubrick|
| Roll| Roll som spelas av en person för att skapa media.|Sings<br/>Anvisningarna från<br/>Av|
| Titel| Namnet på en film app, spel, TV-program eller låt.|Vänner<br/>Minecraft|
| Typ| Typen eller media format för en film app, spel, TV-program eller låt.|Musik<br/>MovieTV <br/>Visar|
| UserRating| Användaren star eller tummen klassificering.|5 stjärnor<br/>3 stjärnor<br/>4 stjärnor|

## <a name="events"></a>Händelser 
Domänen händelser ger avsikter och entiteter som rör boka biljetter för händelser som konserter, festivaler, Sportspel och Komedi visas.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Book| Köp biljetter till en händelse.|Jag vill köpa en biljett för symphony helgen.|


### <a name="entities"></a>Entiteter
| Namn på enheter | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Adress | Plats för händelse eller adress. |Palo Alto<br/>300 112th Ave SE <br/> Seattle |
| Namn | Namnet på en händelse.|Shakespeare i parken|
| PlaceName| Platsnamnet händelse.|Louvre<br/>Opera House<br/>Broadway|
| PlaceType | Typ av platsen händelsen ska behållas i.|Cafe<br/>THEATRE<br/>Bibliotek|
| Typ | Typ av en händelse.|Samklang<br/>Sport-spel|

## <a name="fitness"></a>Lämplighet 
Domänen lämplighet ger avsikter och enheter som är relaterade till Aktivitetsspårning lämplighet. Innehållet är Spara anteckningar återstående tid eller distance eller spara Aktivitetsresultat.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AddNote| Lägger till ytterligare information till en spårade aktivitet.|Svårt för denna körning var 6/10<br/>Förhållanden som jag på körs på är asfalt<br/>Jag använder en cykel 3 hastighet|
|GetRemaining| Hämtar den återstående tid eller avståndet för en aktivitet.|Hur lång tid till nästa lap?<br/>Hur många miles ligger kvar i min kör? Hur lång tid för delningen?|
| LogActivity| Spara eller loggar slutförd Aktivitetsresultat.|Spara mina senaste körningen<br/>Logga min lördag morgon gå<br/>lagra min tidigare simma|
| LogWeight| Spara eller logga användarens aktuella vikt.|Spara min aktuella vikt<br/>Logga min vikt nu<br/>lagra min aktuella brödtext vikt|

### <a name="entities"></a>Entiteter
| Namn på enheter | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| ActivityType | Typ av aktivitet för att spåra. |Kör<br/>Beskriver<br/>Simma<br/>Cykel |
| Livsmedel | En typ av mat att spåra i lämplighet app. |Bananer<br/>Lax<br/>Protein skaka|
| MealType| Typen rätten att spåra i hälsa eller lämplighet app.|Bra<br/>Middag<br/>Lunch<br/>Supper|
| Mått| En typ av mått för tid, avstånd eller vikt för användning i en app lämplighet eller hälsa.|Kilometer<br/>Miles<br/>Minuter<br/>Kg|
| Tal | Ett numeriskt värde i en app lämplighet eller hälsa.|19<br/>tre<br/>200<br/>en|
| StatType | En typ av statistik på sammanställda data för användning i en app lämplighet eller hälsa.|Summa<br/>Medel<br/>Maximal<br/>Minimum|

## <a name="gaming"></a>Spel 
Domänen spel ger avsikter och enheter som är relaterade till att hantera en spel part i spelet.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| InviteParty| Bjud in en kontakt att ansluta till en spel part.|Bjud in den här player till min parten<br/>Gå till min part<br/>Ansluta till Mina clan|
|LeaveParty| Hämtar den återstående tid eller avståndet för en aktivitet.|Jag ut<br/>Jag vill att lämna den här parten för en annan<br/>Jag avsluta|
| StartParty| Starta en spel part i spelet.|Vi skojar bara starta en part<br/>Starta en part<br/>vi börja en clan ditt eget|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Kontakt| Kontaktnamnet ska användas i spelet.|Carol<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
Domänen HomeAutomation ger avsikter och enheter som är relaterade till att styra smart hem enheter som ljus och installationer.

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Avstängning| Inaktivera, stänga eller låsa upp en enhet.|Inaktivera indikeringar<br/>Stoppa kaffe maker<br/>Stäng Garageportar|
|Aktivera| Aktivera en enhet eller ange enheten till en viss inställning eller läge.|Aktivera min kaffe maker<br/>kan du aktivera min kaffe maker?<br/>Ange termostat 72 grader.|


### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Enhet | En typ av enhet som kan aktiveras eller inaktiveras.|Kaffe maker<br/>Termostat<br/>ljus|
| Åtgärd | Tillstånd att ställa in på enheten.|Lås<br/>öppna<br/>på<br/>inaktiverade|
| Plats | Platsen eller enheten är i rummet.|Vardagsrum<br/>sovrum<br/>kökspersonalen|

## <a name="movietickets"></a>MovieTickets 
Domänen MovieTickets ger avsikter och enheter som är relaterade till boka biljetter för filmer på en biografen.

### <a name="examples"></a>Exempel
```
Book me two tickets for Captain Omar and the two Musketeers
Cancel tickets
When is Captain Omar showing?
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Book | Köpa film biljetter.|Bok två biljetter för kapten Omar och två musketörer<br/>Jag vill köpa en biljett för framtidens film<br/>Jag vill använda en biljett för Captian Omar del 2 nästa onsdag|
|GetShowTime| Hämta show av en film.|När visas kapten Omar?|


### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Adress | Adressen till en biografen.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| MovieTitle | Rubrik på en film.|Livslängd för Pi<br/>Svält spel<br/>Start|
| PlaceName | Namnet på en biografen eller filmer.|Filmer Amir<br/>Alexandria Theatre<br/>New York teater|
| PlaceType | Typ av plats som en film visas.|filmer<br/>teater<br/>IMAX filmer|

## <a name="music"></a>Musik 
Domänen musik ger avsikter och enheter som är relaterade till spelar musik på en musikspelare.

### <a name="examples"></a>Exempel
```
play Beethoven
Increase track volume
Skip to the next song
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| DecreaseVolume | Minska volymen för enheten.|minska spåra volymen<br/>Sänk volym|
| IncreaseVolume | Öka volymen för enheten.|öka spåra volymen<br/>volym upp|
| Tyst läge |Stäng av spela upp musik.|Tyst Låt<br/>Placera spåra på tyst läge<br/>Tyst musik |
| Pausa | Pausa spelar musik.|Pausa<br/>Pausa musik<br/>Pausa spåra|
| PlayMusic | Spela upp musik på en enhet.|spela upp Kevin Durant<br/>spela upp Paradise av Coldplay<br/>spela upp Hello av Adele|
| Upprepa |Upprepa spelar musik.|Upprepa Låt<br/>Spela upp Spåra vinst<br/>Upprepa musik|
| Återuppta | Fortsätt spela upp musik.|Återuppta Låt<br/>Starta musik igen<br/>Fortsätt med|
| SkipBack | Hoppa över bakåtkompatibilitet spår.|Gå vidare till nästa låt<br/>Spela upp nästa låt|
| SkipForward |Hoppa framåt ett spår.|Spela upp tidigare Låt<br/>Gå tillbaka till föregående spår |
| Stoppa | Stoppa en åtgärd som rör uppspelning av musik. |Stoppa den här album.|
| På ljud | På ljud en enhet för uppspelning av musik.| På ljud.|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| ArtistName | Aktören, chef, producenten, skrivare, musiker eller artist som är kopplade till media för att spela upp på en enhet.|Elvis Presley<br/>Taylor Swift<br/>Adele<br/>Mozart|
| Genre | Musikgenre den efterfrågas.|Land musik<br/>Broadway klassiker<br/>Spela upp musik klassisk från Baroque period|

## <a name="note"></a>Obs! 
Obs domänen ger avsikter och enheter som är relaterade till att skapa, redigera och att hitta anteckningar.

### <a name="examples"></a>Exempel
```
Add to my groceries note lettuce tomato bread coffee
Check off bananas from my grocery list
Remove all items from my vacation list
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AddToNote | Lägga till information till en anteckning.|Lägg till min mat Obs sallat tomat bröd kaffe<br/>Lägg till att göra-lista<br/>Lägg till muffins min Wunderlist|
| CheckOffItem | Markera objekt från en befintlig anteckning.|Kontrollera av bananer från listan privata<br/>Markera ost enkelt på min helgdag shopping listan som görs|
| Rensa | Ta bort alla objekt från en befintlig anteckning.|Ta bort alla objekt från listan semester<br/>Rensa allt från listan läsning|
| Bekräfta | Bekräfta en åtgärd som är relaterade till en anteckning.|Det gör inget av mig<br/>ja<br/>Jag bekräftar att hålla alla objekt i listor|
| Skapa | Skapa en ny kommentar. | Skapa en lista<br/>Observera att Påminn mig att Jason i staden första veckan i maj|
| Ta bort | Ta bort en hel anteckningen. |Ta bort Min semester. <br/>ta bort min mat anteckning|
| DeleteNoteItem | Ta bort objekt från en befintlig anteckning.| Ta bort kretsar från listan privata<br/>Ta bort pennor från min skolan shopping lista|
| ReadAloud | Läs en lista upp högt.|Viktigt först<br/>Viktigt information|
| ShowNext | Se nästa objekt i en lista med anteckningar.|Visa nästa<br/>Nästa sida<br/>Nästa|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Programnamn | Anteckna programnamnet.|Wunderlist<br/>OneNote|
| Kontaktperson | Namnet på en kontakt i en anteckning.|Carol<br/>Jim<br/>Chris|
| DataSource | Platsen för anteckningar.|OneDrive<br/>Google-dokument<br/>den här datorn|
| Datatyp | Typ av fil eller ett dokument som vanligtvis förknippas med specifika program.|Bilder<br/>Kalkylblad<br/>Kalkylblad|
| Text | Texten i en anteckning eller påminnelse.|Sträck ut innan du går<br/>lång sikt i framtiden|
| Titel | Titeln på en anteckning.|mat<br/>personer som ska anropa<br/>uppgiften|

## <a name="ondevice"></a>OnDevice 
Domänen OnDevice ger avsikter och enheter som är relaterade till att styra enheten.

### <a name="examples"></a>Exempel
```
Close video player
Cancel playback
Can you make the screen brighter?
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AreYouListening | Fråga om enheten lyssnar.|är detta på?<br/>du lyssnar?|
|CloseApplication|Stäng programmet för enheten.|Stäng videospelare|
|FileBug|Filen ett programfel på enheten.|Kontrollera filen ett programfel<br/>Kan du lagra ett programfel för mig?<br/>Låt mig rapportera det här felet|
|GoBack|Fråga om du vill gå tillbaka ett steg eller gå tillbaka till föregående steg.|Gå tillbaka du<br/>Gå till föregående skärm<br/>Gå tillbaka stoppa lyssning|
|Hjälp| Be om hjälp.|Kontrollera att<br/>Hej<br/>Vad kan du göra?<br/>Jag behöver hjälp| 
|LocateDevice|Leta upp enheten.|Du kan hitta min telefon<br/>Hitta tom's iphone<br/>Hitta min telefon|
|Logga in|Logga in på en tjänst med enheten.|Inloggningen du<br/>Facebook-logga in<br/>Logga in på LinkedIn|
|Logga ut|Logga ut från en tjänst med enheten.|Logga ut min telefon<br/>Logga in på twitter<br/>Logga ut|
|MainMenu|Visa huvudmenyn för en enhet.|Visa-menyn.|
|OpenApplication|Öppna ett program på enheten.|Öppna larmet du<br/>Aktivera kameran<br/>Starta kalender|
|OpenSetting|Öppna en inställning på enheten.|Öppna nätverksinställningar.|
|PairDevice|Koppla enheten.|Du kan hjälpa mig i länkning av Bluetooth-signal till telefon<br/>Aktiverar bluetooth och koppla den bärbara datorer<br/>Koppla Bluetooth signal till min bärbara dator|
|Stäng av datorn | Stänga av enheten.|Du kan stänga av datorn<br/>Avstängning<br/>Inaktivera min mobil|
|QueryBattery|Hämta information om batterilivslängd.|Visa batterilivslängd.<br/>Vad är min batteristatus<br/>Hur mycket batteri kvar nu?<br/>Visa batteri|
|QueryWifi|Hämta information om Wi-Fi.|Hämta WiFi-information.|
|Starta om|Starta om enheten.|Starta om.|
|RingDevice| Be att enheten ringer, för att hitta den när den har förlorat.|Ring telefon.| 
|SetBrightness|Ställa in enheten ljusstyrkan.|Ange ljusstyrkan på medel<br/>Ange ljusstyrkan på hög<br/>Ange ljusstyrkan låg|
|SetupDevice|Starta installationsprogrammet för enheten.|Jag vill installera OS-installationsprogrammet<br/>Kontrollera installationen<br/>Göra inställningar för mig|
|ShowAppBar|Visa en appfältet.|Visa programfältet<br/>Programmet fältet Ange<br/>Låt mig program-fält|
|ShowContextMenu|Visa en snabbmeny.|Låt mig finns i snabbmenyn<br/>Snabbmenyn du<br/>Du kan visa snabbmenyn|
|Strömsparläge|Placera enheten i viloläge.|Gå och lägga sig<br/>Strömsparläge<br/>Min dator viloläge|
|SwitchApplication|Växla program att använda på enheten.|Växla till min Mediaspelaren.|
|TurnDownBrightness|Stänga ned enheten intensitet.|Dim skärmen.|
|TurnOffSetting|Inaktivera inställningen för en enhet.|Inaktivera Bluetooth<br/>Inaktivera data<br/>Koppla från bluetooth|
|TurnOnSetting|Aktivera inställningen för en enhet.|På <br/> Av|
|TurnUpBrightness|Höj enheten intensitet.|Kan du göra skärmen ljusare?|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Programnamn | Namnet på ett program på enheten.|SoundCloud<br/>YouTube|
| BrightnessLevel | Ställa in ljusstyrkan på enheten.|100 procent<br/>Femtio<br/>40%|
| Kontaktperson | Namnet på en kontakt på enheten.|Paul<br/>Marlen Max|
| DeviceType | Typ av enhet. |Telefon<br/>Kindle<br/>Bärbar dator|
| Medietyp | Medietypen som hanteras av enheten.|Musik<br/>Film<br/>TV-program|
| SettingType | En typ av inställning eller inställningar för panel som användaren vill redigera.|Wi-Fi<br/>Trådlöst nätverk<br/>Färgschema<br/>Meddelandecentret|

## <a name="places"></a>Platser  
Domänen platser ger avsikter för hantering av frågor som rör platser som företag, institut, restaurang-, offentliga blanksteg och adresser.

### <a name="examples"></a>Exempel
```
Save this location to my favorites
How far away is Holiday Inn?
At what time does Safeway close?
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Lägg till en plats i användarens favoritlista.|Spara den här platsen i Mina favoriter<br/>Lägg till den här adressen i Mina favoriter|
|CheckAccident|Fråga om det finns en olycka på en angiven väg.|Finns det en olycka på 880?<br/>Visa olycka information|
|CheckAreaTraffic|Kontrollera trafik för ett allmänt område eller plats, inte på en angiven väg.|Trafik i Seattle<br/>Vad är trafik som i Seattle?|
|CheckIntoPlace|Checka in till en plats med hjälp av sociala medier.|Kontrollera i mig på Foursquare<br/>Kontrollera i här|
|CheckRouteTraffic| Kontrollera trafik för en specifik väg som angetts av användaren.|Hur är trafik till Mashiko?<br/>Visa traffice till Kirkland<br/>Hur är trafik till Seattle?| 
|Bekräfta|Bekräfta en åtgärd som är relaterade till en plats.|Bekräfta min restaurang reservation.|
|Avsluta|Åtgärd att avsluta en aktivitet som är relaterade till en plats.|Avslutar du<br/>Avsluta ge mig anvisningarna|
|FindPlace|Sök efter en plats (business, institut, restaurang, offentligt adressutrymme, adress).|Var finns det närmaste biblioteket?<br/>Hitta en bra italienska restaurang i Mountain View|
|GetAddress| Be om adressen till en plats.|Visa adressen Guu på Robson gata<br/>Vad är adressen till den närmaste Starbucks?| 
|GetDistance|Fråga om avståndet till en specifik plats.|Hur långt är helgdagar Inn?<br/>hur långt är det att Bellevue kvadratisk härifrån<br/>Vad är avståndet till Tahoe|
|GetHours|Fråga om drifttid för en plats.|Vid vilken tidpunkt Safeway stängs?<br/>Vilka är timmarna för start anläggningen?<br/>Starbucks fortfarande är öppet?|
|GetMenu|Be för menyalternativen på en restaurang.|Hanterar Zucca några något vegan?<br/>Vad som finns på menyn Sizzler<br/>Visa menyn Applebees|
|GetPhoneNumber| Be om telefonnumret till en plats.|Vad är telefonnumret till närmaste Starbucks?<br/>Numrera för start anläggningen| 
|GetPriceRange| Begär prisintervallet för en plats.|Är Zucca billiga?<br/>Är Cineplex halva priset på onsdagar?<br/>Hur mycket kostar en hela hummer middag på Sizzler?|
|GetReviews|Fråga efter granskning av en plats.|Visa omdömen för Cheesecase Factory<br/>Läs Cineplex granskar i Yelp|
|GetRoute|Fråga efter vägen till en plats.|Hur kan du gå till Bellevue kvadratisk<br/>Visa det kortaste sättet att 8th och 59th härifrån<br/>Hämta anvisningarna till Mountain View CA till mig|
|GetStarRating|Be om klassificering av en plats.|Hur klassificeras Zucca enligt Yelp?<br/>Hur många stjärnor har franska tvätt?<br/>Är akvarium i Monterrey bra?|
|GetTransportationSchedule|Hämta bus-schemat för en plats.|Vilken tid är nästa bussen för att downtown?<br/>Visa bussarna för bestämmer region|
|GetTravelTime|Fråga efter tid till angivet mål.|Hur lång tid tar det för att komma till San Francisco härifrån<br/>Vad ska körtid Denver från SA|
|MakeCall|Ringa ett telefonsamtal till en plats.|Anropa mom<br/>Jag vill ringa upp Skype Anna<br/>Anropa Jim|
|MakeReservation|Begär en reservation för en restaurang eller andra företag.|Reservera på Zucca för två för tonight<br/>Boka en tabell för i framtiden<br/>Tabell 3 i Palo Alto på 8|
|MapQuestions|Begära information om instruktioner eller om en angiven väg leder till ett mål.|Klarar 13 via downtown?<br/>Kan jag ta 880 till Oakland?|
|Klassificering|Hämta klassificering-beskrivning av en restaurang eller plats.|Hur många stjärnor har Contoso Inn?|
|ReadAloud|Läs en lista med platser upp högt.|Viktigt först<br/>Viktigt information|
|SelectItem|Välj ett objekt i en lista med alternativ som är relaterade till en plats eller platser.|Välj det andra<br/>Välj först|
|ShowMap|Visa en karta över ett område.|Visa en karta för den andra<br/>Visa karta<br/>Hitta San Francisco på kartan|
|ShowNext|Visa nästa element i en serie.|Visa nästa<br/>Gå till nästa sida|
|ShowPrevious|Visa föregående objekt i en serie.|Visa föregående<br/>föregående<br/>Gå till föregående|
|StartOver|Starta om appen eller starta en ny session.|Starta om<br/>Ny session<br/>
restart|
|TakesReservations|Be om en plats tar emot reservationer.|accepterar galleriet reservationer<br/>Är det möjligt att skapa en reservation på Olive Garden

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| AbsoluteLocation | Platsen eller adressen till en plats.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Faciliteter | Mål egenskaper/fördelarna med en plats.|barnen äta kostnadsfritt<br/>Hamnområde<br/>ledigt parkering|
| Omgivning | Omgivning på en plats.|KID eget<br/>Avslappnad restaurang<br/>Sportig|
| Köket med alla dess | Den köket med alla dess för en plats. |Medelhavet<br/>Italienska<br/>Indiska|
| DestinationAddress| En målplats eller adress.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName| Namnet på ett mål som är en business restaurang, offentliga finns eller institut.|Central park<br/>safeway<br/>walmart|
| DestinationPlaceType | Typ av ett mål som är en lokal business restaurang, offentliga finns eller institut. |Restaurang<br/>Opera<br/>filmer|
| Avstånd | Avståndet till en plats.|15 miles<br/>5 miles<br/>10 mil bort|
| MealType | Typ av rätten som bra eller lunch. |Bra<br/>Middag<br/>Lunch<br/>Supper|
| OpenStatus | Anger om en plats är öppen eller stängd.|Öppet<br/>stängd<br/>Öppna|
| PlaceName | Namnet på en plats.|Tårtbitar fabriken|
| PlaceType | Typ av en plats.|Cafe<br/>THEATRE<br/>Bibliotek|
| PreferredRoute | Prioriterade vägen som anges av användaren. | 101 <br/>202 <br/>Väg 401|
| Produkt | En produkt som erbjuds av en plats. | Kläder<br/>Digitala ASR kameror<br/>Färsk fisk | 
| PublicTransportationRoute | Namnet på vägen offentliga transport som användaren söker efter. | Nordöstra av tåget<br/>Bus väg 3 X |
| Klassificering | Klassificering av en plats. | 5 stjärnor<br/>3 stjärnor<br/>4 stjärnor|
| RouteAvoidanceCriteria | Villkor för att undvika vägar som undvika missöden, konstruktioner eller vägtullar | Vägtullar <br/>Konstruktioner<br/>Väg 11|
| ServiceProvided | Detta är den tjänst som tillhandahålls av ett företag eller plats, till exempel klippte dig, vinter plowing, landskapsformer. | klippte dig<br/>dvs<br/>rörmokare|
| TransportationCompany | Namnet på en transportprovider.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Transport-typen.|Bus<br/>Lär in<br/>Driving (Bil)|

## <a name="reminder"></a>Påminnelse 
Domänen påminnelse ger avsikter och entiteter för att skapa, redigera och söka efter påminnelser.

### <a name="examples"></a>Exempel
```
Change my interview to 9 am tomorrow
Remind me to buy milk on my way back home
Can you check if I have a reminder about Christine's birthday?
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Ändra| Ändra en påminnelse.|Ändra min intervju till 9: 00 i framtiden<br/>Flytta påminnelsen tilldelning till i framtiden|
| Skapa| Skapa en ny påminnelse.|Skapa en påminnelse<br/>Påminn mig att köpa mjölk<br/>Jag vill komma ihåg att anropa Rebecca när jag är hemma|
| Ta bort | Ta bort en påminnelse.|ta bort påminnelsen bild<br/>ta bort den här påminnelsen|
| Hitta | Hitta en påminnelse.|Finns det en påminnelse om min årsdagar?<br/>Kan du kontrollera om jag har en påminnelse om Christinas födelsedag?|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Text | Beskrivning för en påminnelse.|Hämta Kemtvätt<br/>släppa bilen på servicecenter|

## <a name="restaurantreservation"></a>RestaurantReservation 
Domänen RestaurantReservation ger avsikter och enheter som är relaterade till att hantera restaurang reservationer.

### <a name="examples"></a>Exempel
```
Reserve at Zucca for two for tonight
Book a table at BJ's for tomorrow
Table for 3 in Palo Alto at 7
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Reservera | Begär en reservation för en restaurang. |Reservera på Zucca för två för tonight<br/>Boka en tabell för i framtiden<br/>Tabell 3 i Palo Alto på 7|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Adress| En händelse plats eller en adress för en reservation.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Faciliteter | Ett attribut som beskriver erbjuder en plats.|Visa oceanen<br/>ej hälsan|
| Programnamn | Namnet på ett program för att göra reservationer.|ÖppnaTabell<br/>Yelp<br/>TripAdvisor|
| Omgivning | En beskrivning av luft på en restaurang eller annan plats.|Romantiska<br/>Avslappnad<br/>bra för grupper|
| Köket med alla dess | En typ av mat, köket med alla dess eller köket med alla dess medborgarskap. |Kinesiska<br/>Italienska<br/>Mexiko|
| MealType | En rätten typ som är associerade med en reservation.|Bra<br/>Middag<br/>Lunch<br/>Supper|
| PlaceName | Namnet på en lokal business restaurang, offentliga finns eller institut.|IHOP<br/>Tårtbitar fabriken<br/>Louvre|
| PlaceType | Typ av en lokal business restaurang, offentliga finns eller institut.|restaurang<br/>Opera<br/>filmer|
| Klassificering | Klassificering av en plats eller en restaurang.|5 stjärnor<br/>3 stjärnor<br/>4 stjärnor|

## <a name="taxi"></a>Taxi 
 
Domänen Taxi ger avsikter och entiteter för att skapa och hantera taxi bokningar.

### <a name="examples"></a>Exempel
```
Get me a cab at 3 pm
How much longer do I have to wait for my taxi?
Cancel my Uber
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Book | Anropa en taxi. |Hämta mig en CAB-fil<br/>hitta en taxi<br/>Bok mig en uber x|
| Avbryt | Avbryta en åtgärd som rör Bokningstyp taxin.|Avbryt min taxi<br/>Avbryt min Uber|
| Spåra | Spåra en taxi väg.|Hur mycket längre har vänta på min taxi?<br/>Där är min Uber?|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Adress| Den adress som är associerade med Bokningstyp taxin. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationAddress| En målplats eller adress. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName | Namnet på ett mål som är en lokal business restaurang, offentliga finns eller institut. |Central Park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Typ av ett mål som är en lokal business restaurang, offentliga finns eller institut. |Restaurang<br/>Opera<br/>filmer|
| PlaceName | Namn på lokala företag restaurang, offentliga finns eller institut. |Central Park<br/>Safeway<br/>Walmart|
| PlaceType| Typ av plats i en begäran om att boka en taxi.|Restaurang<br/>Opera<br/>filmer|
| TransportationCompany | Namnet på en transportprovider.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Transport-typen.|Bus<br/>Lär in<br/>Driving (Bil)|

## <a name="translate"></a>Översätta 
Översätt domänen ger avsikter och enheter som är relaterade till att översätta text till ett mål-språk.

### <a name="examples"></a>Exempel
```
Translate to French
Translate hello to German
Translate this sentence to English
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Översätta| Översätta text till ett annat språk.|Omvandla till franska<br/>Översätta hello tyska|


### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| TargetLanguage | Målspråk för en översättning.|Franska<br/>Tyska<br/>Koreanska|
| Text | Texten att översätta.|Hello World<br/>God morgon<br/>God kväll|

## <a name="tv"></a>TV 
 
TV-domänen ger avsikter och enheter för att styra TV-apparater.

### <a name="examples"></a>Exempel
```
Switch channel to BBC
Show TV guide
Watch National Geographic
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| ChangeChannel| Ändra en kanal på TV.|Ändra kanal till CNN<br/>Växeln-kanal till BBC<br/>Gå till kanal 4|
| ShowGuide| Visa TV-guide.|Visa TV-guide<br/>Vad är på film kanal nu?<br/>Visa programlistan Mina|
| WatchTV| Be att titta på en TV-kanal.|Jag vill titta på Disney kanalen<br/>Gå till TV du<br/>Titta på geografiska nationella|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Kanalnamn | Namnet på en TV-kanal.|CNN<br/>BBC<br/>Film kanal|

## <a name="utilities"></a>Samhällsservice  
Domänen verktyg ger avsikter för uppgifter som är gemensamma för många aktiviteter, till exempel helg, avbryta, bekräftelse, hjälp, upprepas, navigering, starta och stoppa.

### <a name="examples"></a>Exempel
```
Go back to Twitter
Please help
Repeat last question please
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Avbryt | Avbryta en åtgärd.|Avbryt meddelandet<br/>Jag vill inte skicka e-postmeddelandet längre|
| Bekräfta | Bekräfta åtgärden.|Ja bekräfta OJ jag<br/>Det bra bekräftar<br/>Det OK bekräftar|
| FinishTask | Slut en aktivitet som användaren har börjat.|Jag är klar<br/>Jag har slutfört<br/>Det är klart|
| GoBack | Gå tillbaka ett steg eller gå tillbaka till föregående steg.|Gå tillbaka till Twitter<br/>Gå tillbaka ett steg<br/>Gå tillbaka|
| Hjälp | Begäran om hjälp.|Hjälp<br/>Öppna hjälp<br/>Hjälp|
| Upprepa | Upprepa åtgärden.|Upprepa senaste frågan du<br/>Upprepa senaste Låt|
| ShowNext | Visa nästa element i en serie. |Visa nästa<br/>Gå till nästa sida|
| ShowPrevious | Visa föregående objekt i en serie.|Visa föregående|
| StartOver | Starta om appen eller starta en ny session.|Starta om<br/>Ny session<br/>restart|
| Stoppa | Stoppa en åtgärd.| Stoppa säger denna<br/>Håll käft<br/>Stoppa du|

## <a name="weather"></a>Väder 
Domänen väder ger avsikter och enheter för att hämta väderrapporter och prognoser.

### <a name="examples"></a>Exempel
```
weather in London in september
What?s the 10 day forecast?
What's the average temperature in India in september?
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| GetCondition | Hämta historiska uppgifter som rör väder. |väder i London i September<br/>Vad är medeltemperaturen i Indien i September?|
| GetForecast | Hämta den aktuella väder och göra prognoser för nästa några dagar. |Hur ska väder idag?<br/>Vad är 10 dagar vid en prognos?<br/>Hur kommer väder vara helgen?|

### <a name="entities"></a>Entiteter
| Entitetsnamn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Plats| Absolut platsen för en väder-begäran.|Seattle<br/>Paris<br/>Palo Alto|

## <a name="web"></a>Webb 
Domänen Web innehåller syftet för att navigera till en webbplats.

### <a name="examples"></a>Exempel
```
Navigate to facebook.com
Go to www.twitter.com
Navigate to www.bing.com
```

### <a name="intents"></a>Avsikter
| Avsiktshantering namn | Beskrivning | Exempel |
| ---------------- |-----------------------|----|
| Navigera | En begäran att navigera till en angiven webbplats. |Gå till facebook.com<br/>Gå till www.twitter.com|

