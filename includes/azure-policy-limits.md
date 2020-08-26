---
title: inkludera fil
description: inkludera fil
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: 9fcaca3f89217f649eb970ec70514a5735222782
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854098"
---
Det finns ett maximalt antal för varje objekt typ för Azure Policy. En post av typen _Omfång_ betyder antingen prenumerationen eller [hanteringsgruppen](../articles/governance/management-groups/overview.md).

| Var | Vad | Maximalt antal |
|---|---|---|
| Omfång | Principdefinitioner | 500 |
| Omfång | Initiativdefinitioner | 200 |
| Klientorganisation | Initiativdefinitioner | 2 500 |
| Omfång | Princip-eller initiativ tilldelningar | 200 |
| Definition av princip | Parametrar | 20 |
| Initiativdefinition | Principer | 1000 |
| Initiativdefinition | Parametrar | 100 |
| Princip-eller initiativ tilldelningar | Undantag (notScopes) | 400 |
| Principregel | Kapslade villkor | 512 |
| Reparations uppgift | Resurser | 500 |
