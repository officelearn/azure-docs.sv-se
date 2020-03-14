---
title: Exempel på klass typer på Azure Lab Services | Microsoft Docs
description: Tillhandahåller vissa typer av klasser som du kan använda för att ställa in labb med Azure Lab Services.
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
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296735"
---
# <a name="class-types-overview---azure-lab-services"></a>Översikt över klass typer – Azure Lab Services

Med Azure Lab Services kan du snabbt konfigurera klass rums labb miljöer i molnet. Artiklar i det här avsnittet ger vägledning om hur du ställer in flera olika typer av klass rums labb med Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Djup inlärning i naturlig språk bearbetning

Du kan ställa in ett labb som fokuserar på djup inlärning i naturlig språk bearbetning (NLP) med hjälp av Azure Lab Services. NLP (Natural Language Processing) är en form av artificiell intelligens (AI) som gör det möjligt för datorer med översättning, tal igenkänning och andra funktioner för språk förståelse. Studenter som tar en NLP-klass får en virtuell Linux-dator (VM) för att lära sig hur man använder neurala-nätverks algoritmer för att utveckla djup inlärnings modeller som används för att analysera skrivna mänskliga språk.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb som fokuserar på djup inlärning i naturlig språk bearbetning med hjälp av Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Gränssnittsskript i Linux

Du kan ställa in ett labb för att lära sig använda Shell-skript i Linux. Skript är en användbar del av system administration som gör det möjligt för administratörer att undvika repetitiva uppgifter. I det här exempel scenariot täcker klassen traditionella bash-skript och förbättrade skript. Förbättrade skript är skript som kombinerar bash-kommandon och Ruby. Med den här metoden kan ruby skicka data runt-och bash-kommandon för att interagera med gränssnittet.

Studenter som använder dessa skript klasser får en virtuell Linux-dator för att lära sig grunderna i Linux och även bekanta dig med bash shell-skript. Den virtuella Linux-datorn har åtkomst till fjärr skrivbord och med text redigerarna [gedit](https://help.gnome.org/users/gedit/stable/) och [Visual Studio Code](https://code.visualstudio.com/) installerade.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Shell-skriptning i Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etisk hackning

Du kan ställa in ett labb för en klass som fokuserar på data utredning sida av etiska hackning. Testning av inträngning, en övning som används av den etiska hackning-communityn, inträffar när någon försöker få åtkomst till systemet eller nätverket för att Visa sårbarheter som en skadlig angripare kan utnyttja.

I en etisk hackning-klass kan studenter lära sig moderna tekniker för att försvara mot sårbarheter. Varje elev får en virtuell Windows Server-värd dator som har två kapslade virtuella datorer – en virtuell dator med [Metasploitable3](https://github.com/rapid7/metasploitable3) -avbildning och en annan dator med [Kali Linux](https://www.kali.org/) -avbildning. Den virtuella Metasploitable-datorn används för att utnyttja användnings sätt.  Den virtuella datorn Kali Linux ger till gång till de verktyg som behövs för att köra kriminal tekniska-uppgifter.

Detaljerad information om hur du ställer in den här typen av labb finns i [Konfigurera ett labb för att lära dig etiska hackning-klass](class-type-ethical-hacking.md).

## <a name="database-management"></a>Databashantering
Databas koncept är en av de introduktions kurser som finns i de flesta av dator vetenskaps avdelningarna på hög skola. Du kan ställa in ett labb för en grundläggande hanterings klass för databaser i Azure Lab Services. Du kan till exempel konfigurera en mall för virtuella datorer i ett labb med en [MySQL](https://www.mysql.com/) -databasserver eller en [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) -Server.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära databas hantering för Relations databaser](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Python-och Jupyter-anteckningsböcker
Du kan konfigurera en mall på Azure Lab Services med de verktyg som behövs för att lära eleverna hur de använder [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io). Jupyter Notebooks är ett projekt med öppen källkod som gör att du enkelt kan kombinera text och körbara [python](https://www.python.org/) -källkod på en enda arbets yta som kallas för en bärbar dator. Att köra en bärbar dator resulterar i en linjär inspelning av indata och utdata.  Dessa utdata kan innehålla text, tabeller med information, punkt diagram med mera.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Mobilapputveckling med Android Studio
Du kan ställa in ett labb i Azure Lab Services för att lära en introduktion till en introduktions utvecklings klass för mobil program. Den här klassen fokuserar på mobila Android-program som kan publiceras till [Google Play Butik](https://play.google.com/store/apps).  Eleverna lär dig hur du använder [Android Studio](https://developer.android.com/studio) för att bygga program.  [Visual Studio-emulator för Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) används för att testa programmet lokalt.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära mobil program utveckling med Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Konfigurera ett labb som fokuserar på djup inlärning i naturlig språk bearbetning med hjälp av Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Gränssnitts skript i Linux](class-type-shell-scripting-linux.md)
- [Etisk hackning](class-type-ethical-hacking.md)