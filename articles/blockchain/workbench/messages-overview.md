---
title: Azure Blockchain Workbench meddelanden översikt över integration
description: Översikt över användning av meddelanden i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614369"
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
| begärande-ID            | Klienten angivna GUID                                |
| Förnamn            | Förnamn för användaren                              |
| Efternamn             | Efternamn för användaren                               |
| E-postadress         | E-postadressen för användaren                           |
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
| begärande-ID             | Klienten angivna GUID |
| userId                | ID för den användare som har skapats |
| UserChainIdentifier   | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum, adressen är användarens **i kedjan** adress. |
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

Om begäran misslyckas, inkludera information om felet i ytterligare information.

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
| begärande-ID            | Klienten angivna GUID |
| UserChainIdentifier  | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum, är den här adressen användarens **på kedjan** adress. |
| ApplicationName      | Namnet på programmet |
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
| begärande-ID                | Klienten angivna GUID                                                             |
| ContractId               | Unik identifierare för kontraktet inuti Azure Blockchain Workbench |
| ContractLedgerIdentifier | Kontraktet på redovisningen-adress                                            |
| ConnectionId             | Unik identifierare för blockchain-anslutning                               |
| messageSchemaVersion     | Meddelanden schemaversion                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Status för begäran om kontrakt skapas.  Möjliga värden: **skickad**, **genomförd**, **fel**.  |
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

Om begäran misslyckas, inkludera information om felet i ytterligare information.

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
| begärande-ID                | Klienten angivna GUID |
| UserChainIdentifier      | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum, är användarens **på kedjan** adress. |
| ContractLedgerIdentifier | Kontraktet på redovisningen-adress |
| WorkflowFunctionName     | Namnet på arbetsflödesfunktion |
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
| begärande-ID             | Klienten angivna GUID|
| ContractId            | Unik identifierare för kontraktet inuti Azure Blockchain Workbench |
| ConnectionId          | Unik identifierare för blockchain-anslutning |
| messageSchemaVersion  | Meddelanden schemaversion |
| messageName           | **CreateContractActionUpdate** |
| status                | Status för begäran om åtgärden kontraktet. Möjliga värden: **skickad**, **genomförd**, **fel**.                         |
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

Om begäran misslyckas, inkludera information om felet i ytterligare information.

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

**Felkoden 4000: Felaktig begäran fel**
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

**Felkod 4090: fel i konflikt**
- Användaren finns redan
- Kontraktet finns redan
- Det finns redan kontrakt-åtgärd

**Felkoden 5000: Internt serverfel**
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

1.  Skapa en ny **Azure Logic App** i Azure-portalen.
2.  När du öppnar Azure Logic App i portalen, uppmanas du att välja en utlösare. Välj **Azure Event Grid – om en resurshändelse inträffar**.
3. När Arbetsflödesdesignern visas, uppmanas du att logga in.
4. Välj prenumerationen. Resurs som **Microsoft.EventGrid.Topics**. Välj den **resursnamn** från namnet på resursen från resursgruppen Azure Blockchain Workbench.
5. Välj Event Grid Blockchain Workbench resursgrupp.

## <a name="using-service-bus-topics-for-notifications"></a>Använda Service Bus-ämnen för meddelanden

Service Bus-ämnen kan användas för att meddela användare om händelser som inträffar i Blockchain Workbench. 

1.  Bläddra till en Service Bus i Workbench-resursgrupp.
2.  Välj **ämnen**.
3.  Välj **workbench externa**.
4.  Skapa en ny prenumeration på det här ämnet. Hämta en nyckel för den.
5.  Skapa ett program som prenumererar på händelser från den här prenumerationen.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Använda Service Bus-meddelanden med Logic Apps

1. Skapa en ny **Azure Logic App** i Azure-portalen.
2. När du öppnar Azure Logic App i portalen, uppmanas du att välja en utlösare. Typ **Service Bus** i sökrutan och välj lämplig för typ av interaktion utlösaren som du vill ha med Service Bus. Till exempel **Service Bus – när ett meddelande tas emot i en ämnesprenumeration (Komplettera automatiskt)**.
3. Ange anslutningsinformation för Service Bus när Arbetsflödesdesignern visas.
4. Välj din prenumeration och ange ämnet för **workbench externa**.
5. Utveckla logiken för ditt program som använder meddelandet från den här utlösaren.

## <a name="notification-message-reference"></a>Referens för meddelande

Beroende på den **OperationName**, meddelanden har någon av följande typer av meddelanden.

### <a name="accountcreated"></a>AccountCreated

Anger att ett nytt konto har begärts som ska läggas till angivna kedjan.

| Namn    | Beskrivning  |
|----------|--------------|
| UserId  | ID för den användare som har skapats. |
| ChainIdentifier | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum, skulle detta vara användarens **i kedjan** adress. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Anger att en begäran har gjorts att infoga eller uppdatera ett kontrakt på en distribuerad redovisning.

| Namn | Beskrivning |
|-----|--------------|
| ChainID | Unik identifierare för kedjan kopplat till begäran |
| Block-ID | Unik identifierare för ett block på redovisningen |
| ContractId | En unik identifierare för kontraktet |
| ContractAddress |       Adressen för kontraktet på redovisningen |
| TransactionHash  |     Hash för transaktionen på redovisningen |
| OriginatingAddress |   Adressen för avsändaren i transaktion |
| actionName       |     Namnet på åtgärden |
| IsUpdate        |      Identifierar om det här är en uppdatering |
| Parametrar       |     En lista med objekt som anger vilken namn, värde och data parametrar som skickas till en åtgärd |
| TopLevelInputParams |  I scenarier där ett kontrakt är ansluten till ett eller flera avtal, är dessa parametrar från det översta kontraktet. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Anger att en begäran har gjorts att köra en åtgärd på en specifik kontrakt på en distribuerad redovisning.

| Namn                     | Beskrivning                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Unik identifierare för den här åtgärden kontrakt |
| ChainIdentifier          | Unik identifierare för kedjan |
| ConnectionId             | Unik identifierare för anslutningen |
| UserChainIdentifier      | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum, är den här adressen användarens **på kedjan** adress. |
| ContractLedgerIdentifier | Kontraktet på redovisningen-adress |
| WorkflowFunctionName     | Namnet på arbetsflödesfunktion |
| WorkflowName             | Namnet på arbetsflödet |
| WorkflowBlobStorageURL   | URL: en för kontraktet i blob storage |
| ContractActionParameters | Parametrar för åtgärden kontrakt |
| TransactionHash          | Hash för transaktionen på redovisningen |
| Etableringsstatus      | Den aktuella Etableringsstatus för åtgärden.</br>0 – skapas</br>1 – i processen</br>2 – Slutför</br> Fullständig anger en bekräftelse från transaktionsregister som det här som har lagts till |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Anger att en begäran har gjorts att uppdatera användaren balansen på en specifik distribuerat transaktionsregister.

> [!NOTE]
> Det här meddelandet genereras bara för de huvudböcker som kräver finansiering av konton.
> 

| Namn    | Beskrivning                              |
|---------|------------------------------------------|
| Adress | Adressen för den användare som har finansieras |
| Saldo | Användaren balansen balans         |
| ChainID | Unik identifierare för kedjan     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Meddelandet anger att en begäran har gjorts att lägga till ett block på en distribuerad redovisning.

| Namn           | Beskrivning                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Unik identifierare för kedjan som blocket har lagts till             |
| Block-ID        | Unik identifierare för block i Azure Blockchain Workbench |
| BlockHash      | Hash-blockets                                                 |
| BlockTimeStamp | Tidsstämpel för blocket                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

Meddelandet innehåller information om en begäran om att lägga till en transaktion på en distribuerad redovisning.

| Namn            | Beskrivning                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Unik identifierare för kedjan som blocket har lagts till             |
| Block-ID         | Unik identifierare för block i Azure Blockchain Workbench |
| TransactionHash | Hash för transaktionen                                           |
| Från            | Adressen för avsändaren i transaktion                      |
| Till              | Adressen för den avsedda mottagaren av transaktionen              |
| Värde           | Värdet som ingår i transaktionen                                 |
| IsAppBuilderTx  | Identifierar om det här är en Blockchain Workbench-transaktion                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Innehåller information om tilldelningen av en kedja identifierare för ett kontrakt. Till exempel i Ethereum blockchain, adressen för ett kontrakt på huvudboken.

| Namn            | Beskrivning                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Unik identifierare för kontraktet inuti Azure Blockchain Workbench |
| ChainIdentifier | Identifierare för kontraktet i kedjan                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>Klasser som används av typer av meddelanden

### <a name="messagemodelbase"></a>MessageModelBase

Den grundläggande modellen för alla meddelanden.

| Namn          | Beskrivning                          |
|---------------|--------------------------------------|
| OperationName | Åtgärdens namn           |
| begärande-ID     | Unik identifierare för begäran |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

Innehåller namn, värde och en parametertyp.

| Namn  | Beskrivning                 |
|-------|-----------------------------|
| Namn  | Namnet på parametern  |
| Värde | Värdet för parametern |
| Typ  | Typ parametru  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

Innehåller ID, namn, värde och typ för en egenskap.

| Namn  | Beskrivning                |
|-------|----------------------------|
| Id    | ID för egenskapen    |
| Namn  | Namnet på egenskapen  |
| Värde | Värdet på egenskapen |
| Typ  | Vilken typ av egenskapen  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Smarta kontrakt integration mönster](integration-patterns.md)