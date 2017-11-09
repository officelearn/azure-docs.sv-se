---
title: Med U-SQL kognitiva funktioner i Azure Data Lake Analytics | Microsoft Docs
description: "Lär dig hur du använder intelligence kognitiva funktioner i U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: ec48a07af0aba78f2e508bad232f34102f0c2073
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Självstudier: Kom igång med U-SQL kognitiva funktioner

## <a name="overview"></a>Översikt
Kognitiva funktioner för U-SQL ger utvecklare möjligheten att använda put intelligence i sina program för stordata. 

Följande kognitiva funktioner är tillgängliga:
* Avbildning: Identifiera ytor
* Avbildning: Identifiera känslo
* Avbildning: Identifiera objekt (märkning)
* Avbildning: OCR (OCR)
* Text: Key frasen extrahering
* Text: Sentiment analys

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>Hur du använder kognitiva funktionsnedsättningar i U-SQL-skript

Den övergripande processen är enkel:

* Använd instruktionen REFERENSSAMMANSÄTTNING för att aktivera funktionerna kognitiva för U-SQL-skript
* Använda processen på en Indataraduppsättningen med hjälp av en kognitiva UDO för att generera utdata raduppsättning

### <a name="detecting-objects-in-images"></a>Identifiering av objekt i bilder

I följande exempel visas hur du använder kognitiva funktionerna för att identifiera objekt i bilder.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
Fler exempel titta på den **kognitiva funktionsnedsättningar-U-SQL-exempel** i den **nästa steg** avsnitt.

## <a name="next-steps"></a>Nästa steg
* [U-SQL/kognitiva prover](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Med hjälp av U-SQL-fönstrets funktioner för Azure Data Lake Analytics-jobb](data-lake-analytics-use-window-functions.md)
