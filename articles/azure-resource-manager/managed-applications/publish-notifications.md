---
title: Hanterade appar med aviseringar
description: Konfigurera hanterade program med webhook-slutpunkter för att ta emot meddelanden om skapar, uppdateringar, borttagningar och fel i hanterade programinstanser.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715683"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure-hanterade program med meddelanden

Azure-hanterade programmeddelanden gör det möjligt för utgivare att automatisera åtgärder baserat på livscykelhändelser för hanterade programinstanser. Utgivare kan ange anpassade webhook-slutpunkter för att ta emot händelsemeddelanden om nya och befintliga hanterade programinstanser. Utgivare kan konfigurera anpassade arbetsflöden vid tidpunkten för programetablering, uppdateringar och borttagning.

## <a name="getting-started"></a>Komma igång
Om du vill börja ta emot hanterade program startar du en offentlig HTTPS-slutpunkt och anger den när du publicerar programdefinitionen för tjänstkatalogen eller Azure Marketplace-erbjudandet.

Här är de rekommenderade stegen för att komma igång snabbt:
1. Skapa en offentlig HTTPS-slutpunkt som loggar inkommande `200 OK`POST-begäranden och returnerar .
2. Lägg till slutpunkten i programdefinitionen för tjänstkatalogen eller Azure Marketplace-erbjudandet som förklaras senare i den här artikeln.
3. Skapa en hanterad programinstans som refererar till programdefinitionen eller Azure Marketplace-erbjudandet.
4. Verifiera att meddelandena tas emot.
5. Aktivera auktorisering enligt beskrivningen i avsnittet **Slutpunktsautentisering** i den här artikeln.
6. Följ instruktionerna i avsnittet **Meddelandeschema** i den här artikeln för att tolka meddelandebegäranden och implementera din affärslogik baserat på meddelandet.

## <a name="add-service-catalog-application-definition-notifications"></a>Lägga till programdefinitionsmeddelanden för tjänstkatalog
#### <a name="azure-portal"></a>Azure Portal
Information om hur du kommer igång finns i [Publicera ett tjänstkatalogprogram via Azure Portal](./publish-portal.md).

![Definition av tjänstkatalogprogram i Azure-portalen](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> För närvarande kan du bara ange `notificationEndpoints` en slutpunkt i egenskaperna för programdefinitionen.

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>Lägga till hanterade programmeddelanden för Azure Marketplace
Mer information finns i [Skapa ett Azure-programerbjudande](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Hanterade programmeddelanden i Azure Marketplace i Azure-portalen](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Händelseutlösare
I följande tabell beskrivs alla möjliga kombinationer av EventType och ProvisioningState och deras utlösare:

Eventtype | EtableringStat | Utlösare för meddelande
---|---|---
PUT | Accepterad | Hanterad resursgrupp har skapats och projicerats efter att programmet PUT (innan distributionen i den hanterade resursgruppen startas).
PUT | Lyckades | Fullständig etablering av det hanterade programmet lyckades efter en PUT.
PUT | Misslyckades | Fel i PUT av ansökan instans etablering vid något tillfälle.
Patch | Lyckades | Efter en lyckad PATCH på den hanterade programinstansen för att uppdatera taggar, JIT-åtkomstprincip eller hanterad identitet.
DELETE | Ta bort | Så snart användaren initierar en DELETE av en hanterad appinstans.
DELETE | Borttagen | Efter fullständig och lyckad borttagning av det hanterade programmet.
DELETE | Misslyckades | Efter eventuella fel under avetableringsprocessen som blockerar borttagningen.
## <a name="notification-schema"></a>Schema för meddelanden
När du vrider upp webhook-slutpunkten för att hantera aviseringar måste du tolka nyttolasten för att få viktiga egenskaper att sedan agera på aviseringen. Tjänstkatalog och Azure Marketplace-hanterade programmeddelanden innehåller många av samma egenskaper. Två små skillnader beskrivs i tabellen som följer proverna.

#### <a name="service-catalog-application-notification-schema"></a>Meddelandeschema för tjänstkatalogprogram
Här är ett exempel tjänst katalog meddelande efter den lyckade etableringen av en hanterad programinstans:
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

Om etableringen misslyckas skickas ett meddelande med felinformationen till den angivna slutpunkten.

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

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Marketplace-programmeddelandeschema

Här är ett exempel tjänst katalog meddelande efter den lyckade etableringen av en hanterad programinstans:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Om etableringen misslyckas skickas ett meddelande med felinformationen till den angivna slutpunkten.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
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
Händelsetyp | Den typ av händelse som utlöste meddelandet. (TILL exempel PUT, PATCH, DELETE.)
applicationId | Den fullständigt kvalificerade resursidentifieraren för det hanterade programmet som meddelandet utlöstes för.
Händelsetid | Tidsstämpeln för händelsen som utlöste meddelandet. (Datum och tid i UTC ISO 8601-format.)
etableringStaten | Etableringstillståndet för den hanterade programinstansen. (Till exempel Lyckades, Misslyckades, Ta bort, Bort.)
fel | *Anges endast när etableringstaten misslyckades*. Innehåller felkoden, meddelandet och information om problemet som orsakade felet.
ansökanDefinitionId | *Angiven endast för hanterade program i tjänstkatalogen*. Representerar den fullständigt kvalificerade resursidentifieraren för den programdefinition som den hanterade programinstansen har etablerats för.
planera | *Angiven endast för Azure Marketplace-hanterade program*. Representerar utgivaren, erbjudandet, SKU och versionen av den hanterade programinstansen.
billingDetails | *Angiven endast för Azure Marketplace-hanterade program.* Faktureringsinformationen för den hanterade programinstansen. Innehåller den resursUsageId som du kan använda för att fråga Azure Marketplace för användningsinformation.

## <a name="endpoint-authentication"></a>Autentisering av slutpunkt
Så här säkrar du webhook-slutpunkten och säkerställer meddelandets äkthet:
1. Ange en frågeparameter ovanpå webhook-URI:n, så här: https\://your-endpoint.com?sig=Guid. Kontrollera att frågeparametern `sig` har det `Guid`förväntade värdet med varje meddelande .
2. Utfärda en GET på den hanterade programinstansen med hjälp av applicationId. Verifiera att etableringstaten matchar etableringstaten av meddelandet för att säkerställa konsekvens.

## <a name="notification-retries"></a>Återförsök i anmälan

Tjänsten Meddelande om hanterat program förväntar sig ett `200 OK` svar från webhook-slutpunkten till meddelandet. Meddelandetjänsten försöker igen om webhook-slutpunkten returnerar en HTTP-felkod som är större än eller lika med 500, om den returnerar en felkod på 429 eller om slutpunkten tillfälligt inte kan nås. Om webhook-slutpunkten inte blir tillgänglig inom 10 timmar kommer meddelandemeddelandet att tas bort och försöken stoppas.
