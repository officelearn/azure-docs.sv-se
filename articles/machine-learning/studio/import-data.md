---
title: Importera data till Machine Learning Studio | Microsoft Docs
description: Hur du importerar data till Azure Machine Learning Studio från olika datakällor. Lär dig mer om vilka typer av data och dataformat stöds.
keywords: Importera data, dataformatet, datatyper, datakällor, träningsdata
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 0ecc2262aee34cd3bcc70785915918414eab3ccd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importera dina utbildningsdata till Azure Machine Learning Studio från olika datakällor
Om du vill använda dina egna data i Machine Learning Studio för att utveckla och träna en förutsägelseanalys, kan du: 

* överföra data från en **lokal fil** i förväg från hårddisken för att skapa en dataset-modul i arbetsytan
* åtkomst till data från ett av flera **online datakällor** medan experimentet körs med hjälp av den [importera Data] [ import-data] modul 
* använda data från en annan Azure Machine learning **experimentera** sparas som en datamängd
* använda data från en lokal **SQL Server-databas**

Dessa alternativ beskrivs i något av avsnitt på menyn nedan. Dessa avsnitt visar hur man importerar data från dessa olika datakällor som ska användas i Machine Learning Studio. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Det finns ett antal provdatauppsättningar i Machine Learning Studio som du kan använda för träningsdata. Mer information om dessa finns [använder provdatauppsättningar i Azure Machine Learning Studio](use-sample-datasets.md)).
> 
> 

Den här inledande avsnittet beskrivs hur du hämtar data redo för användning i Machine Learning Studio också och beskriver vilka dataformat och datatyper som stöds. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Hämta data som är redo för användning i Azure Machine Learning Studio
Machine Learning Studio är avsedd att fungera med rektangulär eller tabular data, till exempel textdata som avgränsade eller strukturerade data från en databas, men i vissa fall icke-rektangulärt område data kan användas.

Det är bäst om dina data är relativt ren. Det vill säga vill du ta hand om problem som till exempel ociterade strängar innan du laddar upp data i experimentet.

Det finns dock moduler som är tillgängliga i Machine Learning Studio som gör att vissa manipulation av data i experimentet. Beroende på de maskininlärningsalgoritmer du ska använda, kanske måste du bestämma hur du ska hantera data strukturella problem, till exempel värden som saknas och null-optimerade data och det finns moduler som kan hjälpa dig med som. Leta i den **Data Transformation** avsnitt i modulpaletten för moduler som utför dessa funktioner.

Du kan visa eller hämta data som produceras av en modul genom att klicka på utdataporten när som helst i experimentet. Det kan finnas olika hämtningsalternativ beroende på modulen, eller kanske du vill visualisera data i din webbläsare i Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Format och datatyper som stöds
Du kan importera ett antal datatyper i experimentet, beroende på vilken metod du använder för att importera data och där det kommer från:

* Oformaterad text (txt)
* Fil med kommaavgränsade värden (CSV med ett huvud (.csv) eller utan) (. nh.csv)
* Tabbavgränsade värden (TVS med ett huvud (TSV) eller utan) (. nh.tsv)
* Excel-fil
* Azure-tabell
* Hive-tabell
* SQL-databastabell
* OData-värden
* SVMLight data (.svmlight) (se den [SVMLight definition](http://svmlight.joachims.org/) information format)
* Attributet Relation File Format ARFF ()-data (.arff) (se den [ARFF definition](http://weka.wikispaces.com/ARFF) information format)
* ZIP-filen (.zip)
* R-objekt eller arbetsytan-fil (. RData)

Om du importerar data i ett format till exempel ARFF som innehåller metadata använder dessa metadata definiera rubrik och datatypen för varje kolumn i Machine Learning Studio.

Om du importerar data, till exempel TVS eller CSV-format som inte innehåller dessa metadata härleder Machine Learning Studio datatypen för varje kolumn av provtagning data. Om data inte har kolumnrubriker, tillhandahåller Machine Learning Studio standardnamn.

Du kan uttryckligen ange eller ändra rubriker och datatyper för kolumner med hjälp av den [redigera Metadata][edit-metadata].

Följande **datatyper** som identifieras av Machine Learning Studio:

* Sträng
* Integer
* Dubbel
* Boolesk
* DateTime
* TimeSpan

Machine Learning Studio använder en intern datatyp som kallas ***datatabell*** att överföra data mellan moduler. Du kan uttryckligen konvertera data till tabellen format med hjälp av den [konvertera till Dataset] [ convert-to-dataset] modulen.

Alla moduler som accepterar format än datatabell konverterar data till tabellen tyst vidare till nästa modul.

Om det behövs kan konvertera du datatabell format tillbaka till CSV, TVS, ARFF eller SVMLight format med hjälp av andra moduler för konvertering.
Leta i den **Format datakonvertering** avsnitt i modulpaletten för moduler som utför dessa funktioner.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
