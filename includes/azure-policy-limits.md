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
ms.openlocfilehash: 0a54dfdb810ea578c1e7c8fcc7ca0343e72164ae
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50964646"
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
| Princip-/initiativtilldelningar | Undantag (notScopes) | 250 |
| Principregel | Kapslade villkor | 512 |
