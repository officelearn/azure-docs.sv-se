---
title: Azure App Service som Event Grid källa
description: I den här artikeln beskrivs hur du använder Azure App Service som en Event Grid händelse källa. Det innehåller schemat och länkar till självstudier och instruktions artiklar.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jafreebe
ms.openlocfilehash: 2465b2f260ed6c174b762fcf64a71100a148254d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106719"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service som en Event Grid källa

Den här artikeln innehåller egenskaper och schema för Azure App Service händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md). Du får också en lista med snabb starter och självstudier för att använda Azure App Service som en händelse källa.

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

### <a name="available-event-types"></a>Tillgängliga händelse typer

Azure App Service avger följande händelse typer

|    Händelsetyp                                             |    Description                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft. Web/Sites. BackupOperationStarted             |    Utlöses när en säkerhets kopiering har påbörjats                             |
|    Microsoft. Web/Sites. BackupOperationCompleted           |    Utlöses när en säkerhets kopiering har slutförts                           |
|    Microsoft. Web/Sites. BackupOperationFailed              |    Utlöses när en säkerhets kopiering misslyckades                              |
|    Microsoft. Web/Sites. RestoreOperationStarted            |    Utlöses när en återställning från en säkerhets kopia har startat        |
|    Microsoft. Web/Sites. RestoreOperationCompleted          |    Utlöses när en återställning från en säkerhets kopia har slutförts      |
|    Microsoft. Web/Sites. RestoreOperationFailed             |    Utlöses när en återställning från en säkerhets kopia misslyckades         |
|    Microsoft. Web/Sites. SlotSwapStarted                    |    Utlöses när en plats växling har startat                          |
|    Microsoft. Web/Sites. SlotSwapCompleted                  |    Utlöses när en plats växling har slutförts                      |
|    Microsoft. Web/Sites. SlotSwapFailed                     |    Utlöses när en plats växling har misslyckats                           |
|    Microsoft. Web/Sites. SlotSwapWithPreviewStarted         |    Utlöses när en plats växling med förhands granskning har startat           |
|    Microsoft. Web/Sites. SlotSwapWithPreviewCancelled       |    Utlöses när en plats växling med förhands granskning har avbrutits    |
|    Microsoft. Web/Sites. AppUpdated. startas om               |    Utlöses när en plats har startats om                      |
|    Microsoft. Web/Sites. AppUpdated. stoppad                 |    Utlöses när en plats har stoppats                          |
|    Microsoft. Web/Sites. AppUpdated.ChangedAppSettings      |    Utlöses när en platss appinställningar har ändrats             |
|    Microsoft. Web/Server grupper. AppServicePlanUpdated        |    Utlöses när ett App Services plan uppdateras                 |

### <a name="the-contents-of-an-event-response"></a>Innehållet i ett händelse svar

När en händelse utlöses skickar Event Grid-tjänsten data om händelsen för att prenumerera på slut punkten.
Det här avsnittet innehåller ett exempel på hur data ska se ut för varje händelse. Varje händelse har följande data på översta nivån:

|     Egenskap          |     Typ     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    ämne              |    sträng    |    Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet.                                      |
|    motiv            |    sträng    |    Publisher-definierad sökväg till händelsens ämne.                                                                                              |
|    Händelsetyp          |    sträng    |    En av de registrerade händelse typerna för den här händelse källan.                                                                                  |
|    Händelsetid          |    sträng    |    Tiden då händelsen genereras baserat på providerns UTC-tid.                                                                         |
|    id                 |    sträng    |    Unikt ID för händelsen.                                                                                                            |
|    data               |    objekt    |    Händelse data för Blob Storage.                                                                                                                    |
|    Dataversion        |    sträng    |    Data objektets schema version. Utgivaren definierar schemaversion.                                                          |
|    Metadataversion    |    sträng    |    Schema versionen för händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

Data-objektet innehåller följande egenskaper:

|    Egenskap                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objekt    |    Detaljerad information om appens åtgärd                                                                                       |
|    åtgärd                  |    sträng    |    Åtgärds typ för åtgärden                                                                                   |
|    name                    |    sträng    |    namnet på webbplatsen som innehåller den här händelsen                                                                          |
|    clientRequestId         |    sträng    |    ID för klientbegäran som genererats av App Service för den plats-API-åtgärd som utlöste händelsen         |
|    correlationRequestId    |    sträng    |    Korrelations förfrågan-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen    |
|    requestId               |    sträng    |    Det begär ande-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen                |
|    adress                 |    sträng    |    URL för HTTP-begäran för den här åtgärden                                                                                |
|    verb                    |    sträng    |    HTTP-verb för den här åtgärden                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Data-objektet innehåller följande egenskaper:

|    Egenskap                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objekt    |    Detaljerad information om appens åtgärd                                                                                       |
|    åtgärd                  |    sträng    |    Åtgärds typ för åtgärden                                                                                   |
|    name                    |    sträng    |    namnet på webbplatsen som innehåller den här händelsen                                                                          |
|    clientRequestId         |    sträng    |    ID för klientbegäran som genererats av App Service för den plats-API-åtgärd som utlöste händelsen         |
|    correlationRequestId    |    sträng    |    Korrelations förfrågan-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen    |
|    requestId               |    sträng    |    Det begär ande-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen                |
|    adress                 |    sträng    |    URL för HTTP-begäran för den här åtgärden                                                                                |
|    verb                    |    sträng    |    HTTP-verb för den här åtgärden                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Data-objektet innehåller följande egenskaper:

|    Egenskap                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objekt    |    Detaljerad information om appens åtgärd                                                                                       |
|    åtgärd                  |    sträng    |    Åtgärds typ för åtgärden                                                                                   |
|    name                    |    sträng    |    namnet på webbplatsen som innehåller den här händelsen                                                                          |
|    clientRequestId         |    sträng    |    ID för klientbegäran som genererats av App Service för den plats-API-åtgärd som utlöste händelsen         |
|    correlationRequestId    |    sträng    |    Korrelations förfrågan-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen    |
|    requestId               |    sträng    |    Det begär ande-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen                |
|    adress                 |    sträng    |    URL för HTTP-begäran för den här åtgärden                                                                                |
|    verb                    |    sträng    |    HTTP-verb för den här åtgärden                                                                                       |
|    sourceSlot              |    sträng    |    Växlingens käll plats                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Data-objektet innehåller följande egenskaper:

|    Egenskap                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objekt    |    Detaljerad information om appens åtgärd                                                                                       |
|    åtgärd                  |    sträng    |    Åtgärds typ för åtgärden                                                                                   |
|    name                    |    sträng    |    namnet på webbplatsen som innehåller den här händelsen                                                                          |
|    clientRequestId         |    sträng    |    ID för klientbegäran som genererats av App Service för den plats-API-åtgärd som utlöste händelsen         |
|    correlationRequestId    |    sträng    |    Korrelations förfrågan-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen    |
|    requestId               |    sträng    |    Det begär ande-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen                |
|    adress                 |    sträng    |    URL för HTTP-begäran för den här åtgärden                                                                                |
|    verb                    |    sträng    |    HTTP-verb för den här åtgärden                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. startas om, AppUpdated. stopped, AppUpdated. ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Data-objektet har följande egenskaper:

|    Egenskap                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objekt    |    Detaljerad information om appens åtgärd                                                                                       |
|    åtgärd                  |    sträng    |    Åtgärds typ för åtgärden                                                                                   |
|    name                    |    sträng    |    namnet på webbplatsen som innehåller den här händelsen                                                                          |
|    clientRequestId         |    sträng    |    ID för klientbegäran som genererats av App Service för den plats-API-åtgärd som utlöste händelsen         |
|    correlationRequestId    |    sträng    |    Korrelations förfrågan-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen    |
|    requestId               |    sträng    |    Det begär ande-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen                |
|    adress                 |    sträng    |    URL för HTTP-begäran för den här åtgärden                                                                                |
|    verb                    |    sträng    |    HTTP-verb för den här åtgärden                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Server grupper. AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

Data-objektet har följande egenskaper:

|    Egenskap                         |    Typ      |    Description                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    objekt    |    Information om åtgärder i App Service-planen                                                                          |
|    stampKind                        |    sträng    |    Typ av miljö där App Service-plan är                                                                     |
|    åtgärd                           |    sträng    |    Typ av åtgärd för App Service-planen                                                                            |
|    status                           |    sträng    |    Status för åtgärden i App Service-planen                                                                   |
|    sku                              |    objekt    |    SKU för App Service-planen                                                                                       |
|    name                             |    sträng    |    namnet på App Service-planen                                                                                      |
|    Nivå                             |    sträng    |    nivå för App Service-planen                                                                                      |
|    Storlek                             |    sträng    |    storlek på App Service-planen                                                                                      |
|    Familj                           |    sträng    |    familj av App Service-plan                                                                                        |
|    Kapacitet                         |    sträng    |    kapacitet för App Service-plan                                                                                      |
|    åtgärd                           |    sträng    |    Åtgärds typ för åtgärden                                                                                   |
|    name                             |    sträng    |    namnet på webbplatsen som innehåller den här händelsen                                                                          |
|    clientRequestId                  |    sträng    |    ID för klientbegäran som genererats av App Service för den plats-API-åtgärd som utlöste händelsen         |
|    correlationRequestId             |    sträng    |    Korrelations förfrågan-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen    |
|    requestId                        |    sträng    |    Det begär ande-ID som genererats av App Service för den plats-API-åtgärd som utlöste händelsen                |
|    adress                          |    sträng    |    URL för HTTP-begäran för den här åtgärden                                                                                |
|    verb                             |    sträng    |    HTTP-verb för den här åtgärden                                                                                       |
