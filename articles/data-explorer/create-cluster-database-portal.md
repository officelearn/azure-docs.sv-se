---
title: 'Snabbstart: Skapa ett Azure Data Explorer-kluster och en databas'
description: I den här snabbstarten får du lära dig hur du skapar ett Azure Data Explorer-kluster och en -databas och matar in data (läsa in).
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 3/25/2019
ms.openlocfilehash: aa98fb8f9569282e9636d5cb593523b9642395e1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758634"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Snabbstart: Skapa ett Azure Data Explorer-kluster och en databas

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. I den här snabbstarten skapar du ett kluster och en databas.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Skapa ett kluster

Skapa ett Azure Data Explorer-kluster med en definierad uppsättning beräknings- och lagringsresurser i en Azure-resursgrupp.

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

1. Sök efter *Azure-datautforskaren*.

   ![Sök efter resurser](media/create-cluster-database-portal/search-resources.png)

1. Under **Azure-datautforskaren** väljer du **Skapa** längst ned på skärmen.

1. Fyll i grundläggande klusterinformation med uppgifter.

   ![Skapa kluster](media/create-cluster-database-portal/create-cluster-form.png)

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som ska användas för klustret.|
    | Resursgrupp | *test-resource-group* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Klusternamn | Ett unikt klusternamn | Välj ett unikt namn som identifierar klustret. Exempel: *mydataexplorercluster*. Domännamnet *[region]. kusto.windows .net* läggs till i klusternamnet som du anger. Namnet får bara innehålla gemena bokstäver och siffror. Det måste innehålla mellan 3 och 22 tecken.
    | Plats | *USA, västra* | Välj *USA, västra* för den här snabbstarten. Välj den region som bäst uppfyller dina behov för ett produktionssystem.
    | Beräkningsspecifikationer | *D13_v2* | Välj den lägsta prisspecifikationen för den här snabbstarten. För ett produktionssystem väljer du den specifikation som bäst uppfyller dina behov.
    | | |

1. Välj **Granska + skapa** för att granska din klusterinformation och **Skapa** för att etablera klustret. Etableringen tar normalt cirka 10 minuter.

1. När distributionen är klar väljer du **Gå till resurs**.

    ![Gå till resurs](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Skapa en databas

Nu är du redo för det andra steget i processen: skapa databasen.

1. På fliken **Översikt** väljer du **Skapa databas**.

    ![Steg 2: Skapa en databas](media/create-cluster-database-portal/database-creation.png)

1. Fyll i formuläret med följande information.

    ![Skapa databasformulär](media/create-cluster-database-portal/create-database.png)

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Databasnamn | *TestDatabase* | Databasnamnet måste vara unikt inom klustret.
    | Kvarhållningsperiod | *3650* | Det tidsintervall (i dagar) då det är garanterat att data förblir tillgängliga för frågor. Tidsintervallet mäts från det att data matas in.
    | Cacheperiod | *31* | Det tidsintervall (i dagar) då data som frågor körs mot ofta ska vara tillgängliga i SSD-lagring eller RAM i stället för i långsiktig lagring.
    | | | |

1. Välj **Skapa** för att skapa databasen. Det brukar ta mindre än en minut att skapa en databas. När processen är klar kan du är tillbaka på klustrets **översiktsflik**.

## <a name="run-basic-commands-in-the-database"></a>Köra grundläggande kommandon i databasen

Nu när du har ett kluster och en databas kan köra du frågor och kommandon. Du har inga data i databasen än, men du kan ändå se hur verktyg fungerar.

1. Under ditt kluster väljer du **Fråga**. Klistra in kommandot `.show databases` i frågefönstret och välj sedan **Kör**.

    ![Visa databaskommando](media/create-cluster-database-portal/show-databases.png)

    Resultatuppsättningen visar **TestDatabase**, den enda databasen i klustret.

1. Klistra in kommandot `.show tables` i frågefönstret och välj **Kör**.

    Det här kommandot returnerar en tom resultatuppsättning eftersom du inte har några tabeller än. Du kan lägga till en tabell i nästa artikel i den här serien.

## <a name="stop-and-restart-the-cluster"></a>Stoppa och starta om klustret

Du kan stoppa och starta om ett kluster beroende på företagets behov.

1. Om du vill stoppa klustret väljer du **Stoppa** överst på fliken **Översikt**.

    När klustret har stoppats är data inte tillgängliga för frågor och du kan inte hämta nya data.

1. Om du vill starta om klustret väljer du **Starta** överst på fliken **Översikt**.

    När klustret har startats om tar det ungefär 10 minuter för att det ska bli tillgängligt (som när det etablerades ursprungligen). Det tar extra tid för data att läsas in till den frekventa cachen.  

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att följa andra snabbstarter och självstudier kan du behålla alla resurser som du skapade. Annars bör du rensa resursgruppen för att undvika kostnader.

1. I Azure-portalen väljer du **Resursgrupper** längst till vänster och väljer sedan den resursgrupp som innehåller ditt Data Explorer-kluster.  

1. Välj **Ta bort resursgrupp** för att ta bort hela resursgruppen. Om du använder en befintlig resursgrupp kan du välja att bara ta bort Data Explorer-klustret.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Mata in data från Event Hub till Azure Data Explorer](ingest-data-event-hub.md)


