---
title: Begär uppgifter om kollektivtrafik i realtid | Microsoft Azure Maps
description: Begär kollektivtrafikdata i realtid med hjälp av Microsoft Azure Maps Mobility Service.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086085"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Begär kollektivtrafikdata i realtid med hjälp av Azure Maps Mobility Service

Den här artikeln visar hur du använder Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) för att begära kollektivtrafikdata i realtid.

I den här artikeln får du lära dig att begära nästa ankomst i realtid för alla linjer som anländer till ett visst stopp

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

## <a name="next-steps"></a>Nästa steg

Läs om hur du begär transitdata med Mobilitetstjänsten:

> [!div class="nextstepaction"]
> [Så här begär du transitdata](how-to-request-transit-data.md)

Utforska API-dokumentationen för Azure Maps Mobility Service:

> [!div class="nextstepaction"]
> [API-dokumentation för Mobilitetstjänster](https://aka.ms/AzureMapsMobilityService)
