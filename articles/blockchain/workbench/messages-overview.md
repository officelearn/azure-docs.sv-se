---
title: Azure Blockchain Workbench meddelanden översikt över integration
description: Översikt över användning av meddelanden i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: dcf31bd6b128115962e30dd6653364c4cd65047d
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48243288"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench messaging integration

Förutom att tillhandahålla ett REST-API, dessutom Azure Blockchain Workbench messaging-baserad integrering. Workbench publicerar ledger-centric händelser via Azure Event Grid, aktivera underordnade konsumenter att mata in data eller vidta åtgärder baserat på dessa händelser. För de klienter som kräver tillförlitlig meddelandehantering levererar meddelanden till en Azure Service Bus-slutpunkt i Azure Blockchain Workbench.

Utvecklare har också uttryckt intresse möjlighet att aktivera externa system kommunicera initiera transaktioner för att skapa användare, skapa kontrakt och uppdatera kontrakt på ett transaktionsregister. Även om den här funktionen inte exponeras för närvarande i offentlig förhandsversion, ett exempel som ger den här funktionen finns på [ http://aka.ms/blockchain-workbench-integration-sample ](http://aka.ms/blockchain-workbench-integration-sample).

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
| ChainID | En unik identifierare för kedjan kopplat till begäran.|
| Block-ID | Den unika identifieraren för ett block på huvudboken.|
| ContractId | En unik identifierare för kontraktet.|
| ContractAddress |       Adressen till kontraktet på huvudboken.|
| TransactionHash  |     Hash för transaktionen på huvudboken.|
| OriginatingAddress |   Adressen till avsändaren av transaktionen.|
| actionName       |     Namnet på åtgärden.|
| IsUpdate        |      Identifierar om det här är en uppdatering.|
| Parametrar       |     En lista över objekt som anger vilken namn, värde och data parametrar som skickas till en åtgärd.|
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
| ContractActionId         | Den unika identifieraren för den här åtgärden kontrakt                                                                                                                                |
| ChainIdentifier          | Den unika identifieraren för kedjan                                                                                                                                           |
| ConnectionId             | Den unika identifieraren för anslutningen                                                                                                                                      |
| UserChainIdentifier      | Adress för den användare som har skapats i blockchain-nätverket. I Ethereum är det användarens ”på kedja”-adress.                                                     |
| ContractLedgerIdentifier | Adressen för kontraktet på huvudboken.                                                                                                                                        |
| WorkflowFunctionName     | Namnet på funktionen arbetsflöde.                                                                                                                                                |
| WorkflowName             | Namnet på arbetsflödet.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Url till kontraktet i blob storage.                                                                                                                                      |
| ContractActionParameters | Parametrar för åtgärden kontraktet.                                                                                                                                           |
| TransactionHash          | Hash för transaktionen på huvudboken.                                                                                                                                    |
| Etableringsstatus      | Den aktuella Etableringsstatus för åtgärden.</br>0 – skapas</br>1 – i processen</br>2 – Slutför</br> Fullständig anger en bekräftelse från transaktionsregister som det här som har lagts till.                                               |
|                          |                                                                                                                                                                               |

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
| Adress | Adressen till den användare som har finansieras. |
| Balans | Användaren balansen balans.         |
| ChainID | Den unika identifieraren för kedjan.     |


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
| ChainId        | Den unika identifieraren för kedjan som blocket har lagts till.             |
| Block-ID        | Den unika identifieraren för block i Azure Blockchain Workbench. |
| BlockHash      | Hash för blocket.                                                 |
| BlockTimeStamp | Tidsstämpel för blocket.                                            |

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
| ChainId         | Den unika identifieraren för kedjan som blocket har lagts till.             |
| Block-ID         | Den unika identifieraren för block i Azure Blockchain Workbench. |
| TransactionHash | Hash för transaktionen.                                           |
| Från            | Adressen till avsändaren av transaktionen.                      |
| Till              | Adressen till den avsedda mottagaren av transaktionen.              |
| Värde           | Det värde som ingår i transaktionen.                                 |
| IsAppBuilderTx  | Identifierar om det här är en Blockchain Workbench-transaktion.                         |

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
| ContractId      | Det här är den unika identifieraren för kontraktet inuti Azure Blockchain Workbench. |
| ChainIdentifier | Det här är identifieraren för kontraktet i kedjan.                             |

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
| OperationName | Namnet på åtgärden.           |
| begärande-ID     | En unik identifierare för begäran. |

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
| Namn  | Namnet på parametern.  |
| Värde | Värdet på parametern. |
| Typ  | Typen för parametern.  |

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
| Id    | ID för egenskapen.    |
| Namn  | Namnet på egenskapen.  |
| Värde | Värdet på egenskapen. |
| Typ  | Typ av egenskapen.  |

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