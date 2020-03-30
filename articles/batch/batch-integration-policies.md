---
title: Integrering med Azure-principer – Azure Batch | Microsoft-dokument
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618378"
---
# <a name="integration-with-azure-policy"></a>Integrering med Azure-princip

Azure Policy är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer som tillämpar regler över dina resurser för att säkerställa att dessa resurser förblir kompatibla med dina företagsstandarder och servicenivåavtal. Azure Policy utvärderar dina resurser för bristande efterlevnad av de principer som du tilldelar. 

Azure Batch har två inbyggda tillägg som hjälper dig att hantera principefterlevnad. 

|**Namn**...|   **Beskrivning**|**Effekt(er)**|  **Version**|    **Källkod**
|----------------|----------|----------|----------------|---------------|
|Diagnostikloggar i batchkonton ska aktiveras|   Granskning aktivera diagnostiska loggar. På så sätt kan du återskapa aktivitetsspår som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket äventyras|AuditIfNotExists, Inaktiverad|  2.0.0|  GitHub|
|Måttvarningsregler bör konfigureras för batchkonton| Granskningskonfiguration av måttvarningsregler för batchkonto för att aktivera det nödvändiga måttet|   AuditIfNotExists, Inaktiverad| 1.0.0|  GitHub|

I principdefinitionerna beskrivs de villkor som måste uppfyllas. Ett villkor jämför resursegenskapen med ett obligatoriskt värde. Resursegenskapsfält används med fördefinierade alias. Du kan använda egenskapsalias för att komma åt specifika egenskaper för en resurstyp. Med alias kan du begränsa vilka värden eller villkor som tillåts för en egenskap på en resurs. Varje alias mappar till sökvägar i olika API-versioner för en viss resurstyp. Under principutvärderingen får principmotorn egenskapssökvägen för den API-versionen.

De resurser som krävs av Batch inkluderar: konto, beräkningsnod, pool, jobb och aktivitet. Därför använder du egenskapsalias för att komma åt specifika egenskaper för dessa resurser. Läs mer om [Alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Om du vill vara säker på att du känner till de aktuella aliasen och granskar dina resurser och principer använder du Azure-principtillägget för Visual Studio-kod. Den kan installeras på alla plattformar som stöds av Visual Studio Code. Det här stödet omfattar Windows, Linux och macOS. Se [installationsriktlinjer](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



