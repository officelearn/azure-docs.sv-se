---
title: 'Funktioner: Action och sammanhang - Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer använder funktioner, information om åtgärder och sammanhang, för att göra bättre ranking förslag. Funktioner kan vara mycket generiska, eller specifika för ett objekt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 408501232891a7971d03c89acc647d9ed19609b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77026157"
---
# <a name="features-are-information-about-actions-and-context"></a>Funktioner är information om åtgärder och sammanhang

Personalizer-tjänsten fungerar genom att lära dig vad ditt program ska visa för användare i ett visst sammanhang.

Personalizer använder **funktioner**, vilket är information om den **aktuella kontexten** för att välja den bästa **åtgärden**. Funktionerna representerar all information du tror kan hjälpa till att anpassa för att uppnå högre belöningar. Funktioner kan vara mycket generiska, eller specifika för ett objekt. 

Du kan till exempel ha en **funktion** om:

* _Användarpersonen,_ till `Sports_Shopper`exempel en . Detta bör inte vara ett enskilt användar-ID. 
* _Innehållet,_ till exempel om `Documentary`en `Movie`video är `TV Series`en , en eller en , eller om ett butiksobjekt är tillgängligt i butik.
* Den _aktuella_ tidsperioden, till exempel vilken dag i veckan det är.

Personalizer föreskriver inte, begränsar eller fixar inte vilka funktioner du kan skicka för åtgärder och sammanhang:

* Du kan skicka vissa funktioner för vissa åtgärder och inte för andra, om du inte har dem. Tv-serier kan till exempel ha attribut som filmer inte har.
* Du kanske bara har vissa funktioner tillgängliga vissa gånger. Ett mobilprogram kan till exempel ge mer information än en webbsida. 
* Med tiden kan du lägga till och ta bort funktioner om kontext och åtgärder. Personalizer fortsätter att lära av tillgänglig information.
* Det måste finnas minst en funktion för sammanhanget. Personalizer stöder inte en tom kontext. Om du bara skickar en fast kontext varje gång väljer Personalizer åtgärden för rankningar endast när det gäller funktionerna i åtgärderna.
* För kategoriska funktioner behöver du inte definiera möjliga värden och du behöver inte fördefiniera intervall för numeriska värden.

## <a name="supported-feature-types"></a>Funktionstyper som stöds

Personalizer stöder funktioner i sträng-, numeriska och booleska typer.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Hur val av funktionstyp påverkar Maskininlärning i Personalizer

* **Strängar**: För strängtyper skapar varje kombination av nyckel och värde nya vikter i personalizermaskininlärningsmodellen. 
* **Numerisk :** Du bör använda numeriska värden när talet proportionellt ska påverka anpassningsresultatet. Detta är mycket scenario beroende. I ett förenklat exempel t.ex. Funktioner som baseras på numeriska enheter men där innebörden inte är linjär - till exempel Ålder, Temperatur eller Personhöjd - är bäst kodade som strängar, och funktionens kvalitet kan vanligtvis förbättras med hjälp av intervall. Ålder kan till exempel kodas som "Ålder":"0-5", "Ålder":"6-10" etc.
* **Booleska** värden som skickas med värdet "falska" fungerar som om de inte hade skickats alls.

Funktioner som inte finns bör utelämnas från begäran. Undvik att skicka funktioner med ett null-värde, eftersom det kommer att bearbetas som befintliga och med värdet "null" när du tränar modellen.

## <a name="categorize-features-with-namespaces"></a>Kategorisera funktioner med namnområden

Personalizer tar in funktioner som är organiserade i namnområden. Du bestämmer i ditt program om namnområden används och vad de ska vara. Namnområden används för att gruppera funktioner om ett liknande ämne eller funktioner som kommer från en viss källa.

Följande är exempel på funktionsnamnområden som används av program:

* User_Profile_from_CRM
* Tid
* Mobile_Device_Info
* http_user_agent
* VideoUpplösning
* AnvändareDeviceInfo
* Väder
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Du kan namnge namnområden för funktionen efter dina egna konventioner så länge de är giltiga JSON-nycklar. Namnområden används för att ordna funktioner i olika uppsättningar och för att disambiguate funktioner med liknande namn. Du kan tänka dig namnområden som ett "prefix" som läggs till i funktionsnamn. Namnområden kan inte kapslas.


I följande `user`JSON, `state`, `device` och är funktionsnamnområden. 

> [!Note]
> För närvarande rekommenderar vi starkt att du använder namn för funktionsnamnområden som är UTF-8-baserade och börjar med olika bokstäver. Till exempel `user` `state`, `device` och `u`börja `s`med `d`, och . För närvarande har namnområden med samma första tecken kan resultera i kollisioner i index som används för maskininlärning.

JSON-objekt kan innehålla kapslade JSON-objekt och enkla egenskaps-/värden. En matris kan bara inkluderas om matrisobjekten är tal. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Begränsningar i teckenuppsättningar för namnområden

Strängen som du använder för att namnge namnområdet måste följa vissa begränsningar: 
* Det kan inte vara unicode.
* Du kan använda några av de utskrivbara symbolerna med koder < 256 för namnområdesnamnen. 
* Du kan inte använda symboler med koder < 32 (ej utskrivbara), 32 (mellanslag), 58 (kolon), 124 (rör) och 126–140.
* Det bör inte börja med ett understreck "_" eller funktionen kommer att ignoreras.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Hur man gör funktionsuppsättningar mer effektiva för Personalizer

En bra uppsättning funktioner hjälper Personalizer lära sig att förutsäga den åtgärd som kommer att driva den högsta belöningen. 

Överväg att skicka funktioner till Personalizer Rank API som följer dessa rekommendationer:

* Det finns tillräckligt med funktioner för att driva anpassning. Ju mer exakt riktade innehållet måste vara, desto fler funktioner behövs.

* Det finns tillräckligt med funktioner i olika *densiteter*. En funktion är *tät* om många objekt är grupperade i några buckets. Till exempel kan tusentals videor klassificeras som "Lång" (över 5 min lång) och "Kort" (under 5 min lång). Detta är en *mycket tät* funktion. Å andra sidan kan samma tusentals objekt ha ett attribut som kallas "Titel", som nästan aldrig kommer att ha samma värde från ett objekt till ett annat. Detta är en mycket icke-tät eller *gles* funktion.  

Med funktioner med hög densitet hjälper Personalizer extrapolera lärande från ett objekt till ett annat. Men om det bara finns ett fåtal funktioner och de är för täta, kommer Personalizer försöka exakt rikta innehåll med bara några hinkar att välja mellan.

### <a name="improve-feature-sets"></a>Förbättra funktionsuppsättningar 

Analysera användarbeteendet genom att göra en offlineutvärdering. På så sätt kan du titta på tidigare data för att se vilka funktioner som kraftigt bidrar till positiva belöningar jämfört med de som bidrar mindre. Du kan se vilka funktioner som hjälper, och det kommer att vara upp till dig och din ansökan att hitta bättre funktioner att skicka till Personalizer för att förbättra resultaten ytterligare.

Följande avsnitt är vanliga metoder för att förbättra funktioner som skickas till Personalizer.

#### <a name="make-features-more-dense"></a>Gör funktionerna mer täta

Det är möjligt att förbättra dina funktionsuppsättningar genom att redigera dem för att göra dem större och mer eller mindre täta.

En tidsstämpel ner till den andra är till exempel en mycket gles funktion. Det kan göras tätare (effektiv) genom att klassificera tider i "morgon", "middag", "eftermiddag", etc.

Platsinformation drar också vanligtvis nytta av att skapa bredare klassificeringar. Till exempel är en Latitude-Longitud-koordinat som Lat: 47.67402° N, Long: 122.12154° W för exakt och tvingar modellen att lära sig latitud och longitud som distinkta dimensioner. När du försöker anpassa baserat på platsinformation hjälper det att gruppera platsinformation i större sektorer. Ett enkelt sätt att göra det är att välja en lämplig avrundningsprecision för Lat-Long-talen och kombinera latitud och longitud till "områden" genom att göra dem till en sträng. Till exempel, ett bra sätt att representera 47.67402° N, Lång: 122.12154° W i regioner cirka några kilometer bred skulle vara "location":"34.3 , 12.1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Utöka funktionsuppsättningar med extrapolerad information

Du kan också få fler funktioner genom att tänka på outforskade attribut som kan härledas från information som du redan har. Till exempel, i en fiktiv filmlista anpassning, är det möjligt att en helg vs vardag framkallar olika beteende från användarna? Tiden kan utökas för att ha ett "helg" eller "veckodag"-attribut. Har nationella kulturella helgdagar driva uppmärksamheten på vissa filmtyper? Ett "Halloween"-attribut är till exempel användbart på platser där det är relevant. Är det möjligt att regnigt väder har stor inverkan på valet av en film för många människor? Med tid och plats kan en vädertjänst tillhandahålla den informationen och du kan lägga till den som en extra funktion. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Utöka funktionsuppsättningar med artificiell intelligens och kognitiva tjänster

Artificiell intelligens och färdiga kognitiva tjänster kan vara ett mycket kraftfullt tillägg till Personalizer. 

Genom att förbehandla dina objekt med hjälp av artificiella underrättelsetjänster kan du automatiskt extrahera information som sannolikt är relevant för anpassning.

Ett exempel:

* Du kan köra en filmfil via [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) för att extrahera scenelement, text, sentiment och många andra attribut. Dessa attribut kan sedan göras tätare för att återspegla egenskaper som det ursprungliga objektet metadata inte hade. 
* Bilder kan köras genom objektidentifiering, ansikten genom känsla, etc.
* Information i text kan utökas genom att extrahera entiteter, sentiment, expanderande entiteter med Bing kunskapsdiagram, etc.

Du kan använda flera andra [Azure Cognitive Services,](https://www.microsoft.com/cognitive-services)till exempel

* [Länkning av entiteter](../entitylinking/home.md)
* [Textanalys](../text-analytics/overview.md)
* [Känsla](../emotion/home.md)
* [Datorseende](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Åtgärder representerar en lista med alternativ

Varje åtgärd:

* Har _event_ ett händelse-ID. Om du redan har ett händelse-ID bör du skicka in det. Om du inte har ett händelse-ID ska du inte skicka ett, Personalizer skapar ett åt dig och returnerar det som svar på Rank-begäran. ID:et är associerat med händelsen Rank, inte användaren. Om du skapar ett ID fungerar ett GUID bäst. 
* Har en lista med funktioner.
* Listan över funktioner kan vara stor (hundratals) men vi rekommenderar att du utvärderar funktionens effektivitet för att ta bort funktioner som inte bidrar till att få belöningar. 
* Funktionerna i **åtgärderna** kan ha eller inte har någon korrelation med funktioner i det **sammanhang** som används av Personalizer.
* Funktioner för åtgärder kan finnas i vissa åtgärder och inte andra. 
* Funktioner för ett visst åtgärds-ID kan vara tillgängliga en dag, men senare blir de otillgängliga. 

Personalizers maskininlärningsalgoritmer kommer att fungera bättre när det finns stabila funktionsuppsättningar, men Rank-anrop misslyckas inte om funktionsuppsättningen ändras med tiden.

Skicka inte in mer än 50 åtgärder när Rankningsåtgärder. Det kan vara samma 50 åtgärder varje gång, eller så kan de ändras. Om du till exempel har en produktkatalog med 10 000 artiklar för ett e-handelsprogram kan du använda en rekommendations- eller filtreringsmotor för att avgöra vilka 40 bästa en kund kanske gillar och använda Personalizer för att hitta den som genererar mest belöning (till exempel lägger användaren till i varukorgen) för den aktuella kontexten.


### <a name="examples-of-actions"></a>Exempel på åtgärder

Vilka åtgärder du skickar till Rank API beror på vad du försöker anpassa.

Här följer några exempel:

|Syfte|Åtgärd|
|--|--|
|Anpassa vilken artikel som är markerad på en nyhetswebbplats.|Varje åtgärd är en potentiell nyhetsartikel.|
|Optimera annonsplaceringen på en webbplats.|Varje åtgärd kommer att vara en layout eller regler för att skapa en layout för annonserna (till exempel högst upp, till höger, små bilder, stora bilder).|
|Visa personlig rangordning av rekommenderade objekt på en shoppingwebbplats.|Varje åtgärd är en specifik produkt.|
|Föreslå element i användargränssnittet, till exempel filter som ska tillämpas på ett visst foto.|Varje åtgärd kan vara ett annat filter.|
|Välj en chattrobots svar för att klargöra användarnas avsikt eller föreslå en åtgärd.|Varje åtgärd är ett alternativ för hur du tolkar svaret.|
|Välj vad som ska visas högst upp i en lista med sökresultat|Varje åtgärd är ett av de få första sökresultaten.|


### <a name="examples-of-features-for-actions"></a>Exempel på funktioner för åtgärder

Följande är goda exempel på funktioner för åtgärder. Dessa beror mycket på varje program.

* Funktioner med egenskaper hos åtgärderna. Till exempel, är det en film eller en TV-serie?
* Funktioner om hur användare kan ha interagerat med den här åtgärden tidigare. Den här filmen ses till exempel mest av personer i demografin A eller B, den spelas vanligtvis inte mer än en gång.
* Funktioner om egenskaperna hos hur användaren *ser* åtgärderna. Till exempel innehåller affischen för filmen som visas i miniatyren ansikten, bilar eller landskap?

### <a name="load-actions-from-the-client-application"></a>Läsa in åtgärder från klientprogrammet

Funktioner från åtgärder kan vanligtvis komma från innehållshanteringssystem, kataloger och rekommenderare system. Din ansökan ansvarar för att läsa in information om åtgärderna från relevanta databaser och system du har. Om dina åtgärder inte ändras eller får dem inlästa varje gång har en onödig inverkan på prestanda, kan du lägga till logik i ditt program för att cachelagra den här informationen.

### <a name="prevent-actions-from-being-ranked"></a>Förhindra att åtgärder rangordnas

I vissa fall finns det åtgärder som du inte vill visa för användarna. Det bästa sättet att förhindra att en åtgärd rankas som högst rankade är att inte inkludera den i åtgärdslistan till Rank API i första hand.

I vissa fall kan det bara fastställas senare i affärslogiken om en resulterande _åtgärd_ för ett Rank API-anrop ska visas för en användare. I dessa fall bör du använda _inaktiva händelser_.

## <a name="json-format-for-actions"></a>JSON-format för åtgärder

När du ringer Rank skickar du flera åtgärder att välja mellan:

JSON-objekt kan innehålla kapslade JSON-objekt och enkla egenskaps-/värden. En matris kan bara inkluderas om matrisobjekten är tal. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Exempel på kontextinformation

Informationen för _sammanhanget_ beror på varje program och användningsfall, men det kan vanligtvis innehålla information som:

* Demografisk information och profilinformation om din användare.
* Information som extraherats från HTTP-huvuden, till exempel användaragent, eller härledd från HTTP-information, till exempel omvända geografiska sökningar baserat på IP-adresser.
* Information om aktuell tid, till exempel veckodag, helg eller inte, morgon eller eftermiddag, semesterperioden eller inte, etc.
* Information som extraheras från mobila applikationer, till exempel plats, rörelse eller batterinivå.
* Historiska aggregat av beteendet hos användare - till exempel vilka är de filmgenrer som den här användaren har sett mest.

Din ansökan ansvarar för att läsa in information om sammanhanget från relevanta databaser, sensorer och system som du kan ha. Om kontextinformationen inte ändras kan du lägga till logik i programmet för att cachelagra den här informationen innan du skickar den till Rank API.If your context information doesn't change, you can add logic in your application to cache this information, before sending it to the Rank API.

## <a name="json-format-for-context"></a>JSON-format för kontext 

Kontexten uttrycks som ett JSON-objekt som skickas till Rank API:

JSON-objekt kan innehålla kapslade JSON-objekt och enkla egenskaps-/värden. En matris kan bara inkluderas om matrisobjekten är tal. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

[Kunskapsförmedling](concepts-reinforcement-learning.md) 
