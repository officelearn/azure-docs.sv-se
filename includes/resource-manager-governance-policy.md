---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740590"
---
[Azure-principer](/azure/azure-policy/) hjälper dig se till att alla resurser i prenumerationen uppfyller företagets standarder. Använd principer för att minska kostnaderna genom att begränsa distributionsalternativen till endast de resurstyper och SKU:er som är godkända. Du definierar regler och åtgärder för dina resurser, och dessa regler tillämpas automatiskt under distributionen. Du kan till exempel styra vilka typer av resurser som distribueras. Eller så kan du begränsa de godkända platserna för resurser. Vissa principer nekar en åtgärd, och vissa principer konfigurerar granskning av en åtgärd.

Principen kompletterar rollbaserad åtkomstkontroll (RBAC). RBAC fokuserar på användaråtkomst och är ett system som nekar som standard och tillåter endast uttryckligen. Principer fokuserar på resursegenskaper under och efter distributionen. De tillåter som standard, medan ”neka” måste anges uttryckligen.

Det finns två begrepp att förstå när det gäller principer – *principdefinitioner* och *principtilldelningar*. Principdefinitioner beskriver hanteringsvillkor som du vill tillämpa. Principtilldelningar verkställer en principdefinition för ett visst omfång.

![Tilldela principer](./media/resource-manager-governance-policy/policy-concepts.png)

Azure innehåller flera inbyggda principdefinitioner som du kan använda utan ändringar. Du skickar parametervärden för att ange de värden som tillåts i omfånget. Om den inbyggda principdefinition inte uppfyller dina krav kan du [skapa anpassade principdefinitioner](../articles/azure-policy/create-manage-policy.md).
