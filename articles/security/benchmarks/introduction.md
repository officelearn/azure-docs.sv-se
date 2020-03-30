---
title: Introduktion till benchmark-värden för Azure-säkerhet
description: Introduktion till säkerhetsriktmärken
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945325"
---
# <a name="azure-security-benchmark-introduction"></a>Introduktion av Azure-säkerhetsriktmärken

Du kan ha flera år eller till och med årtionden av erfarenhet av lokal databehandling. Du vet hur du säkrar dessa distributioner; men molnet är annorlunda. Hur vet du om dina molndistributioner är säkra? Vilka är skillnaderna mellan säkerhetsrutinerna för lokala system och molndistributioner?

Det finns en stor samling vitböcker, metodtips, referensarkitekturer, webbvägledning, verktyg med öppen källkod, kommersiella lösningar, intelligensflöden med mera som kan användas för att skydda molnet. Vilket alternativ ska du använda? Vad kan du göra för att få en acceptabel säkerhetsnivå i molnet? 

Ett av de bästa sätten att skydda dina molndistributioner är att fokusera på rekommendationer för distributionssäkerhet i molnet. Benchmark rekommendationer, för att säkra alla tjänster, börjar med en grundläggande förståelse för cybersäkerhetsrisk och hur man hanterar den. Du kan sedan använda den här förståelsen genom att anta säkerhetsrekommendationer för referensvärden från molntjänstleverantören för att välja specifika säkerhetskonfigurationsinställningar i din miljö. 

Azure Security Benchmark innehåller en samling säkerhetsrekommendationer med stor genomslagskraft som du kan använda för att skydda de flesta av de tjänster du använder i Azure. Du kan se dessa rekommendationer som "allmänna" eller "organisatoriska" som de är tillämpliga på de flesta Azure-tjänster. Azure Security Benchmark-rekommendationerna anpassas sedan för varje Azure-tjänst, och den här anpassade vägledningen finns i artiklar om tjänstrekommendationer. 

Azure Security Benchmark-dokumentationen anger säkerhetskontroller och tjänstrekommendationer.

- **Säkerhetskontroller:** Azure Security Benchmark-rekommendationerna kategoriseras av säkerhetskontroller. Säkerhetskontroller representerar högnivåsäkerhetskrav för leverantörer, till exempel nätverkssäkerhet och dataskydd. Varje säkerhetskontroll har en uppsättning säkerhetsrekommendationer och instruktioner som hjälper dig att implementera dessa rekommendationer. 
- **Tjänstrekommendationer:** När det är tillgängligt innehåller benchmark-rekommendationer för Azure-tjänster Azure Security Benchmark-rekommendationer som är skräddarsydda specifikt för den tjänsten. 

Termerna "Control", "Benchmark" och "Baseline" används ofta i Azure Security Benchmark-dokumentationen och det är viktigt att förstå hur Azure använder dessa termer. 

| Period | Beskrivning | Exempel |
|--|--|--|
| Kontroll | En **kontroll** är en beskrivning på hög nivå av en funktion eller aktivitet som måste åtgärdas och som inte är specifik för en teknik eller implementering. | Dataskydd är en av säkerhetskontrollerna. Den här kontrollen innehåller specifika åtgärder som måste åtgärdas för att säkerställa att data skyddas. |
| Benchmark | Ett **riktmärke** innehåller säkerhetsrekommendationer för en viss teknik, till exempel Azure. Rekommendationerna kategoriseras efter den kontroll som de tillhör. | Azure Security-riktmärket innehåller de säkerhetsrekommendationer som är specifika för Azure-plattformen  |
| Baslinje | En **baslinje** är säkerhetskraven för en organisation. Säkerhetskraven baseras på riktmärkesrekommendationer. Varje organisation bestämmer vilka referensrekommendationer som ska inkluderas i deras baslinje. | Contoso-företaget skapar sin säkerhetsbaslinje genom att välja att kräva specifika rekommendationer i Azure Security Benchmark. |

Vi välkomnar din feedback på Azure Security Benchmark! Vi uppmuntrar dig att lämna synpunkter i feedbackområdet nedan. Om du föredrar att dela dina indata mer privat med Azure Security Benchmark-teamet är du välkommen att fylla i formuläret påhttps://aka.ms/AzSecBenchmark 
