---
title: Snabb start skapa en Apache Spark pool (förhands granskning)
description: Skapa en ny Apache Spark pool för en Azure Synapse Analytics-arbetsyta genom att följa stegen i den här hand boken.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2501f66c42360d3ec1626f5f4ee42e0212f1967b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792249"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Snabb start: skapa en ny Apache Spark pool (förhands granskning)

Synapse Analytics erbjuder olika analys verktyg som hjälper dig att mata in, transformera, modellera, analysera och hantera dina data. En Apache Spark pool har funktioner för Big data Compute med öppen källkod. När du har skapat en Apache Spark-pool på din Synapse-arbetsyta kan data läsas in, modelleras, bearbetas och hanteras för att få insikter.

I den här snabb starten får du lära dig hur du använder Azure Portal för att skapa en Apache Spark pool i en Synapse-arbetsyta.

> [!IMPORTANT]
> Faktureringen för Spark-instanser beräknas per minut, oavsett om du använder dem eller inte. Se till att stänga av Spark-instansen när du är färdig med den, eller ange en kort tids gräns. Mer information finns i avsnittet **Rensa resurser** i den här artikeln.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Synapse Analytics-arbetsyta](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Skapa ny Apache Spark pool

1. I arbets ytan Synapse där du vill skapa Apache Spark pool klickar du på **ny Apache Spark pool**.
![Översikt över Synapse-arbetsytan med en röd ruta runt kommandot för att skapa en ny Apache Spark pool](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Ange följande information på fliken **grundläggande** :

    |Inställningen | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **Namn på Apache Spark bassäng** | Ett giltigt poolnamn | Detta är det namn som Apache Spark-poolen kommer att ha. |
    | **Node-storlek** | Liten (4 vCPU/32 GB) | Ange den minsta storleken för att minska kostnaderna för den här snabb starten |
    | **Automatisk skalning** | Enabled | Lämna standardvärdet |
    | **Antal noder** | 3 - 40 | Lämna standardvärdet |
    ||||

    ![Fliken Skapa Flow-grunder för Apache Spark pool.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Observera att det finns vissa begränsningar för de namn som Apache Spark pooler kan använda. Namn får bara innehålla bokstäver eller siffror, måste bestå av högst 15 tecken, måste inledas med en bokstav, får inte innehålla reserverade ord och vara unika i arbets ytan.

3. Klicka på **Nästa: ytterligare inställningar** och granska standardinställningarna. Ändra inte några standardinställningar.
![Apache Spark fliken Skapa flöde-ytterligare inställningar.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Klicka på **Nästa: Taggar**. Lägg inte till några taggar.
![Apache Spark fliken Skapa flöde-ytterligare inställningar.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Klicka på **Granska + skapa**.

6. Se till att informationen ser korrekt ut baserat på vad som tidigare har angetts och klicka på **skapa**.
![Apache Spark fliken Skapa flöde-granska inställningar.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. I det här läget startar resurs etablerings flödet och anger när det är klart ![Apache Spark pool för att skapa flödes resurs etablering.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. När etableringen är klar visas en ny post för den nyligen skapade Apache Spark-poolen när du navigerar tillbaka till arbets ytan.
 ![Apache Spark poolen skapa flödes resurs etablering.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. Det finns inga resurser att köra, inga avgifter för Spark, du har skapat metadata om de Spark-instanser som du vill skapa.

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort Apache Spark-poolen från arbets ytan.
> [!WARNING]
> Om du tar bort en Apache Spark pool tas analys motorn bort från arbets ytan. Det kommer inte längre att gå att ansluta till poolen, och alla frågor, pipeliner och antecknings böcker som använder den här Apache Spark-poolen fungerar inte längre.

Gör så här om du vill ta bort Apache Spark poolen:

1. Gå till bladet Apache Spark pooler på arbets ytan.
2. Välj den Apache-pool som ska tas bort (i det här fallet **contosospark**)
3. Tryck på **ta bort**.
 ![Lista över Apache Spark pooler med den nyligen skapade poolen vald.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Bekräfta borttagningen och tryck på knappen **ta bort** .
 ![Bekräftelse dialog ruta för att ta bort den valda Apache Spark poolen.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. När processen har slutförts visas inte längre den Apache Spark poolen i arbets ytans resurser.

När SQL-poolen har skapats är den tillgänglig i arbets ytan för att läsa in data, bearbeta strömmar, läsa från sjö och så vidare.

## <a name="next-steps"></a>Nästa steg

- Se [snabb start: skapa en Apache Spark Notebook](quickstart-apache-spark-notebook.md).
- Se [snabb start: skapa en SYNAPSE SQL-pool med hjälp av Azure Portal](quickstart-create-sql-pool.md).
