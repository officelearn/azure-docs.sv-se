---
title: Azure Blockchain Workbench meddelanden översikt över integration
description: Översikt över användning av meddelanden i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/01/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 48e7de2798d9c34942df281febcc1d4ec443010d
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662236"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench messaging integration

Förutom att tillhandahålla ett REST-API, dessutom Azure Blockchain Workbench messaging-baserad integrering. Workbench publicerar ledger-centric händelser via Azure Event Grid, aktivera underordnade konsumenter att mata in data eller vidta åtgärder baserat på dessa händelser. För de klienter som kräver tillförlitlig meddelandehantering levererar meddelanden till en Azure Service Bus-slutpunkt i Azure Blockchain Workbench.

## <a name="input-apis"></a>Indata-API: er

Om du vill initiera transaktioner från externa system för att skapa användare, skapa kontrakt och uppdatera kontrakt, kan du använda meddelanden inkommande API: er för att genomföra transaktioner på ett transaktionsregister. Se [meddelanden integration exempel](https://aka.ms/blockchain-workbench-integration-sample) för ett exempel som visar indata API: er.

Följande är för närvarande tillgänglig indata API: er.

### <a name="create-user"></a>Skapa användare

Skapar en ny användare.

Begäran kräver följande fält:

| **Namn**             | **Beskrivning**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Klienten angivna GUID                                |
| Förnamn            | Förnamn för användaren                              |
| lastName             | Efternamn för användaren                               |
| emailAddress         | E-postadressen för användaren                           |
| externalId           | Azure AD-objekt-ID för användaren                      |
| ConnectionId         | Unik identifierare för blockchain-anslutning |
| messageSchemaVersion | Meddelanden schemaversion                            |
| messageName          | **CreateUserRequest**                               |

Exempel:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench returnerar ett svar med följande fält:

| **Namn**              | **Beskrivning**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | Klienten angivna GUID |
| userId                | ID för den användare som har skapats |
| userChainIdentifier   | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum, adressen är användarens **i kedjan** adress. |
| ConnectionId          | Unik identifierare för blockchain-anslutning|
| messageSchemaVersion  | Meddelanden schemaversion |
| messageName           | **CreateUserUpdate** |
| status                | Status för skapa användarbegäran.  Om lyckas, är värdet **lyckades**. Värdet är på fel, **fel**.     |
| AdditionalInformation | Ytterligare informationen baserat på status |

Exempel lyckad **skapa användare** svar från Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Om begäran lyckades, med information om felet i ytterligare information.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Skapa kontrakt

Skapar ett nytt kontrakt.

Begäran kräver följande fält:

| **Namn**             | **Beskrivning**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | Klienten angivna GUID |
| userChainIdentifier  | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum, är den här adressen användarens **på kedjan** adress. |
| ApplicationName      | Namnet på programmet |
| version              | Versionen av programmet. Obligatoriskt om du har flera versioner av programmet aktiverat. Annars är version valfritt. Läs mer om programmet versionshantering [Azure Blockchain Workbench programmet versionshantering](version-app.md). |
| WorkflowName         | Namnet på arbetsflödet |
| parameters           | Parametrar som har angetts för skapande av kontrakt |
| ConnectionId         | Unik identifierare för blockchain-anslutning |
| messageSchemaVersion | Meddelanden schemaversion |
| messageName          | **CreateContractRequest** |

Exempel:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench returnerar ett svar med följande fält:

| **Namn**                 | **Beskrivning**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | Klienten angivna GUID                                                             |
| contractId               | Unik identifierare för kontraktet inuti Azure Blockchain Workbench |
| contractLedgerIdentifier | Kontraktet på redovisningen-adress                                            |
| ConnectionId             | Unik identifierare för blockchain-anslutning                               |
| messageSchemaVersion     | Meddelanden schemaversion                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Status för begäran om kontrakt skapas.  Möjliga värden: **Skickat**, **allokerat**, **fel**.  |
| AdditionalInformation    | Ytterligare informationen baserat på status                              |

Exempel på en skickade **skapa kontrakt** svar från Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Exempel på ett dedikerat **skapa kontrakt** svar från Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Om begäran lyckades, med information om felet i ytterligare information.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Skapa kontrakt åtgärd

Skapar en ny kontrakt-åtgärd.

Begäran kräver följande fält:

| **Namn**                 | **Beskrivning**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | Klienten angivna GUID |
| userChainIdentifier      | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum, är den här adressen användarens **på kedjan** adress. |
| contractLedgerIdentifier | Kontraktet på redovisningen-adress |
| version                  | Versionen av programmet. Obligatoriskt om du har flera versioner av programmet aktiverat. Annars är version valfritt. Läs mer om programmet versionshantering [Azure Blockchain Workbench programmet versionshantering](version-app.md). |
| workflowFunctionName     | Namnet på arbetsflödesfunktion |
| parameters               | Parametrar som har angetts för skapande av kontrakt |
| ConnectionId             | Unik identifierare för blockchain-anslutning |
| messageSchemaVersion     | Meddelanden schemaversion |
| messageName              | **CreateContractActionRequest** |

Exempel:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench returnerar ett svar med följande fält:

| **Namn**              | **Beskrivning**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | Klienten angivna GUID|
| contractId            | Unik identifierare för kontraktet inuti Azure Blockchain Workbench |
| ConnectionId          | Unik identifierare för blockchain-anslutning |
| messageSchemaVersion  | Meddelanden schemaversion |
| messageName           | **CreateContractActionUpdate** |
| status                | Status för begäran om åtgärden kontraktet. Möjliga värden: **Skickat**, **allokerat**, **fel**.                         |
| AdditionalInformation | Ytterligare informationen baserat på status |

Exempel på en skickade **skapa kontrakt åtgärd** svar från Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exempel på ett dedikerat **skapa kontrakt åtgärd** svar från Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

Om begäran lyckades, med information om felet i ytterligare information.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Inkommande API-felkoder och meddelanden

**Felkod 4000: Felaktig begäran fel**
- Ogiltig connectionId
- Det gick inte att avbryta serialiseringen CreateUserRequest
- Det gick inte att avbryta serialiseringen CreateContractRequest
- Det gick inte att avbryta serialiseringen CreateContractActionRequest
- Programmet {identifieras av programnamnet} finns inte
- Programmet {identifieras av programnamnet} har inte arbetsflöde
- UserChainIdentifier finns inte
- Kontrakt {identifieras av transaktionsregister identifierare} finns inte
- Kontraktet {identifieras av transaktionsregister ID} har inte funktionen {arbetsflöde funktionsnamn}
- UserChainIdentifier finns inte

**Felkod 4090: Fel vid konfliktlösning**
- Användaren finns redan
- Kontraktet finns redan
- Det finns redan kontrakt-åtgärd

**Felkod 5000: Internt serverfel**
- Undantag meddelanden

## <a name="event-notifications"></a>Händelseaviseringar

Händelsemeddelanden kan användas för att meddela användare och underordnade system händelser som inträffar i Blockchain Workbench och blockchain-nätverk som den är ansluten till. Händelsemeddelanden kan konsumeras direkt i kod eller med verktyg som Logic Apps och Flow används för att utlösa flödet av data till underordnade system.

Se [referens för Notification](#notification-message-reference) för information om olika meddelanden som tas emot.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Använda Event Grid-händelser med Azure Functions

Om en användare vill använda Event Grid för att få meddelanden om händelser som inträffar i Blockchain Workbench kan du använda händelser från Event Grid med Azure Functions.

1. Skapa en **Azure Function-App** i Azure-portalen.
2. Skapa en ny funktion.
3. Leta upp mallen för Event Grid. Grundläggande mallkod för att läsa meddelandet visas. Ändra koden efter behov.
4. Spara funktionen. 
5. Välj Event Grid Blockchain Workbench resursgrupp.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Använda Event Grid-händelser med Logic Apps

1. Skapa en ny **Azure Logic App** i Azure-portalen.
2. När du öppnar Azure Logic App i portalen, uppmanas du att välja en utlösare. Välj **Azure Event Grid – om en resurshändelse inträffar**.
3. När Arbetsflödesdesignern visas, uppmanas du att logga in.
4. Välj prenumerationen. Resurs som **Microsoft.EventGrid.Topics**. Välj den **resursnamn** från namnet på resursen från resursgruppen Azure Blockchain Workbench.
5. Välj Event Grid Blockchain Workbench resursgrupp.

## <a name="using-service-bus-topics-for-notifications"></a>Använda Service Bus-ämnen för meddelanden

Service Bus-ämnen kan användas för att meddela användare om händelser som inträffar i Blockchain Workbench. 

1. Bläddra till en Service Bus i Workbench-resursgrupp.
2. Välj **ämnen**.
3. Välj **utgående avsnittet**.
4. Skapa en ny prenumeration på det här ämnet. Hämta en nyckel för den.
5. Skapa ett program som prenumererar på händelser från den här prenumerationen.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Använda Service Bus-meddelanden med Logic Apps

1. Skapa en ny **Azure Logic App** i Azure-portalen.
2. När du öppnar Azure Logic App i portalen, uppmanas du att välja en utlösare. Typ **Service Bus** i sökrutan och välj lämplig för typ av interaktion utlösaren som du vill ha med Service Bus. Till exempel **Service Bus – när ett meddelande tas emot i en ämnesprenumeration (Komplettera automatiskt)**.
3. Ange anslutningsinformation för Service Bus när Arbetsflödesdesignern visas.
4. Välj din prenumeration och ange ämnet för **workbench externa**.
5. Utveckla logiken för ditt program som använder meddelandet från den här utlösaren.

## <a name="notification-message-reference"></a>Referens för meddelande

Beroende på den **OperationName**, meddelanden har någon av följande typer av meddelanden.

### <a name="block-message"></a>Blockeringsmeddelande

Innehåller information om enskilda block. Den *BlockMessage* innehåller ett avsnitt med block nivåinformation och ett avsnitt med information om transaktioner.

| Namn | Beskrivning |
|------|-------------|
| blockera | Innehåller [blockera information](#block-information) |
| transaktioner | Innehåller en samling [transaktionsinformation](#transaction-information) för blocket |
| ConnectionId | Unik identifierare för anslutningen |
| messageSchemaVersion | Meddelanden schemaversion |
| messageName | **BlockMessage** |
| AdditionalInformation | Ytterligare information |

#### <a name="block-information"></a>Blockera information

| Namn              | Beskrivning |
|-------------------|-------------|
| Block-ID           | Unik identifierare för block i Azure Blockchain Workbench |
| blockNumber       | Unik identifierare för ett block på redovisningen |
| blockHash         | Hash-blockets |
| previousBlockHash | Hash för föregående block |
| blockTimestamp    | Tidsstämpel för blocket |

#### <a name="transaction-information"></a>Transaktionsinformation

| Namn               | Beskrivning |
|--------------------|-------------|
| transactionId      | Unik identifierare för transaktionen inuti Azure Blockchain Workbench |
| TransactionHash    | Hash för transaktionen på redovisningen |
| från               | Unik identifierare på redovisningen för transaktionens ursprung |
| till                 | Unik identifierare på redovisningen för mål-transaktion |
| provisioningStatus | Identifierar den aktuella statusen för etableringsprocessen för transaktionen. Möjliga värden: </br>0 – transaktionen har skapats av API: et i databasen</br>1 – transaktionen har skickats till redovisningen</br>2 – transaktionen har har allokerats till redovisningen</br>3 eller 4 - transaktionen kunde inte genomföras i redovisningen</br>5 – transaktionen har skickats till redovisningen |

Exempel på en *BlockMessage* från Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Kontrakt-meddelande

Innehåller information om ett kontrakt. Meddelandet innehåller ett avsnitt med kontrakt egenskaper och ett avsnitt med information om transaktioner. Alla transaktioner som har ändrat kontraktet för visst block ingår i avsnittet transaktion.

| Namn | Beskrivning |
|------|-------------|
| Block-ID | Unik identifierare för block i Azure Blockchain Workbench |
| blockHash | Hash-blockets |
| modifyingTransactions | [Transaktioner som ändrade](#modifying-transaction-information) kontraktet |
| contractId | Unik identifierare för kontraktet inuti Azure Blockchain Workbench |
| contractLedgerIdentifier | Unik identifierare för kontraktet på redovisningen |
| contractProperties | [Egenskaper för kontraktet](#contract-properties) |
| isNewContract | Anger huruvida det här kontraktet skapades nyligen. Möjliga värden är: SANT: det här kontraktet har ett nytt kontrakt skapas. FALSKT: det här kontraktet är en kontrakt-uppdatering. |
| ConnectionId | Unik identifierare för anslutningen |
| messageSchemaVersion | Meddelanden schemaversion |
| messageName | **ContractMessage** |
| AdditionalInformation | Ytterligare information |

#### <a name="modifying-transaction-information"></a>Ändra transaktionsinformation

| Namn               | Beskrivning |
|--------------------|-------------|
| transactionId | Unik identifierare för transaktionen inuti Azure Blockchain Workbench |
| TransactionHash | Hash för transaktionen på redovisningen |
| från | Unik identifierare på redovisningen för transaktionens ursprung |
| till | Unik identifierare på redovisningen för mål-transaktion |

#### <a name="contract-properties"></a>Kontrakt-egenskaper

| Namn               | Beskrivning |
|--------------------|-------------|
| WorkflowPropertyId | Unik identifierare för arbetsflöde-egenskapen i Azure Blockchain Workbench |
| namn | Namnet på egenskapen arbetsflöde |
| värde | Värdet för egenskapen arbetsflöde |

Exempel på en *ContractMessage* från Blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Händelsemeddelandet: Kontrakt-funktionsanrop

Innehåller information när ett kontrakt-funktionen anropas som funktionsnamnet, parametrar som har angetts och den som anroparen funktionen.

| Namn | Beskrivning |
|------|-------------|
| EventName                   | **ContractFunctionInvocation** |
| uppringare                      | [Anroparen information](#caller-information) |
| contractId                  | Unik identifierare för kontraktet inuti Azure Blockchain Workbench |
| contractLedgerIdentifier    | Unik identifierare för kontraktet på redovisningen |
| Funktionsnamn                | Namnet på funktionen |
| parameters                  | [Parameterinformation](#parameter-information) |
| Transaktionen                 | [Transaktionsinformation](#eventmessage-transaction-information) |
| inTransactionSequenceNumber | Sekvensnumret för transaktionen i blocket |
| ConnectionId                | Unik identifierare för anslutningen |
| messageSchemaVersion        | Meddelanden schemaversion |
| messageName                 | **EventMessage** |
| AdditionalInformation       | Ytterligare information |

#### <a name="caller-information"></a>Anroparen information

| Namn | Beskrivning |
|------|-------------|
| typ | Typ av anroparen som en användare eller ett kontrakt |
| id | Unik identifierare för anroparen inuti Azure Blockchain Workbench |
| ledgerIdentifier | Unik identifierare för anroparen på redovisningen |

#### <a name="parameter-information"></a>Parameterinformation

| Namn | Beskrivning |
|------|-------------|
| namn | Parameternamn |
| värde | Parametervärde |

#### <a name="event-message-transaction-information"></a>Information om händelsen meddelande transaktion

| Namn               | Beskrivning |
|--------------------|-------------|
| transactionId      | Unik identifierare för transaktionen inuti Azure Blockchain Workbench |
| TransactionHash    | Hash för transaktionen på redovisningen |
| från               | Unik identifierare på redovisningen för transaktionens ursprung |
| till                 | Unik identifierare på redovisningen för mål-transaktion |

Exempel på en *EventMessage ContractFunctionInvocation* från Blockchain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Händelsemeddelandet: Program-inmatning

Innehåller information när ett program har överförts till Workbench, t.ex. namn och version av programmet som laddats upp.

| Namn | Beskrivning |
|------|-------------|
| EventName | **ApplicationIngestion** |
| ApplicationId | Unik identifierare för programmet i Azure Blockchain Workbench |
| ApplicationName | Programnamn |
| applicationDisplayName | Programvisningsnamn |
| applicationVersion | Programversion |
| applicationDefinitionLocation | URL: en där programmets konfigurationsfil finns |
| contractCodes | Insamling av [kontrakt koder](#contract-code-information) för programmet |
| ApplicationRoles | Insamling av [programroller](#application-role-information) för programmet |
| applicationWorkflows | Insamling av [programarbetsflöden](#application-workflow-information) för programmet |
| ConnectionId | Unik identifierare för anslutningen |
| messageSchemaVersion | Meddelanden schemaversion |
| messageName | **EventMessage** |
| AdditionalInformation | Mer information finns här innehåller programtillstånd för arbetsflödet och övergången information. |

#### <a name="contract-code-information"></a>Kontrakt-kodinformation

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för kontraktet kodfilen i Azure Blockchain Workbench |
| LedgerId | Unik identifierare för redovisningen i Azure Blockchain Workbench |
| location | URL: en där kontrakt kodfilen finns |

#### <a name="application-role-information"></a>Rollen programinformation

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för programrollen inuti Azure Blockchain Workbench |
| namn | Namnet på programrollen |

#### <a name="application-workflow-information"></a>Programinformationen för arbetsflöde

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för arbetsflöde för program i Azure Blockchain Workbench |
| namn | Programnamnet för arbetsflöde |
| displayName | Programmets visningsnamn för arbetsflöde |
| functions | Insamling av [funktioner för arbetsflöde för program ](#workflow-function-information)|
| tillstånd | Insamling av [tillstånd för arbetsflöde för program](#workflow-state-information) |
| properties | Programmet [egenskaper arbetsflödesinformation](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Funktionen arbetsflödesinformation

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för programmet arbetsflödesfunktion inuti Azure Blockchain Workbench |
| namn | Funktionsnamn |
| parameters | Parametrar för funktionen |

##### <a name="workflow-state-information"></a>Information om arbetsflödets tillstånd

| Namn | Beskrivning |
|------|-------------|
| namn | Delstatsnamn |
| displayName | Statliga visningsnamn |
| stil | Statliga format (lyckade eller misslyckade) |

##### <a name="workflow-property-information"></a>Egenskapen arbetsflödesinformation

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för arbetsflödet programegenskap inuti Azure Blockchain Workbench |
| namn | Egenskapsnamn |
| typ | Typ vlastnosti |

Exempel på en *EventMessage ApplicationIngestion* från Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url"
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                                "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                                "name": "int"
                     }
                }
            ]
        }
    ]
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept"
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Händelsemeddelandet: Rolltilldelning

Innehåller information om en användare har tilldelats en roll i Workbench, till exempel vem som utförde rolltilldelningen och namnet på rollen och motsvarande program.

| Namn | Beskrivning |
|------|-------------|
| EventName | **RoleAssignment** |
| ApplicationId | Unik identifierare för programmet i Azure Blockchain Workbench |
| ApplicationName | Programnamn |
| applicationDisplayName | Programvisningsnamn |
| applicationVersion | Programversion |
| applicationRole        | Information om den [programroll](#roleassignment-application-role) |
| assigner               | Information om den [tilldelare](#roleassignment-assigner) |
| Tilldelad person               | Information om den [tilldelad person](#roleassignment-assignee) |
| ConnectionId           | Unik identifierare för anslutningen |
| messageSchemaVersion   | Meddelanden schemaversion |
| messageName            | **EventMessage** |
| AdditionalInformation  | Ytterligare information |

#### <a name="roleassignment-application-role"></a>RoleAssignment programroll

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för programrollen inuti Azure Blockchain Workbench |
| namn | Namnet på programrollen |

#### <a name="roleassignment-assigner"></a>RoleAssignment tilldelare

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för användaren i Azure Blockchain Workbench |
| typ | Typ av du |
| chainIdentifier | Unik identifierare för användaren på redovisningen |

#### <a name="roleassignment-assignee"></a>RoleAssignment tilldelad person

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för användaren i Azure Blockchain Workbench |
| typ | Typ av den tilldelad personen |
| chainIdentifier | Unik identifierare för användaren på redovisningen |

Exempel på en *EventMessage RoleAssignment* från Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Nästa steg

- [Smarta kontrakt integration mönster](integration-patterns.md)