---
title: Snabbstart - Skapa en Synapse SQL-pool
description: Skapa en ny Synapse SQL-pool för en Azure Synapse Analytics Synapse Workspace genom att följa stegen i den här guiden.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b3cd4ed280529efbde32fb89e6bac20640940fff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423910"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Snabbstart: Skapa en Synapse SQL-pool

Azure Synapse Analytics erbjuder olika analysmotorer som hjälper dig att inta, omvandla, modellera, analysera och betjäna dina data. En SQL-pool erbjuder T-SQL-baserade beräknings- och lagringsfunktioner. När du har skapat en SQL-pool i Synapse Workspace kan data läsas in, modelleras, bearbetas och användas för att få insikter.

I den här snabbstarten lär du dig att skapa en SQL-pool i en Synapse-arbetsyta med hjälp av Azure-portalen.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Krav

[Synapse arbetsyta](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Skapa ny SQL-pool

1. Klicka på **Nytt SQL-poolkommando** i det övre fältet på arbetsytan Synaps där du vill skapa SQL-poolen.
![Översikt över Synapse Workspace med en röd ruta runt kommandot för att skapa en ny SQL-pool.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Ange följande information på fliken **Grunderna:**

    | Inställning | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **Sql-poolnamn** | Valfritt giltigt namn | Namn på SQL-poolen. |
    | **Prestandanivå** | DW100c (på andra) | Ställ in på den minsta storleken för att minska kostnaderna för den här snabbstarten |
    ||||
  
    ![SQL pool skapa flöde - grunderna fliken.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Observera att det finns särskilda begränsningar för de namn som SQL-pooler kan använda. Namn får inte innehålla specialtecken, måste vara 15 eller färre tecken, inte innehålla reserverade ord och vara unika på arbetsytan.

3. Klicka på **Nästa: Ytterligare inställningar**.
4. Välj **Ingen** om du vill etablera SQL-poolen utan data. Låt standardsorteringen vara markerad.
![SQL pool skapa flöde - ytterligare inställningar fliken.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Klicka på **Granska + skapa**.
6. Kontrollera att informationen ser korrekt ut baserat på vad som tidigare angavs och klicka på **Skapa**.
![Sql pool skapa flöde - översyn inställningar fliken.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. Nu startar resursetableringsflödet.
 ![SQL pool skapa flöde - resursetablering.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. När etableringen är klar visas en ny post för den nyskapade SQL-poolen om du navigerar tillbaka till arbetsytan.
 ![SQL pool skapa flöde - resursetablering.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort SQL-poolen från arbetsytan.
> [!WARNING]
> Om du tar bort en SQL-pool tas analysmotorn och data som lagras i databasen för den borttagna SQL-poolen bort från arbetsytan bort. Det kommer inte längre att vara möjligt att ansluta till SQL-poolen och alla frågor, pipelines och anteckningsböcker som läser eller skriver till den här SQL-poolen fungerar inte längre.

Om du vill ta bort SQL-poolen gör du följande:

1. Navigera till SQL-poolbladet i arbetsytebladet
1. Välj den SQL-pool som ska tas bort (i det här fallet **contosoedw**)
1. Markera den och tryck på **delete**.
1. Bekräfta borttagningen och tryck på **Knappen Ta bort.**
 ![Översikt över SQL-pool - markera borttagningsbekräftelse.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. När processen är klar visas inte längre SQL-poolen i arbetsytans resurser.

När SQL-poolen har skapats kommer den att vara tillgänglig på arbetsytan för inläsning av data, bearbetning av strömmar, läsning från sjön, etc.

## <a name="next-steps"></a>Nästa steg

- Se [Snabbstart: Skapa en Apache Spark-pool i Synapse Studio med hjälp av webbverktyg](spark/apache-spark-notebook-create-spark-use-sql.md).
- Se [Snabbstart: Skapa en Apache Spark-pool med Azure-portalen](quickstart-create-apache-spark-pool.md).
