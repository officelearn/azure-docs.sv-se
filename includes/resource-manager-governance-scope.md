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
ms.openlocfilehash: 14ac8ff54f5627509fe10a90bfdd0a01c07762d9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027165"
---
Innan du skapar några objekt går vi igenom begreppet omfång. Azure har fyra nivåer av hantering: hanteringsgrupper, prenumeration, resursgrupp och resurs. [Hanteringsgrupper](../articles/governance/management-groups/overview.md) är i förhandsversion. Följande bild visar ett exempel på dessa lager.

![Omfång](./media/resource-manager-governance-scope/scope-levels.png)

Du tillämpar hanteringsinställningar på vilken som helst av dessa omfångsnivåer. Den nivå nu väljer avgör hur brett inställningen tillämpas. Lägre nivåer ärver inställningar från högre nivåer. När du tillämpar en inställning på prenumerationen tillämpas den inställningen för alla resursgrupper och resurser i prenumerationen. När du tillämpar en inställning på resursgruppen tillämpas den inställningen på den resursgruppen och alla dess resurser. En annan resursgrupp har dock inte den inställningen.

Vanligtvis är det bra att tillämpa kritiska inställningar på högre nivåer och projektspecifika krav på lägre nivåer. Till exempel vill du kanske se till att alla resurser för din organisation har distribuerats till vissa regioner. För att åstadkomma det här kravet tillämpar du en princip på den prenumeration som anger tillåtna platser. När andra användare i organisationen lägger till nya resursgrupper och resurser framtvingas de tillåtna platserna automatiskt.