---
title: Webhook-aktivitet i Azure Data Factory
description: Webhook-aktiviteten fortsätter inte körningen av pipelinen förrän den validerar den bifogade datauppsättningen med vissa kriterier som användaren anger.
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
ms.openlocfilehash: ced2279878ee2eb361ec7338647418658e411513
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213005"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-aktivitet i Azure Data Factory

En webhook-aktivitet kan styra körningen av pipelines via din anpassade kod. Med webhook-aktiviteten kan kundernas kod anropa en slutpunkt och skicka en motringnings-URL. Pipeline-körningen väntar på motringningen innan den fortsätter till nästa aktivitet.

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

## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
**Namn** | Namnet på webhook-aktiviteten. | String | Ja |
**Typ** | Måste ställas in på "WebHook". | String | Ja |
**Metod** | REST API-metoden för målslutpunkten. | Sträng. Den typ som stöds är "POST". | Ja |
**Url** | Målslutpunkten och sökvägen. | En sträng eller ett uttryck med **resultType-värdet** för en sträng. | Ja |
**Headers** | Rubriker som skickas till begäran. Här är ett exempel som anger språk och `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`typ på en begäran: . | En sträng eller ett uttryck med **resultType-värdet** för en sträng. | Ja. En `Content-Type` rubrik `"headers":{ "Content-Type":"application/json"}` som krävs. |
**Kroppen** | Representerar nyttolasten som skickas till slutpunkten. | Giltig JSON eller ett uttryck med **värdet resultType** för JSON. Se [Schema för begäran om nyttolast](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) för schemat för nyttolasten för begäran. | Ja |
**autentisering** | Autentiseringsmetoden som används för att anropa slutpunkten. Typer som stöds är "Basic" och "ClientCertificate". Mer information finns i [Autentisering](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication). Om autentisering inte krävs utesluter du den här egenskapen. | En sträng eller ett uttryck med **resultType-värdet** för en sträng. | Inga |
**timeout** | Hur länge aktiviteten väntar på att motringningen som anges av **callBackUri** ska anropas. Standardvärdet är 10 minuter ("00:10:00"). Värden har TimeSpan-formatet *d*. *hh*:*mm*:*ss*. | String | Inga |
**Rapportstatus vid motringning** | Gör det möjligt för en användare att rapportera den misslyckade statusen för en webhook-aktivitet. | Boolean | Inga |

## <a name="authentication"></a>Autentisering

En webhook-aktivitet stöder följande autentiseringstyper.

### <a name="none"></a>Inget

Om autentisering inte krävs ska du **authentication** inte inkludera autentiseringsegenskapen.

### <a name="basic"></a>Basic

Ange användarnamn och lösenord som ska användas med grundläggande autentisering.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klientcertifikat

Ange base64-kodade innehållet i en PFX-fil och ett lösenord.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Hanterad identitet

Använd datafabrikens hanterade identitet för att ange den resurs-URI som åtkomsttoken begärs för. Använd för att anropa Azure `https://management.azure.com/`Resource Management API. Mer information om hur hanterade identiteter fungerar finns i [översikten över hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Om din datafabrik är konfigurerad med en Git-databas måste du lagra dina autentiseringsuppgifter i Azure Key Vault för att kunna använda grundläggande autentisering eller klientcertifikatautentisering. Azure Data Factory lagrar inte lösenord i Git.

## <a name="additional-notes"></a>Ytterligare information

Data Factory skickar ytterligare **egenskapsanropEt TillbakaUri** i brödtexten som skickas till URL-slutpunkten. Data Factory förväntar sig att den här URI:n anropas före det angivna timeout-värdet. Om URI:n inte anropas misslyckas aktiviteten med statusen "TimedOut".

Webhook-aktiviteten misslyckas när anropet till den anpassade slutpunkten misslyckas. Alla felmeddelanden kan läggas till i motringningstexten och användas i en senare aktivitet.

För varje REST API-anrop time time outs klienten om slutpunkten inte svarar inom en minut. Det här beteendet är standardpraxis för HTTP.This behavior is standard HTTP best practice. Lös problemet genom att implementera ett 202-mönster. I det aktuella fallet returnerar slutpunkten 202 (Accepterad) och klientavstämningen.

Tidsgränsen på en minut för begäran har inget att göra med tidsgränsen för aktiviteten. Den senare används för att vänta på motringningen som anges av **callbackUri**.

Kroppen som skickas tillbaka till motringnings-URI:n måste vara giltig JSON. Ställ `Content-Type` in `application/json`sidhuvudet på .

När du använder egenskapen **Rapportera status för motringning** måste du lägga till följande kod i brödtexten när du ringer motringningen:

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

Se följande kontrollflödesaktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
