---
title: Snabbstart skapa en Apache Spark-pool (förhandsgranskning)
description: Skapa en ny Apache Spark-pool för en Azure Synapse Analytics-arbetsyta genom att följa stegen i den här guiden.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424435"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Snabbstart: Skapa en ny Apache Spark-pool (förhandsgranskning)

Synapse Analytics erbjuder olika analysmotorer som hjälper dig att inta, omvandla, modellera, analysera och betjäna dina data. En Apache Spark-pool erbjuder funktioner för beräkning av stordata med öppen källkod. När du har skapat en Apache Spark-pool på din Synapse-arbetsyta kan data läsas in, modelleras, bearbetas och användas för att få insikter.

I den här snabbstarten får du lära dig hur du använder Azure-portalen för att skapa en Apache Spark-pool på en Synapse-arbetsyta.

> [!IMPORTANT]
> Fakturering för Spark-instanser beräknas proportionellt per minut, oavsett om du använder dem eller inte. Var noga med att stänga av Spark-instansen när du har använt den eller angett en kort timeout. Mer information finns i avsnittet **Rensa resurser** i den här artikeln.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto innan du börjar](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https:/azure.microsoft.com/free/)
- [Synapse Analytics arbetsyta](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Skapa en ny Apache Spark-pool

1. I Den Synapse-arbetsyta där du vill skapa Apache Spark-poolen klickar du på **Ny Apache Spark-pool**.
![Översikt över Synapse-arbetsytan med en röd ruta runt kommandot för att skapa en ny Apache Spark-pool](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Ange följande information på fliken **Grunderna:**

    |Inställning | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **Apache Spark-poolnamn** | Ett giltigt poolnamn | Det här är namnet som Apache Spark-poolen kommer att ha. |
    | **Nodstorlek** | Liten (4 vCPU / 32 GB) | Ställ in detta på den minsta storleken för att minska kostnaderna för den här snabbstarten |
    | **Automatisk skalning** | Enabled | Lämna den här standardinställningen |
    | **Antal noder** | 3 - 40 | Lämna den här standardinställningen |
    ||||

    ![Apache Spark pool skapa flöde - grunderna fliken.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Observera att det finns särskilda begränsningar för de namn som Apache Spark-pooler kan använda. Namn får innehålla endast bokstäver eller siffror, måste vara 15 eller färre tecken, börja med en bokstav, inte innehålla reserverade ord och vara unika på arbetsytan.

3. Klicka på **Nästa: ytterligare inställningar** och granska standardinställningarna. Ändra inga standardinställningar.
![Apache Spark pool skapa flöde - ytterligare inställningar fliken.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Klicka på **Nästa: taggar**. Lägg inte till några taggar.
![Apache Spark pool skapa flöde - ytterligare inställningar fliken.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Klicka på **Granska + skapa**.

6. Kontrollera att informationen ser korrekt ut baserat på vad som tidigare angavs och klicka på **Skapa**.
![Apache Spark pool skapa flöde - översyn inställningar fliken.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. Nu startar resursetableringsflödet, vilket anger när ![det är klart att Apache Spark-poolen skapar flöde - resursetablering.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. När etableringen är klar visas en ny post för den nyskapade Apache Spark-poolen när du navigerar tillbaka till arbetsytan.
 ![Apache Spark pool skapa flöde - resurs etablering.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. Nu finns det inga resurser som körs, inga avgifter för Spark, du har skapat metadata om Spark-instanserna som du vill skapa.

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort Apache Spark-poolen från arbetsytan.
> [!WARNING]
> Om du tar bort en Apache Spark-pool tas analysmotorn bort från arbetsytan. Det kommer inte längre att vara möjligt att ansluta till poolen, och alla frågor, pipelines och anteckningsböcker som använder den här Apache Spark-poolen fungerar inte längre.

Om du vill ta bort Apache Spark-poolen gör du följande:

1. Navigera till Apache Spark pooler bladet i arbetsytan.
2. Välj apachepoolen som ska tas bort (i det här fallet **contosospark**)
3. Tryck **på ta bort**.
 ![Lista över Apache Spark-pooler, med den nyligen skapade poolen markerad.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Bekräfta borttagningen och tryck på **Knappen Ta bort.**
 ![Bekräftelsedialogrutan för att ta bort den valda Apache Spark-poolen.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. När processen är klar visas inte längre Apache Spark-poolen i arbetsytans resurser.

När SQL-poolen har skapats är den tillgänglig på arbetsytan för inläsning av data, bearbetning av strömmar, läsning från sjön och så.

## <a name="next-steps"></a>Nästa steg

- Se [Snabbstart: Skapa en Apache Spark-pool i Synapse Studio med hjälp av webbverktyg](spark/apache-spark-notebook-create-spark-use-sql.md).
- Se [Snabbstart: Skapa en Synapse SQL-pool med Azure-portalen](quickstart-create-sql-pool.md).
