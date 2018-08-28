---
title: Felsöka Azure Data Lake Analytics kod lokalt
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att felsöka U-SQL-jobb på den lokala arbetsstationen.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 9e73fc4db1404842e6d3878d88d8f02511587bc9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044985"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Felsöka Azure Data Lake Analytics kod lokalt

Du kan använda Azure Data Lake Tools för Visual Studio för att köra och felsöka Azure Data Lake Analytics-kod på den lokala arbetsstationen, precis som i Azure Data Lake Analytics-tjänsten.

Lär dig hur du [köra U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Felsök skript och C#-sammansättningar lokalt

Du kan felsöka C#-sammansättningar utan att skicka och registrera dem till Azure Data Lake Analytics-tjänsten. Du kan ange brytpunkter i både koden bakom filen och i ett refererat C#-projekt.

### <a name="debug-local-code-in-a-code-behind-file"></a>Felsöka lokal kod i en bakomliggande kod-fil

1. Ange brytpunkter i koden bakom filen.
2. Välj **F5** att felsöka skript lokalt.

> [!NOTE]
   > Följande procedur fungerar bara i Visual Studio 2015. I äldre versioner av Visual Studio, kanske du måste manuellt lägga till den **PDB** filer.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Felsöka lokal kod i ett refererat C#-projekt

1. Skapa ett C#-sammansättningsprojekt och skapa det för att generera utdata **DLL** fil.
2. Registrera den **DLL** filen med hjälp av en U-SQL-instruktion:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Ange brytpunkter i C#-koden.
4. Välj **F5** felsöka skript genom att referera till C# **DLL** lokalt.


## <a name="next-steps"></a>Nästa steg

- Ett exempel på en mer komplex fråga, se [analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Om du vill visa jobbinformation Se [Använd Jobbwebbläsare och Jobbvy för Azure Data Lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
- Om du vill använda Körningsvy Se [använda den Körningsvy i Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
