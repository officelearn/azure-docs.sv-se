---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "67132990"
---
Det finns ett maximalt antal för varje objekt typ för Azure Policy. En post av typen _Omfång_ betyder antingen prenumerationen eller [hanteringsgruppen](../articles/governance/management-groups/overview.md).

| Där | Vad | Maximalt antal |
|---|---|---|
| Omfång | Principdefinitioner | 500 |
| Omfång | Initiativdefinitioner | 100 |
| Klient | Initiativdefinitioner | 1,000 |
| Omfång | Princip-eller initiativ tilldelningar | 100 |
| Definition av princip | Parametrar | 20 |
| Initiativdefinition | Principer | 100 |
| Initiativdefinition | Parametrar | 100 |
| Princip-eller initiativ tilldelningar | Undantag (notScopes) | 400 |
| Principregel | Kapslade villkor | 512 |
