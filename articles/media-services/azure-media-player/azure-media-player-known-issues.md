---
title: Kända problem med Azure Media Player
description: Den aktuella versionen har följande kända problem.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727221"
---
# <a name="known-issues"></a>Kända problem #

Den aktuella versionen har följande kända problem:

## <a name="azure-media-player"></a>Azure Media Player ##

- Felaktigt konfigurerade kodare kan orsaka problem med uppspelning
- Strömmar med tidsstämplar som är större än 2^53 kan ha uppspelningsproblem.
  - Begränsning: Använd 90 kHz-video och 44,1 kHz ljudtidsskalor
- Inget spela automatiskt på mobila enheter utan användarinteraktion. Den är blockerad av plattformen.
- Om du söker nära avbrott kan det leda till att uppspelningen misslyckas.
- Hämtning av stora presentationer kan leda till att användargränssnittet låss.
- Det går inte att automatiskt spela upp samma källa igen efter att presentationen avslutats.
  - För att spela upp en källa igen, måste den ställa in källan igen.
- Tomma manifest kan orsaka problem med spelaren.
  - Det här problemet kan uppstå när du startar en livestream och inte tillräckligt många bitar finns i manifestet.
- Uppspelningsposition kanske utanför UI seekbar.
- Händelsebeställning är inte konsekvent för alla tekniker.
- Buffrad egenskap är inte konsekvent mellan tekniker.
- nativeControlsForTouch måste vara falskt (standard) för att undvika "Objektet stöder inte egenskap eller metod 'setControls'"
- Affischer måste nu vara absoluta webbadresser
- Mindre estetiska problem kan uppstå i användargränssnittet när du använder enhetens högkontrastläge
- Webbadresser som innehåller "%" eller "+" i den helt avkodade strängen kan ha problem med att ange källan

## <a name="ad-insertion"></a>Insättning av annons ##

- Annonser kan ha problem som infogas (på begäran eller live) när en annonsblockerare installeras i webbläsaren
- Mobila enheter kan ha problem med att spela upp annonser.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- I DVR-fönstret med liveinnehåll fortsätter tidslinjen att växa tills du söker till området eller når slutet av presentationen.
- Live presentationer i Firefox med MSE aktiverat har vissa problem
- Tillgångar som bara är ljud eller video spelas inte upp via AzureHtml5JS-tekniken.
  - Om du vill spela upp resurser utan ljud eller video kan du göra det genom att infoga tomt ljud eller video med [verktyget Azure Media Services Explorer](https://aka.ms/amse)
    - Instruktioner om hur du sätter in tyst ljud finns [här](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>Flash ##

- AES-innehåll spelas inte upp i Flash version 30.0.0.134 på grund av en bugg i Adobes cachelagringslogik. Adobe har åtgärdat problemet och släppt den i 30.0.0.154
- Tech och http-fel (till exempel 404 nätverkstidsutskrifter) tar det längre tid för spelaren att återställa än andra tekniker.
- Klicka på videoområde med flashSS tech kommer inte att spela / pausa spelaren.
- Om användaren har Flash installerat men inte ger tillstånd att ladda den på webbplatsen, kan oändlig spinning uppstå. Detta beror på att spelaren tror att plugin är installerat och tillgängligt och det tror att plugin kör innehållet. JavaScript-kod har skickats men webbläsarinställningarna har blockerat insticksprogrammet från att köra tills användaren accepterar uppmaningen att tillåta plugin. Detta kan inträffa i alla webbläsare.  

## <a name="silverlight"></a>Silverlight ##

- Funktioner som saknas
- Tech och http-fel (till exempel 404 nätverkstidsutskrifter) tar det längre tid för spelaren att återställa än andra tekniker.
- Safari och Firefox på Mac-uppspelning med `"http://` `https://` Silverlight kräver att uttryckligen definiera eller för källan.
- Om ett API saknas för den här tekniken returneras null.
- Om användaren har Flash installerat men inte ger tillstånd att ladda den på webbplatsen, kan oändlig spinning uppstå. Detta beror på att spelaren tror att plugin är installerat och tillgängligt och det tror att plugin kör innehållet. JavaScript-kod har skickats men webbläsarinställningarna har blockerat insticksprogrammet från att köra tills användaren accepterar uppmaningen att tillåta plugin. Detta kan inträffa i alla webbläsare.  

## <a name="native-html5"></a>Inbyggd HTML5 ##

- Html5 tech är bara att skicka canplaythrough händelse för första uppsättning källa.
- Denna teknik stöder bara vad webbläsaren har genomfört.  Vissa funktioner kan saknas i den här tekniken.  
- Om ett API saknas för den här tekniken returneras null.
- Det finns problem med bildtexter och undertexter på denna teknik. De kanske eller kanske inte är tillgängliga eller kan visas på denna teknik.
- Med begränsad bandbredd i HLS/ Html5 tech scenario resulterar i ljuduppspelning utan video.

### <a name="microsoft"></a>Microsoft ###

- IE8-uppspelning fungerar för närvarande inte på grund av inkompatibilitet med ECMAScript 5
- I IE och vissa versioner av Edge kan helskärm inte anges genom att tabb till knappen och välja den eller använda F/f-snabbnyckeln.

## <a name="google"></a>Google ##

- Flera kodningsprofiler i samma manifest har vissa uppspelningsproblem i Chrome och rekommenderas inte.
- Chrome kan inte spela upp HE-AAC med AzureHtml5JS. Följ information om [felspåraren](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- Från och med Chrome v58 måste widevine-innehåll laddas/spelas upp via https://-protokollet annars misslyckas uppspelningen.

## <a name="mozilla"></a>Mozilla ##

- Ljudströmsväxeln kräver att ljudströmmar har samma codec-privata data när du använder AzureHtml5JS. Firefox-plattformen kräver detta.

## <a name="apple"></a>Apple ##

- Safari på Mac möjliggör ofta Energisparläge som standard med inställningen "Stop plug-ins to save power", som blockerar plugins som Flash och Silverlight när de tror att det inte är för användaren. Detta block blockerar inte plugin's existent, bara funktioner. Med tanke på standardteknikenOrder kan detta orsaka problem när du försöker spela upp
  - Begränsning 1: Om videospelaren är "fram- och mitten" (inom en gräns på 3 000 x 3 000 pixlar som börjar i det övre vänstra hörnet av dokumentet), bör den fortfarande spelas upp.
  - Begränsning 2: Ändra techOrder för Safari vara ["azureHtml5JS", "html5"]. Den här begränsningen har en konsekvens av att inte få alla funktioner som är tillgängliga i FlashSS-tekniken.
- PlayReady-innehåll via Silverlight kan ha problem med att spela upp i Safari.
- AES och begränsat tokeninnehåll spelas inte upp med iOS och äldre Android-enheter.
  - För att uppnå det här scenariot måste en proxy läggas till i din tjänst.
- Standardskal för iOS Phone visas igenom.
- iPhone-uppspelning sker alltid i den inbyggda spelarens helskärm.
  - Funktioner, inklusive bildtexter, kanske inte finns kvar i den här icke-infogade uppspelningen.
- När livepresentationen avslutas kommer iOS-enheter inte att avsluta presentationen korrekt.
- iOS tillåter inte DVR-funktioner.
- iOS audio stream switch kan bara göras om iOS native player UI och kräver ljudströmmar för att ha samma codec privata data
- Äldre versioner av Safari kan eventuellt ha problem med att spela upp livestreamar.

## <a name="older-android"></a>Äldre Android ##

- AES och begränsat tokeninnehåll spelas inte upp med iOS och äldre Android-enheter.
  - För att uppnå det här scenariot måste en proxy läggas till i din tjänst.

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)