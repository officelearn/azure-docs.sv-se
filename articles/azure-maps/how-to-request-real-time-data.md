---
title: Begär data överföring i real tid | Microsoft Azure Maps
description: Begär real tids data med hjälp av Microsoft Azure mappar mobilitets tjänsten.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 053e6c84f69e8b3d3fed0a90a8b632aa4eb311cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198164"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Begär real tids data med tjänsten Azure Maps Mobility

Den här artikeln visar hur du använder Azure Maps [mobilitets tjänst](https://aka.ms/AzureMapsMobilityService) för att begära överförings data i real tid.

I den här artikeln får du lära dig att:


 * Begär nästa mottagna real tid för alla rader som kommer vid ett angivet stopp
 * Begär real tids information för en specifik cykel docknings Station.


## <a name="prerequisites"></a>Förutsättningar

Du måste först ha ett Azure Maps konto och en prenumerations nyckel för att kunna anropa de Azure Maps offentliga API: erna för överföring. Om du vill ha mer information följer du instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) för att skapa ett Azure Maps-konto. Följ stegen i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att hämta den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).


I den här artikeln används [Postman-appen](https://www.getpostman.com/apps) för att bygga rest-anrop. Du kan använda valfri API utvecklings miljö som du föredrar.


## <a name="request-real-time-arrivals-for-a-stop"></a>Begär real tids mottagningar för ett stopp

För att begära ingångs data i real tid för en viss offentlig överförings stopp, måste du göra en begäran till [real tids ingångs-API: t](https://aka.ms/AzureMapsMobilityRealTimeArrivals) för [tjänsten Azure Maps Mobility](https://aka.ms/AzureMapsMobilityService). Du behöver **metroID** och **stopID** för att slutföra begäran. Mer information om hur du begär dessa parametrar finns i vår guide om hur du [begär offentliga överförings vägar](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Vi använder "522" som vårt tunnelbane-ID, som är Metro-ID: t för "Seattle – Tacoma – Bellevue, WA"-ytan. Använd "522---2060603" som stopp-ID: t det här buss steget är "ne 24 st & 162nd Ave Ne, Bellevue WA". Om du vill begära nästa fem real tids mottagande data, för alla nästa Live-införsel i detta steg, slutför du följande steg:

1. Öppna Postman-appen och skapa en samling där du kan lagra begär Anden. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **samling**.  Namnge samlingen och välj knappen **skapa** .

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg, som den plats där du vill spara begäran. Välj sedan **Spara**.

    ![Skapa en begäran i Postman](./media/how-to-request-transit-data/postman-new.png)

3. Välj metoden **Hämta** http på fliken Builder och ange följande URL för att skapa en get-begäran. Ersätt `{subscription-key}`med din Azure Maps primär nyckel.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Efter en lyckad begäran får du följande svar.  Observera att parametern "scheduleType" definierar om den uppskattade införsel tiden baseras på real tids data eller statiska data.

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


## <a name="real-time-data-for-bike-docking-station"></a>Real tids data för cykel docknings Station

Med [informations-API: t för överföring av överföring](https://aka.ms/AzureMapsMobilityTransitDock) kan användarna begära statisk information och information i real tid. Användare kan till exempel begära tillgänglighets-och vakans-information för en cykel eller en Scooter docknings Station. [Informations-API: t för att hämta överföring](https://aka.ms/AzureMapsMobilityTransitDock) är också en del av tjänsten Azure Maps [Mobility](https://aka.ms/AzureMapsMobilityService).

Du behöver **dockId** för den stationen för att kunna göra en begäran till [informations-API: t för överförings](https://aka.ms/AzureMapsMobilityTransitDock)tjänsten. Du kan hämta dock-ID genom att göra en sökbegäran till [Get i närheten av överförings-API: t](https://aka.ms/AzureMapsMobilityNearbyTransit) med parametern **objectType** som tilldelats "bikeDock". Följ stegen nedan för att hämta real tids data för en docknings station för cyklar.


### <a name="get-dock-id"></a>Hämta dock-ID

Om du vill hämta **dockID**följer du stegen nedan för att göra en begäran till API: et för närliggande överföring:

1. I Postman, klicka på **ny begäran** | **Hämta begäran** och ge den ett **dock-ID**.

2.  På fliken Builder väljer du metoden **Hämta** http, anger följande URL för begäran och klickar på **Skicka**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Efter en lyckad begäran får du följande svar. Observera att vi nu har **ID: t** i svaret, som kan användas senare som en frågeparameter i begäran till API: et för att hämta överförings information.

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


### <a name="get-real-time-bike-dock-status"></a>Hämta status för cykel Docking i real tid

Följ stegen nedan för att göra en begäran till API: t för att hämta överförings information för att hämta real tids data för den valda dock.

1. I Postman klickar du på **ny begäran** | **Get-begäran** och namnger den för att **få data i real tid**.

2.  På fliken Builder väljer du metoden **Hämta** http, anger följande URL för begäran och klickar på **Skicka**.
 
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

Lär dig hur du begär överförings data med mobilitets tjänsten:

> [!div class="nextstepaction"]
> [Så här begär du överförings data](how-to-request-transit-data.md)

Utforska dokumentationen för Azure Maps Mobility Service API:

> [!div class="nextstepaction"]
> [API-dokumentation för Mobility Service](https://aka.ms/AzureMapsMobilityService)
