---
title: Om Azure Lab Services | Microsoft Docs
description: Lär dig hur Lab Services kan göra det enkelt att skapa, hantera och skydda labb med virtuella datorer som kan användas av utvecklare, testare, lärare, elever och andra.
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
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660597"
---
# <a name="introduction-to-classroom-labs"></a>Introduktion till klassrumslabb
Azure Lab Services låter dig snabbt konfigurera en ett klassrums-labbmiljö i molnet. En undervisare skapar ett klassrumslabb, etablerar virtuella Windows- eller Linux-datorer, installerar nödvändig programvara och verktyg i klassen och gör dem tillgängliga för studenterna. Studenterna i klassen ansluter till virtuella datorer (VM) i labbet och använder dem för sina projekt, uppgifter, klassrumsövningar. 

Klassrumlabben är hanterade labb som hanteras av Azure. Själva tjänsten hanterar all infrastrukturhantering för ett hanterat labb, från att skapa virtuella datorer till att hantera fel och skala infrastrukturen. Du kan ange vilken typ av infrastruktur du behöver och installera alla verktyg och programvara som krävs för klassen. De hanterade labben finns för närvarande i en förhandsversion.  

## <a name="scenarios"></a>Scenarier
Här är det huvudsakliga scenariot som klassrumslabben i Azure Lab Services stöder: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Konfigurera ett ändringsbart labb i molnet för ditt klassrum  

- Skapa ett hanterat klassrumslabb. Du säger bara till tjänsten exakt vad du behöver så skapar den och hanterar labbets infrastruktur åt dig så att du kan fokusera på att undervisa din klass istället för att lägga tid på labbets tekniska detaljer. 
- Förse eleverna med ett labb med virtuella datorer som är konfigurerat med exakt det som behövs för en lektion. Ge varje elev ett begränsat antal timmar för användning av de virtuella datorerna för skolarbete.  
- Flytta din skolas fysiska datorlabb till molnet. Skala automatiskt antalet virtuella datorer endast efter den maximala användning och de kostnadströsklar som du anger för labbet. 
- Ta bort labbet med en enda klickning när du är klar. 

## <a name="user-profiles"></a>Användarprofiler
Den här artikeln beskriver olika användarprofiler i Azure Lab Services. 

### <a name="lab-account-owner"></a>Labbkontoägare
Vanligtvis fungerar en IT-administratör för organisationens molnresurser som ansvarar för Azure-prenumerationen som labbkontoägare och utför följande uppgifter:   

- Konfigurerar ett labbkonto för organisationen.
- Hanterar och konfigurerar principer för alla labb.
- Ger behörigheter till personer i organisationen för att skapa ett labb under labbkontot.

### <a name="educator"></a>Utbildare
Normalt skapar användare till exempel en lärare eller en online-undervisare klassrumslabb under ett labbkonto. En utbildare utför följande uppgifter: 

- Skapar ett klassrumslabb.
- Skapar virtuella maskiner i labbet. 
- Installerar rätt programvara på virtuella datorer.
- Anger vem som kan få åtkomst till labbet.
- Tillhandahåller registreringslänken till labbet för studenter.

### <a name="student"></a>Student
En student utför följande uppgifter:

- Använder registreringslänken som denne får från labbskaparen för att registrera sig för labbet. 
- Ansluter till en virtuell dator i labbet och använder den för att göra klassarbete, uppgifter och projekt. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett labbkonto som krävs för att skapa ett klassrumslabb med Azure Lab Services:

- [Konfigurera ett labbkonto](tutorial-setup-lab-account.md)
