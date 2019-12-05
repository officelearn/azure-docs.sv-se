---
title: Felsöka Kusto Query Language On python using VS Code-Azure Datautforskaren
description: Lär dig hur du felsöker Kusto Query Language (KQL) infogad python med VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: f0bf81ce6392e1650d9986d81a55eac9c416c24d
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822908"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Felsöka Kusto Query Language On python med VS Code

Azure Datautforskaren stöder körning av python-kod som bäddats in i Kusto-frågespråk med [python ()-plugin-programmet](/azure/kusto/query/pythonplugin). Plugin-programmet finns i ett begränsat läge, en isolerad och säker python-miljö. Plugin-funktionen python () utökar Kusto-språkets inbyggda funktioner med det stora arkivet med OSS python-paket. Med det här tillägget kan du köra avancerade algoritmer, till exempel Machine Learning, artificiell intelligens, statistik och tids serier som en del av frågan.

Språk verktygen för Kusto är inte praktiska för att utveckla och felsöka python-algoritmer. Därför ska du utveckla algoritmen på din favorit-Integrated Development Environment, till exempel Jupyter, pycharm med, VS eller VS Code. När algoritmen är klar kopierar du och klistrar in i KQL. För att förbättra och effektivisera det här arbets flödet stöder Azure Datautforskaren integration mellan Kusto Explorer-och webb GRÄNSSNITTs klienter och VS-kod för redigering och fel sökning av KQL i intern python-kod. 

> [!NOTE]
> Det här arbets flödet kan bara användas för att felsöka relativt små ingångs tabeller (upp till några MB). Därför kan du behöva begränsa indatamängden för fel sökning.  Om du behöver bearbeta en stor tabell begränsar du den för fel sökning med hjälp av `| take`, `| sample`eller `where rand() < 0.x`.

## <a name="prerequisites"></a>Krav

1. Installera python [Anaconda-distribution](https://www.anaconda.com/distribution/#download-section). I **Avancerade alternativ**väljer **du Lägg till Anaconda i miljövariabeln min sökväg**.
2. Installera [Visual Studio Code](https://code.visualstudio.com/Download)
3. Installera [python-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Kör din fråga i klient programmet

1. I klient programmet ska du använda en fråga som innehåller infogad python med `set query_python_debug;`
1. Kör frågan.
    * Kusto Explorer: VS Code startas automatiskt med skriptet *debug_python. py* .
    * Kusto webb gränssnitt: 
        1. Hämta och spara *debug_python. py*, *DF. txt*och *kargs. txt*. I fönstret väljer du **Tillåt**. **Spara** filer i den valda katalogen. 

            ![Webb gränssnitt laddar ned de infogade python-filerna](media/debug-inline-python/webui-inline-python.png)

        1. Högerklicka på *debug_python. py* och öppna med vs Code. 
        Skriptet *debug_python. py* innehåller den infogade python-koden från frågan KQL, som föregås av mallkod för att initiera indatamängden dataframe från *DF. txt* och ord listan för parametrarna från *kargs. txt*.    
            
1. I VS Code startar du VS Code-felsökaren: **felsöka** > **Starta fel sökning (F5)** , Välj **python** -konfiguration. Fel söknings programmet startar och Bryt punkten automatiskt för att felsöka den infogade koden.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Hur fungerar infogade python-fel i VS Code?

1. Frågan parsas och körs på servern tills den nödvändiga `| evaluate python()`-satsen har nåtts.
1. Sand boxen python anropas, men i stället för att köra koden serialiserar den indatalistan, parameter listan och koden och skickar tillbaka dem till klienten.
1. Dessa tre objekt sparas i tre filer: *DF. txt*, *kargs. txt*och *debug_python. py* i den valda katalogen (webb gränssnitt) eller i klienten% Temp% Directory (Kusto Explorer).
1. VS Code startas, förinstallerat med *debug_python. py* -filen som innehåller en prefixlängd för att initiera DF och kargs från sina respektive filer, följt av python-skriptet inbäddat i KQL-frågan.

## <a name="query-example"></a>Exempel på frågor

1. Kör följande KQL-fråga i klient programmet:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Se den resulterande tabellen:

    | x  | 4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Kör samma KQL-fråga i klient programmet med hjälp av `set query_python_debug;`:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code startas:

    ![Starta VS-kod](media/debug-inline-python/launch-vs-code.png)

1. VS kod fel och skriver ut resultat dataframe i fel söknings konsolen:

    ![VS Code-felsökning](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Det kan finnas skillnader mellan python sandbox-avbildningen och den lokala installationen. [Kontrol lera sandbox-avbildningen för vissa paket genom att fråga plugin-programmet](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
