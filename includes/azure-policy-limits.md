---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/30/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 7907504401f4b47aafe6032ea895d9647e6c303c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420794"
---
Det finns ett högsta antal för varje objekttyp för Azure Policy. En post av typen _Omfång_ betyder antingen prenumerationen eller [hanteringsgruppen](../articles/governance/management-groups/overview.md).

| Var | Vad | Maximalt antal |
|---|---|---|
| Scope | Principdefinitioner | 250 |
| Scope | Initiativdefinitioner | 100 |
| Klientorganisation | Initiativdefinitioner | 1,000 |
| Scope | Princip eller ett initiativ tilldelningar | 100 |
| Definition av princip | Parametrar | 20 |
| Initiativdefinition | Principer | 100 |
| Initiativdefinition | Parametrar | 100 |
| Princip eller ett initiativ tilldelningar | Undantag (notScopes) | 400 |
| Principregel | Kapslade villkor | 512 |
