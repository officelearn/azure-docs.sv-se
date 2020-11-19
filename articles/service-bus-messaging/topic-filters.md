---
title: Azure Service Bus ämnes filter | Microsoft Docs
description: Den här artikeln förklarar hur prenumeranter kan definiera vilka meddelanden som ska tas emot från ett ämne genom att ange filter.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 04ae585c42f8acfbf338bf23befb32a5521fcf57
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889039"
---
# <a name="topic-filters-and-actions"></a>Ämnesfilter och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden anges i form av en eller flera namngivna prenumerationsregler. Varje regel består av ett villkor som väljer specifika meddelanden och en åtgärd som kommenterar det valda meddelandet. Prenumerationen skapar en kopia av meddelandet som får kommenteras på olika sätt för varje matchande regel för varje matchande regelvillkor.

Varje nyligen skapad ämnes prenumeration har en första standard prenumerations regel. Om du inte uttryckligen anger ett filter villkor för regeln, är det filter som används det **sanna** filtret som gör att alla meddelanden kan väljas i prenumerationen. Standard regeln har ingen associerad antecknings åtgärd.

Service Bus stöder tre filter villkor:

-   *Booleska filter* – **TrueFilter** och **FalseFilter** kan antingen orsaka att alla inkommande meddelanden (**True**) eller inget av de inkommande meddelandena (**falskt**) väljs för prenumerationen. Dessa två filter härleds från SQL-filtret. 

-   *SQL-filter* – en **SQLFILTER** innehåller ett SQL-liknande villkorligt uttryck som utvärderas i Service Broker mot de inkommande meddelanden som är användardefinierade egenskaper och system egenskaper. Alla system egenskaper måste föregås av `sys.` i villkors uttrycket. [SQL-språkets delmängd för filter villkor](service-bus-messaging-sql-filter.md) testar om det finns egenskaper ( `EXISTS` ), null-värden ( `IS NULL` ), logiska not/och/eller relationella operatorer, enkla numeriska aritmetiska och enkla text mönster matchning med `LIKE` .

-   *Korrelations filter* – en **CorrelationFilter** innehåller en uppsättning villkor som matchas mot ett eller flera av de inloggade meddelandets användar-och system egenskaper. En vanlig användning är att matcha mot egenskapen **correlationId** , men programmet kan också välja att matcha följande egenskaper:

    - **Innehålls**
     - **Etikett**
     - **Meddelande**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Att**
     - användardefinierade egenskaper. 
     
     En matchning finns när värdet för en egenskap som anländer är lika med värdet som anges i korrelations filtret. Jämförelsen är Skift läges känslig för sträng uttryck. När du anger flera matchnings egenskaper kombinerar filtret dem som ett logiskt och villkor, vilket innebär att filtret ska matcha, alla villkor måste matcha.

Alla filter utvärderar meddelande egenskaper. Filter kan inte utvärdera meddelande texten.

Komplexa filter regler kräver bearbetnings kapacitet. I synnerhet orsakar användningen av SQL filter-regler lägre övergripande meddelande data flöde vid prenumeration, ämne och namn områdes nivå. När det är möjligt bör program välja korrelations filter över SQL-liknande filter eftersom de är mycket effektivare i bearbetningen och har mindre påverkan på data flödet.

## <a name="actions"></a>Åtgärder

Med villkor för SQL-filter kan du definiera en åtgärd som kan kommentera meddelandet genom att lägga till, ta bort eller ersätta egenskaper och deras värden. Åtgärden [använder ett SQL-like-uttryck som har en](service-bus-messaging-sql-filter.md) felaktig Lean-syntax i SQL Update-uttrycket. Åtgärden utförs på meddelandet efter att den har matchats och innan meddelandet har marker ATS i prenumerationen. Ändringarna i meddelande egenskaperna är privata för det meddelande som kopieras till prenumerationen.

## <a name="usage-patterns"></a>Användningsmönster

Det enklaste användnings scenariot för ett ämne är att varje prenumeration får en kopia av varje meddelande som skickas till ett ämne, vilket möjliggör ett sändnings mönster.

Filter och åtgärder möjliggör två ytterligare grupper av mönster: partitionering och routning.

Partitionering använder filter för att distribuera meddelanden över flera befintliga ämnes prenumerationer på ett förutsägbart och ömsesidigt uteslutande sätt. Partitionerings mönstret används när ett system skalas ut för att hantera många olika kontexter i funktions identiska avdelningar som var och en innehåller en delmängd av de totala data. till exempel kund profil information. Med partitionering skickar en utgivare meddelandet till ett ämne utan att kräva någon kunskap om partitionerings modellen. Meddelandet flyttas sedan till rätt prenumeration från vilken det sedan kan hämtas av partitionens meddelande hanterare.

Routning använder filter för att distribuera meddelanden över ämnes prenumerationer på ett förutsägbart sätt, men inte nödvändigt vis exklusivt. I samband med funktionen för [automatisk vidarebefordring](service-bus-auto-forwarding.md) kan ämnes filter användas för att skapa komplexa vägvals diagram inom ett Service Bus namn område för meddelande distribution inom en Azure-region. Med Azure Functions eller Azure Logic Apps fungerar som en bro mellan Azure Service Bus namnrum kan du skapa komplexa globala topologier med direkt integrering i branschspecifika program.

## <a name="examples"></a>Exempel

### <a name="set-rule-action-for-a-sql-filter"></a>Ange regel åtgärd för ett SQL-filter

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="sql-filter-on-a-system-property"></a>SQL-filter på en system egenskap

```csharp
sys.Label LIKE '%bus%'`
```

### <a name="using-or"></a>Använda eller 

```csharp
sys.Label LIKE '%bus%' OR user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>Använda i och inte i

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ett C#-exempel som använder dessa filter finns i [avsnittet Filtrera exempel på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).

### <a name="correlation-filter-using-correlationid"></a>Korrelations filter med hjälp av CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Den filtrerar meddelanden med `CorrelationID` inställningen till `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Korrelations filter med system-och användar egenskaper

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Det motsvarar: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`


> [!NOTE]
> Eftersom Azure Portal nu stöder Service Bus Explorer-funktioner kan du skapa eller redigera prenumerations filter från portalen. 

## <a name="next-steps"></a>Nästa steg
Se följande exempel: 

- [.NET – grundläggande självstudier för att skicka och ta emot med filter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-ämnes filter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-mall](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)