---
title: Komma igång med Azure Storage med Visual Studio (webbjobb-projekt)
description: Komma igång med Azure Table Storage i ett Azure WebJobs-projekt i Visual Studio efter anslutning till ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e4d8299c06bfa5b0f33bff8fa592a2fa549c695c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707603"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Komma igång med Azure Storage (Azure webbjobb-projekt)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt
Den här artikeln C# innehåller kod exempel som visar hur du använder Azure WebJobs SDK version 1. x med Azure Table Storage-tjänsten. I kod exemplen används [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1. x.

Med tjänsten Azure Table Storage kan du lagra stora mängder strukturerade data. Tjänsten är ett NoSQL-datalager som accepterar autentiserade anrop inifrån och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.  Mer information finns i [komma igång med Azure Table Storage med hjälp av .net](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) .

Några av kodfragmenten visar det **tabellattribut** som används i funktioner som anropas manuellt, det vill säga inte genom att använda ett av utlösarens attribut.

## <a name="how-to-add-entities-to-a-table"></a>Så här lägger du till entiteter i en tabell

Om du vill lägga till entiteter i en tabell använder du **Table** -attributet med en **ICollector\<t >** eller **IAsyncCollector\<t >** parameter där **t** anger schemat för de entiteter som du vill lägga till. Attributet konstruktorn använder en sträng parameter som anger namnet på tabellen.

Följande kod exempel lägger till **person** entiteter i en tabell med namnet *ingress*.

```csharp
[NoAutomaticTrigger]
public static void IngressDemo(
    [Table("Ingress")] ICollector<Person> tableBinding)
{
    for (int i = 0; i < 100000; i++)
    {
        tableBinding.Add(
            new Person() {
                PartitionKey = "Test",
                RowKey = i.ToString(),
                Name = "Name" }
            );
    }
}
```

Vanligt vis är den typ som du använder med **ICollector** härledd från **TableEntity** eller implementerar **ITableEntity**, men det behöver inte. Någon **av följande klasser** fungerar med koden som visas i föregående **ingress** -metod.

```csharp
public class Person : TableEntity
{
    public string Name { get; set; }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Om du vill arbeta direkt med Azure Storage-API: et kan du lägga till en **CloudStorageAccount** -parameter till Metodsignaturen.

## <a name="real-time-monitoring"></a>Real tids övervakning

Eftersom data ingångs funktioner ofta bearbetar stora data mängder innehåller WebJobs SDK-instrumentpanelen real tids övervaknings data. I avsnittet **anrops logg** får du information om funktionen fortfarande körs.

![Ingress-funktion körs](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Sidan med **information om anrops** sidan rapporterar funktionens förlopp (antal enheter som skrivits) medan den körs och ger dig möjlighet att avbryta den.

![Ingress-funktion körs](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

När funktionen slutförs rapporterar sidan med **anrops information** antalet rader som skrivs.

![Ingångs funktion avslutad](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Läsa flera entiteter från en tabell

Om du vill läsa en tabell använder du **Table** -attributet med en **IQueryable-\<t >** -parameter där typ **t** härleds från **TableEntity** eller implementerar **ITableEntity**.

Följande kod exempel läser och loggar alla rader från **ingress** -tabellen:

```csharp
public static void ReadTable(
    [Table("Ingress")] IQueryable<Person> tableBinding,
    TextWriter logger)
{
    var query = from p in tableBinding select p;
    foreach (Person person in query)
    {
        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
            person.PartitionKey, person.RowKey, person.Name);
    }
}
```

### <a name="how-to-read-a-single-entity-from-a-table"></a>Läsa en enskild entitet från en tabell

Det finns en konstruktor för **Table** -attribut med två ytterligare parametrar som låter dig ange partitionsnyckel och rad nyckel när du vill binda till en enskild tabell enhet.

Följande kod exempel läser en tabell rad för **en entitet som** baseras på partitionsnyckel och rad nyckel värden som tas emot i ett köat meddelande:

```csharp
public static void ReadTableEntity(
    [QueueTrigger("inputqueue")] Person personInQueue,
    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
    TextWriter logger)
{
    if (personInTable == null)
    {
        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                personInQueue.PartitionKey, personInQueue.RowKey);
    }
    else
    {
        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
    }
}
```

**Person** klassen i det här exemplet behöver inte implementera **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Så här använder du .NET Storage-API: et direkt för att arbeta med en tabell

Du kan också använda **Table** -attributet med ett **CloudTable** -objekt för mer flexibilitet när du arbetar med en tabell.

I följande kod exempel används ett **CloudTable** -objekt för att lägga till en enskild entitet i *ingress* -tabellen.

```csharp
public static void UseStorageAPI(
    [Table("Ingress")] CloudTable tableBinding,
    TextWriter logger)
{
    var person = new Person()
        {
            PartitionKey = "Test",
            RowKey = "100",
            Name = "Name"
        };
    TableOperation insertOperation = TableOperation.Insert(person);
    tableBinding.Execute(insertOperation);
}
```

Mer information om hur du använder **CloudTable** -objektet finns i [komma igång med Azure Table Storage med hjälp av .net](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Närliggande ämnen som omfattas av artikeln om köer

Information om hur du hanterar tabell bearbetning som utlöses av ett Queue meddelande, eller för WebJobs SDK-scenarier som inte är särskilt för tabell bearbetning, finns i [komma igång med Azure Queue Storage och Visual Studio Connected Services (webb jobb projekt)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln finns kod exempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-tabeller. Mer information om hur du använder Azure WebJobs och WebJobs SDK finns i [Azure WebJobs dokumentations resurser](https://go.microsoft.com/fwlink/?linkid=390226).
