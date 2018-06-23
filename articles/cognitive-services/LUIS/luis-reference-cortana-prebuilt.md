---
title: Referera till Cortana färdiga appen | Microsoft Docs
description: Referens för Cortana personlig assistent ett fördefinierade program från språk förstå Intelligent tjänster (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351924"
---
# <a name="cortana-prebuilt-app-reference"></a>Referera till Cortana färdiga appen
Den här referensen visar avsikter och enheter som identifieras i Cortana färdiga appen.

## <a name="cortana-prebuilt-intents"></a>Cortana färdiga avsikter
Den färdiga personliga assistent programmet kan identifiera följande avsikter:

| Avsikten | Exempel utterances |
|--------| ------------------|
| Builtin.Intent.Alarm.alarm_other|Uppdatera mina 7:30 larm ska åtta klockan nio <br/>Ändra min larm från 8: 00 till 9: 00 |
| Builtin.Intent.Alarm.delete_alarm|ta bort ett larm <br/>ta bort min larmet ”väcka”|
| Builtin.Intent.Alarm.find_alarm|vilken tid anges min wake-up larm för? <br/> är Mina wake-up larm på? |
| Builtin.Intent.Alarm.set_alarm|Aktivera larm min väckningspaket<br/>kan du ställa in ett larm för 12 kallas ta antibiotika?|
| Builtin.Intent.Alarm.snooze|viloläge larm i 5 minuter<br/>viloläge larm|
| Builtin.Intent.Alarm.time_remaining| hur mycket längre har tillgång till ”wake-up”? <br/> hur lång tid tills min nästa larm?|
| Builtin.Intent.Alarm.turn_off_alarm | inaktivera min 7 am larm <br/> inaktivera min wake-up larm |
| Builtin.Intent.Calendar.change_calendar_entry| ändra ett möte<br/>Flytta mitt möte från dagens datum till i framtiden|
|Builtin.Intent.Calendar.check_availability|är tim upptagen fredag?<br/>är brian ledigt på lördag|
|Builtin.Intent.Calendar.connect_to_meeting|ansluta till ett möte<br/>Anslut till möte online|
|Builtin.Intent.Calendar.create_calendar_entry|Jag behöver för att schemalägga en tid med tony för fredag<br/>göra en tid med lisa klockan 2 på söndag|
|Builtin.Intent.Calendar.delete_calendar_entry|ta bort min möte<br/>ta bort möte klockan 3 imorgon från min kalender|
|Builtin.Intent.Calendar.find_calendar_entry|Visa min kalender<br/>Visa kalendern varje vecka|
|Builtin.Intent.Calendar.find_calendar_when|När är min nästa möte med jon?<br/>vilka är Mina möte med larry måndag?|
|Builtin.Intent.Calendar.find_calendar_where|Visa platsen för mitt 6 klockan nio möte<br/>var finns den möten med jon?|
|Builtin.Intent.Calendar.find_calendar_who|Vem är den här möte med?<br/>vem mer uppmanas?|
|Builtin.Intent.Calendar.find_calendar_why|Vad är information om min 11 klockan nio möte?<br/>Vad är den möten med jon om?|
|Builtin.Intent.Calendar.find_duration|hur lång tid är min nästa möte<br/>hur många minuter lång är mitt möte den här här|
|Builtin.Intent.Calendar.time_remaining|hur lång tid tills min nästa möte?<br/>hur mycket mer gång finns före mötet?|
|Builtin.Intent.Communication.add_contact|Spara det här numret och placera namn som jose<br/>Placera jason i kontakter|
|Builtin.Intent.Communication.answer_phone|svaret<br/>besvara samtalet|
|Builtin.Intent.Communication.assign_nickname|Redigera smeknamn för nickolas<br/>Lägg till ett smeknamn för john Berg|
|Builtin.Intent.Communication.call_voice_mail|röstmeddelanden<br/>anropa röstmeddelanden|
|Builtin.Intent.Communication.find_contact|Visa mina kontakter<br/>hitta kontakter|
|Builtin.Intent.Communication.forwarding_off|Stoppa vidarebefordran min anrop<br/>inaktivera vidarekoppling|
|Builtin.Intent.Communication.forwarding_on|Ange vidarekoppling att skicka anrop till telefon, hem<br/>vidarebefordra anrop till telefon, hem|
|Builtin.Intent.Communication.ignore_incoming|Svara inte på det anropet<br/>Jag vill inte nu upptagen|
|Builtin.Intent.Communication.ignore_with_message|inte besvara anropet men skicka ett meddelande i stället<br/>Ignorera och skickar tillbaka en text|
|Builtin.Intent.Communication.make_call|anropa bob och john<br/>anropa mamma och pappa|
|Builtin.Intent.Communication.press_key|Ring stjärna<br/>Tryck på 1 2 3|
|Builtin.Intent.Communication.read_aloud|Läs texten<br/>Vad hon säga i meddelandet|
|Builtin.Intent.Communication.ReDial|Ring upp igen min senaste anropet<br/>Ring upp igen|
|Builtin.Intent.Communication.send_email|e-post att vatten mike att middag förra veckan var splendid<br/>Skicka ett e-postmeddelande till bob|
|Builtin.Intent.Communication.send_text|Skicka text till bob och john<br/>meddelande|
|Builtin.Intent.Communication.speakerphone_off|ta me av talare<br/>inaktivera Högtalartelefon|
|Builtin.Intent.Communication.speakerphone_on|Högtalartelefon läge<br/>Spärra Högtalartelefon|
|Builtin.Intent.mystuff.find_attachment|hitta john för dokumentet via e-post mig igår <br/>hitta dokumentet från john|
|Builtin.Intent.mystuff.find_my_stuff|Jag vill redigera i listan från igår<br/>hitta anteckningarna kemi från September
|Builtin.Intent.mystuff.search_messages|Öppna meddelande <br/>meddelanden|
|Builtin.Intent.mystuff.transform_my_stuff|dela i listan med min make<br/>ta bort i listan|
|Builtin.Intent.ondevice.open_setting|Öppna cortana-inställningar <br/>Gå till meddelanden|
|Builtin.Intent.ondevice.pause|inaktivera musik<br/>musik av|
|Builtin.Intent.ondevice.play_music|Jag vill höra ägaren av ensamma hjärta<br/>spela upp gospel musik|
|Builtin.Intent.ondevice.recognize_song|Berätta vad den här Låt är<br/>analysera och hämta titeln på Låt|
|Builtin.Intent.ondevice.Repeat|Upprepa den här spåra<br/>spela upp den här låt igen|
|Builtin.Intent.ondevice.Resume|Starta om musik<br/>Starta musik igen|
|Builtin.Intent.ondevice.skip_back|Säkerhetskopiera en spårning<br/>tidigare Låt|
|Builtin.Intent.ondevice.skip_forward|nästa låt<br/>Gå vidare spår|
|Builtin.Intent.ondevice.turn_off_setting|Wi-Fi ut<br/>inaktivera Flygplansläge|
|Builtin.Intent.ondevice.turn_on_setting|Wi-Fi på<br/>aktivera bluetooth|
|Builtin.Intent.Places.add_favorite_place|Lägg till den här adressen i Mina favoriter<br/>Spara den här platsen i Mina favoriter|
|Builtin.Intent.Places.book_public_transportation|Köp en train-biljett<br/>Book spårvagn lyckat|
|Builtin.Intent.Places.book_taxi|Du hittar jag en väg på denna timme?<br/>hitta en taxi|
|Builtin.Intent.Places.check_area_traffic|Vad är trafik som på 520<br/>hur är trafik på i-5|
|Builtin.Intent.Places.check_into_place|Kontrollera i joya<br/>Kontrollera i här|
|Builtin.Intent.Places.check_route_traffic|Visa trafiken på sättet att kirkland<br/>hur är trafik till seattle?|
|Builtin.Intent.Places.find_place|var jag bor <br/>kan jag få upp 3 japanska hotell|
|Builtin.Intent.Places.get_address|Visa adressen guu på robson gata<br/>Vad är adressen till den närmaste starbucks?|
|Builtin.Intent.Places.get_coupon|du hitta erbjudanden på TV-apparater i min område<br/>rabatter i mountain view|
|Builtin.Intent.Places.get_distance|hur långt är helgdagar inn?<br/>Vad är avståndet till tahoe|
|Builtin.Intent.Places.get_hours|Vad är liggande del corso timmar på måndagen?<br/>När är biblioteket öppet?|
|Builtin.Intent.Places.get_menu|Visa menyn applebee's<br/>vad som finns på menyn sizzler|
|Builtin.Intent.Places.get_phone_number|Numrera för hem anläggningen<br/>Vad är telefonnumret till närmaste starbucks?|
|Builtin.Intent.Places.get_price_range|hur mycket kostar middag red hummer kostnad<br/>hur dyr är lila kafé|
|Builtin.Intent.Places.get_reviews|Visa granskning av lokala maskinvaran lagrar<br/>Du vill se restaurang granskar|
|Builtin.Intent.Places.get_route|kan jag få anvisningar till|Det går att komma åt pizza hut på fotavtryck|
|Builtin.Intent.Places.get_star_rating|hur många stjärnor har franska tvätt?<br/>är akvarium i monterrey bra?|
|Builtin.Intent.Places.get_transportation_schedule|vilken tid san francisco passagerarfartyget lämna?<br/>När är det senaste tåget Stockholm?|
|Builtin.Intent.Places.get_travel_time|Vad ska körtid denver från SA<br/>hur lång tid tar det för att komma till san francisco härifrån|
|Builtin.Intent.Places.make_call|anropa Dr. smith i bellevue<br/>anropa hem anläggningen på 1 gata|
|Builtin.Intent.Places.rate_place|ge en klassificering för den här restaurang<br/>hastigheten med vilken il fornaio 5 stjärnor på yelp|
|Builtin.Intent.Places.show_map|Vad är min aktuella plats <br/>Vad är min plats|
|Builtin.Intent.Places.takes_reservations|är det möjligt att skapa en reservation på produktiva garden<br/>accepterar galleriet reservationer|
|Builtin.Intent.Reminder.change_reminder|ändra en påminnelse<br/>Flytta upp påminnelsen bild dag 30 minuter|
|Builtin.Intent.Reminder.create_single_reminder|Påminn mig att väcka kl 7<br/>Påminn mig att gå lura eller behandlar med luca klockan 4:40|
|Builtin.Intent.Reminder.delete_reminder|ta bort den här påminnelsen<br/>ta bort påminnelsen bild|
|Builtin.Intent.Reminder.find_reminder|finns det några påminnelser ställs in för idag<br/>finns det en påminnelse för Lucas part|
|Builtin.Intent.Reminder.read_aloud|läsa påminnelse upp högt<br/>Läs den påminnelsen|
|Builtin.Intent.Reminder.snooze|viloläge som påminnelse förrän i framtiden<br/>den här påminnelsen i viloläge|
|Builtin.Intent.Reminder.turn_off_reminder|inaktivera påminnelse<br/>stänga en flygplats pickup påminnelse|
|Builtin.Intent.weather.change_temperature_unit|Ändra från fahrenheit till kelvin<br/>Ändra från fahrenheit till celsius|
|Builtin.Intent.weather.check_weather|visa prognosen för mitt kommande resa till seattle<br/>Hur kommer väder vara helgen|
|Builtin.Intent.weather.check_weather_facts|Vad är väder som i hawaii i December?<br/>Vad hette temperaturen nu förra året?|
|Builtin.Intent.weather.compare_weather|kan jag få en jämförelse mellan temperatur höga och låga värden i dallas och houston, tx<br/>hur väder Jämför med serverlicensgivarcertifikatet och nyc|
|Builtin.Intent.weather.get_frequent_locations|jag min vanligaste plats<br/>Visa oftast slutar|
|Builtin.Intent.weather.get_weather_advisory|väder varningar<br/>Visa väder advisory för sacramento|
|Builtin.Intent.weather.get_weather_maps|Visa en väder-karta<br/>Visa väder maps Afrika|
|Builtin.Intent.weather.question_weather|kommer den att dimmig imorgon morgon?<br/>måste jag shovel vinter helgen?|
|Builtin.Intent.weather.show_weather_progression|Visa lokalt väder polärdiagram<br/>Börja polärdiagram|
|Builtin.Intent.NONE|Hur gammal är du<br/>Öppna kamera|

## <a name="prebuilt-entities"></a>Fördefinierade entiteter 

Här följer några exempel på enheter som är färdiga personliga assistent programmet kan identifiera:

|Entitet |Exempel på entiteten i utterance |
|-------|------------------|
|Builtin.Alarm.alarm_state | Aktivera `off` min wake-up larm    <br/> är Mina wake-up larm `on`  | 
|Builtin.Alarm.Duration |viloläge för `10 minutes`    <br/> larmindex för viloläge `5 minutes`  |
|Builtin.Alarm.start_date | Ange ett larm för `monday` kl 7   <br/> Ange ett larm för `tomorrow` kl   |
|Builtin.Alarm.start_time | Skapa ett larm för `30 minutes`    <br/> ställa in larmet att gå `in 20 minutes`   |
|Builtin.Alarm.title | är Mina `wake up` Påminnelse aktiverad <br/> anger du ett larm för kvartalet 12 måndag-fredag kallas `take antibiotics` |
|Builtin.Calendar.absolute_location | Skapa ett möte för imorgon på `123 main street`   <br/> mötet ska äga rum `cincinnati` på den 5 för juni    |
|Builtin.Calendar.contact_name|Placera marknadsföring möte på min kalender och se till att `joe` är det <br/>Jag vill konfigurera en lunch vid il fornaio och bjuda in `paul` |   
|Builtin.Calendar.destination_calendar|Lägg till denna till min `work` schema   <br/>Placera det min `personal` kalender |
|Builtin.Calendar.Duration| Ställ in ett möte för `an hour` på ditt eget 6 <br/>  Book en `2 hour` möte med joe |  
|Builtin.Calendar.end_date | Skapa en kalender anropas semester från imorgon tills `next monday` <br/>    blockera min tiden som upptagen tills `monday, october 5th` | 
|Builtin.Calendar.end_time | möte slutar vid `5:30 PM` <br/> schema från 11 till `noon`  |   
|Builtin.Calendar.implicit_location | Avbryt möte på dmv <br/> ändra platsen för miles' födelsedagen till `poppy restaurant` |    
|Builtin.Calendar.move_earlier_time | push-mötet framåt `an hour` <br/> Flytta den tandläkartiden upp `30 minutes`       |
|Builtin.Calendar.move_later_time | Flytta Mina tandläkartiden `30 minutes`    <br/> push-mötet `an hour` |  
|Builtin.Calendar.original_start_date | examenstillfället på barber från ”tisdag' till onsdag <br/> Flytta mötet med ken från `monday` till tisdag |
|Builtin.Calendar.original_start_time | schemalägga mitt möte från `2:00` till 3  <br/> Ändra min tandläkartiden från `3:30` till 4 |  
|Builtin.Calendar.start_date | vilken tid startar mitt part på `flag day` <br/> schemalägga lunch för den `friday after next` kl 
|Builtin.Calendar.start_time | Jag vill schemalägga jobbet `this morning` <br/> Jag vill schemalägga den i den `morning` |  
|Builtin.Calendar.title | `vet appointment`  <br/> `dentist` Tisdag |
|Builtin.Communication.audio_device_type | Se anropet med hjälp av `bluetooth`  <br/> Ring upp med min `headset` | 
|Builtin.Communication.contact_name | text `bob jones`  <br/> | anropa `sarah`|
|Builtin.Communication.destination_platform | anropa dave i `london` <br/> anropa operativsystemets `work line` |    
|Builtin.Communication.from_relationship_name | Visa samtal från min `daughter` <br/> Läs e-post från `mom`   |   
|Builtin.Communication.Key | fjärråtkomst `star` <br/>  Tryck på den `hash` nyckel    |
|Builtin.Communication.Message | carly att säga e-post `i'm running late` <br/> text angus smith `good luck on your exam` |    
|Builtin.Communication.message_category | nytt e-postmeddelande som markerats för `follow up`  <br/> nytt e-postmeddelande som markerats `high priority` |    
|Builtin.Communication.message_type | Skicka ett `email`   <br/> Läs den `text` meddelanden högt |
|Builtin.Communication.Phone_Number | Jag vill ringa upp `1-800-328-9459` <br/>     anropa `555-555-5555` |   
|Builtin.Communication.relationship_name | text min `husband` <br/>  e-post `family`| 
|Builtin.Communication.slot_attribute | ändra den `recipient` <br/>    ändra den `text` | 
|Builtin.comminication.source_platform | anropa honom från `skype` <br/> anropa honom från min `personal line` |
|Builtin.mystuff.Attachment| med dokument `attached` <br/> hitta e-postmeddelandet `attachment` bob skickas |
|Builtin.mystuff.contact_name| söka efter lisa skickas till `me` <br/> där dokumentet i skickas till `susan` går kväll |
|Builtin.mystuff.data_source | `c:\dev\` <br/> Min `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|Builtin.mystuff.data_type | Leta upp den `document` i arbetat går kväll <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> Öppna Mikaels `visio diagram`  |
|Builtin.mystuff.end_date| Visa dokumenten i arbetat `between yesterday and today`   <br/> Sök efter vilken dokument i arbetade med `before thursday the 31st` |
|Builtin.mystuff.end_time| hitta filer sparas i `before noon` <br/> Sök efter vilken dokument i arbetade med `before 4pm`|      
|Builtin.mystuff.file_action| Öppna kalkylbladet i `saved` igår <br/> hitta kalkylbladet kevin `created`| 
|Builtin.mystuff.from_contact_name | hitta förslag `jason` skickas mig <br/> Öppna `isaac` 's senaste e-post |
|Builtin.mystuff.Keyword | Visa den `french conjugation` filer <br/> hitta de `marketing plan` i utformas igår |
|Builtin.mystuff.Location | Redigera dokumentet i på `work` <br/> foton i tog `paris` |
|Builtin.mystuff.message_category | Leta efter min `new` e-post <br/> Sök efter min `high priority` e-post |
|Builtin.mystuff.message_type | Kontrollera min `email` <br/>  Visa mina `text` meddelanden  |
|Builtin.mystuff.source_platform | Sök min `hotmail` e-post för e-post från john    <br/> hitta dokument i skickas från `work` |
|Builtin.mystuff.start_date | hitta anteckningar från `january` <br/> hitta skickar e-post i bo `after january 1st` |
|Builtin.mystuff.start_time | hitta den e-post i skickas bo någon gång innan 14: 00 men `after noon`? <br/> hitta kalkylbladet kristin som skickats till mig som jag redigeras `last night` | 
|Builtin.mystuff.title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|Builtin.mystuff.transform_action | `download` filen john skickas mig <br/> `open` Mina anteckningen riktlinjer dokument |
|Builtin.Note.note_text | Skapa en privata listan inklusive `pork chops, applesauce and milk` <br/> Notera att `buy milk` |
|Builtin.Note.title | Notera namnet `grocery list` <br/> Notera namnet `people to call` |
|Builtin.ondevice.music_artist_name | spela upp allt av `rufus wainwright` <br/> spela upp `garth brooks` musik |   
|Builtin.ondevice.music_genre | Visa `classic rock` låtar <br/> spela upp min `classical` musik från baroque period |
|Builtin.ondevice.music_playlist | Blanda alla britney spears från `workout` spelningslista <br/> spela upp `breakup` spelningslista
|Builtin.ondevice.music_song_name | spela upp `summertime` <br/> spela upp `me and bobby mcgee` | 
|Builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|Builtin.Places.absolute_location | Gå till skärningspunkten för `5th and pike` <br/> Nej, jag vill ha anvisningar till `1 microsoft way redmond wa 98052` |
|Builtin.Places.atmosphere | Leta efter `interesting` platser att gå    <br/> Var hittar jag en `casual` restaurang |  
|Builtin.Places.audio_device_type |anropa posten i `hands free` <br/> anropa papa john med `speakerphone` |    
|Builtin.Places.avoid_route | Undvik den `toll road` <br/> Hämta mig san francisco undvika den `construction on 101` |  
|Builtin.Places.cuisine | `halal` Tidsavg nära mountain view   <br/> `kosher` finjustering matsal på halvøen |
|Builtin.Places.Date | Skapa en reservation `next friday the 12th` <br/>  är mashiko öppen på `mondays` ? |
|Builtin.Places.discount_type | hitta en `coupon` för macy's <br/> hitta mig en `coupon` |
|Builtin.Places.Distance | finns det en bra diner `within 5 miles` av här <br/> hitta de `within 15 miles` |   
|Builtin.Places.from_absolute_location | anvisningar från `45 elm street` till startsidan <br/> Hämta mig anvisningar från `san francisco` till palo alto  |
|Builtin.Places.from_place_name | Kör från den `post office` 56 center gatuadress <br/> Hämta mig anvisningar från `home depot` till lowes |
|Builtin.Places.from_place_type | anvisningar till downtown från `work` <br/>  Hämta mig anvisningar från den `drug store` till startsidan |
|Builtin.Places.meal_type | Närliggande platser för `dinner` <br/> hitta en bra plats för ett företag `lunch` | 
|Builtin.Places.nearby | Visa vissa kall butiker `near` mig  <br/> finns det några bra Libanesiska hotell `around` här? |
|Builtin.Places.open_status | När är mall `closed` <br/> Hämta mig på `opening` timmar från arkivet | 
|Builtin.Places.place_name | Gå till `central park` <br/> Leta upp den `eiffel tower` |   
|Builtin.Places.place_type | `atms` <br/> `post office` |
|Builtin.Places.prefer_route | Visa anvisningarna av den `shortest` väg <br/> ta de `fastest` väg | 
|Builtin.Places.price_range| kan jag få platser som är `moderately affordable` <br/>  Jag vill ha en `expensive` en   |
|Builtin.Places.Product | Var hittar jag `fresh fish` runt här  <br/> var runt här säljer `bare minerals` |
|Builtin.Places.public_transportation_route | Bus-schemat för den `m2` bus <br/> Bus väg `3x` |  
|Builtin.Places.Rating | Visa `3 star` hotell <br/> Visa resultat som är `3 stars or higher`  |
|Builtin.Places.reservation_number | Boka en tabell för `seven` personer <br/>  Skapa en reservation `two` vid il fornaio |
|Builtin.Places.results_number | Visa den `10` kaféer närmast här <br/> Visa översta `3` akvarier  
|Builtin.Places.service_provided | var kan jag få `whale watch` av bus? <br/> Jag behöver en dvs till `fix my brakes` |    
|Builtin.Places.Time | Jag vill platser som är öppna på lördag kl. `8 am`| är mashiko öppen `now` |
|Builtin.Places.transportation_company | Träna scheman för `new jersey transit` <br/> Jag kan få det `bart` | 
|Builtin.Places.transportation_type | där är en musik store i får åtkomst till `on foot`? <br/>  ge mig `biking` anvisningar till mashiko|
|Builtin.Places.travel_time | Jag vill kunna enhet `less than 15 minutes` <br/>   Jag vill att någon kan jag till i `under 15 minutes` |   
|Builtin.Reminder.absolute_location | Påminn mig att anropa min pappa när i hamna i `chicago` <br/> När jag tillbaka till `seattle` Påminn mig att hämta gas |
|Builtin.Reminder.contact_name | När `bob` anrop, Påminn mig honom på skämt <br/> Skapa en påminnelse ange skola bus när jag prata med `arthur` |
|Builtin.Reminder.leaving_absolute_location | påminnelse hämta craig när du stänger `1200 main` |
|Builtin.Reminder.leaving_implicit_location | Påminn mig att hämta gas när jag lämnar `work`  |
|Builtin.Reminder.original_start_date | ändra påminnelsen om gräsmattan från `saturday` till söndag <br/> Flytta påminnelsen skolan från `monday` till tisdag   |
|Builtin.Reminder.relationship_name | När min `husband` anrop, Påminn mig att han eller hon berätta tereftalsyra möte <br/> Påminn mig igen när `mom` anrop|
|Builtin.Reminder.reminder_text | kan du Påminn mig `bring up my small spot of patchy skin` när dr smith'calls  <br/> Påminn mig att `pick up dry cleaning` på 4:40   |
|Builtin.Reminder.start_date | Påminn mig den `thursday after next` klockan 8  <br/> Påminn mig `next thursday the 18th` klockan 8    |
|Builtin.Reminder.start_time | Skapa en påminnelse `in 30 minutes` <br/> Skapa en påminnelse vattnet anläggning `this evening at 7` |  
|Builtin.weather.absolute_location | den kommer rain i `boston` <br/> Vad är en prognos `seattle`?  |
|Builtin.weather.date_range | väder i nyc `this weekend` <br/>   Leta upp den `five day` prognos hollywood florida |
|Builtin.weather.suitable_for | kan jag få `hiking` i shorts helgen?   <br/> ska det vara bra att `walk` till spel idag? | 
|Builtin.weather.temperature_unit | Vad är temperaturen idag i `kelvin`   <br/> Visa temps i `celsius` |  
|Builtin.weather.time_range | ser det ut som om det kommer Snöröjning `tonight`? <br/> är det windy höger `now`?  |
|Builtin.weather.weather_condition | Visa `precipitation` <br/> tjocklek är den `snow` på lake tahoe nu?  |

