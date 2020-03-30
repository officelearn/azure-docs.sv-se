---
title: Exempel på klasstyper på Azure Lab Services | Microsoft-dokument
description: Innehåller vissa typer av klasser som du kan ställa in labb för med Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 27619a69a1f7fbded8ce6430afc2b8e9a8b4a00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296735"
---
# <a name="class-types-overview---azure-lab-services"></a>Översikt över klasstyper - Azure Lab Services

Med Azure Lab Services kan du snabbt konfigurera labbmiljöer i klassrummet i molnet. Artiklar i det här avsnittet innehåller vägledning om hur du konfigurerar flera typer av klassrumslabb med Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Djupinlärning i bearbetning av naturligt språk

Du kan konfigurera ett labb som fokuserar på djupinlärning i bearbetning av naturligt språk (NLP) med Hjälp av Azure Lab Services. Bearbetning av naturligt språk (NLP) är en form av artificiell intelligens (AI) som möjliggör datorer med översättning, taligenkänning och andra funktioner för språks förståelse. Studenter som tar en NLP-klass får en virtuell Linux-maskin (VM) för att lära sig att tillämpa neurala nätverksalgoritmer för att utveckla djupinlärningsmodeller som används för att analysera skriftligt mänskligt språk.

Detaljerad information om hur du konfigurerar den här typen av labb finns [i Konfigurera ett labb fokuserat på djupinlärning i bearbetning av naturligt språk med Hjälp av Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Gränssnittsskript i Linux

Du kan skapa ett labb för att lära ut skalskript på Linux. Skript är en användbar del av systemadministrationen som gör det möjligt för administratörer att undvika repetitiva uppgifter. I det här exempelscenariot täcker klassen traditionella bash-skript och förbättrade skript. Förbättrade skript är skript som kombinerar bash-kommandon och Ruby. Den här metoden gör det möjligt för Ruby att skicka data runt och bash kommandon för att interagera med skalet.

Studenter som tar dessa skript klasser få en Linux virtuell maskin för att lära sig grunderna i Linux, och även bekanta sig med bash skal skript. Linux virtuella maskinen levereras med fjärrskrivbordsåtkomst aktiverad och med [gedit](https://help.gnome.org/users/gedit/stable/) och [Visual Studio Code](https://code.visualstudio.com/) textredigerare installerade.

Detaljerad information om hur du ställer in den här typen av labb finns i [Shell-skript på Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etisk hackning

Du kan ställa in ett labb för en klass som fokuserar på kriminalteknik sidan av etiska hacking. Penetrationstester, en metod som används av den etiska hacking community, inträffar när någon försöker få tillgång till systemet eller nätverket för att visa sårbarheter som en skadlig angripare kan utnyttja.

I en etisk hacking klass, kan eleverna lära sig moderna tekniker för att försvara sig mot sårbarheter. Varje elev får en virtuell dator värd för Windows Server som har två kapslade virtuella datorer – en virtuell dator med [Metasploitable3-avbildning](https://github.com/rapid7/metasploitable3) och en annan dator med [Kali Linux-avbildning.](https://www.kali.org/) Den metasploitable virtuella datorn används för att utnyttja ändamål.  Kali Linux virtuella maskin ger tillgång till de verktyg som behövs för att utföra kriminaltekniska uppgifter.

Detaljerad information om hur du ställer in den här typen av labb finns i [Konfigurera ett labb för att lära ut etisk hackningsklass](class-type-ethical-hacking.md).

## <a name="database-management"></a>Databashantering
Databaskoncept är en av de introduktionskurser som undervisas vid de flesta datavetenskapliga avdelningar på college. Du kan konfigurera ett labb för en grundläggande databashanteringsklass i Azure Lab Services. Du kan till exempel konfigurera en mall för virtuella datorer i ett labb med en [MySQL](https://www.mysql.com/) Database Server eller en [SQL Server 2019-server.](https://www.microsoft.com/sql-server/sql-server-2019)

Detaljerad information om hur du ställer in den här typen av labb finns i [Konfigurera ett labb för att lära databashantering för relationsdatabaser](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Python och Jupyter bärbara datorer
Du kan konfigurera en malldator i Azure Lab Services med de verktyg som behövs för att lära eleverna hur du använder [Jupyter-anteckningsböcker](http://jupyter-notebook.readthedocs.io). Jupyter Notebooks är ett projekt med öppen källkod som gör att du enkelt kan kombinera RTF och körbar [Python-källkod](https://www.python.org/) på en enda duk som kallas en anteckningsbok. Om du kör en anteckningsbok visas en linjär post med indata och utdata.  Dessa utdata kan innehålla text, tabeller med information, punktdiagram med mera.

Detaljerad information om hur du ställer in den här typen av labb finns i [Konfigurera ett labb för att lära ut datavetenskap med Python- och Jupyter-anteckningsböcker](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Mobilapputveckling med Android Studio
Du kan konfigurera ett labb i Azure Lab Services för att lära ut en inledande utvecklingsklass för mobila program. Den här klassen fokuserar på Android-mobilappar som kan publiceras i [Google Play Butik](https://play.google.com/store/apps).  Eleverna lär sig att använda [Android Studio](https://developer.android.com/studio) för att skapa program.  [Visual Studio Emulator för Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) används för att testa programmet lokalt.

Detaljerad information om hur du konfigurerar den här typen av labb finns i Konfigurera ett labb för att lära ut utveckling av [mobilapplikationer med Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Konfigurera ett labb med fokus på djupinlärning i bearbetning av naturligt språk med Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Gränssnittsskript i Linux](class-type-shell-scripting-linux.md)
- [Etisk hackning](class-type-ethical-hacking.md)