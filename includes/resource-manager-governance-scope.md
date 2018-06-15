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
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
ms.locfileid: "29529963"
---
Innan du skapar objekt nu ska vi se begreppet omfång. Azure erbjuder fyra olika management: hanteringsgrupper, prenumerationen, resursgruppen och resursen. [Hanteringsgrupper](../articles/billing/billing-enterprise-mgmt-group-overview.md) finns i en förhandsversionen. Följande bild visar ett exempel på dessa lager.

![Omfång](./media/resource-manager-governance-scope/scope-levels.png)

Du kan tillämpa inställningar på någon av dessa nivåer för scope. Du väljer avgör hur ofta den tillämpas. Lägre nivåer ärva inställningar från högre nivåer. När du använder en inställning för prenumerationen används som inställning för alla resursgrupper och resurser i din prenumeration. När du använder en inställning på tillämpas den resursgrupp som inställningen är resursgruppen och alla dess resurser. En annan resursgrupp har dock inte den här inställningen.

Vanligtvis är det praktiskt att använda kritiska inställningar på högre nivåer och projekt-specifika krav på lägre nivåer. Du kanske vill kontrollera att alla resurser för din organisation har distribuerats till vissa regioner. För att åstadkomma detta krav gäller en princip för den prenumeration som anger de tillåtna platserna. När andra användare i organisationen lägger till nya resursgrupper och resurser, tillämpas automatiskt de tillåtna platserna. 
