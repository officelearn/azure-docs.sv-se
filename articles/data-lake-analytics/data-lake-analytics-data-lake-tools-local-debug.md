---
title: Felsöka Azure Data Lake Analytics kod lokalt | Microsoft Docs
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att felsöka U-SQL-jobb på den lokala arbetsstationen.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889677"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Felsöka Azure Data Lake Analytics kod lokalt

Du kan använda Azure Data Lake Tools för Visual Studio för att köra och felsöka Azure Data Lake Analytics-kod på din arbetsstation, precis som i Azure Data Lake-tjänsten.

Lär dig [köra U-SQL-skript på din lokala dator](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Felsök skript och C#-sammansättningar lokalt

Du kan felsöka C#-sammansättningar utan att skicka och registrera dem till Azure Data Lake Analytics-tjänsten. Du kan ange brytpunkter i både koden bakom filen och ett refererat C#-projekt.

### <a name="to-debug-local-code-in-code-behind-file"></a>Felsöka lokal kod i koden bakom filen

1. Ange brytpunkter i koden bakom filen.
2. Tryck på F5 för att felsöka skript lokalt.

> [!NOTE]
   > Följande procedur fungerar bara i Visual Studio 2015. Du kan behöva lägga till PDB-filer manuellt i äldre Visual Studio.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Om du vill felsöka lokal kod i ett refererat C#-projekt

1. Skapa ett C#-sammansättningsprojekt och skapa det för att generera DLL-filen för utdata.
2. Registrera DLL-filen med hjälp av ett U-SQL-uttryck:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Ange brytpunkter i C#-koden.
4. Tryck på F5 för att felsöka skriptet med refererar till C#-DLL: en lokalt.


## <a name="next-steps"></a>Nästa steg

- Om du vill se en mer komplex fråga, se [analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Om du vill visa jobbinformation Se [Använd Jobbwebbläsare och Jobbvy för Azure Data Lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
- Om du vill använda Körningsvy Se [använda den Körningsvy i Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
