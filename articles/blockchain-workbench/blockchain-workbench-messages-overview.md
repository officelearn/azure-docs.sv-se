---
title: Översikt av Azure Blockchain arbetsstationen meddelanden
description: Översikt över användning av meddelanden i Azure Blockchain arbetsstationen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 4a2e85cc619d17745be9d8f72af5f99049ce7c6b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="azure-blockchain-workbench-messages-overview"></a>Översikt av Azure Blockchain arbetsstationen meddelanden

Förutom att tillhandahålla en REST-API ger Azure Blockchain arbetsstation också messaging-baserade integrering. Arbetsstationen publicerar redovisning till Central händelser via Azure händelse rutnät, aktiverar underordnade konsumenter att mata in data eller utföra åtgärder baserat på dessa händelser. För de klienter som kräver reliable messaging levererar Azure Blockchain arbetsstationen meddelanden till en Azure Service Bus-slutpunkt.

Utvecklare har också uttryckt intresse för möjligheten att låta externa system kommunicerar initiera transaktioner för att skapa användare, skapa kontrakt och uppdatera kontrakt på en redovisning. När den här funktionen inte visas för närvarande i förhandsversion, ett exempel som ger den här funktionen finns på [ http://aka.ms/blockchain-workbench-integration-sample ](http://aka.ms/blockchain-workbench-integration-sample).


## <a name="event-notifications"></a>Händelseaviseringar

Händelsemeddelanden kan användas för att meddela användare och underordnade system av händelser som sker i arbetsstationen och blockchain-nätverket som den är ansluten till. Händelsemeddelanden kan användas direkt i koden eller används med verktyg som Logic Apps och flödet för att utlösa flödet av data till underordnade system.

Se [meddelande referens](#notification-message-reference) mer information om olika meddelanden som kan tas emot.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Förbrukar händelse rutnätet händelser med Azure Functions

Om en användare vill använda händelsen rutnätet för att få information om händelser som sker i Blockchain arbetsstationen, kan du använda händelser från Event rutnät med hjälp av Azure Functions.

1. Skapa en **Azure Funktionsapp** i Azure-portalen.
2. Skapa en ny funktion.
3. Leta upp mallen för händelsen rutnät. Grundläggande mallkod för att läsa meddelandet visas. Ändra koden.
4. Spara funktionen. 
5. Välj händelse rutnätet Blockchain arbetsstationen resursgruppen.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Förbrukar händelse rutnätet händelser med Logic Apps

1.  Skapa en ny **Azure Logikapp** i Azure-portalen.
2.  När du öppnar appen Azure logik i portalen, uppmanas du att välja en utlösare. Välj **Azure händelse rutnät – när en resurs-händelse inträffar**.
3. När Arbetsflödesdesignern visas, uppmanas du att logga in.
4. Välj prenumerationen. Resursen som **Microsoft.EventGrid.Topics**. Välj den **resursnamnet** från namnet på resursen från resursgruppen Azure Blockchain arbetsstationen.
5. Välj händelse rutnätet Blockchain arbetsstationen resursgruppen.

## <a name="using-service-bus-topics-for-notifications"></a>Använda Service Bus-ämnen för meddelanden

Service Bus-ämnen kan användas för att meddela användare om händelser som sker i Blockchain arbetsstationen. 

1.  Bläddra till Service Bus inom i arbetsstationen resursgrupp.
2.  Välj **avsnitt**.
3.  Välj **arbetsstationen externa**.
4.  Skapa en ny prenumeration till det här avsnittet. Skaffa en nyckel för den.
5.  Skapa ett program som prenumererar på händelser från den här prenumerationen.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Använda Service Bus-meddelanden med Logic Apps

1. Skapa en ny **Azure Logikapp** i Azure-portalen.
2.  När du öppnar appen Azure logik i portalen, uppmanas du att välja en utlösare. Typen **Service Bus** i sökrutan och väljer lämpliga för typ av interaktion utlösaren som du vill ha med Service Bus. Till exempel **Service Bus - när ett meddelande tas emot i en avsnittet prenumeration (automatisk komplettering)**.
3. Ange anslutningsinformation för Service Bus när Arbetsflödesdesignern visas.
4. Välj din prenumeration och ange ämnet för **arbetsstationen externa**.
5. Utveckla logik för ditt program som använder meddelandet från den här utlösaren.

## <a name="notification-message-reference"></a>Referens för meddelande

Beroende på OperationName har meddelanden något av följande meddelandetyper av.

### <a name="accountcreated"></a>AccountCreated

Anger att ett nytt konto har ombetts att lägga till den angivna kedjan.

| Namn    | Beskrivning  |
|----------|--------------|
| UserId  | ID för den användare som har skapats |
| ChainIdentifier | Adressen till den användare som har skapats i blockchain nätverket. I Ethereum, skulle detta vara användarens ”på kedja” adress. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Anger att en begäran har gjorts att infoga eller uppdatera ett kontrakt i en distribuerad redovisning.

| Namn | Beskrivning |
|-----|--------------|
| ChainID | En unik identifierare för kedjan kopplad till begäran.|
  Block-ID | Unik identifierare för ett block på redovisningen.|
  ContractId | En unik identifierare för kontraktet.|
  ContractAddress |       Adressen till kontraktet på redovisningen.|
  TransactionHash  |     Hash för transaktionen på redovisningen.|
  OriginatingAddress |   Adressen till avsändaren av transaktionen.|
  actionName       |     Namnet på åtgärden.|
  IsUpdate        |      Identifierar om det här är en uppdatering.|
  Parametrar       |     En lista över objekt som anger vilken namn, värde och data parametrar som skickas till en åtgärd.|
  TopLevelInputParams |  I scenarier där ett kontrakt är ansluten till ett eller flera avtal, är dessa parametrar från det översta kontraktet. |

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

Anger att en begäran har gjorts att köra en åtgärd på en specifik kontraktet i en distribuerad redovisning.

| Namn                     | Beskrivning                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Den unika identifieraren för den här åtgärden i kontraktet                                                                                                                                |
| ChainIdentifier          | Den unika identifieraren för kedjan                                                                                                                                           |
| ConnectionId             | Den unika identifieraren för anslutningen                                                                                                                                      |
| UserChainIdentifier      | Adressen till den användare som har skapats i blockchain nätverket. I Ethereum, skulle detta vara användarens ”på kedja” adress.                                                     |
| ContractLedgerIdentifier | Adressen för kontraktet på redovisningen.                                                                                                                                        |
| WorkflowFunctionName     | Namnet på arbetsflödesfunktion.                                                                                                                                                |
| WorkflowName             | Namnet på arbetsflödet.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Url till avtalet i blob storage.                                                                                                                                      |
| ContractActionParameters | Parametrar för åtgärden kontraktet.                                                                                                                                           |
| TransactionHash          | Hash för transaktionen på redovisningen.                                                                                                                                    |
| Etableringsstatus      | Den aktuella Etableringsstatus för åtgärden.</br>0 – skapas</br>1 – i processen</br>2 – slutföra</br> Fullständig anger en bekräftelse från redovisningen som det här som lagts till.                                               |
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

Anger att en begäran har gjorts att uppdatera användaren saldot på en specifik distribuerade redovisning.

> [!NOTE]
> Det här meddelandet genereras endast för dessa kundreskontra som kräver stöd av konton.
> 

| Namn    | Beskrivning                              |
|---------|------------------------------------------|
| Adress | Adressen till den användare som har grundval. |
| Balans | Användaren saldot saldot.         |
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
| ChainId        | Unik identifierare för kedjan som blocket har lagts till.             |
| Block-ID        | Den unika identifieraren för block i Azure Blockchain arbetsstationen. |
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
| ChainId         | Unik identifierare för kedjan som blocket har lagts till.             |
| Block-ID         | Den unika identifieraren för block i Azure Blockchain arbetsstationen. |
| TransactionHash | Hash för transaktionen.                                           |
| Från            | Adressen till avsändaren av transaktionen.                      |
| Till              | Adressen till den avsedda mottagaren av transaktionen.              |
| Värde           | Det värde som ingår i transaktionen.                                 |
| IsAppBuilderTx  | Identifierar om det här är en Blockchain arbetsstationen transaktion.                         |

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

Innehåller information om tilldelning av en kedja identifierare för ett kontrakt. Till exempel i Ethereum blockchain, adressen till ett avtal på redovisningen.

| Namn            | Beskrivning                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Det här är den unika identifieraren för kontraktet inuti Azure Blockchain arbetsstationen. |
| ChainIdentifier | Det här är en identifierare för kontraktet i kedjan.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Smart kontraktet integration mönster](blockchain-workbench-integration-patterns.md)