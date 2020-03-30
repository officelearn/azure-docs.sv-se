---
title: Använda meddelanden för att integrera med Azure Blockchain Workbench
description: Översikt över hur du använder meddelanden för att integrera Azure Blockchain Workbench Preview med andra system.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324511"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integrering av Azure Blockchain Workbench-meddelanden

Förutom att tillhandahålla ett REST API tillhandahåller Azure Blockchain Workbench även meddelandebaserad integrering. Workbench publicerar redovisningscentrerade händelser via Azure Event Grid, vilket gör det möjligt för nedströmskonsumenter att förtära data eller vidta åtgärder baserat på dessa händelser. För de klienter som kräver tillförlitliga meddelanden levererar Azure Blockchain Workbench meddelanden till en slutpunkt för Azure Service Bus.

## <a name="input-apis"></a>API:er för indata

Om du vill initiera transaktioner från externa system för att skapa användare, skapa kontrakt och uppdatera kontrakt kan du använda API:er för meddelandeindata för att utföra transaktioner i en redovisning. Se [exempel på meddelandeintegrering](https://aka.ms/blockchain-workbench-integration-sample) för ett exempel som demonstrerar indata-API:er.

Följande är de tillgängliga indata-API:erna.

### <a name="create-user"></a>Skapa användare

Skapar en ny användare.

Begäran kräver följande fält:

| **Namn**             | **Beskrivning**                                      |
|----------------------|------------------------------------------------------|
| Id            | GUID för klienten                                |
| firstName            | Användarens förnamn                              |
| lastName             | Användarens efternamn                               |
| Emailaddress         | Användarens e-postadress                           |
| externt           | Azure AD-objekt-ID för användaren                      |
| connectionId         | Unik identifierare för blockchain-anslutningen |
| meddelandeSchemaVersion | Schemaversion för meddelanden                            |
| messageName (messageName)          | **SkapaAnvändarRequest**                               |

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
| Id             | GUID för klienten |
| userId                | ID för den användare som skapades |
| användareKedja   | Adressen till den användare som skapades i blockchain-nätverket. I Ethereum är adressen användarens **adress i kedjan.** |
| connectionId          | Unik identifierare för blockchain-anslutningen|
| meddelandeSchemaVersion  | Schemaversion för meddelanden |
| messageName (messageName)           | **SkapaAnvändareUppdat** |
| status                | Status för begäran om att skapa användare.  Om det lyckas är värdet **Framgång**. Vid fel är värdet **Fel**.     |
| ytterligare information | Ytterligare information som lämnas baserat på status |

Exempel framgångsrikt **skapa användarsvar** från Blockchain Workbench:

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

Om begäran misslyckades inkluderas information om felet i ytterligare information.

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
| Id            | GUID för klienten |
| användareKedja  | Adressen till den användare som skapades i blockchain-nätverket. I Ethereum är den här adressen användarens **på kedjeadress.** |
| applicationName      | Programmets namn |
| version              | Programmets version. Krävs om du har flera versioner av programmet aktiverat. Annars är version valfri. Mer information om programversioner finns i [Version av Azure Blockchain Workbench-programversioner](version-app.md). |
| arbetsflödeNamn         | Arbetsflödets namn |
| parameters           | Parametrar indata för att skapa kontrakt |
| connectionId         | Unik identifierare för blockchain-anslutningen |
| meddelandeSchemaVersion | Schemaversion för meddelanden |
| messageName (messageName)          | **Skapa kontraktRequest** |

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
| Id                | GUID för klienten                                                             |
| kontraktId               | Unik identifierare för kontraktet i Azure Blockchain Workbench |
| kontraktLederidentifier | Kontraktets adress i redovisningen                                            |
| connectionId             | Unik identifierare för blockchain-anslutningen                               |
| meddelandeSchemaVersion     | Schemaversion för meddelanden                                                         |
| messageName (messageName)              | **Skapa kontraktuppdra**                                                      |
| status                   | Status för begäran om att skapa kontrakt.  Möjliga värden: **Skickat**, **Bekräftat**, **Misslyckande**.  |
| ytterligare information    | Ytterligare information som lämnas baserat på status                              |

Exempel på ett inskickat **skapa kontraktsvar** från Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exempel på ett bekräftat **skapa kontraktsvar** från Blockchain Workbench:

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

Om begäran misslyckades inkluderas information om felet i ytterligare information.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Skapa kontraktsåtgärd

Skapar en ny kontraktsåtgärd.

Begäran kräver följande fält:

| **Namn**                 | **Beskrivning**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Id                | GUID för klienten |
| användareKedja      | Adressen till den användare som skapades i blockchain-nätverket. I Ethereum är den här adressen användarens **på kedjeadress.** |
| kontraktLederidentifier | Kontraktets adress i redovisningen |
| version                  | Programmets version. Krävs om du har flera versioner av programmet aktiverat. Annars är version valfri. Mer information om programversioner finns i [Version av Azure Blockchain Workbench-programversioner](version-app.md). |
| arbetsflödeFunctionName     | Namn på arbetsflödesfunktionen |
| parameters               | Parametrar indata för att skapa kontrakt |
| connectionId             | Unik identifierare för blockchain-anslutningen |
| meddelandeSchemaVersion     | Schemaversion för meddelanden |
| messageName (messageName)              | **SkapacontractActionRequest** |

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
| Id             | GUID för klienten|
| kontraktId            | Unik identifierare för kontraktet i Azure Blockchain Workbench |
| connectionId          | Unik identifierare för blockchain-anslutningen |
| meddelandeSchemaVersion  | Schemaversion för meddelanden |
| messageName (messageName)           | **SkapacontractActionUpdate** |
| status                | Status för begäran om kontraktsåtgärd. Möjliga värden: **Skickat**, **Bekräftat**, **Misslyckande**.                         |
| ytterligare information | Ytterligare information som lämnas baserat på status |

Exempel på ett inskickat skapa svar på **kontraktsåtgärder** från Blockchain Workbench:

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

Exempel på ett bekräftat **skapa kontraktsåtgärdssvar** från Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Om begäran misslyckades inkluderas information om felet i ytterligare information.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Felkoder och meddelanden om indata-API

**Felkod 4000: Felfel för begäran**
- Ogiltigt connectionId
- CreateUserRequest deserialization misslyckades
- CreateContractRequest deserialization misslyckades
- Det gick inte att skapa en deserialisering av CreateContractRequest-deserialisering
- Programmet {identifieras av programnamnet} finns inte
- Programmet {identifieras av programnamnet} har inte arbetsflöde
- UserChainIdentifier finns inte
- Kontraktet {identifieras av redovisningsidentifieraren} finns inte
- Kontrakt {identifieras av redovisningsidentifierare} har inte funktion {arbetsflödesfunktionsnamn}
- UserChainIdentifier finns inte

**Felkod 4090: Konfliktfel**
- Användaren finns redan
- Kontraktet finns redan
- Kontraktsåtgärd finns redan

**Felkod 5000: Internt serverfel**
- Undantagsmeddelanden

## <a name="event-notifications"></a>Händelseaviseringar

Händelsemeddelanden kan användas för att meddela användare och nedströmssystem om händelser som inträffar i Blockchain Workbench och blockchain-nätverket som det är anslutet till. Händelsemeddelanden kan förbrukas direkt i kod eller användas med verktyg som Logic Apps och Flow för att utlösa dataflöde till underordnade system.

Se [Meddelandemeddelandereferens](#notification-message-reference) för information om olika meddelanden som kan tas emot.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Använda händelserutnätshändelser med Azure-funktioner

Om en användare vill använda Event Grid för att få ett meddelande om händelser som inträffar i Blockchain Workbench kan du använda händelser från Event Grid med hjälp av Azure Functions.

1. Skapa en **Azure-funktionsapp** i Azure-portalen.
2. Skapa en ny funktion.
3. Leta reda på mallen för Händelserutnät. Grundläggande mallkod för att läsa meddelandet visas. Ändra koden efter behov.
4. Spara funktionen. 
5. Välj händelserutnätet från Blockchain Workbenchs resursgrupp.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Använda händelserutnätshändelser med Logic Apps

1. Skapa en ny **Azure Logic App** i Azure-portalen.
2. När du öppnar Azure Logic App i portalen uppmanas du att välja en utlösare. Välj **Azure Event Grid – När en resurshändelse inträffar**.
3. När arbetsflödesdesignern visas uppmanas du att logga in.
4. Välj prenumeration. Resurs som **Microsoft.EventGrid.Topics**. Välj **resursnamnet** från namnet på resursen från resursgruppen Azure Blockchain Workbench.
5. Välj händelserutnätet från Blockchain Workbenchs resursgrupp.

## <a name="using-service-bus-topics-for-notifications"></a>Använda Service Bus-ämnen för meddelanden

Service Bus Topics kan användas för att meddela användare om händelser som inträffar i Blockchain Workbench. 

1. Bläddra till servicebussen i Arbetsbänkens resursgrupp.
2. Välj **ämnen**.
3. Välj **utgående ämne**.
4. Skapa en ny prenumeration på det här avsnittet. Skaffa en nyckel för det.
5. Skapa ett program som prenumererar på händelser från den här prenumerationen.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Använda servicebussmeddelanden med logic apps

1. Skapa en ny **Azure Logic App** i Azure-portalen.
2. När du öppnar Azure Logic App i portalen uppmanas du att välja en utlösare. Skriv **Service Bus** i sökrutan och välj den utlösare som är lämplig för den typ av interaktion du vill ha med servicebussen. Servicebuss **– När ett meddelande tas emot i en ämnesprenumeration (slutför automatiskt).**
3. När arbetsflödesdesignern visas anger du anslutningsinformationen för servicebussen.
4. Välj din prenumeration och ange ämnet **workbench-external**.
5. Utveckla logiken för ditt program som använder meddelandet från den här utlösaren.

## <a name="notification-message-reference"></a>Referens för meddelandemeddelande

Beroende på **meddelandetName**har meddelandemeddelandena någon av följande meddelandetyper.

### <a name="block-message"></a>Blockera meddelande

Innehåller information om enskilda block. *BlockMessage* innehåller ett avsnitt med blocknivåinformation och ett avsnitt med transaktionsinformation.

| Namn | Beskrivning |
|------|-------------|
| blockera | Innehåller [blockinformation](#block-information) |
| transaktioner | Innehåller information om [inkassotransaktion](#transaction-information) för blocket |
| connectionId | Unik identifierare för anslutningen |
| meddelandeSchemaVersion | Schemaversion för meddelanden |
| messageName (messageName) | **BlockMessage (Blockering)** |
| ytterligare information | Ytterligare information som tillhandahålls |

#### <a name="block-information"></a>Blockera information

| Namn              | Beskrivning |
|-------------------|-------------|
| blockId (blockId)           | Unik identifierare för blocket inuti Azure Blockchain Workbench |
| blockNumber (blockNumber)       | Unik identifierare för ett block i redovisningen |
| blockHash (blockeraHash)         | Hash av blocket |
| föregåendeBlockHash | Hash för föregående block |
| blockTimestamp    | Tidsstämpeln för blocket |

#### <a name="transaction-information"></a>Transaktionsinformation

| Namn               | Beskrivning |
|--------------------|-------------|
| transaktionId      | Unik identifierare för transaktionen i Azure Blockchain Workbench |
| transaktionHash (transaktionHash)    | Hash-värdet för transaktionen i redovisningen |
| Från               | Unik identifierare i redovisningen för transaktionens ursprung |
| till                 | Unik identifierare i redovisningen för transaktionsmålet |
| etableringStatus | Identifierar den aktuella statusen för etableringsprocessen för transaktionen. Möjliga värden: </br>0 – Transaktionen har skapats av API:et i databasen</br>1 – Transaktionen har skickats till redovisningen</br>2 – Transaktionen har genomförts i redovisningen</br>3 eller 4 - Transaktionen kunde inte bindas till redovisningen</br>5 - Transaktionen har genomförts i redovisningen |

Exempel på ett *BlockMessage* från Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
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

### <a name="contract-message"></a>Meddelande om kontrakt

Innehåller information om ett kontrakt. Meddelandet innehåller ett avsnitt med kontraktsegenskaper och ett avsnitt med transaktionsinformation. Alla transaktioner som har ändrat kontraktet för det aktuella blocket ingår i transaktionsavsnittet.

| Namn | Beskrivning |
|------|-------------|
| blockId (blockId) | Unik identifierare för blocket inuti Azure Blockchain Workbench |
| blockHash (blockeraHash) | Hash av blocket |
| ändraTransaktioner | [Transaktioner som ändrade](#modifying-transaction-information) kontraktet |
| kontraktId | Unik identifierare för kontraktet i Azure Blockchain Workbench |
| kontraktLederidentifier | Unik identifierare för kontraktet i redovisningen |
| kontraktEgenskaper | [Kontraktets egenskaper](#contract-properties) |
| ärNewContract | Anger om kontraktet har skapats eller inte. Möjliga värden är: sant: detta kontrakt var ett nytt kontrakt som skapats. falskt: det här kontraktet är en kontraktsuppdatering. |
| connectionId | Unik identifierare för anslutningen |
| meddelandeSchemaVersion | Schemaversion för meddelanden |
| messageName (messageName) | **KontraktMessage** |
| ytterligare information | Ytterligare information som tillhandahålls |

#### <a name="modifying-transaction-information"></a>Ändra transaktionsinformation

| Namn               | Beskrivning |
|--------------------|-------------|
| transaktionId | Unik identifierare för transaktionen i Azure Blockchain Workbench |
| transaktionHash (transaktionHash) | Hash-värdet för transaktionen i redovisningen |
| Från | Unik identifierare i redovisningen för transaktionens ursprung |
| till | Unik identifierare i redovisningen för transaktionsmålet |

#### <a name="contract-properties"></a>Egenskaper för kontrakt

| Namn               | Beskrivning |
|--------------------|-------------|
| arbetsflödenPropertyId | Unik identifierare för arbetsflödesegenskapen i Azure Blockchain Workbench |
| namn | Namn på egenskapen arbetsflöde |
| värde | Värdet för egenskapen arbetsflöde |

Exempel på ett *ContractMessage* från Blockchain Workbench:

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

### <a name="event-message-contract-function-invocation"></a>Händelsemeddelande: Anrop av funktionen Kontrakt

Innehåller information när en kontraktsfunktion anropas, till exempel funktionsnamn, parametrar indata och anroparen av funktionen.

| Namn | Beskrivning |
|------|-------------|
| händelseNamn                   | **Kontraktfunktionsfunktionsinkallelse** |
| Ringer                      | [Information om uppringaren](#caller-information) |
| kontraktId                  | Unik identifierare för kontraktet i Azure Blockchain Workbench |
| kontraktLederidentifier    | Unik identifierare för kontraktet i redovisningen |
| funktionNamn                | Funktionens namn |
| parameters                  | [Information om parameter](#parameter-information) |
| Transaktionen                 | Transaktionsinformation |
| inTransactionSequenceNumber | Transaktionens sekvensnummer i blocket |
| connectionId                | Unik identifierare för anslutningen |
| meddelandeSchemaVersion        | Schemaversion för meddelanden |
| messageName (messageName)                 | **EventMessage (EventMessage)** |
| ytterligare information       | Ytterligare information som tillhandahålls |

#### <a name="caller-information"></a>Information om uppringaren

| Namn | Beskrivning |
|------|-------------|
| typ | Typ av uppringare, till exempel en användare eller ett kontrakt |
| id | Unik identifierare för anroparen inuti Azure Blockchain Workbench |
| ledgerIdentifier (redovisningIdentifier) | Unik identifierare för anroparen i redovisningen |

#### <a name="parameter-information"></a>Information om parameter

| Namn | Beskrivning |
|------|-------------|
| namn | Parameternamn |
| värde | Parametervärde |

#### <a name="event-message-transaction-information"></a>Transaktionsinformation för händelsemeddelande

| Namn               | Beskrivning |
|--------------------|-------------|
| transaktionId      | Unik identifierare för transaktionen i Azure Blockchain Workbench |
| transaktionHash (transaktionHash)    | Hash-värdet för transaktionen i redovisningen |
| Från               | Unik identifierare i redovisningen för transaktionens ursprung |
| till                 | Unik identifierare i redovisningen för transaktionsmålet |

Exempel på ett *EventMessage ContractFunctionInvocation* från Blockchain Workbench:

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

### <a name="event-message-application-ingestion"></a>Händelsemeddelande: Inmatning av program

Innehåller information när ett program överförs till Workbench, till exempel namn och version av programmet som överförs.

| Namn | Beskrivning |
|------|-------------|
| händelseNamn | **Ansökan** |
| applicationId | Unik identifierare för programmet i Azure Blockchain Workbench |
| applicationName | Programnamn |
| applicationDisplayName | Visningsnamn för program |
| applicationVersion | Programversion |
| applicationDefinitionLocation | URL där programkonfigurationsfilen finns |
| kontraktKoder | Insamling av [kontraktskoder](#contract-code-information) för ansökan |
| ansökanRoles | Insamling av [programroller](#application-role-information) för programmet |
| applicationWorkflows | Insamling av [programarbetsflöden](#application-workflow-information) för programmet |
| connectionId | Unik identifierare för anslutningen |
| meddelandeSchemaVersion | Schemaversion för meddelanden |
| messageName (messageName) | **EventMessage (EventMessage)** |
| ytterligare information | Ytterligare information som finns här inkluderar programmets arbetsflödestillstånd och övergångsinformation. |

#### <a name="contract-code-information"></a>Information om kontraktskod

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för kontraktskodfilen i Azure Blockchain Workbench |
| ledgerId (redovisningId) | Unik identifierare för liggaren i Azure Blockchain Workbench |
| location | URL där kontraktskodfilen finns |

#### <a name="application-role-information"></a>Information om programroll

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för programrollen i Azure Blockchain Workbench |
| namn | Namn på programrollen |

#### <a name="application-workflow-information"></a>Information om programarbetsflöde

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för programarbetsflödet i Azure Blockchain Workbench |
| namn | Namn på programarbetsflöde |
| displayName | Visningsnamn för programarbetsflöde |
| Funktioner | Samling [funktioner för programmets arbetsflöde](#workflow-function-information)|
| Stater | Samling [av tillstånd för programarbetsflödet](#workflow-state-information) |
| properties | [Information om egenskaper för programarbetsflöde](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Information om arbetsflödesfunktion

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för programarbetsflödesfunktionen i Azure Blockchain Workbench |
| namn | Funktionsnamn |
| parameters | Parametrar för funktionen |

##### <a name="workflow-state-information"></a>Information om arbetsflödestillstånd

| Namn | Beskrivning |
|------|-------------|
| namn | Tillståndsnamn |
| displayName | Namn på tillståndsvisning |
| stil | Tillståndsformat (framgång eller misslyckande) |

##### <a name="workflow-property-information"></a>Information om arbetsflödesegenskap

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för egenskapen programarbetsflöde i Azure Blockchain Workbench |
| namn | Egenskapsnamn |
| typ | Egenskapstyp |

Exempel på en *EventMessage ApplicationIngestion* från Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
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
    ],
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
                            "DisplayName": "Accept",
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

### <a name="event-message-role-assignment"></a>Händelsemeddelande: Rolltilldelning

Innehåller information när en användare tilldelas en roll i Workbench, till exempel vem som utförde rolltilldelningen och namnet på rollen och motsvarande program.

| Namn | Beskrivning |
|------|-------------|
| händelseNamn | **RollTilldelning** |
| applicationId | Unik identifierare för programmet i Azure Blockchain Workbench |
| applicationName | Programnamn |
| applicationDisplayName | Visningsnamn för program |
| applicationVersion | Programversion |
| ansökanRoll        | Information om [programrollen](#roleassignment-application-role) |
| tilldelare               | Information om [överlåtaren](#roleassignment-assigner) |
| förvärvaren               | Information om [förvärvaren](#roleassignment-assignee) |
| connectionId           | Unik identifierare för anslutningen |
| meddelandeSchemaVersion   | Schemaversion för meddelanden |
| messageName (messageName)            | **EventMessage (EventMessage)** |
| ytterligare information  | Ytterligare information som tillhandahålls |

#### <a name="roleassignment-application-role"></a>RollTilldelningsprogramroll

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för programrollen i Azure Blockchain Workbench |
| namn | Namn på programrollen |

#### <a name="roleassignment-assigner"></a>Tilldelning av RoleAssignment-tilldelare

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för användaren i Azure Blockchain Workbench |
| typ | Typ av överlåtaren |
| chainIdentifier (chainIdentifier) | Unik identifierare för användaren i redovisningen |

#### <a name="roleassignment-assignee"></a>Tilldelning av RollAstilldelning

| Namn | Beskrivning |
|------|-------------|
| id | Unik identifierare för användaren i Azure Blockchain Workbench |
| typ | Typ av förvärvaren |
| chainIdentifier (chainIdentifier) | Unik identifierare för användaren i redovisningen |

Exempel på en *EventMessage RoleAssignment* från Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
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

- [Integreringsmönster för smart kontrakt](integration-patterns.md)
