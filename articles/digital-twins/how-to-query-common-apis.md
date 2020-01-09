---
title: Vanliga fråge mönster – Azure Digitals flätar | Microsoft Docs
description: 'Lär dig flera vanliga API-frågor för API: er för Azure Digitals sammanflätade hantering.'
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 5be44cb2b3747cccc5063163496b5df56df68df1
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552193"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Så här frågar du Azure Digitals dubbla API: er för vanliga uppgifter

Den här artikeln visar fråge mönster som hjälper dig att köra vanliga scenarier för din Azure Digital-instansen. Detta förutsätter att din digitala-instansen redan körs. Du kan använda alla REST-klienter, till exempel Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Frågor för mellanslag och typer

Det här avsnittet innehåller exempel frågor för att få mer information om dina allokerade utrymmen. Gör autentiserade Hämta HTTP-förfrågningar med exempel frågorna och ersätt plats hållarna med värden från din installation. 

- Hämta blank steg som är rotnoder.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Få ett mellanslag efter namn och inkludera enheter, sensorer, beräknade värden och sensor värden. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Hämta utrymmen och deras enhet/sensor information, vars överordnade är det tilldelade Utrymmes-ID: t och som finns på nivå två till fem i [förhållande till det aktuella utrymmet](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Hämta utrymmet med angivet ID och inkludera beräknade värden och sensor värden.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Hämta egenskaps nycklar för ett visst utrymme.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Hämta blank steg med egenskaps nyckeln med namnet *AreaInSqMeters* och dess värde är 30. Du kan också utföra sträng åtgärder, till exempel hämta blank steg som innehåller egenskaps nyckel med `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Hämta alla namn med namn *temperatur* och associerade beroenden och Ontologies.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Frågor för roller och roll tilldelningar

I det här avsnittet visas några frågor för att få mer information om roller och deras tilldelningar. 

- Hämta alla roller som stöds av Azure Digitals dubbla.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Hämta alla roll tilldelningar i din digitala dubbla instansen. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Hämta roll tilldelningar för en viss sökväg.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Frågor för enheter

I det här avsnittet visas några exempel på hur du kan använda hanterings-API: er för att få detaljerad information om dina enheter. Alla API-anrop måste autentiseras Hämta HTTP-begäranden.

- Hämta alla enheter.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Sök efter alla enhets status.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Hämta en speciell enhet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Hämta alla enheter som är anslutna till rot utrymmet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Hämta alla enheter som är kopplade till utrymmen på nivå 2 till 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Hämta alla enheter direkt anslutna till ett visst Utrymmes-ID.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Hämta alla enheter som är kopplade till ett visst utrymme och dess underordnade.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Hämta alla enheter som är kopplade till underordnade objekt till ett utrymme, förutom det utrymmet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Hämta alla enheter som är kopplade till direkta underordnade objekt till ett utrymme.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Hämta alla enheter som är kopplade till en av ett utrymmes överordnade objekt.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Hämta alla enheter som är kopplade till underordnade objekt till ett utrymme som är mindre än eller lika med 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Hämta alla enheter som är kopplade till utrymmen på samma nivå som utrymmet med ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Hämta enhetens anslutnings sträng för IoT Hub enhet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Hämta enhet med angivet maskinvaru-ID, inklusive anslutna sensorer.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Hämta sensorer för särskilda data typer, i det här fallet *rörelse* och *temperatur*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Frågor för matchare och användardefinierade funktioner 

- Hämta alla etablerade matchare och deras ID.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Hämta information om en viss matchnings funktion, inklusive de blank steg och användardefinierade funktionen som är associerade med den.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Utvärdera en matchning mot en sensor och aktivera loggning för fel söknings syfte. Returen av detta HTTP GET-meddelande anger om matchnings tjänsten och sensorn tillhör data typen. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Hämta ID för användardefinierade funktioner. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Hämta innehållet i en specifik användardefinierad funktion 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Frågor för användare

I det här avsnittet visas några exempel på API-frågor för att hantera användare i digitala Azure-enheter. Gör en HTTP GET-begäran och ersätt plats hållarna med värden från din installation. 

- Hämta alla användare. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Hämta en speciell användare.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Nästa steg

Läs [autentisering med API: er](./security-authenticating-apis.md)för att lära dig hur du autentiserar med ditt hanterings-API.

Om du vill veta mer om dina API-slutpunkter läser [du så här använder du digitala dubbla Swagger](./how-to-use-swagger.md).
