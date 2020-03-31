---
title: Felsöka Azure Data Lake Analytics-kod lokalt
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att felsöka U-SQL-jobb på din lokala arbetsstation.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61472999"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Felsöka Azure Data Lake Analytics-kod lokalt

Du kan använda Azure Data Lake Tools för Visual Studio för att köra och felsöka Azure Data Lake Analytics-kod på din lokala arbetsstation, precis som du kan i Azure Data Lake Analytics-tjänsten.

Lär dig hur du [kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Felsök skript och C#-sammansättningar lokalt

Du kan felsöka C#-sammansättningar utan att skicka in och registrera dem till Azure Data Lake Analytics-tjänsten. Du kan ange brytpunkter i både den bakomkodfilen och i ett refererat C#-projekt.

### <a name="debug-local-code-in-a-code-behind-file"></a>Felsök lokal kod i en bakomkodfil

1. Ange brytpunkter i den bakomgående koden.
2. Välj **F5** om du vill felsöka skriptet lokalt.

> [!NOTE]
   > Följande procedur fungerar endast i Visual Studio 2015. I äldre Visual Studio-versioner kan du behöva lägga till **handdubbarfilerna** manuellt.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Felsöka lokal kod i ett refererat C#-projekt

1. Skapa ett C#-monteringsprojekt och skapa det för att generera **DLL-filen** för utdata.
2. Registrera **DLL-filen** med hjälp av ett U-SQL-uttryck:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Ange brytpunkter i C#-koden.
4. Välj **F5** för att felsöka skriptet genom att referera till C# **DLL-filen** lokalt.


## <a name="next-steps"></a>Nästa steg

- Ett exempel på en mer komplex fråga finns i [Analysera webbplatsloggar med Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Information om hur du visar jobbinformation finns i [Använda jobbwebbläsare och jobbvy för Azure Data Lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
- Information om hur du använder hörnkörningsvyn finns [i Använda vertexutförandevyn i DataSjöverktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
