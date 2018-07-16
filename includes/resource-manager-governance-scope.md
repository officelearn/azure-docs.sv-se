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
ms.openlocfilehash: 4f5dbbd1da8a221fb5f8b9641e016829987db538
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740613"
---
Innan du skapar några objekt går vi igenom begreppet omfång. Azure har fyra nivåer av hantering: hanteringsgrupper, prenumeration, resursgrupp och resurs. [Hanteringsgrupper](../articles/billing/billing-enterprise-mgmt-group-overview.md) är i förhandsversion. Följande bild visar ett exempel på dessa lager.

![Omfång](./media/resource-manager-governance-scope/scope-levels.png)

Du tillämpar hanteringsinställningar på vilken som helst av dessa omfångsnivåer. Den nivå nu väljer avgör hur brett inställningen tillämpas. Lägre nivåer ärver inställningar från högre nivåer. När du tillämpar en inställning på prenumerationen tillämpas den inställningen för alla resursgrupper och resurser i prenumerationen. När du tillämpar en inställning på resursgruppen tillämpas den inställningen på den resursgruppen och alla dess resurser. En annan resursgrupp har dock inte den inställningen.

Vanligtvis är det bra att tillämpa kritiska inställningar på högre nivåer och projektspecifika krav på lägre nivåer. Till exempel vill du kanske se till att alla resurser för din organisation har distribuerats till vissa regioner. För att åstadkomma det här kravet tillämpar du en princip på den prenumeration som anger tillåtna platser. När andra användare i organisationen lägger till nya resursgrupper och resurser framtvingas de tillåtna platserna automatiskt. 
