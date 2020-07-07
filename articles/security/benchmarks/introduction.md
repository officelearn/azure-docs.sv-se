---
title: Introduktion till benchmark-värden för Azure-säkerhet
description: Introduktion till säkerhet benchmark
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 883042c94576014c3bdeabd38516d9531c8fd3dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82606865"
---
# <a name="azure-security-benchmark-introduction"></a>Introduktion till Azure Security benchmark

Du kan ha flera år eller till och med årtionden med lokal dator användning. Du vet hur man skyddar dessa distributioner; men molnet är annorlunda. Hur vet du om dina moln distributioner är säkra? Vad är skillnaderna mellan säkerhets praxis för lokala system och moln distributioner?

Det finns en stor samling fakta blad, metod tips, referens arkitekturer, webb vägledning, verktyg med öppen källkod, kommersiella lösningar, informations flöden med mera, som kan användas för att skydda molnet. Vilket alternativ ska du använda? Vad kan du göra för att få en acceptabel säkerhets nivå i molnet? 

Ett av de bästa sätten att skydda dina moln distributioner är att fokusera på Cloud Security benchmark-rekommendationer. Benchmark-rekommendationer, för att skydda vilken tjänst som helst, börjar med en grundläggande förståelse för cybersäkerhet-risk och hur du hanterar den. Du kan sedan använda den här förståelsen genom att införa grundläggande säkerhets rekommendationer från din moln tjänst leverantör för att få hjälp att välja vissa inställningar för säkerhets konfiguration i din miljö. 

Azures säkerhets benchmark innehåller en samling med högkvalitativa säkerhets rekommendationer som du kan använda för att skydda de flesta tjänster som du använder i Azure. Du kan tänka på dessa rekommendationer som "allmänt" eller "organisatoriska" eftersom de är tillämpliga på de flesta Azure-tjänster. Rekommendationerna för Azures säkerhets benchmark anpassas sedan för varje Azure-tjänst och den här anpassade vägledningen finns i service rekommendationer artiklar. 

I dokumentationen för Azures säkerhets benchmark anges säkerhets kontroller och tjänst rekommendationer.

- **Säkerhets kontroller**: rekommendationer för Azures säkerhets benchmark kategoriseras av säkerhets kontroller. Säkerhets kontroller representerar oberoende säkerhets krav på hög nivå, till exempel nätverks säkerhet och data skydd. Varje säkerhets kontroll har en uppsättning säkerhets rekommendationer och anvisningar som hjälper dig att implementera dessa rekommendationer. 
- **Tjänst rekommendationer**: när det är tillgängligt kommer benchmark-rekommendationer för Azure-tjänster att omfatta rekommendationer för Azure Security benchmark som skräddarsys specifikt för den tjänsten. 

Termerna "kontroll", "benchmark" och "bas linje" används ofta i Azures säkerhets benchmark-dokumentation och det är viktigt att förstå hur Azure använder dessa villkor. 

| Term | Beskrivning | Exempel |
|--|--|--|
| Kontroll | En **kontroll** är en övergripande beskrivning av en funktion eller aktivitet som behöver åtgärdas och som inte är särskilt för en teknik eller implementering. | Data skydd är en av säkerhets kontrollerna. Den här kontrollen innehåller vissa åtgärder som måste åtgärdas för att säkerställa att data skyddas. |
| Benchmark | Ett **riktmärke** innehåller säkerhets rekommendationer för en speciell teknik, till exempel Azure. Rekommendationerna kategoriseras med den kontroll som de tillhör. | Azures säkerhets benchmark omfattar de säkerhets rekommendationer som är aktuella för Azure-plattformen  |
| Baslinje | En **bas linje** är säkerhets kraven för en organisation. Säkerhets kraven baseras på benchmark-rekommendationer. Varje organisation bestämmer vilka benchmark-rekommendationer som ska ingå i deras bas linje. | Contoso-företaget skapar säkerhets bas linjen genom att välja att kräva vissa rekommendationer i Azures säkerhets benchmark. |

Vi välkomnar din feedback om Azures säkerhets benchmark! Vi rekommenderar att du anger kommentarer i feedback-avsnittet nedan. Om du föredrar att dela dina inloggade privat med Azures säkerhets benchmark-teamet, är du välkommen att fylla i formuläret påhttps://aka.ms/AzSecBenchmark 
