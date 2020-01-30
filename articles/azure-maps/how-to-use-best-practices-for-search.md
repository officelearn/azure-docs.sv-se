---
title: Sök effektivt med Azure Maps Search Service | Microsoft Azure Maps
description: Lär dig hur du använder bästa praxis för Sök tjänsten med hjälp av Microsoft Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845748"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Metod tips för att använda Azure Maps Search Service

Azure Maps [search service](https://docs.microsoft.com/rest/api/maps/search) innehåller API: er med olika funktioner. Adress Sök-API: t kan till exempel användas för att söka efter intresse punkt (POI) eller data runt en viss plats. Den här artikeln visar de ljud metoder som du kan använda när du anropar data från Azure Maps Sök tjänster. Du lär dig att göra följande:

* Bygg frågor för att returnera relevanta matchningar
* Begränsa Sök Resultat
* Lär dig skillnaden mellan olika resultat typer
* Läs svars strukturen för adress sökning


## <a name="prerequisites"></a>Krav

Om du vill göra anrop till Maps-tjänstens API: er behöver du ett Azure Maps konto och en nyckel. Om du behöver följer du instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) och [hämtar en primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account). Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

> [!Tip]
> Om du vill fråga Sök tjänsten kan du använda [Postman-appen](https://www.getpostman.com/apps) för att bygga rest-anrop eller så kan du använda valfri API utvecklings miljö som du föredrar.


## <a name="best-practices-for-geocoding-address-search"></a>Metod tips för kodning (adresss ökning)

När du söker efter en fullständig eller en del av adressen med hjälp av Azure Maps Search Service, läser API: et nyckelord från Sök frågan och returnerar adressens longitud-och latitud-koordinater. Den här processen kallas för kodning. Möjligheten att koda i ett land är beroende av vägtrafikens data täckning och den landsspecifika precisionen för den landsspecifika tjänsten.

Se [täcknings täckning](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) för att lära dig mer om Azure Maps funktioner för att koda efter land/region.

### <a name="limit-search-results"></a>Begränsa Sök Resultat

 Azure Maps Sök-API: n kan hjälpa dig att begränsa Sök resultaten korrekt, så att du kan visa relevanta data för dina användare.

   > [!Note]
   > Alla parametrar som stöds för Search API: er visas inte nedan

   **Sök Resultat för geo-bias**

   För att geo-bias ska kunna användas i det relevanta området för din användare bör du alltid lägga till maximalt antal indata från den detaljerade platsen. Överväg att lägga till följande typer av indatatyper för att begränsa Sök resultaten:

   1. Ange `countrySet` parameter, till exempel "USA, FR". Standard Sök beteendet är att söka hela världen och eventuellt returnera onödiga resultat. Om din fråga har parametern `countrySet` kan sökningen returnera felaktiga resultat. Sök till exempel efter en stad med namnet **Bellevue** kommer att returnera resultat från USA och Frankrike, eftersom det finns städer med namnet **Bellevue** i substratet Frankrike och USA.

   2. Du kan använda parametrarna `btmRight` och `topleft` för att ange begränsnings rutan för att begränsa sökningen till ett särskilt område på kartan.

   3. För att påverka relevansen för resultaten kan du definiera `lat`och `lon` koordinatens parametrar och ange radien för sökområdeet med hjälp av `radius`-parametern.


   **Parametrar för fuzzy-sökning**
   
  Azure Maps [API för oskarp sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) är den rekommenderade tjänsten som du kan använda när du inte vet vad dina användarindata är för en Sök fråga. API: et kombinerar en POI-sökning (Point of Interest) och en kanonisk *sökning på en enskild rad*.

   1. `minFuzzyLevel`-och `maxFuzzyLevel`-hjälpen returnerar relevanta matchningar även om frågeparametrar inte matchar den efterfrågade informationen. För att få prestanda och minska ovanliga resultat kan du använda standard Sök frågor för att `minFuzzyLevel=1` och `maxFuzzyLevel=2`. Ta ett exempel på en Sök term "restrant", den matchas till "restaurang" när `maxFuzzyLevel` har värdet 2. De förvalda fuzzy-nivåerna kan åsidosättas efter behov.  

   2. Du kan också prioritera den exakta uppsättning resultat typer som ska returneras med hjälp av parametern `idxSet`. För det här ändamålet kan du skicka en kommaavgränsad lista över index; objekt ordningen spelar ingen roll. Följande index stöds:

       * `Addr` - **adress intervall**: för vissa gator finns det adress punkter som interpoleras från början och slutet av gatan. Dessa punkter visas som adress intervall.
       * `Geo` - **geografiska**områden: områden på en karta som representerar administrativ division av en mark, det vill säga land, delstat, stad.
       * `PAD` - **adress**: punkter på en karta där en viss adress med ett gatu namn och nummer kan hittas i ett index, till exempel Soquel Dr 2501. Det här idxSet-värdet är den högsta möjliga noggrannhets nivån för adresser.  
       * `POI` - **intressanta punkter**: punkter på en karta som är värda och kan vara intressanta.  [Hämta Sök adressen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) returnerar inte POI: er.  
       * `Str` - **Streets**: representation av Streets på kartan.
       * `XStr` - **korsa gator/skärnings punkter**: åter givning av Knut punkter; platser där två gator korsar varandra.


       **Användnings exempel**:

       * idxSet = POI (endast Sök punkter av intresse) 

       * idxSet = PAD, addr (Sök endast adresser, PAD = punkt adress, addr = adress intervall)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Filter för omvänd polycode och geografi enhets typ

När du gör en omvänd kod sökning med [Omvänd söknings adress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), har tjänsten möjlighet att returnera polygoner för de administrativa områdena. Genom att ange parametern `entityType` i begäran kan du begränsa sökningen efter angivna typer av geografi enheter. Det resulterande svaret kommer att innehålla geografi-ID: t och enhets typen matchad. Om du anger mer än en entitet returnerar slut punkten den **minsta tillgängliga entiteten**. Returnerat geometri-ID kan användas för att hämta geometrin för detta geografi via [Get polygon service](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Exempel förfrågan:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Svar:**

```JSON
{
    "summary": {
        "queryTime": 14,
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

### <a name="search-results-language"></a>Sök Resultat språk

Med parametern `language` kan du välja språk för resultaten som returneras av API: et. Om språket inte är inställt i begäran är Sök tjänsten automatiskt standardvärdet för det vanligaste språket i landet/regionen. När data på det angivna språket inte är tillgängliga används också standard språket. Se [språk som stöds](https://docs.microsoft.com/azure/azure-maps/supported-languages) för en lista över språk som stöds från Azure Maps tjänster efter land/region.


### <a name="predictive-mode-autosuggest"></a>Förutsägande läge (automatiska förslag)

Om du vill hitta fler matchningar för partiella frågor ska `typeahead` parameter vara inställd på "true". Frågan kommer att tolkas som en del Indatatyp och sökningen kommer att ange ett förutsägande läge. Annars förutsätter tjänsten att all relevant information har skickats in.

I exempel frågan nedan kan du se att Sök tjänst frågan efter frågas för "mi" med parametern `typeahead` inställd på **True**. Om du ser svaret kan du se att Sök tjänsten tolkade frågan som partiell fråga. Svaret innehåller resultat för den automatiska förslags frågan.

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
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI-kodning för att hantera specialtecken 

Om du vill hitta kors adresser måste du koda URI: n för att hantera specialtecken i adressen. Tänk på följande adress exempel: "1st minimering & union gatan, Seattle". Specialtecknet "&" måste kodas innan begäran skickas. Vi rekommenderar att du kodar tecken data i en URI, där alla tecken är kodade med ett '% ' tecken och ett HEX-värde med två tecken som motsvarar deras UTF-8-tecken.

**Användnings exempel**:

Hämta Sök adress:

```
query=1st Avenue & E 111th St, New York
```

 ska kodas som:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Här följer de olika metoder som du kan använda för olika språk: 

Java Script/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#Visual
```csharp
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

Införliva
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Offline
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Metod tips för POI-sökning

Med POI-sökningen (Points of Interest) kan du begära POI resultat per namn, till exempel söka efter företag efter namn. Vi rekommenderar starkt att du använder `countrySet` parameter för att ange länder där ditt program behöver täckning, eftersom standard beteendet är att söka hela världen, vilket kan returnera onödiga resultat och/eller leda till längre Sök tider.

### <a name="brand-search"></a>Varumärkes sökning

För att förbättra relevansen för resultaten och informationen i svars-och POI-Söksvaret, innehåller Sök informationen som kan användas för att analysera svaret.

Du kan också skicka en kommaavgränsad lista med märkes namn i begäran. Du kan använda listan för att begränsa resultatet till vissa varumärken med hjälp av parametern `brandSet`. Objekt ordningen spelar ingen roll. När flera varumärken anges returneras endast resultat som tillhör (minst) en av de angivna listorna.

Låt oss göra en [POI kategori search](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) -begäran för gas stationer nära Microsoft Campus (REDMOND, WA). Om du ser svaret kan du se varumärkes information för varje POI som returneras.


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
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Sök efter flyg plats

POI search stöder sökning av flyg platser genom att använda officiella flyg plats koder. Till exempel **Sea** (Seattle-Tacoma International Airport). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Närliggande sökning

Om du bara vill hämta POI resultat kring en bestämd plats kan [API: t i närheten](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) vara det rätta valet. Den här slut punkten returnerar endast POI resultat och det går inte att utföra en Sök fråga. För att begränsa resultaten rekommenderar vi att du anger radien.

## <a name="understanding-the-responses"></a>Förstå Svaren

Låt oss göra en Sök-begäran till Azure Maps [Sök tjänsten](https://docs.microsoft.com/rest/api/maps/search) för en adress i Seattle. Om du tittar noga på fråge-URL: en nedan har vi ställt in `countrySet`-parametern till **oss** för att söka efter adressen i USA i Amerika.

**Exempelfråga:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Nu ska vi titta på svars strukturen nedan. Resultat typerna för resultat objekt i svaret är olika. Om du ser noggrant kan vi se att vi har tre olika typer av resultat objekt, som är "punkt adress", "gata" och "kors gatan". Observera att adresss ökningen inte returnerar POI: er. Parametern `Score` för varje Response-objekt anger de relativa matchnings poängen till resultat från andra objekt i samma svar. Se [Hämta Sök adress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) om du vill veta mer om svars objekt parametrar.

**Resultat typer som stöds:**

* **Adress för punkt:** Pekar på en karta med en speciell adress med ett gatu namn och en siffra. Den högsta möjliga noggrannhets nivån för adresser. 

* **Adress intervall:**  För vissa gator finns det adress punkter som interpoleras från början och slutet av gatan. dessa punkter visas som adress intervall. 

* **Geografi:** Områden på en karta som representerar den administrativa indelningen av en mark, det vill säga land, delstat, stad. 

* **POI – (intressanta punkter):** Punkter på en karta som är värda och kan vara intressanta.

* **Gata:** Representation av gator på kartan. Adresser matchas mot latitud/longitud-koordinaten för den gata som innehåller adressen. Hus numret kan inte bearbetas. 

* **Kors gatan:** Skärnings punkter. Representationer av Knut punkter; platser där två gator korsar varandra.

**Svar:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometri

När svars typen är **geometri**, kan den innehålla det geometri-ID som returneras i **DataSources** -objektet under "Geometry" och "ID". Med hjälp av funktionen för att [Hämta polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) kan du till exempel begära geometri data i det geometriska JSON-formatet. Till exempel en stad eller en flyg plats för en uppsättning entiteter. Du kan använda dessa avgränsnings data för [polystaket](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) eller [Sök POI: er i geometrin](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Sök adressen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) eller [Sök i fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API-svar kan innehålla det **geometri-ID** som returneras i DataSource-objektet under "Geometry" och "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du skapar Azure Maps Sök tjänst begär Anden](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Utforska Azure Maps [Search Service API-dokumentationen](https://docs.microsoft.com/rest/api/maps/search). 
