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
ms.openlocfilehash: c3365450c90c4fda37884e8998fad70f5d164244
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006529"
---
Det finns ett maxantal för varje objekttyp för Azure Policy. En post av typen _Omfång_ betyder antingen prenumerationen eller [hanteringsgruppen](../articles/governance/management-groups/overview.md).

| Var | Vad | Maximalt antal |
|---|---|---|
| Omfång | Principdefinitioner | 250 |
| Omfång | Initiativdefinitioner | 100 |
| Klientorganisation | Initiativdefinitioner | 1000 |
| Omfång | Princip-/initiativtilldelningar | 100 |
| Principdefinition | Parametrar | 20 |
| Initiativdefinition | Principer | 100 |
| Initiativdefinition | Parametrar | 100 |
| Princip-/initiativtilldelningar | Undantag (notScopes) | 100 |
| Principregel | Kapslade villkor | 512 |
