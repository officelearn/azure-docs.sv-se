---
title: Begär transitdata i realtid | Microsoft Azure Maps
description: Begär realtidsdata med hjälp av Microsoft Azure Maps Mobility Service.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335488"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Begär realtidsdata med hjälp av Azure Maps Mobility Service

Den här artikeln visar hur du använder Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) för att begära transitdata i realtid.

I den här artikeln får du lära dig hur du:


 * Begär nästa ankomst i realtid för alla linjer som anländer till ett visst stopp
 * Begär realtidsinformation för en viss cykeldockningsstation.


## <a name="prerequisites"></a>Krav

Du måste först ha ett Azure Maps-konto och en prenumerationsnyckel för att ringa alla samtal till Azure Maps public transit API:er. Om du vill ha information följer du instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) för att skapa ett Azure Maps-konto. Följ stegen för [att få primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att få den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](./how-to-manage-authentication.md).


I den här artikeln används [Postman-appen](https://www.getpostman.com/apps) för att skapa REST-anrop. Du kan använda valfri API-utvecklingsmiljö som du föredrar.


## <a name="request-real-time-arrivals-for-a-stop"></a>Begär ankomster i realtid för ett stopp

För att begära ankomstdata i realtid för ett visst stopp för kollektivtrafik måste du begära [ankomst-API:et](https://aka.ms/AzureMapsMobilityRealTimeArrivals) för ankomster i realtid i Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService). Du behöver **metroID** och **stopID** för att slutföra begäran. Mer information om hur du begär dessa parametrar finns i vår guide om hur du [begär kollektivtrafikvägar.](https://aka.ms/AMapsHowToGuidePublicTransitRouting) 

Låt oss använda "522" som vår tunnelbana ID, som är tunnelbanan ID för "Seattle-Tacoma-Bellevue, WA" område. Använd "522---2060603" som stopp-ID, denna busshållplats är vid "Ne 24th St & 162nd Ave Ne, Bellevue WA". För att begära de kommande fem ankomstdata i realtid, för alla nästa levande ankomster vid detta stopp, utför du följande steg:

1. Öppna Postman-appen och låt oss skapa en samling för att lagra begäranden. Välj **Ny**högst upp i Postman-appen . Välj **Samling**i fönstret **Skapa nytt** .  Namnge samlingen och välj knappen **Skapa.**

2. Om du vill skapa begäran väljer du **Ny** igen. Välj **Begär**i fönstret **Skapa nytt** . Ange ett **begärandenamn** för begäran. Välj den samling som du skapade i föregående steg, som den plats där begäran ska sparas. Välj sedan **Spara**.

    ![Skapa en begäran i Postman](./media/how-to-request-transit-data/postman-new.png)

3. Välj metoden **GET** HTTP på fliken Builder och ange följande URL för att skapa en GET-begäran. Ersätt `{subscription-key}`, med din azure maps-primärnyckel.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Efter en lyckad begäran får du följande svar.  Observera att parametern 'scheduleType' definierar om den uppskattade ankomsttiden baseras på realtidsdata eller statiska data.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Realtidsdata för cykeldockningsstationen

[Api:et Hämta information för transitdocka](https://aka.ms/AzureMapsMobilityTransitDock) gör det möjligt för användare att begära statisk information och realtidsinformation. Användare kan till exempel begära tillgänglighets- och utvan-information för en cykel eller en dockningsstation för scooter. [Api:et Hämta information för överföringsdocka](https://aka.ms/AzureMapsMobilityTransitDock) är också en del av Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService).

För att kunna göra en begäran till API:et hämta [transitdocka](https://aka.ms/AzureMapsMobilityTransitDock)behöver du **dockid** för den stationen. Du kan hämta docknings-ID genom att göra en sökbegäran till [Get Närliggande transit-API](https://aka.ms/AzureMapsMobilityNearbyTransit) med **parametern objectType** som tilldelats "bikeDock". Följ stegen nedan för att få realtidsdata om en dockningsstation för cyklar.


### <a name="get-dock-id"></a>Hämta docknings-ID

Så här hämtar du **dockID**och gör en begäran till Api:et hämta i närheten:

1. I Postman klickar du på **Ny begäran** | **HÄMTA-begäran** och namnger **den Hämta docknings-ID**.

2.  Välj metoden **GET** HTTP på fliken Builder, ange följande URL för begäran och klicka på **Skicka**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Efter en lyckad begäran får du följande svar. Observera att vi nu har **id** i svaret, som kan användas senare som en frågeparameter i begäran till Get Transit Dock Info API.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Få status för cykeldockan i realtid

Följ stegen nedan för att göra en begäran till Api:et hämta information för överföringsdocka för att hämta realtidsdata för den valda dockan.

1. I Postman klickar du på **Ny begäran** | **HÄMTA begäran** och namnge den Hämta data i **realtid.**

2.  Välj metoden **GET** HTTP på fliken Builder, ange följande URL för begäran och klicka på **Skicka**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Efter en lyckad begäran får du ett svar på följande struktur:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Nästa steg

Läs om hur du begär transitdata med Mobilitetstjänsten:

> [!div class="nextstepaction"]
> [Så här begär du transitdata](how-to-request-transit-data.md)

Utforska API-dokumentationen för Azure Maps Mobility Service:

> [!div class="nextstepaction"]
> [API-dokumentation för Mobilitetstjänster](https://aka.ms/AzureMapsMobilityService)
