---
title: Snabb start – skapa en Synapse SQL-pool
description: Skapa en ny Synapse SQL-pool för en Azure Synapse Analytics Synapse-arbetsyta genom att följa stegen i den här hand boken.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a50f203b8057dd4ea51eff1cf09fc97fb598cb3e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096443"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Snabb start: skapa en Synapse SQL-pool

Azure Synapse Analytics erbjuder olika analys verktyg som hjälper dig att mata in, transformera, modellera, analysera och hantera dina data. En SQL-pool ger T-SQL-baserade funktioner för beräkning och lagring. När du har skapat en SQL-pool på din Synapse-arbetsyta kan data läsas in, modelleras, bearbetas och hanteras för att få insikter.

I den här snabb starten lär du dig att skapa en SQL-pool i en Synapse-arbetsyta med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Krav

[Synapse-arbetsyta](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Skapa ny SQL-pool

1. På arbets ytan Synapse där du vill skapa SQL-poolen klickar du på kommandot **ny SQL-pool** i det översta fältet.
![Översikt över Synapse-arbetsytan med en röd ruta runt kommandot för att skapa en ny SQL-pool.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Ange följande information på fliken **grundläggande** :

    | Inställning | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **SQL-poolnamn** | Valfritt giltigt namn | SQL-poolens namn. |
    | **Prestanda nivå** | DW100c | Ställ in på den minsta storleken för att minska kostnaderna för den här snabb starten |
    ||||
  
    ![Fliken Skapa Flow-grunder i SQL-pool.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Observera att det finns vissa begränsningar för de namn som SQL-pooler kan använda. Namn får inte innehålla specialtecken, måste bestå av högst 15 tecken, får inte innehålla reserverade ord och vara unika i arbets ytan.

3. Klicka på **Nästa: ytterligare inställningar**.
4. Välj **ingen** för att etablera SQL-poolen utan data. Lämna standard sorteringen markerad.
![Fliken Skapa flöde för SQL-pool – ytterligare inställningar.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Klicka på **Granska + skapa**.
6. Se till att informationen ser korrekt ut baserat på vad som tidigare har angetts och klicka på **skapa**.
![SQL-pool skapa flöde-granska inställningar fliken.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. I det här läget startar resurs etablerings flödet.
 ![SQL-pool skapa flödes resurs etablering.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. När etableringen är klar visas en ny post för den nyligen skapade SQL-poolen när du navigerar tillbaka till arbets ytan.
 ![SQL-pool skapa flödes resurs etablering.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort SQL-poolen från arbets ytan.
> [!WARNING]
> Om du tar bort en SQL-pool tas både analys motorn och de data som lagras i databasen för den borttagna SQL-poolen bort från arbets ytan. Det går inte längre att ansluta till SQL-poolen och alla frågor, pipeliner och antecknings böcker som läser eller skriver till den här SQL-poolen fungerar inte längre.

Utför följande steg om du vill ta bort SQL-poolen:

1. Gå till bladet SQL-pooler på bladet arbets yta
1. Välj den SQL-pool som ska tas bort (i det här fallet **contosoedw**)
1. Markera den och tryck på **ta bort**.
1. Bekräfta borttagningen och tryck på knappen **ta bort** .
 ![Översikt över SQL-pool – borttagnings bekräftelse.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. När processen har slutförts visas inte längre SQL-poolen i resurserna för arbets ytan.

När SQL-poolen har skapats kommer den att vara tillgänglig i arbets ytan för att läsa in data, bearbeta strömmar, läsa från sjö osv.

## <a name="next-steps"></a>Nästa steg

- Se [snabb start: skapa en Apache Spark pool i Synapse Studio med hjälp av webb verktyg](quickstart-apache-spark-notebook.md).
- Se [snabb start: skapa en Apache Spark pool med hjälp av Azure Portal](quickstart-create-apache-spark-pool.md).
