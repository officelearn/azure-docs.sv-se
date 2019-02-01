---
title: Importera data till Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Hur du importerar dina data till Azure Machine Learning Studio från olika datakällor. Lär dig vilka datatyper och dataformat stöds.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 15e84d1246a1a4c81b6a6c6f6c28a33a210a6ae6
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487660"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importera dina utbildningsdata till Azure Machine Learning Studio från olika datakällor

Om du vill använda dina egna data i Machine Learning Studio för att utveckla och träna en lösning för förutsägelseanalys, kan du använda data från: 

* En [ **lokal fil** ](import-data-from-local-file.md) -läsa in lokala data i tid från hårddisken för att skapa en datauppsättning-modulen i din arbetsyta
* [**Online-datakällor** ](import-data-from-online-sources.md) – Använd den [importera Data] [ import-data] modul för att komma åt data från en av flera onlinekällor när experimentet körs
* [**Machine Learning Studio-experiment** ](import-data-from-an-experiment.md) -använder data som har sparats som en datauppsättning i Machine Learning Studio
* [**En lokal SQL Server-databas** ](use-data-from-an-on-premises-sql-server.md) -använda data från en lokal SQL Server-databas utan att behöva kopiera data manuellt

> [!NOTE]
> Det finns ett antal provdatauppsättningar i Machine Learning Studio som du kan använda för träningsdata. Information om dessa finns i [Använd exemplen på datauppsättningar i Azure Machine Learning Studio](use-sample-datasets.md)).
> 
> 

Den här inledande artikeln beskrivs hur du förbereder data för användning i Machine Learning Studio också och beskriver vilka dataformat och datatyper som stöds.

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Förbereda data för användning i Azure Machine Learning Studio
Machine Learning Studio är utformad för att arbeta med rektangulär eller tabular data, till exempel textdata som har avgränsade eller strukturerade data från en databas, men i vissa fall icke-rektangulär data kan användas.

Det är bäst om dina data är relativt ren. Det vill säga vill du ta hand om problem som till exempel ociterade strängar innan du laddar upp data i experimentet.

Det finns dock moduler som är tillgängliga i Machine Learning Studio som gör att vissa manipulation av data i experimentet. Beroende på de machine learning-algoritmer som du ska använda, kanske måste du bestämma hur du ska hantera strukturella problem med till exempel värden som saknas och null-optimerade data och det finns moduler som kan hjälpa dig med som. Titta i den **Dataomvandling** avsnittet på modulpaletten för moduler som utför dessa funktioner.

Du kan visa eller ladda ned de data som produceras av en modul genom att klicka på utdataporten när som helst i experimentet. Det kan finnas olika hämtningsalternativ beroende på modulen eller du kan visualisera data i din webbläsare i Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Data-format och -datatyper som stöds
Du kan importera en rad olika datatyper till ditt experiment, beroende på vilken metod du använder för att importera data och där det kommer från:

* Oformaterad text (.txt)
* Fil med kommaavgränsade värden (CSV med en rubrik (.csv) eller utan) (. nh.csv)
* Tabbavgränsade värden (TVS med en rubrik (TSV) eller utan) (. nh.tsv)
* Excel-fil
* Azure-tabell
* Hive-tabell
* SQL-databastabell
* OData-värden
* SVMLight data (.svmlight) (se den [SVMLight definition](http://svmlight.joachims.org/) information format)
* Attributet ARFF Relation File Format ()-data (.arff) (se den [ARFF definition](http://weka.wikispaces.com/ARFF) information format)
* ZIP-filen (.zip)
* R-objekt eller arbetsyta-fil (. RData)

Om du importerar data i ett format till exempel ARFF som innehåller metadata, använder Machine Learning Studio metadata för att definiera rubrik och datatypen för varje kolumn.

Om du importerar data, till exempel TSV- eller CSV-format som inte innehåller metadata härleder Machine Learning Studio datatypen för varje kolumn genom att samlar data. Om data inte också har kolumnrubriker, tillhandahåller Machine Learning Studio standardnamn.

Du kan uttryckligen ange eller ändra rubriker och datatyper för kolumner med hjälp av den [redigera Metadata][edit-metadata].

Följande **datatyper** som identifieras av Machine Learning Studio:

* String
* Integer
* Double
* Boolesk
* DateTime
* TimeSpan

Machine Learning Studio använder en intern datatyp som kallas ***datatabell*** att överföra data mellan moduler. Du kan uttryckligen omvandla dina data till datatabell format med hjälp av den [konvertera till datauppsättningen] [ convert-to-dataset] modulen.

Alla moduler som format än datatabell konverterar data till datatabell tyst vidare till nästa modul.

Om det behövs kan konvertera du tabellformat med Data tillbaka till CSV, TVS, ARFF eller SVMLight format med hjälp av andra moduler för konvertering.
Titta i den **Format datakonvertering** avsnittet på modulpaletten för moduler som utför dessa funktioner.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
