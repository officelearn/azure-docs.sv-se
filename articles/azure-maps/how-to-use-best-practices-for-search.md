---
title: Metodtips för Söktjänsten för Azure Maps | Microsoft Azure Maps
description: Lär dig hur du använder metodtipsen när du använder söktjänsten från Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335310"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Metodtips för söktjänsten för Azure Maps

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) innehåller API:er som erbjuder olika funktioner. Api:et för sökadress kan till exempel hitta intressanta platser (POI) eller data runt en viss plats. 

I den här artikeln beskrivs hur du tillämpar ljudred praxis när du anropar data från Azure Maps Search Service. Du lär dig följande:

* Skapa frågor för att returnera relevanta matchningar.
* Begränsa sökresultaten.
* Lär dig skillnaderna mellan resultattyper.
* Läs adresssöksvarsstrukturen.

## <a name="prerequisites"></a>Krav

Om du vill ringa samtal till Azure Maps-tjänst-API:erna behöver du ett Azure Maps-konto och en nyckel. Mer information finns i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) och Hämta en [primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Information om autentisering i Azure Maps finns [i Hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

> [!TIP]
> Om du vill fråga efter söktjänst kan du använda [Postman-appen](https://www.getpostman.com/apps) för att skapa REST-anrop. Eller så kan du använda någon API-utvecklingsmiljö som du föredrar.

## <a name="best-practices-to-geocode-addresses"></a>Metodtips för geokodadresser

När du söker efter en fullständig eller partiell adress med hjälp av Azure Maps Search Service läser API:et nyckelord från sökfrågan. Sedan returnerar den adressens longitud- och latitudkoordinater. Den här processen kallas *geokodning*. 

Möjligheten att geokoda i ett land beror på tillgängligheten av vägdata och precisionen i geokodningstjänsten. Mer information om Geokodningsfunktioner för Azure Maps per land eller region finns i [Geocoding-täckning](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Begränsa sökresultaten

 Azure Maps Search API kan hjälpa dig att begränsa sökresultaten på rätt sätt. Du begränsar resultaten så att du kan visa relevanta data för användarna.

> [!NOTE]
> Sök-API:erna stöder fler parametrar än bara de som beskrivs i den här artikeln.

#### <a name="geobiased-search-results"></a>Geobiased sökresultat

För att geobias resultat till det relevanta området för din användare, alltid lägga till så många platsinformation som möjligt. Du kanske vill begränsa sökresultaten genom att ange vissa inmatningstyper:

* Ställ `countrySet` in parametern. Du kan till `US,FR`exempel ställa in den på. Som standard söker API:et igenom hela världen, så att det kan ge onödiga resultat. Om frågan inte `countrySet` har någon parameter kan sökningen ge felaktiga resultat. Till exempel, en sökning efter en stad som heter *Bellevue* returnerar resultat från USA och Frankrike eftersom båda länderna innehåller en stad som heter *Bellevue*.

* Du kan `btmRight` använda `topleft` parametrarna och för att ställa in markeringsramen. Dessa parametrar begränsar sökningen till ett visst område på kartan.

* För att påverka det relevanta området `lat` för `lon` resultaten, definiera och samordna parametrar. Använd `radius` parametern för att ange radien för sökområdet.


#### <a name="fuzzy-search-parameters"></a>Suddiga sökparametrar

Vi rekommenderar att du använder Azure Maps [Search Fuzzy API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) när du inte känner till dina användarindata för en sökfråga. API:et kombinerar POI-sökning och geokodning till en kanonisk *enkelradssökning:* 

* `minFuzzyLevel` Parametrarna `maxFuzzyLevel` och hjälper till att returnera relevanta matchningar även när frågeparametrar inte exakt matchar den information som användaren vill ha. Om du vill maximera prestanda och minska ovanliga `minFuzzyLevel=1` resultat `maxFuzzyLevel=2`ställer du in sökfrågor till standardvärden och . 

    När parametern `maxFuzzyLevel` till exempel är inställd på 2 matchas söktermen *restrant* med *restaurang*. Du kan åsidosätta standardavstjiga nivåer när du behöver. 

* Använd `idxSet` parametern för att prioritera den exakta uppsättningen resultattyper. Om du vill prioritera en exakt uppsättning resultat kan du skicka en kommaavgränsad lista med index. I listan spelar artikelordern ingen roll. Azure Maps stöder följande index:

* `Addr` - **Adressintervall**: Adresspunkter som interpoleras från början och slutet av gatan. Dessa punkter representeras som adressintervall.
* `Geo` - **Geografier**: Administrativa uppdelningar av landet. En geografi kan vara ett land, en delstat eller en stad, till exempel.
* `PAD` - **Punktadresser**: Adresser som innehåller ett gatunamn och gatunummer. Punktadresser finns i ett index. Ett exempel är *Soquel Dr 2501*. En poängadress ger den högsta noggrannhetsnivån som är tillgänglig för adresser.  
* `POI` - **Intressanta platser:** Poäng på en karta som anses vara värt uppmärksamhet eller som kan vara intressant. [Api:et för sökadressen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) returnerar inte INTRESSE för sökresultat.  
* `Str` - **Gator:** Gator på kartan.
* `XStr` - **Korsa gator eller korsningar**: Korsningar eller platser där två gator skär.


#### <a name="usage-examples"></a>Exempel på användning

* `idxSet=POI`- Sök endast sevärdheter. 

* `idxSet=PAD,Addr`- Sök endast adresser. `PAD`anger punktadressen och `Addr` anger adressintervallet.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Omvänd geokod och filter för en geografientitetstyp

När du gör en omvänd geokodsökning i [sökadressen Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)kan tjänsten returnera polygoner för administrativa områden.Om du vill begränsa sökningen till `entityType` specifika geografientitetstyper inkluderar du parametern i dina begäranden. 

Det resulterande svaret innehåller geografi-ID och entitetstypen som matchades. Om du tillhandahåller mer än en entitet returnerar slutpunkten den *minsta tillgängliga entiteten*. Du kan använda det returnerade geometri-ID:t för att få geografins geometri via [search polygon-tjänsten](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Exempel på begäran

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Svar

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

### <a name="set-the-results-language"></a>Ange resultatspråk

Använd `language` parametern för att ange språk för de returnerade sökresultaten. Om begäran inte anger språket använder söktjänsten som standard det vanligaste språket i landet eller regionen. När inga data är tillgängliga på det angivna språket används standardspråket. 

Mer information finns i [Språk som stöds av Azure Maps](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="use-predictive-mode-automatic-suggestions"></a>Använd prediktivt läge (automatiska förslag)

Om du vill hitta fler matchningar för partiella frågor anger du parametern `typeahead` till `true`. Den här frågan tolkas som en partiell indata och sökningen går in i prediktivt läge. Om du inte ställer `typeahead` in `true`parametern på antar tjänsten att all relevant information har skickats in.

I följande exempelfråga efterfrågas tjänsten Sökadress för *Microso*. Här är `typeahead` parametern `true`inställd på . Svaret visar att söktjänsten tolkade frågan som partiell fråga. Svaret innehåller resultat för en automatiskt föreslagen fråga.

#### <a name="sample-query"></a>Exempelfråga

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Svar

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


### <a name="encode-a-uri-to-handle-special-characters"></a>Koda en URI för att hantera specialtecken 

Om du vill hitta gatuadresser måste du koda URI:n för att hantera specialtecken i adressen. Tänk på det här adressexemplet: *1st Avenue & Union Street, Seattle*. Här kodar du et-tecknet (`&`) innan du skickar begäran. 

Vi rekommenderar att du kodar teckendata i en URI. I en URI kodar du alla tecken`%`med hjälp av ett procenttecken ( ) och ett hexadecimalt värde med två tecken som motsvarar tecknens UTF-8-kod.

#### <a name="usage-examples"></a>Exempel på användning

Börja med den här adressen:

```
query=1st Avenue & E 111th St, New York
```

Koda adressen:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Du kan använda följande metoder.

JavaScript eller TypeScript:
```Javascript
encodeURIComponent(query)
```

C# eller Visual Basic:
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

Php:
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


## <a name="best-practices-for-poi-searching"></a>Metodtips för POI-sökning

I en POI-sökning kan du begära POI-resultat efter namn. Du kan till exempel söka efter ett företag efter namn. 

Vi rekommenderar starkt att `countrySet` du använder parametern för att ange länder där ditt program behöver täckning. Standardbeteendet är att söka i hela världen. Den här breda sökningen kan ge onödiga resultat och sökningen kan ta lång tid.

### <a name="brand-search"></a>Varumärkessökning

För att förbättra resultatens relevans och informationen i svaret innehåller ett POI-söksvar varumärkesinformation. Du kan använda den här informationen för att ytterligare tolka svaret.

I en begäran kan du skicka en kommaavgränsad lista med varumärken. Använd listan för att begränsa resultaten till `brandSet` specifika varumärken genom att ange parametern. I listan spelar artikelorder ingen roll. När du anger flera varumärkeslistor måste de resultat som returneras tillhöra minst en av dina listor.

Om du vill utforska varumärkessökning ska vi göra en sökbegäran för [POI-kategori.](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) I följande exempel letar vi efter bensinstationer nära Microsofts campus i Redmond, Washington. Svaret visar varumärkesinformation för varje sevärdhet som returnerades.

#### <a name="sample-query"></a>Exempelfråga

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Svar

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


### <a name="airport-search"></a>Sökning på flygplatsen

Genom att använda Sök POI API, kan du leta efter flygplatser med hjälp av deras officiella kod. Du kan till exempel använda *SEA* för att hitta Seattle-Tacoma International Airport: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Sökning i närheten

Om du vill hämta POI-resultat runt en viss plats kan du prova att använda [API:et sök i närheten](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). Slutpunkten returnerar endast POI-resultat. Det tar inte i en sökfråga parameter. 

För att begränsa resultaten rekommenderar vi att du anger radien.

## <a name="understanding-the-responses"></a>Förstå svaren

Låt oss hitta en adress i Seattle genom att göra en begäran om adresssökning till Azure Maps Search Service. I följande url för begäran `countrySet` ställer `US` vi in parametern på att söka efter adressen i USA.

### <a name="sample-query"></a>Exempelfråga

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Typer av resultat som stöds

* **Poängadress**: Pekar på en karta som har en specifik adress med gatunamn och gatunummer. Punktadress ger den högsta noggrannhetsnivån för adresser. 

* **Adressintervall**: Det adressintervall som interpoleras från början och slutet av gatan.  

* **Geografi**: Områden på en karta som representerar administrativa indelningar av ett land, till exempel land, stat eller stad. 

* **POI:** Punkter på en karta som är värda uppmärksamhet och som kan vara intressant.

* **Gata:** Gator på kartan. Adresser matchas till latitud- och longitudkoordinaterna för gatan som innehåller adressen. Husnumret kanske inte bearbetas. 

* **Cross Street:** Korsningar. Tvärgator representerar korsningar där två gator skär.

### <a name="response"></a>Svar

Låt oss titta på svarsstrukturen. I det svar som följer är typerna av resultatobjekten olika. Om du tittar noga visas tre typer av resultatobjekt:

* Punktadress
* Gata
* Korsa gatan

Observera att adresssökningen inte returnerar SEVÄRDHETER.  

Parametern `Score` för varje svarsobjekt anger hur matchningspoängen relaterar till poängen för andra objekt i samma svar. Mer information om parametrar för svarsobjekt finns i [Hämta sökadress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

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

En svarstyp *av geometri* kan innehålla geometri-ID `dataSources` som `geometry` `id`returneras i objektet under och . Du kan till exempel använda [tjänsten Sök polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) för att begära geometridata i ett GeoJSON-format. Genom att använda det här formatet kan du få en stads- eller flygplatskontur för en uppsättning entiteter. Du kan sedan använda de här gränsdata för att [ställa in en geofence-](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) eller [sök-API:er inuti geometrin](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Svar för [API för sökadress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) eller [Api för sökfust](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) kan innehålla `dataSources` geometri-ID som returneras i objektet under `geometry` och `id`:


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

> [!div class="nextstepaction"]
> [Så här skapar du begäranden i Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Api-dokumentation för söktjänst](https://docs.microsoft.com/rest/api/maps/search)