---
title: Komma igång med Azure storage och Visual Studio anslutna tjänster (Webbjobb projekt)
description: Hur du kommer igång med Azure Table storage i Azure WebJobs-projekt i Visual Studio efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 78ef7d98d9e11ba9b2c52bfc88d6ecb050cafa84
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793308"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Komma igång med Azure Storage (Azure Webjobs-projekt)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt
Den här artikeln innehåller C#-kodexempel som visar visar hur du använder Azure WebJobs SDK version 1.x med Azure table storage-tjänsten. Koden exempel användning av [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1.x.

Tjänsten Azure Table storage kan du lagra stora mängder strukturerade data. Tjänsten är en NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.  Se [komma igång med Azure Table storage med hjälp av .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) för mer information.

Vissa av koden kodavsnitt visas den **tabell** attribut som används i funktioner som kallas manuellt, det vill säga inte genom att använda ett av attributen för utlösare.

## <a name="how-to-add-entities-to-a-table"></a>Lägga till entiteter i en tabell
Om du vill lägga till enheter i en tabell, använder den **tabell** attribut med ett **ICollector<T>**  eller **IAsyncCollector<T>**  parametern där **T** anger schemat för de enheter som du vill lägga till. Attributkonstruktorn använder en strängparameter som anger namnet på tabellen.

Följande kodexempel lägger till **Person** enheter till en tabell med namnet *ingång*.

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

Typen du använder vanligtvis med **ICollector** härleds från **TableEntity** eller implementerar **ITableEntity**, men det behöver inte. Något av följande **Person** klasserna arbete med koden som visas i den föregående **ingång** metod.

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

Om du vill arbeta direkt med Azure storage API, kan du lägga till en **CloudStorageAccount** parameter till Metodsignaturen.

## <a name="real-time-monitoring"></a>Realtidsövervakning
Eftersom dataåtkomstfunktioner ofta bearbetar stora mängder data, innehåller WebJobs SDK instrumentpanelen övervakning realtidsdata. Den **anrop loggen** avsnittet berättar om funktionen fortfarande körs.

![Ingång funktionen körs](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Den **anrop information** sidan rapporterar funktionens status (antal entiteter som skrivs) medan den körs och ger dig en möjlighet att avbryta den.

![Ingång funktionen körs](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

När funktionen har slutförts i **anrop information** sidan rapporterar antalet rader som har skrivits.

![Ingång funktionen klar](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Läsa flera enheter från en tabell
Läs en tabell genom att använda den **tabell** attribut med ett **IQueryable<T>**  parametern skriver **T** härleds från **TableEntity**eller implementerar **ITableEntity**.

Följande kodexempel läser och loggar alla rader från den **ingång** tabell:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Läsa en enda entitet från en tabell
Det finns en **tabell** Attributkonstruktorn med två ytterligare parametrar som kan du ange partitionsnyckel och radnyckel när du vill binda till en enskild tabell entitet.

Följande kodexempel läser en tabellrad för en **Person** entitet baserat på partitionen nyckel- och nyckelvärden togs emot i ett kömeddelande:  

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


Den **Person** klassen i det här exemplet behöver inte implementera **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Hur du använder .NET Storage API direkt för att arbeta med en tabell
Du kan också använda den **tabell** attribut med en **CloudTable** objekt för större flexibilitet när du arbetar med en tabell.

I följande kod exempel används en **CloudTable** objekt att lägga till en enda entitet till den *ingång* tabell.

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

Mer information om hur du använder den **CloudTable** objekt, se [komma igång med Azure Table storage med hjälp av .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Närliggande ämnen som omfattas av den här artikeln köer
Information om hur du hanterar tabell bearbetning som utlöses av ett kömeddelande eller WebJobs-SDK-scenarier som inte är specifika för bearbetning av tabellen finns [komma igång med Azure Queue storage och Visual Studio anslutna tjänster (Webbjobb projekt) ](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Nästa steg
Den här artikeln har lämnat kodexempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-tabeller. Mer information om hur du använder Azure WebJobs och WebJobs-SDK finns [Azure WebJobs-dokumentation](http://go.microsoft.com/fwlink/?linkid=390226).

