---
title: Komma igång med Azure-lagring med Visual Studio (WebJob-projekt)
description: Komma igång med Azure Table-lagring i ett Azure WebJobs-projekt i Visual Studio efter anslutning till ett lagringskonto med Hjälp av Visual Studio-anslutna tjänster
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707603"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Komma igång med Azure Storage (Azure WebJob Projects)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt
Den här artikeln innehåller C#-kodexempel som visar hur du använder Azure WebJobs SDK version 1.x med Azure-table storage-tjänsten. Kodexemplen använder [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1.x.

Med Azure Table Storage-tjänsten kan du lagra stora mängder strukturerad data. Tjänsten är ett NoSQL-datacenter som accepterar autentiserade samtal från och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.  Mer information [finns i Komma igång med Azure Table storage med .NET.](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table)

Några av kodavsnitten visar **tabellattributet** som används i funktioner som anropas manuellt, det vill än en av utlösarattributen.

## <a name="how-to-add-entities-to-a-table"></a>Så här lägger du till entiteter i en tabell

Om du vill lägga till entiteter i en tabell använder du **tabellattributet** med en **\<ICollector T>** eller **IAsyncCollector\<T>** parameter där **T** anger schemat för de entiteter som du vill lägga till. Attributkonstruktorn tar en strängparameter som anger namnet på tabellen.

I följande kodexempel läggs personentiteter till i en tabell med namnet *Ingress*. **Person**

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

Vanligtvis den typ du använder med **ICollector** härleds från **TableEntity** eller implementerar **ITableEntity**, men det behöver inte. Någon av följande **personklasser** arbetar med koden som visas i föregående **ingressmetod.**

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

Om du vill arbeta direkt med Azure storage API kan du lägga till en **CloudStorageAccount-parameter** i metodsignaturen.

## <a name="real-time-monitoring"></a>Övervakning i realtid

Eftersom datainträngningsfunktioner ofta bearbetar stora mängder data tillhandahåller WebJobs SDK-instrumentpanelen övervakningsdata i realtid. Avsnittet **Åkaropsloggen** talar om för dig om funktionen fortfarande körs.

![Ingress-funktionen körs](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Sidan **Anropsinformation** rapporterar funktionens förlopp (antal entiteter som skrivs) medan den körs och ger dig möjlighet att avbryta den.

![Ingress-funktionen körs](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

När funktionen är klar rapporterar sidan **Anropsinformation** antalet skrivna rader.

![Ingress-funktionen klar](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Så här läser du flera entiteter från en tabell

Om du vill läsa en tabell använder du attributet **Table** med en **IQueryable\<T>-parameter** där typ **T** härleds från **TableEntity** eller implementerar **ITableEntity**.

Följande kodexempel läser och loggar alla rader från **tabellen Ingress:**

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Så här läser du en entitet från en tabell

Det finns **Table** en tabellattributkonstruktor med ytterligare två parametrar som gör att du kan ange partitionsnyckeln och radnyckeln när du vill binda till en enda tabellentitet.

Följande kodexempel läser en tabellrad för en **personentitet** baserat på partitionsnyckel- och radnyckelvärden som tas emot i ett kömeddelande:

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

Klassen **Person** i det här exemplet behöver inte implementera **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Så här använder du .NET Storage API direkt för att arbeta med en tabell

Du kan också använda **attributet Table** med ett **CloudTable-objekt** för större flexibilitet i arbetet med en tabell.

I följande kodexempel används ett **CloudTable-objekt** för att lägga till en enskild entitet *i tabellen Ingress.*

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

Mer information om hur du använder **CloudTable-objektet** finns i [Komma igång med Azure Table storage med .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Relaterade ämnen som omfattas av köerna instruktioner artikel

Information om hur du hanterar tabellbearbetning som utlöses av ett kömeddelande eller för WebJobs SDK-scenarier som inte är specifika för tabellbearbetning finns i [Komma igång med Azure Queue storage och Visual Studio-anslutna tjänster (WebJob Projects)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Nästa steg

Den här artikeln har kodexempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-tabeller. Mer information om hur du använder Azure WebJobs och WebJobs SDK finns i [Azure WebJobs dokumentationsresurser](https://go.microsoft.com/fwlink/?linkid=390226).
