---
title: Webhook-aktivitet i Azure Data Factory
description: Webhook-aktiviteten fortsätter inte att köra pipelinen förrän den verifierar den bifogade data uppsättningen med vissa villkor som användaren anger.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417886"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-aktivitet i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En webhook-aktivitet kan styra körningen av pipeliner via din anpassade kod. Med webhook-aktiviteten kan kundernas kod anropa en slut punkt och skicka den till en återanrops-URL. Pipeline-körningen väntar på motringningen innan den fortsätter till nästa aktivitet.

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
**Namn** | Namnet på webhook-aktiviteten. | Sträng | Ja |
**bastyp** | Måste vara inställt på "webhook". | Sträng | Ja |
**metodsignatur** | REST API metod för mål slut punkten. | Sträng. Den typ som stöds är "POST". | Ja |
**url** | Mål slut punkten och sökvägen. | En sträng eller ett uttryck med **resultType** -värdet för en sträng. | Ja |
**sidhuvud** | Huvuden som skickas till begäran. Här är ett exempel som anger språk och typ på en begäran: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | En sträng eller ett uttryck med **resultType** -värdet för en sträng. | Ja. En `Content-Type` rubrik som `"headers":{ "Content-Type":"application/json"}` är obligatorisk. |
**brödtext** | Representerar den nytto last som skickas till slut punkten. | Giltigt JSON eller ett uttryck med **resultType** -värdet för JSON. Se [nytto Last schema för begäran](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) om schemat för nytto lasten för begäran. | Ja |
**anspråksautentisering** | Autentiseringsmetoden som används för att anropa slut punkten. De typer som stöds är "grundläggande" och "ClientCertificate". Mer information finns i [Autentisering](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication). Om autentisering inte krävs utelämnar du den här egenskapen. | En sträng eller ett uttryck med **resultType** -värdet för en sträng. | Nej |
**timeout** | Hur länge aktiviteten väntar på att återanropet som anges av **callBackUri** ska anropas. Standardvärdet är 10 minuter ("00:10:00"). Värdena har formatet TimeSpan *d*. *HH*:*mm*:*SS*. | Sträng | Nej |
**Rapportera status vid motringning** | Låter en användare rapportera den misslyckade statusen för en webhook-aktivitet. | Boolesk | Nej |

## <a name="authentication"></a>Autentisering

En webhook-aktivitet stöder följande typer av autentisering.

### <a name="none"></a>Inga

Om autentisering inte krävs ska du inte inkludera egenskapen **Authentication** .

### <a name="basic"></a>Basic

Ange det användar namn och lösen ord som ska användas med grundläggande autentisering.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klient certifikat

Ange det Base64-kodade innehållet i en PFX-fil och ett lösen ord.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Hanterad identitet

Använd Data Factory: s hanterade identitet för att ange den resurs-URI som åtkomsttoken begärs för. Använd `https://management.azure.com/`för att anropa Azure Resource Management-API: et. Mer information om hur hanterade identiteter fungerar finns i [Översikt över hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Om din data fabrik har kon figurer ATS med en git-lagringsplats måste du lagra dina autentiseringsuppgifter i Azure Key Vault för att kunna använda Basic eller autentisering med klient certifikat. Azure Data Factory lagrar inte lösen ord i git.

## <a name="additional-notes"></a>Ytterligare information

Data Factory skickar ytterligare egenskapen **callBackUri** i texten som skickas till URL-slutpunkten. Data Factory förväntar sig att denna URI ska anropas före det angivna tids gräns värdet. Om URI: n inte anropas, Miss lyckas aktiviteten med statusen "stängningsåtgärd".

Webhook-aktiviteten Miss lyckas när anropet till den anpassade slut punkten Miss lyckas. Eventuella fel meddelanden kan läggas till i återanrops texten och användas i en senare aktivitet.

För varje REST API-anrop har klienten nått tids gränsen om slut punkten inte svarar inom en minut. Det här är en standard metod för HTTP-användning. För att åtgärda det här problemet implementerar du ett 202-mönster. I det aktuella fallet returnerar slut punkten 202 (accepterad) och klient avsökningarna.

Tids gränsen på en minut på begäran har inget att göra med aktivitetens tids gräns. Den senare används för att vänta på återanropet som anges av **callbackUri**.

Texten som skickas tillbaka till återanrops-URI: n måste vara giltig JSON. Ange `Content-Type` sidhuvudet till `application/json`.

När du använder egenskapen **rapportera status på motringning** måste du lägga till följande kod i bröd texten när du gör återanropet:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Nästa steg

Se följande kontroll flödes aktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Köra pipelineaktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Tills-aktivitet](control-flow-until-activity.md)
