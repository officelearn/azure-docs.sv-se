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
ms.openlocfilehash: dc4281c17b92e1720625764a52a34a94d6f296ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64732734"
---
Innan du skapar några objekt går vi igenom begreppet omfång. Azure har fyra nivåer av hantering: hanteringsgrupper, prenumeration, resursgrupp och resurs. [Hanteringsgrupper](../articles/billing/billing-enterprise-mgmt-group-overview.md) är i förhandsversion. Följande bild visar ett exempel på dessa lager.

![Scope](./media/resource-manager-governance-scope/scope-levels.png)

Du tillämpar hanteringsinställningar på vilken som helst av dessa omfångsnivåer. Den nivå nu väljer avgör hur brett inställningen tillämpas. Lägre nivåer ärver inställningar från högre nivåer. När du tillämpar en inställning på prenumerationen tillämpas den inställningen för alla resursgrupper och resurser i prenumerationen. När du tillämpar en inställning på resursgruppen tillämpas den inställningen på den resursgruppen och alla dess resurser. En annan resursgrupp har dock inte den inställningen.

Vanligtvis är det bra att tillämpa kritiska inställningar på högre nivåer och projektspecifika krav på lägre nivåer. Till exempel vill du kanske se till att alla resurser för din organisation har distribuerats till vissa regioner. För att åstadkomma det här kravet tillämpar du en princip på den prenumeration som anger tillåtna platser. När andra användare i organisationen lägger till nya resursgrupper och resurser framtvingas de tillåtna platserna automatiskt. 
