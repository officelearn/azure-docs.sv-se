---
title: "Använda Azure Machine Learning med SQL Data Warehouse | Microsoft Docs"
description: "Självstudier för att använda Azure Machine Learning med Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: ac6bc731-6add-47a9-b3fe-68996e656f4d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: c19860c6b5b1c15d1e29ddc67f9cf9ad4618725b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Använda Azure Machine Learning med SQL Data Warehouse
Azure Machine Learning är en helt hanterad förutsägelseanalyser-tjänst som du kan använda för att skapa förutsägelsemodeller mot dina data i SQL Data Warehouse och sedan publicera som redo att använda webbtjänster. Du kan lära dig grunderna i förutsägelseanalyser och maskininlärning genom att läsa [introduktion till Machine Learning på Azure][Introduction to Machine Learning on Azure].  Du kan sedan lära dig att skapa, träna, poängsätta och testa machine learning-modellen med hjälp av den [skapa experiment kursen][Create experiment tutorial].

I den här artikeln får du lära dig hur du kan göra följande med den [Azure Machine Learning Studio][Azure Machine Learning Studio]:

* Läsa data från databasen för att skapa, träna och betygsätta en förutsägelsemodell
* Skriva data till databasen

## <a name="read-data-from-sql-data-warehouse"></a>Läsa data från SQL Data Warehouse
Vi läser data från produkt-tabellen i AdventureWorksDW-databasen.

### <a name="step-1"></a>Steg 1
Starta ett nytt experiment genom att klicka på + ny längst ned i Machine Learning Studio-fönstret Välj EXPERIMENT och välj sedan tomt Experiment. Välj experiment för standardnamnet överst på arbetsytan och Byt till ett beskrivande, till exempel cykel pris förutsägelse.

### <a name="step-2"></a>Steg 2
Sök efter modulen läsare i palett med datauppsättningar och moduler till vänster om arbetsytan för experimentet. Dra modulen till experimentarbetsytan.
![][drag_reader]

### <a name="step-3"></a>Steg 3
Välj modulen läsare och fylla i egenskapsfönstret.

1. Välj Azure SQL-databas som datakälla.
2. Databasservernamn: Ange namnet på servern. Du kan använda den [Azure-portalen] [ Azure portal] att hitta detta.

![][server_name]

1. Databasnamn: Ange namnet på en databas på servern som du precis angav.
2. Servern användarkontonamnet: Ange användarnamnet för ett konto som har åtkomstbehörighet för databasen.
3. Serverlösenord: Ange lösenordet för det angivna användarkontot.
4. Acceptera alla servercertifikat: Använd det här alternativet (mindre säkert) om du vill hoppa över granska platscertifikatet innan du läser dina data.
5. Databasfrågan: Ange en SQL-instruktion som beskriver de data som du vill läsa. I det här fallet kommer vi läsa data från produkt-tabellen med hjälp av följande fråga.

```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Steg 4
1. Kör experimentet genom att klicka på kör under arbetsytan för experimentet.
2. När experimentet är klar har Reader-modulen en grön bock för att ange att den har slutförts. Notera också avslutad Körningsstatus i det övre högra hörnet.

![][run]

1. Om du vill se den importerade informationen, klicka på utdataporten längst ned i uppsättningen med bildata och välj visualisera.

## <a name="create-train-and-score-a-model"></a>Skapa, träna och betygsätta en modell
Nu kan du använda denna dataset för att:

* Skapa en modell: bearbeta data och definiera funktioner
* Träna modellen: välja och tillämpa en inlärningsalgoritm
* Poängsätta och testa modellen: förutsäga nya cykel pris

![][model]

Mer information om hur du skapar träna, poängsätta och testa machine learning-modellen används den [skapa experiment kursen][Create experiment tutorial].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Skriva data till Azure SQL Data Warehouse
Vi kommer att skriva resultatmängden ProductPriceForecast tabellen i AdventureWorksDW-databasen.

### <a name="step-1"></a>Steg 1
Sök efter modulen skrivaren i palett med datauppsättningar och moduler till vänster om arbetsytan för experimentet. Dra modulen till experimentarbetsytan.

![][drag_writer]

### <a name="step-2"></a>Steg 2
Välj modulen skrivare och fylla i egenskapsfönstret.

1. Välj Azure SQL-databas som datamål.
2. Databasservernamn: Ange namnet på servern. Du kan använda den [Azure-portalen] [ Azure portal] att hitta detta.
3. Databasnamn: Ange namnet på en databas på servern som du precis angav.
4. Servern användarkontonamnet: Ange användarnamnet för ett konto som har skrivbehörighet för databasen.
5. Serverlösenord: Ange lösenordet för det angivna användarkontot.
6. Acceptera alla servercertifikat (osäker): Välj det här alternativet om du inte vill att visa certifikatet.
7. Kommaavgränsad lista med kolumner som ska sparas: Ange en lista över de datamängd eller resultatet kolumner som du vill spara.
8. Data tabellnamn: Ange namnet på tabellen.
9. Kommaavgränsad lista med kolumner som datatable: Ange kolumnnamn i den nya tabellen. Kolumnnamnen kan skilja sig från de i datauppsättningen källan, men du måste ange samma antal kolumner som du definierar för utdatatabellen.
10. Antalet rader som skrivs per SQL Azure-åtgärd: du kan konfigurera hur många rader som har skrivits till en SQL-databas i en enda åtgärd.

![][writer_properties]

### <a name="step-3"></a>Steg 3
1. Kör experimentet genom att klicka på kör under arbetsytan för experimentet.
2. När experimentet är klar kommer alla moduler som har en grön bockmarkering som bekräftar att de har slutförts.

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling][SQL Data Warehouse development overview].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Create experiment tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction to machine learning on Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
