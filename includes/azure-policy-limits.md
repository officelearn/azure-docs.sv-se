---
title: inkludera fil
description: inkludera fil
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: e22594a50a9c0d814ef7d66443f8253f5832cb1d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038502"
---
Det finns ett maximalt antal för varje objekt typ för Azure Policy. En post av typen _Omfång_ betyder antingen prenumerationen eller [hanteringsgruppen](../articles/governance/management-groups/overview.md).

| Var | Vad | Maximalt antal |
|---|---|---|
| Omfång | Principdefinitioner | 500 |
| Omfång | Initiativdefinitioner | 200 |
| Klientorganisation | Initiativdefinitioner | 2 500 |
| Omfång | Princip-eller initiativ tilldelningar | 200 |
| Principdefinition | Parametrar | 20 |
| Initiativdefinition | Principer | 1000 |
| Initiativdefinition | Parametrar | 100 |
| Princip-eller initiativ tilldelningar | Undantag (notScopes) | 400 |
| Principregel | Kapslade villkor | 512 |
| Reparations uppgift | Resurser | 1000 |
