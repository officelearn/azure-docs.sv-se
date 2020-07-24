---
title: Modul, dubbla JSON-schema – Azure
description: I det här avsnittet beskrivs modul dubbla JSON-scheman för video analys i real tid på IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8bd86bdc2c8de9ee586e785db2074fa772100420
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053065"
---
# <a name="module-twin-json-schema"></a>Modul, dubbla JSON-schema

Enhets dubbla är JSON-dokument som lagrar information om enhets tillstånd, inklusive metadata, konfigurationer och villkor. Azure IoT Hub lagrar en enhetstvilling för varje enhet som du ansluter till IoT Hub. För detaljerad förklaring, se [förstå och använda modul dubbla i IoT Hub](../../iot-hub/iot-hub-devguide-module-twins.md)

I det här avsnittet beskrivs modul dubbla JSON-scheman för video analys i real tid på IoT Edge.

> [!NOTE]
> Du måste autentiseras innan du kan få åtkomst till Media Services-resurser och Media Services-API:et. Mer information finns i [komma åt Azure Media Services API](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api).

## <a name="module-twin-properties"></a>Modul, dubbla egenskaper

Video analys i real tid på IoT Edge visar följande modul, dubbla egenskaper. 

|Egenskap |Krävs |Dynamisk |Beskrivning |
|---|---|---|---|
|applicationDataDirectory |Yes |No |Sökväg till en monterad volym för att spara konfigurationen. |
|azureMediaServicesArmId |Yes |No |Unikt ID för Azure-resurs hantering för det Media Services kontot.|
|aadTenantId |Yes |No |Kund-ID för Azure AD-klient.|
|aadServicePrincipalAppId |Ja |Ja |Kunden skapade Azure AD AppId.|
|aadServicePrincipalCertificate |Ja<sup>*</sup>  |Yes |Kunden skapade Azure AD AppId-certifikatet.|
|aadServicePrincipalPassword |Ja<sup>*</sup>  |Yes |Kunden skapade Azure AD AppId-lösenord.|
|aadEndpoint |Inga |Inga |Molnbaserad Azure AD-slutpunkt. <br/>Objekt`https://login.microsoftonline.com` |
|aadResourceId |Inga |Inga |Molnbaserad Azure AD-Audience/resurs-ID <br/>Objekt`https://management.core.windows.net/` |
|armEndpoint |Inga |Inga |Molnbaserad Azure Resource Manage-slutpunkt. <br/>Objekt`https://management.azure.com/` |
|diagnosticsLevel |No |Yes |Utförlig händelse: <br/>Information & # x02758; Varning & # x02758; Fel & # x02758; Kritiskt & # x02758; Alternativet |
|diagnosticsEventsOutputName |No |Yes |NAV-utdata för diagnostiska händelser. <br/>(Tom innebär att diagnostik inte publiceras)|
|operationalEventsOutputName|No|Yes|NAV-utdata för drift händelser.<br/>(Tom innebär att operativa händelser inte publiceras)
|logLevel|No|Yes|Något av följande: <br/>& # x000B7; Utförlig<br/>& # x000B7; Information (standard)<br/>& # x000B7; Honom<br/>& # x000B7; Fels<br/>& # x000B7; Alternativet|
|logCategories|No|Yes|En kommaavgränsad lista över följande: program, MediaPipeline, händelser <br/>Standard: program, händelser|
|debugLogsDirectory|No|Yes|Katalog för fel söknings loggar. Om nuvarande loggar genereras inaktive ras om inte några fel söknings loggar har inaktiverats.

<sup>*</sup>Du måste ange antingen tjänstens huvud certifikat eller lösen ord. 

Dynamiska egenskaper kan uppdateras utan att modulen startas om. Du kan hämta värdena för flera av dessa egenskaper genom att följa anvisningarna i artikeln [få åtkomst till Media Services API](../latest/access-api-howto.md) . 

Se artikeln om [övervakning och loggning](monitoring-logging.md) för mer information om rollen för de valfria diagnostikinställningar.

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>Nästa steg

[Direkta metoder](direct-methods.md)
