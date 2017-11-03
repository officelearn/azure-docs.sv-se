---
title: "Utveckla U-SQL-sammansättningar för Azure Data Lake Analytics-jobb | Microsoft Docs"
description: "Lär dig hur du utvecklar sammansättningar som ska användas och återanvändas i Data Lake Analytics-jobb. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Utveckla U-SQL-sammansättningar för Azure Data Lake Analytics-jobb
Lär dig hur du aktiverar bakomliggande kod i sammansättningar som används och återanvändas i Data Lake Analytics-jobb. 

U-SQL gör det lätt att lägga till din egen kod i .net-språk, till exempel C#, VB.Net eller F #. Du kan även distribuera din egen runtime för att stödja andra språk.

Det enklaste sättet att använda anpassade kod är att använda Data Lake-verktyg för Visual Studio bakomliggande kod funktioner. Mer information finns i [Självstudier: utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Det finns några nackdelar med bakomliggande kod:

- Källkoden hämtar upp för varje skript.
- bakomliggande kod kan inte delas med andra jobb.

Du kan aktivera bakomliggande kod till sammansättningar och registrera sammansättningarna i Data Lake Analytics-katalogen för att åtgärda dessa nackdelar.

## <a name="prerequisites"></a>Krav
* Visual Studio 2017, Visual Studio 2015, Visual Studio 2013 update 4 eller Visual Studio 2012 med Visual C++ installerat
* Microsoft Azure SDK för .NET version 2.5 eller högre.  Installera den med hjälp av installationsprogram för webbplattform eller Visual Studio-installationsprogrammet
* Ett Data Lake Analytics-konto.  Se [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).
* Gå igenom den [Kom igång med Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md) kursen.
* Ansluta till Azure.
* Överför källdata, se [Kom igång med Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md). 

## <a name="develop-assemblies-for-u-sql"></a>Utveckla sammansättningar för U-SQL

**Skapa och skicka ett U-SQL-jobb**

1. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**.
2. Expandera **installerad**, **mallar**, **Azure Data Lake**, **U-SQL(ADLA)**, Välj den **Class Library (för U-SQL Program)** mallen och klicka sedan på **OK**.
3. Skriv koden i Class1.cs.  Följande är ett kodexempel.

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. Klicka på den **skapa** -menyn och klicka sedan på **skapa lösning** att skapa DLL-filen.

## <a name="register-assemblies"></a>Registrera sammansättningar

Se [Använd Data Lake Analytics(U-SQL) katalogen](data-lake-analytics-use-u-sql-catalog.md).


## <a name="use-the-assemblies"></a>Använda sammansättningarna

Se [Använd Azure Data Lake-verktyg för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

## <a name="see-also"></a>Se även
* [Kom igång med Data Lake Analytics med hjälp av PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Använd Data Lake-verktyg för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använd Data Lake Analytics(U-SQL) katalog](data-lake-analytics-use-u-sql-catalog.md)
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)