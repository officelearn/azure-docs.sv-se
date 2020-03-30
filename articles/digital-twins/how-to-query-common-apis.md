---
title: Vanliga frågemönster - Azure Digital Twins | Microsoft-dokument
description: Lär dig flera vanliga API-frågemönster för Azure Digital Twins management API:er.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589121"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Fråga Azure Digital Twins API:er för vanliga uppgifter

Den här artikeln visar frågemönster som hjälper dig att köra vanliga scenarier för din Azure Digital Twins-instans. Detta förutsätter att din Digital Twins-instans redan körs. Du kan använda valfri REST-klient, till exempel Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Frågor om mellanslag och typer

I det här avsnittet visas exempelfrågor för att få mer information om dina etablerade blanksteg. Gör autentiserade GET HTTP-begäranden med exempelfrågorna och ersätt platshållarna med värden från din inställning. 

- Hämta blanksteg som är rotnoder.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Få ett utrymme efter namn och inkludera enheter, sensorer, beräknade värden och sensorvärden. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Hämta mellanslag och deras enhets-/sensorinformation, vars överordnade är det angivna rymd-ID:t, och som ligger på nivå två till fem [i förhållande till det givna utrymmet](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Hämta utrymmet med det angivna ID:t och inkludera beräknade värden och sensorvärden.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Hämta egenskapsnycklar för ett visst utrymme.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Få blanksteg med *egenskapsnyckeln AreaInSqMeters* och dess värde är 30. Du kan också utföra strängåtgärder, till exempel `name = X contains Y`hämta blanksteg som innehåller egenskapsnyckel med .

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Få alla namn med namn *Temperatur* och tillhörande beroenden och ontologier.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Frågor om roller och rolltilldelningar

I det här avsnittet visas några frågor för att få mer information om roller och deras tilldelningar. 

- Få alla roller som stöds av Azure Digital Twins.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Få alla rolluppgifter i din Digital Twins-instans. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Hämta rolltilldelningar på en viss bana.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Frågor för enheter

I det här avsnittet visas några exempel på hur du kan använda api:erna för hantering för att få specifik information om dina enheter. Alla API-anrop måste autentiseras HÄMTA HTTP-begäranden.

- Hämta alla enheter.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Hitta alla enhetsstatusar.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Skaffa en specifik enhet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Få alla enheter anslutna till rotutrymmet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Få alla enheter anslutna till utrymmen på nivåerna 2 till 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Få alla enheter direkt anslutna till ett visst utrymmes-ID.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Få alla enheter anslutna till ett visst utrymme och dess underordnade.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Få alla enheter anslutna till underordnade i ett blanksteg, exklusive det utrymmet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Få alla enheter anslutna till direkt underordnade i ett utrymme.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Få alla enheter anslutna till en av förfäderna till ett utrymme.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Få alla enheter anslutna till underordnade i ett utrymme som är av nivå mindre än eller lika med 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Hämta alla enheter som är anslutna till blanksteg som är på samma nivå som utrymmet med ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Hämta anslutningssträngen för IoT Hub-enheten för enheten.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Skaffa enheten med det angivna maskinvaru-ID:t, inklusive anslutna sensorer.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Hämta sensorer för vissa datatyper, i det här fallet *Rörelse* och *Temperatur*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Frågor om matchningars och användardefinierade funktioner 

- Få alla etablerade matchers och deras ID.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Få information om en viss matchning, inklusive de utrymmen och användardefinierade funktion som är associerade med den.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Utvärdera en matchning mot en sensor och aktivera loggning för felsökning. Returen av det här HTTP GET-meddelandet talar om för dig om matchnings- och sensorn tillhör datatypen. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Hämta ID:et för de användardefinierade funktionerna. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Hämta innehållet i en viss användardefinierad funktion 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Frågor för användare

I det här avsnittet visas några exempel på API-frågor för hantering av användare i Azure Digital Twins. Gör en HTTP GET-begäran som ersätter platshållarna med värden från din konfiguration. 

- Hämta alla användare. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Skaffa en viss användare.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du autentiserar med ditt hanterings-API läser du [Autentisering med API: er](./security-authenticating-apis.md).

Mer information om dina API-slutpunkter finns i [Så här använder du Digital Twins Swagger](./how-to-use-swagger.md).
