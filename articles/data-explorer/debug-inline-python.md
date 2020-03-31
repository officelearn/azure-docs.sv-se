---
title: Felsöka Kusto-frågespråk infogad Python med VS-kod - Azure Data Explorer
description: Lär dig hur du felsöker Kusto-frågespråk (KQL) infogad Python med VS-kod.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444461"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debug Kusto frågespråk infogad Python med VS-kod

Azure Data Explorer stöder körning av Python-kod inbäddad i Kusto-frågespråk med [plugin-programmet python().](/azure/kusto/query/pythonplugin) Plugin-körningen finns i en sandlåda, en isolerad och säker Python-miljö. Plugin-funktionen python() utökar Kusto-frågespråkets modersmål med det enorma arkivet av OSS Python-paket. Med det här tillägget kan du köra avancerade algoritmer, till exempel maskininlärning, artificiell intelligens, statistik och tidsserier som en del av frågan.

Kusto frågespråkverktyg är inte praktiska för att utveckla och felsöka Python-algoritmer. Därför kan du utveckla algoritmen på din favorith python-integrerad utvecklingsmiljö som Jupyter, PyCharm, VS eller VS-kod. När algoritmen är klar kopierar och klistrar du in i KQL. För att förbättra och effektivisera det här arbetsflödet stöder Azure Data Explorer integrering mellan Kusto Explorer- eller Webbgränssnittsklienter och VS-kod för redigering och felsökning av KQL-inline Python-kod. 

> [!NOTE]
> Det här arbetsflödet kan bara användas för att felsöka relativt små indatatabeller (upp till få MB). Därför kan du behöva begränsa indata för felsökning.  Om du behöver bearbeta en stor tabell begränsar `| take`du `| sample`den `where rand() < 0.x`för felsökning med , eller .

## <a name="prerequisites"></a>Krav

1. Installera Python [Anaconda Distribution](https://www.anaconda.com/distribution/#download-section). I **Avancerade alternativ**väljer du Lägg till **Anaconda i miljövariabeln FÖR SÖKS.**
2. Installera [Visual Studio-kod](https://code.visualstudio.com/Download)
3. Installera [Python-tillägg för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Kör frågan i klientprogrammet

1. I klientprogrammet prefixar du en fråga som innehåller inline Python med`set query_python_debug;`
1. Kör frågan.
    * Kusto Explorer: VS Code startas automatiskt med *debug_python.py* skriptet.
    * Kusto Webb-användargränssnitt: 
        1. Ladda ner och spara *debug_python.py*, *df.txt*och *kargs.txt*. Välj **Tillåt**i fönstret . **Spara** filer i vald katalog. 

            ![Webbgränssnittet hämtar inline python-filer](media/debug-inline-python/webui-inline-python.png)

        1. Högerklicka *debug_python.py* och öppna med VS-kod. 
        Skriptet *debug_python.py* innehåller den infogade Python-koden från KQL-frågan, som föregås av mallkoden för att initiera indataramen från *df.txt* och ordlistan med parametrar från *kargs.txt*.    
            
1. Starta VS-kodfelsökaren: **Felsök** > **startfelsökning (F5) i VS-kod**och välj **Python-konfiguration.** Felsökaren startar och bryts automatiskt för att felsöka den infogade koden.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Hur fungerar infogad Python-felsökning i VS-kod?

1. Frågan tolkas och körs på servern tills `| evaluate python()` den nödvändiga satsen har nåtts.
1. Python-sandlådan anropas, men i stället för att köra koden serialiserar den indatatabellen, ordlistan med parametrar och koden och skickar tillbaka dem till klienten.
1. Dessa tre objekt sparas i tre filer: *df.txt*, *kargs.txt*och *debug_python.py* i den valda katalogen (Webbgränssnitt) eller i klienten %TEMP% katalog (Kusto Explorer).
1. VS-kod startas, förinstalleras med *filen debug_python.py* som innehåller en prefixkod för att initiera df och kargs från sina respektive filer, följt av Python-skriptet inbäddat i KQL-frågan.

## <a name="query-example"></a>Exempel på fråga

1. Kör följande KQL-fråga i klientprogrammet:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Se den resulterande tabellen:

    | x  | x4 (på andra)  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Kör samma KQL-fråga i `set query_python_debug;`klientprogrammet med:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS-koden lanseras:

    ![starta VS-kod](media/debug-inline-python/launch-vs-code.png)

1. VS-kod felsöker och skriver ut dataram för resultat i felsökningskonsolen:

    ![VS-kodfelsökning](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Det kan finnas skillnader mellan Python-sandlådan och den lokala installationen. [Kontrollera sandlådebilden för specifika paket genom att fråga insticksprogrammet](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
