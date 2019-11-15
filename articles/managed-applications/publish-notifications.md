---
title: Azure Managed Applications med meddelanden
description: Konfigurera hanterade program med webhook-slutpunkter för att ta emot meddelanden om att skapa, uppdatera, ta bort och fel i de hanterade program instanserna.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: a00e5be4493b8c8116e2925e88a3ce4bf8cfb722
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085322"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure Managed Applications med meddelanden

Azure-hanterade program meddelanden låter utgivare automatisera åtgärder baserat på livs cykel händelser för de hanterade program instanserna. Utgivare kan ange anpassade meddelande-webhook-slutpunkter för att ta emot händelse meddelanden om nya och befintliga hanterade program instanser. Det gör att utgivaren kan konfigurera anpassade arbets flöden vid tidpunkten för programmets etablering, uppdateringar och borttagning.

## <a name="getting-started"></a>Komma igång
Börja ta emot hanterade program genom att sätta upp en offentlig HTTPS-slutpunkt och ange den när du publicerar tjänst katalogens program definition eller Marketplace-erbjudandet.

Här är de rekommenderade stegen för att komma igång snabbt:
1. Skapa en offentlig HTTPS-slutpunkt som loggar inkommande POST-begäranden och returnerar `200 OK`.
2. Lägg till slut punkten för program definitionen för tjänst katalogen eller Marketplace-erbjudandet enligt beskrivningen nedan.
3. Skapa en hanterad program instans som hänvisar till program definitionen eller Marketplace-erbjudandet.
4. Verifiera att aviseringarna har tagits emot.
5. Aktivera auktorisering enligt beskrivningen i avsnittet **Endpoint Authentication** nedan.
6. Följ dokumentationen för **meddelande schema** nedan för att analysera meddelande förfrågningar och implementera affärs logik baserat på meddelandet.

## <a name="adding-service-catalog-application-definition-notifications"></a>Lägga till program definitions meddelanden för tjänst katalog
#### <a name="azure-portal"></a>Azure Portal
Kom igång [genom att läsa publicera ett tjänst katalog program via Azure Portal](./publish-portal.md) .

![Program definitions meddelanden för tjänst katalog på portalen](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>REST-API

> [!NOTE]
> För närvarande stöds endast en slut punkt som en del av **notificationEndpoints** i egenskaperna för program definitionen

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="adding-marketplace-managed-application-notifications"></a>Lägga till Marketplace-hanterade program meddelanden
Mer information finns i [skapa ett erbjudande för Azure-program](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Program definitions meddelanden för tjänst katalog på portalen](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Händelseutlösare
I följande tabell beskrivs alla möjliga kombinationer av EventType + ProvisioningState och deras utlösare:

Typ | ProvisioningState | Utlösare för avisering
---|---|---
PLACERA | Accept | Den hanterade resurs gruppen har skapats och projicerats efter att program har lagts till. (Innan distributionen i den hanterade RG startas.)
PLACERA | Lyckades | Fullständig etablering av det hanterade programmet lyckades efter en placering.
PLACERA | Misslyckad | Det gick inte att ställa in program instansens etablerings plats.
9\.0a | Lyckades | Efter en lyckad korrigering på den hanterade program instansen för att uppdatera taggar, JIT-åtkomstkontroll eller hanterad identitet.
DELETE | Tas bort | Så snart användaren initierar en borttagning av en hanterad App-instans.
DELETE | Borttagen | Efter en fullständig och lyckad borttagning av det hanterade programmet.
DELETE | Misslyckad | Efter ett fel under avetablerings processen som blockerar borttagningen.
## <a name="notification-schema"></a>Meddelande schema
När du skapar en webhook-slutpunkt för att hantera meddelanden måste du parsa nytto lasten för att få viktiga egenskaper för att sedan agera på aviseringen. Både tjänst katalogen och Marketplace-hanterade program meddelanden innehåller många av samma egenskaper med den små skillnaden som beskrivs nedan.

#### <a name="service-catalog-application-notification-schema"></a>Tjänst katalog program aviserings schema
Här är ett exempel på en tjänst katalog meddelande efter en lyckad etablering av en hanterad program instans.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Om etableringen Miss lyckas skickas ett meddelande med fel information till den angivna slut punkten.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="marketplace-application-notification-schema"></a>Program meddelande schema för Marketplace

Här är ett exempel på en tjänst katalog meddelande efter en lyckad etablering av en hanterad program instans.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Om etableringen Miss lyckas skickas ett meddelande med fel information till den angivna slut punkten.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parameter | Beskrivning
---|---
eventType | Typ av händelse som utlöste meddelandet. (t. ex. "placering", "korrigering", "ta bort")
applicationId | Det fullständigt kvalificerade resurs-ID: t för det hanterade program som meddelandet utlöstes för. 
eventTime | Tidsstämpeln för händelsen som utlöste meddelandet. (Datum och tid i formatet UTC-ISO 8601.)
ProvisioningState | Etablerings statusen för den hanterade program instansen. (t. ex. "lyckad", "Misslyckad", "borttagning", "borttagen")
error | *Endast anges när ProvisioningState misslyckades*. Innehåller felkod, meddelande och information om problemet som orsakade felet.
applicationDefinitionId | *Endast angivet för hanterade program i tjänst katalogen*. Representerar det fullständigt kvalificerade resurs-ID: t för den program definition som den hanterade program instansen etablerades för.
projektplan | *Endast angivet för Marketplace-hanterade program*. Representerar utgivare, erbjudande, SKU och version för den hanterade program instansen.

## <a name="endpoint-authentication"></a>Endpoint Authentication
För att skydda webhook-slutpunkten och se till att aviseringen är äkta:
- Ange en frågeparameter ovanpå webhook-URI: n som https://your-endpoint.com?sig=Guid. Med varje meddelande gör du en snabb kontroll att Frågeparametern `sig` har det förväntade värdet `Guid`.
- Utfärda en GET på den hanterade program instansen med applicationId. Kontrol lera att provisioningState matchar provisioningState för meddelandet för att säkerställa konsekvens.

## <a name="notification-retries"></a>Aviserings försök

Aviserings tjänsten för hanterade program förväntar sig ett `200 OK` svar från webhook-slutpunkten till meddelandet. Meddelande tjänsten kommer att försöka igen om webhook-slutpunkten returnerar en HTTP-felkod > = 500, 429 eller om slut punkten är tillfälligt otillgänglig. Om webhook-slutpunkten inte blir tillgänglig inom 10 timmar tas aviserings meddelandet bort och Återförsöken stoppas.

