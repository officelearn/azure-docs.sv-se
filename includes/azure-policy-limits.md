---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 57cec39bde460c6079091490acf541761c61e003
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119290"
---
Det finns ett högsta antal för varje objekttyp för Azure Policy. En post av typen _Omfång_ betyder antingen prenumerationen eller [hanteringsgruppen](../articles/governance/management-groups/overview.md).

| Där | Vad | Maximalt antal |
|---|---|---|
| Scope | Principdefinitioner | 250 |
| Scope | Initiativdefinitioner | 100 |
| Klient | Initiativdefinitioner | 1,000 |
| Scope | Princip eller ett initiativ tilldelningar | 100 |
| Definition av princip | Parametrar | 20 |
| Initiativdefinition | Principer | 100 |
| Initiativdefinition | Parametrar | 100 |
| Princip eller ett initiativ tilldelningar | Undantag (notScopes) | 250 |
| Principregel | Kapslade villkor | 512 |
