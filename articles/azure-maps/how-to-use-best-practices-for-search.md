---
title: Hur du söker efter effektivt med Azure Maps söktjänsten | Microsoft Docs
description: Lär dig mer om bästa praxis för sökning med hjälp av Azure Maps Search-tjänst
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e60b202a32745062d2078d9721dfeba7aba69bda
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713888"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Bästa praxis att använda Azure Maps Search-tjänst

Azure Maps [söktjänst](https://docs.microsoft.com/rest/api/maps/search) innehåller API: er med olika funktioner, till exempel från adress-sökning för att söka efter punkt av intresse (POI) data över en viss plats. I den här artikeln ska vi dela bästa praxis för att anropa data via söktjänster för Azure Maps. Du lär dig hur du:

* Skapa frågor för att returnera relevanta matchningar
* Begränsa sökresultat
* Lär dig skillnaden mellan olika resultattyper
* Läsa adress search svar struktur


## <a name="prerequisites"></a>Nödvändiga komponenter

För att göra något anrop till API: er för Maps-tjänsten, behöver du en Maps-konto och nyckel. Information om att skapa ett konto och hämta en nyckel finns i [så här hanterar du ditt Azure Maps-konto och dina nycklar](how-to-manage-account-keys.md).

> [!Tip]
> Om du vill fråga söktjänsten kan du använda den [Postman-appen](https://www.getpostman.com/apps) för att skapa REST-anrop eller om du kan använda alla API-utvecklingsmiljö du föredrar.


## <a name="best-practices-for-geocoding"></a>Metodtips för Geokodning

När du söker efter en fullständig eller partiell-adress med hjälp av Azure Maps Search-tjänst tar din sökterm och returnerar koordinaterna för longitud och latitud för adressen. Den här processen kallas geokodning. Möjligheten att geokoda i ett land är beroende av väg data täckning och geokodning precisionen för tjänsten geokodning.

Se [täckning för geokodning](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) vill veta mer om funktionerna i Azure Maps geokodning efter land/region.

### <a name="limit-search-results"></a>Begränsa sökresultat

   I det här avsnittet får du lära dig hur du använder Azure Maps search API: er för att begränsa sökresultaten. 

   > [!Note]
   > Inte alla search API: er fullständigt stöd för parametrarna som visas nedan

   **GEO-bias sökresultat**

   För att geo-bias resultaten till området relevanta för dina användare, bör du alltid lägga till högsta möjliga detaljerad plats indata. Överväg att lägga till följande typer av indata för att begränsa sökresultaten:

   1. Ange den `countrySet` parameter, till exempel ”USA, FR”. Sök standardbeteendet är att söka i hela världen, potentiellt returnera onödiga resultat. Om din fråga inte innehåller `countrySet` parametern sökningen kan returnera felaktiga resultat. Till exempel söka efter en ort med namnet **Bellevue** returnera resultat från USA och Frankrike, eftersom det finns städer med namnet **Bellevue** i Frankrike och i USA.

   2. Du kan använda den `btmRight` och `topleft` parametrar för att konfigurera den avgränsar rutan för att begränsa sökningen till ett visst område på kartan.

   3. Om du vill påverka området i relevans för resultat, kan du definiera den `lat`och `lon` koordinera parametrar och ange radien för sökningen området med den `radius` parametern.


   **Fuzzy-sökning parametrar**

   1. Den `minFuzzyLevel` och `maxFuzzyLevel`, att returnera relevanta matchningar trots att frågeparametrar inte exakt motsvarar önskad information. De flesta sökfrågor som standard `minFuzzyLevel=1` och `maxFuzzyLevel=2` att få prestanda och minska ovanliga resultat. Ta ett exempel på en sökterm ”restrant”, det kan matchas med ”restaurang” när den `maxFuzzyLevel` 2 anges. Fuzzy standardnivåerna kan åsidosättas enligt begäran behov. 

   2. Du kan också ange exakt vilka resultattyper som ska returneras genom att använda den `idxSet` parametern. För detta ändamål kan du skicka kommaavgränsad lista över index, objektordningen spelar ingen roll. Följande är index som stöds:

       * `Addr` - **Adressintervall**: För vissa gator finns adress punkter som interpolerade från början och slutet av gata; dessa punkter visas som adressintervall.
       * `Geo` - **Geografiska områden**: Områden på en karta som representerar administrativa divisionen av ett land, det vill säga, land, delstat, stad.
       * `PAD` - **Adressen**:  Pekar på en karta där specifik adress med en gatunamn och nummer finns i ett index, till exempel Soquel Dr 2501. Det är den högsta nivån av precision som är tillgängliga för adresser.  
       * `POI` - **Orienteringspunkter**: Pekar på en karta som är värda att uppmärksamhet och kan vara av intresse.  [Hämta Search adressen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) inte returnera PoI.  
       * `Str` - **Gator**: Representation av gator på kartan.
       * `XStr` - **Mellan gator/skärningspunkterna**:  Representation av vägkorsningar. platser där två gator överlappar varandra.


       **Användningsexempel**:

       * idxSet = POI (endast Sök Orienteringspunkter) 

       * idxSet = PAD ADR (Sök efter adresser, PAD = adressen ADR = adressintervallet)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Omvänt geokoda och geografi filter för entitet

När du utför en sökning i omvänd geokoda med [omvänd API för webbsökning adress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), tjänsten har möjlighet att returnera polygoner för administrativa områden. Genom att ange parametern `entityType` i begäran, kan du begränsa sökningen för angivna geografi entitetstyper. Resulterande svaret innehåller geografi-ID samt den entitetstypen matchas. Om du anger mer än en entitet kan slutpunkten returnerar den **minsta entitet som är tillgängliga**. Returnerade geometri ID kan användas för att hämta den geografi via geometri [hämta Polygon service](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Exempel på begäran:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Svar:**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Resultaten sökspråk

Den `language` parametern kan du ange i vilka språk search resultat ska returneras. Om språket inte har angetts i begäran söktjänsten automatiskt som standard till det vanligaste språket i landet/regionen. När data i det angivna språket inte är tillgängligt, används också standardspråk. Se [språk som stöds](https://docs.microsoft.com/azure/azure-maps/supported-languages) en lista över språk som stöds med avseende på Azure Maps-tjänsterna efter land/region.


### <a name="predictive-mode-auto-suggest"></a>Förutsägande läge (automatiska förslag)

Du hittar fler matchningar för frågor med partiella `typeahead` parameter ska vara inställd på att ”true”. Frågan tolkas som en partiell indata och sökningen att infoga förutsägande läge. Annars förutsätter tjänsten all relevant information som har skickats i.

I exemplet frågan nedan att du kan se att sökningen tjänsten adress krävs för ”Microsoft” med den `typeahead` parameteruppsättning till **SANT**. Om du ser att svaret kan se du att söktjänsten tolkas frågan som partiell fråga och svar innehåller förslag på automatisk fråga.

**Exempelfråga:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Svar:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI-kodning för att hantera specialtecken 

Att hitta specialtecken mellan gatuadresser, det vill säga ”1: a minimering & Union gata, Seattle”, ”&” måste vara kodad innan förfrågan skickas. Vi rekommenderar kodning teckendata i en URI, där alla tecken är kodad med tecknet '%' och ett två-teckens hexadecimalt värde som motsvarar deras UTF-8-tecken.

**Användningsexempel**:

Hämta Search-adressen:

```
query=1st Avenue & E 111th St, New York
```

 skall vara kodad som:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Här är de olika metoderna ska användas för olika språk: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```C#
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Gå:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Metodtips för POI sökning

Punkter av intresse (POI) Search kan du begära POI resultaten efter namn, till exempel sökning företag efter namn. Vi rekommenderar starkt att du kan använda den `countrySet` parametern för att ange länder/regioner där ditt program behöver täckning som standard kommer att söka i hela världen, potentiellt returnera onödiga resultat och/eller leda till längre search gånger.

### <a name="brand-search"></a>Varumärke sökning

För att förbättra upplevelsen av resultaten och informationen i svaret, innehåller punkt av intresse (POI) search svaret varumärke informationen som du kan använda ytterligare att parsa svaret.

Vi gör en [POI kategorisökning](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) begäran om bensinstationer nära Microsoft campus (Redmond, WA). Om du upptäcker svaret, visas varumärke information för varje POI returneras.

**Exempelfråga:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Svar:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Sök efter en flygplats

POI Search har stöd för sökning flygplatser med officiella flygplats-koder. Till exempel **SEA** (Seattle Tacoma International Airport). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Sök i närheten

Hämta endast POI resultat runt en viss plats, den [Närliggande API för webbsökning](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) kan vara rätt val. Den här slutpunkten returnerar endast POI resultat och tar inte i en sökning-frågeparameter. För att begränsa resultaten, rekommenderar vi att du anger radien.

## <a name="understanding-the-responses"></a>Förstå svar

Låt oss göra en sökbegäran adress i Azure Maps [söktjänsten](https://docs.microsoft.com/rest/api/maps/search) i en adress i Seattle. Om du tittar noggrant på begäran-URL nedan, har vi angett den `countrySet` parameter **USA** att söka efter den i Amerikas förenta stater.

**Exempelfråga:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Vi har ytterligare en titt på hur svar nedan. Resultattyper över resultatobjekt i svaret är olika. Om du upptäcker noggrant du kan se att vi har tre olika typer av resultatobjekt, som är ”adressen”, ”gata” och ”mellan gata”. Observera att adressen sökningen inte returnerar PoI. Den `Score` parametern för varje svarsobjekt anger den relativa matchande poängen till massor av andra objekt i samma svar. Se [hämta Search adress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) vill veta mer om svar objektparametrar.

**Typer som stöds av resultat:**

* **Punkt-adress:** Pekar på en karta med specifik adress med en gatunamn och nummer. Den högsta nivån av precision som är tillgängliga för adresser. 

* **Adressintervall:**  För vissa gator finns adress punkter som interpolerade från början och slutet av gata; dessa punkter visas som adressintervall. 

* **Geografi:** Områden på en karta som representerar administrativa divisionen av ett land, det vill säga, land, delstat, stad. 

* **POI - (Orienteringspunkter):** Pekar på en karta som är värda att uppmärksamhet och kan vara av intresse.

* **Gata:** Representation av gator på kartan. Adresser som ska matchas med latitud/longitud-koordinaten för gata som innehåller adressen. Gatuadress kan inte bearbetas. 

* **Mellan gata:** Skärningspunkterna. Framställningar av vägkorsningar. platser där två gator överlappar varandra.

**Svar:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>geometri

När svarstypen är **geometri**, den kan omfatta geometri-ID som returneras i de **datakällor** objekt under ”geometri” och ”id”. Till exempel [hämta Polygon service](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) kan du begära geometridata i GeoJSON-format, till exempel en stad eller flygplatskoder kantlinje för en uppsättning entiteter. Du kan använda den här gränsen data för [Geofencing](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) eller [Sök efter Orienteringspunkter i geometrin](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Sök adress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) eller [Fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API-svar kan innehålla den **geometri ID** som returneras i objektet datakällor under ”geometri” och ”id”.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du skapar tjänstbegäranden för Azure Maps Search](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Utforska Azure Maps [Sök service API-dokumentation](https://docs.microsoft.com/rest/api/maps/search). 