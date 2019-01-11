---
title: Azure Digital Twins vanliga frågemönster | Microsoft Docs
description: 'Läs om vanliga mönster skicka frågor till Azure Digital Twins management API: er.'
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198651"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Hur du frågar Azure Digital Twins API: er för vanliga uppgifter

Den här artikeln visar frågemönster att hjälpa dig att utföra vanliga scenarier för din Azure Digital Twins-instans. Detta förutsätter att din digitala Twins instans körs redan. Du kan använda REST-klient, till exempel Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Frågor om lagringsutrymmen och typer

Det här avsnittet visas exempelfrågor som du vill ha mer information om ditt etablerade blanksteg. Göra autentiserade GET HTTP-begäranden med exempelfrågor, ersätta platshållarna med värden från din konfiguration. 

- Hämta utrymmen som rotnoder.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Få ett blanksteg efter namn och inkludera enheter, sensorer, beräknade värden och sensorvärdena. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Hämta blanksteg och deras enhet/sensorinformation, vars överordnade är angivna utrymmes-ID och som är på nivå två till fem [i förhållande till det angivna utrymmet](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Få utrymme med angivet ID och inkludera beräknas och sensorvärdena.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Hämta egenskapen nycklar för en viss sida.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Hämta blanksteg med Egenskapsnyckel med namnet *AreaInSqMeters* och dess värde är 30. Du kan också sträng åtgärder, till exempel get lagringsutrymmen som innehåller Egenskapsnyckel med `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Hämta alla namnen med namnet *temperatur* associerade beroenden och ontologi.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Frågor för roller och rolltilldelningar

Det här avsnittet visar vissa frågor och få mer information om roller och deras tilldelningar. 

- Hämta alla roller som stöds av Azure Digital Twins.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Hämta alla rolltilldelningar i din digitala Twins-instans. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Hämta rolltilldelningar på en viss sökväg.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Frågor för enheter

Det här avsnittet visas några exempel på hur du kan använda Management-API: er för att få specifik information om dina enheter. Alla API-anrop måste vara autentiserade GET HTTP-begäranden.

- Hämta alla enheter.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Hitta alla Enhetsstatus.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Hämta en specifik enhet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Hämta alla enheter som är anslutna till området rot.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Hämta alla enheter som är anslutna till utrymmen på nivå 2 till 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Hämta alla enheter som är direkt anslutna till ett visst utrymme-ID.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Hämta alla enheter som är kopplade till en viss utrymme och dess underordnade.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Hämta alla enheter som är anslutna till underordnade av ett blanksteg, förutom det området.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Hämta alla enheter som är anslutna till direkt underordnade grupper till ett blanksteg.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Hämta alla enheter som är anslutna till en av överordnade för ett blanksteg.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Hämta alla enheter som är anslutna till underordnade ett utrymme som är på nivån mindre än eller lika med 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Hämta alla enheter som är anslutna till utrymmen som är på samma nivå som utrymme med ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Hämta anslutningssträngen för IoT Hub-enhet för din enhet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Få enheten med den angivna maskinvaru-ID, inklusive anslutna sensorer.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Hämta sensorer för specifika datatyper, i det här fallet *rörelse* och *temperatur*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Frågor för matchers och användardefinierade funktioner 

- Hämta alla etablerade matchers och deras ID: N.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Få information om en viss matcher, inklusive blanksteg och användardefinierad funktion som är associerade med den.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Utvärdera en matcher mot en sensor och aktivera loggning för felsökning. Avkastningen på det här HTTP GET-meddelandet anger om matcher och sensorn hör till datatypen. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Hämta ID för de användardefinierade funktionerna. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Hämta innehållet i en viss användardefinierad funktion 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Frågor för användare

Det här avsnittet visar vissa Exempelfrågor för API för att hantera användare i Azure Digital Twins. Se en HTTP GET-begäran som ersätter platshållarna med värden från din konfiguration. 

- Hämta alla användare. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Få en viss användare.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Nästa steg

Läs hur du autentiserar med ditt API Management, [autentisering med API: er](./security-authenticating-apis.md).

Om du vill se alla API-slutpunkter, läsa [hur du använder digitala Twins Swagger](./how-to-use-swagger.md).
