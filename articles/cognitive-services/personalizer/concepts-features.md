---
title: 'Funktioner: Åtgärden och kontext - Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer använder funktioner, information om åtgärder och kontext för att göra bättre rangordning förslag. Funktioner kan vara mycket allmän eller specifika för ett objekt.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: ebe7f9307fcfa39d6cb133203a4c17243ad390c5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027140"
---
# <a name="features-are-information-about-actions-and-context"></a>Funktioner är information om åtgärder och kontext

Tjänsten Personalizer fungerar genom att lära dig vad ditt program bör visas för användarna i ett visst sammanhang.

Personalizer använder **funktioner**, vilket är information om den **kontext** att välja bäst **åtgärd**. Funktionerna representerar all information som du tror kan hjälpa dig att anpassa för att uppnå högre belöningar. Funktioner kan vara mycket allmän eller specifika för ett objekt. 

Du kan till exempel ha en **funktionen** om:

* Den _användaren_ som en `UserID`. 
* Den _innehåll_ till exempel om en video är en `Documentary`, ett `Movie`, eller en `TV Series`, eller om ett retail-objekt är tillgängliga i store.
* Den _aktuella_ period tid som det är till exempel vilken dag i veckan.

Personalizer föreskriver, begränsa eller åtgärda vilka funktioner som du kan skicka för åtgärder och kontext:

* Du kan skicka vissa funktioner för vissa åtgärder och inte för andra, om du inte har något. TV-serie kan exempelvis ha filmer inte har attribut.
* Du kanske vissa funktioner som är tillgängliga bara några gånger. En mobilapp kan till exempel ge mer än en webbsida. 
* Med tiden kan du lägga till och ta bort funktioner om kontext och åtgärder. Personalizer fortsätter att lära sig från tillgänglig information.
* Det måste finnas minst en funktion för kontexten. Personalizer stöder inte en tom kontext. Om du skickar bara en fast kontext varje gång, väljer Personalizer åtgärden för rangordning endast om funktionerna i åtgärderna. 
* Personalizer försöker att välja åtgärder som passar bäst för alla när som helst.

## <a name="supported-feature-types"></a>Typer av funktion som stöds

Personalizer har stöd för funktionerna i sträng, numeriska och booleska typer.

Funktioner som inte finns ska utelämnas från begäran. Undvika att skicka funktioner med ett null-värde eftersom det kommer att behandlas som befintlig och med värdet ”null” vid utbildning av modellen.

## <a name="categorize-features-with-namespaces"></a>Kategorisera funktioner med namnområden

Personalizer tar funktioner som är ordnade i namnområden. Du avgör i ditt program, om namnområden används och vad de ska vara. Namnområden använder för att gruppera funktioner om ett liknande ämne eller funktioner som kommer från en viss källa.

Här följer några exempel på funktionen namnområden som används av program:

* User_Profile_from_CRM
* Tid
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Väder
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Du kan kalla funktionen namnområden efter egna konventioner så länge de är giltig JSON-nycklarna.

I följande JSON `user`, `state`, och `device` är funktionen namnområden.

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
                "Windows":true
            }
        }
    ]
}
```

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Så här gör funktionen anger effektivare för Personalizer

En bra funktionsuppsättningen hjälper Personalizer Lär dig att förutse vad som kommer att öka den högsta trafik. 

Överväg att funktioner för att skicka till Personalizer rangordning API som följer de här rekommendationerna:

* Det finns tillräckligt med funktioner till enheten anpassning. Ju fler mål exakt content måste vara, fler funktioner krävs.

* Det finns tillräckligt med funktionerna i olika *densitet*. En funktion är *kompakta* om många objekt som är grupperade i några buckets. Till exempel tusentals videor kan klassificeras som ”långt” (under 5 minuter långa) och ”korta” (under 5 minuter långa). Det här är en *mycket kompakta* funktionen. Å andra sidan kan samma tusentals objekt ha ett attribut som kallas ”Title” som nästan aldrig har samma värde från ett objekt till en annan. Det här är en mycket icke-dense eller *null-optimerade* funktionen.  

Med funktioner med hög densitet hjälper Personalizer extrapolera learning från ett objekt till en annan. Men om det finns bara ett par funktioner och de är för kompakta, Personalizer försöker exakt mål innehåll med bara några få bucketarna att välja mellan.

### <a name="improve-feature-sets"></a>Förbättra funktioner 

Analysera användarnas beteende genom att göra en Offline-utvärdering. På så sätt kan du titta på senaste data och se vad funktioner kraftigt bidrar till positivt fördelar jämfört med de som bidrar mindre. Du kan se vilka funktioner hjälper och det är upp till dig och ditt program för att hitta bättre funktioner att skicka till Personalizer att förbättra resultatet ytterligare.

De följande är vanliga metoder för att förbättra funktioner som skickas till Personalizer.

#### <a name="make-features-more-dense"></a>Göra funktioner mer kompakta

Det är möjligt att förbättra din funktionsuppsättningarna genom att redigera dem för att göra dem större och mer eller mindre kompakta.

Till exempel är en tidsstämpel till andra en mycket begränsad funktion. Det kan göras mer kompakta (gäller från) genom att klassificera gånger i ”morgon”, ”dag”, ”eftermiddagen” osv.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Expandera funktionsuppsättningar med extrapolerade information

Du kan också få fler funktioner genom att tänka på nytt attribut som kan härledas från information som du redan har. Till exempel i en lista över anpassning fiktiva film är du det möjliga som en helg vs vardag framkalla olika beteenden från användare? Tid kan utökas för att ha ett ”lördag” eller ”veckodag”-attribut. Kulturella helgdagar enhet uppmärksam på vissa typer av film? Attributet ”Halloween” är till exempel användbart på platser där det är relevant. Är det möjligt att regn väder har stor inverkan på valet av en film för många användare? En väder-tjänst kan ange att information och du kan lägga till den som en extra funktion för med tid och plats. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Expandera funktionsuppsättningar med artificiell intelligens och cognitive services

Artificiell intelligens och redo att köra Cognitive Services kan vara en mycket kraftfull utöver Personalizer. 

Förbearbeta dina objekt som använder artificiell intelligens tjänster för att automatiskt extrahera information som sannolikt kommer att vara relevanta för anpassning.

Exempel:

* Du kan köra en filmfilen via [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) att extrahera scen element, text, känsla och många andra attribut. Dessa attribut kan sedan göras mer kompakta att återspegla egenskaper som den ursprungliga objektmetadata hade. 
* Bilder kan köras via objektidentifiering, ansikten via sentiment, osv.
* Informationen i text kan förstärkas genom att extrahera entiteter, sentiment, expandera entiteter med Bing knowledge graph och så vidare.

Du kan använda flera andra [Azure Cognitive Services](https://www.microsoft.com/cognitive-services), till exempel

* [Entitetslänkning](../entitylinking/home.md)
* [Textanalys](../text-analytics/overview.md)
* [Igenkänning](../emotion/home.md)
* [Visuellt innehåll](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Åtgärder som representerar en lista med alternativ

Varje åtgärd:

* Har ett ID.
* Har en lista över funktioner.
* Lista över funktioner kan vara stor (hundratals) men rekommenderar vi att utvärdera funktionen effektivitet för att ta bort funktioner som inte är bidrar till att hämta alla. 
* Funktioner i den **åtgärder** kanske eller kanske inte alla samband med funktioner i den **kontext** används av Personalizer.
* Funktioner för åtgärder som kan finnas i vissa åtgärder och inte andra. 
* Funktioner för en viss åtgärds-ID kan vara tillgänglig en dag, men senare på blir otillgänglig. 

Personalizers maskininlärningsalgoritmer ger bättre prestanda när det finns stabila funktioner, men misslyckas inte rangordnas anrop om funktionen ställer in ändringar över tid.

Skicka inte i mer än 50 åtgärder när rangordning åtgärder. Dessa kan vara samma 50 åtgärder varje gång eller de kan förändras. Till exempel, om du har en produktkatalog över 10 000 objekt för en e-handelsprogram kan kan du använda en rekommendation eller filtrering-motorn för att fastställa upp 40 kan en kund som och använda Personalizer för att hitta det som genererar de flesta utmärkelse (till exempel användaren läggs till i korgen) för den aktuella kontexten.


### <a name="examples-of-actions"></a>Exempel på åtgärder

De åtgärder som du skickar till API: et rangordning beror på vad du vill anpassa.

Här följer några exempel:

|Syfte|Åtgärd|
|--|--|
|Anpassa vilka artikel är markerad på en webbplats med nyheter.|Varje åtgärd är en potentiell nyhetsartikel.|
|Optimera ad placering på en webbplats.|Varje åtgärd är en layout eller regler för att skapa en layout för annonser (till exempel på överkanten på rätt, små bilderna, stora bilder).|
|Visa personligt anpassade rangordning rekommenderade objekt på en perioder webbplats.|Varje åtgärd är en specifik produkt.|
|Föreslå element för användargränssnitt, till exempel filter som används i ett specifikt foto.|Varje åtgärd kan vara ett annat filter.|
|Välj en chattrobot svar på förtydliga användaravsikt eller föreslå en åtgärd.|Varje åtgärd är ett alternativ för hur du tolkar svaret.|
|Välj vad som ska visas överst i en lista över sökresultat|Varje åtgärd är en av övre några sökresultaten.|


### <a name="examples-of-features-for-actions"></a>Exempel på funktioner för åtgärder

Här följer några bra exempel på funktioner för åtgärder. Dessa beror mycket på varje program.

* Funktioner med egenskaper av åtgärder. Till exempel är det en film eller en serie?
* Funktioner om hur användare kan har interagerat med den här åtgärden i förflutna. Exempelvis kan den här filmen är främst ses av personer i demografi A eller B, det är vanligtvis speldagar mer än en gång.
* Funktioner vilka egenskaper som hur användaren *ser* åtgärderna. Till exempel fungerar affisch för filmen visas i miniatyr inkludera ansikten, bilar eller landskap?

### <a name="load-actions-from-the-client-application"></a>Läsa in åtgärder från klientprogrammet

Funktioner från åtgärder kan normalt komma från innehållshanteringssystem, kataloger och rekommenderare system. Programmet ansvarar för information om åtgärderna som lästes in från relevanta databaser och system som du har. Om dina åtgärder inte ändra eller hämta dem inläst varje gång har en onödig inverkan på prestanda, kan du lägga till logik i din app för att cachelagra den här informationen.

### <a name="prevent-actions-from-being-ranked"></a>Förhindra åtgärder från som rangordnas

I vissa fall kan finns det åtgärder som du inte vill ska visas för användarna. Det bästa sättet att förhindra att en åtgärd från som rangordnas som översta ska inte inkludera den i åtgärdslistan rangordning-API: et i första hand.

I vissa fall kan det kan bara fastställas senare i din affärslogik om en som är resultatet _åtgärd_ anrop ska visas för en användare av en rangordning API. För dessa fall bör du använda _inaktiva händelser_.

## <a name="json-format-for-actions"></a>JSON-format för åtgärder

När du anropar rankning, skickar du flera åtgärder för att välja mellan:

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
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
          "spiceLevel": "none"
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
          "spiceLevel": "none"
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
          "spiceLevel": "low"
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

Information för den _kontext_ beror på varje program och Använd fall, men det vanligtvis kan innehålla information som:

* Demografi och profilen information om dina användare.
* Information som extraherats från HTTP-huvuden, till exempel användaragenten eller härleds från HTTP-information, till exempel omvänd geografiska uppslag baserat på IP-adresser.
* Information om den aktuella tiden, till exempel dag i veckan, helgen eller inte, morgon eller eftermiddagen, Rean eller inte osv.
* Information som extraherats från mobila program, till exempel plats, flytt eller batterinivå.
* Historiska aggregeringar av beteendet för användare – till exempel vilka är film genrer den här användaren har visat mest.

Programmet ansvarar för att läsa in information om kontexten från relevanta databaser, sensorer och system som du kan ha. Om din kontextinformation inte ändras du lägga till logik i din app för att cachelagra den här informationen innan den skickas till API: et för rangordning.

## <a name="json-format-for-context"></a>JSON-format för kontext 

Kontext uttrycks som ett JSON-objekt som ska skickas till API: et rangordning:

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
                "Windows":true
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

[Förstärkande inlärning](concepts-reinforcement-learning.md) 