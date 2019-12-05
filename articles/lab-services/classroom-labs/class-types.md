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
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: e1c5504b30c2784e8657ccc0dc4ec18689fe2a68
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806821"
---
# <a name="class-types-overview---azure-lab-services"></a>Översikt över klass typer – Azure Lab Services

Med Azure Lab Services kan du snabbt konfigurera klass rums labb miljöer i molnet. Artiklar i det här avsnittet ger vägledning om hur du ställer in flera olika typer av klass rums labb med Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Djup inlärning i naturlig språk bearbetning

Du kan ställa in ett labb som fokuserar på djup inlärning i naturlig språk bearbetning (NLP) med hjälp av Azure Lab Services. NLP (Natural Language Processing) är en form av artificiell intelligens (AI) som gör det möjligt för datorer med översättning, tal igenkänning och andra funktioner för språk förståelse. Studenter som tar en NLP-klass får en virtuell Linux-dator (VM) för att lära sig hur man använder neurala-nätverks algoritmer för att utveckla djup inlärnings modeller som används för att analysera skrivna mänskliga språk.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb som fokuserar på djup inlärning i naturlig språk bearbetning med hjälp av Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Gränssnittsskript i Linux

Du kan ställa in ett labb för att lära sig använda Shell-skript i Linux. Skript är en användbar del av system administration som gör det möjligt för administratörer att undvika repetitiva uppgifter. I det här exempel scenariot täcker klassen traditionella bash-skript och förbättrade skript. Förbättrade skript är skript som kombinerar bash-kommandon och Ruby. Med den här metoden kan ruby skicka data runt-och bash-kommandon för att interagera med gränssnittet.

Studenter som använder dessa skript klasser får en virtuell Linux-dator för att lära sig grunderna i Linux och även bekanta dig med bash shell-skript. Den virtuella Linux-datorn har åtkomst till fjärr skrivbord och med text redigerarna [gedit](https://help.gnome.org/users/gedit/stable/) och [Visual Studio Code](https://code.visualstudio.com/) installerade.

För detaljerad information om hur du konfigurerar den här typen av labb, se [Shell-skript i Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etisk hackning

Du kan ställa in ett labb för en klass som fokuserar på data utredning sida av etiska hackning. Testning av inträngning, en övning som används av den etiska hackning-communityn, inträffar när någon försöker få åtkomst till systemet eller nätverket för att Visa sårbarheter som en skadlig angripare kan utnyttja.

I en etisk hackning-klass kan studenter lära sig moderna tekniker för att försvara mot sårbarheter. Varje elev får en virtuell Windows Server-värd dator som har två kapslade virtuella datorer – en virtuell dator med [Metasploitable3](https://github.com/rapid7/metasploitable3) -avbildning och en annan dator med [Kali Linux](https://www.kali.org/) -avbildning. Den virtuella Metasploitable-datorn används för att utnyttja användnings sätt.  Den virtuella datorn Kali Linux ger till gång till de verktyg som behövs för att köra kriminal tekniska-uppgifter.

Detaljerad information om hur du konfigurerar den här typen av labb finns i [Konfigurera ett labb för att lära dig etiska hackning-klass](class-type-ethical-hacking.md).

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Konfigurera ett labb som fokuserar på djup inlärning i naturlig språk bearbetning med hjälp av Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Gränssnitts skript i Linux](class-type-shell-scripting-linux.md)
- [Etisk hackning](class-type-ethical-hacking.md)
