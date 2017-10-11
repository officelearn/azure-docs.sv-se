---
title: "Azure portal för resursprinciper | Microsoft Docs"
description: "Beskriver hur du använder Azure-portalen för att skapa och hantera principer för hanteraren för filserverresurser. Principer kan tillämpas på grupperna prenumerationen eller resursen."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Använd Azure-portalen för att tilldela och hantera principer för företagsresurser
Azure-portalen kan du tilldela resursprinciper resursgrupper och prenumerationer. Användargränssnittet gör det enkelt att välja den princip som du vill tilldela och ange parametervärden att anpassa inställningarna för principen. 

Om du vill tilldela en princip via portalen finnas principdefinitionen redan i din prenumeration. Din prenumeration har flera definitioner av inbyggd princip som du vill tilldela till resursgrupper eller prenumerationer. Du kan se dessa inbyggda principer och eventuella anpassade principer som du har definierat när du använder portalen för att tilldela principer. En introduktion till principer och hur du definierar en anpassad princip finns [resurs uppgifter](resource-manager-policy.md).

Principer ärvs av alla underordnade resurser. Om en princip används för en resursgrupp, är det så gäller för alla resurser i resursgruppen. I den här artikeln termen **omfång** refererar till den resursgrupp eller prenumeration som är tilldelade principen. 

Principer utvärderas när du skapar och uppdaterar resurser (PLACERA och korrigering operations).

## <a name="assign-a-policy"></a>Tilldela en princip

1. Om du vill tilldela en princip till en resursgrupp eller prenumeration väljer du den resursgrupp eller prenumeration. I inställningar, väljer **principer**.

   ![Välj principer](./media/resource-manager-policy-portal/select-policies.png)

2. Om du vill skapa en principtilldelning för detta scope, Välj **Lägg till tilldelning**.

   ![Lägg till tilldelning](./media/resource-manager-policy-portal/add-assignment.png)

3. Välj den princip som du vill tilldela. Även om du inte har lagt till alla principdefinitioner till din prenumeration kan du se de inbyggda principer som är tillgängliga för tilldelning. De här inbyggda täcker många vanliga scenarier.

   ![Välj definition](./media/resource-manager-policy-portal/select-definition.png)

4. När du har valt en princip, finns en beskrivning av principen och eventuella parametrar för grupprincipobjekt. Till exempel följande bild visar den **tillåtna platser** som krävs för den princip som begränsar tillgängliga platser.

   ![Visa parametrar](./media/resource-manager-policy-portal/show-parameters.png)

5. Markera värdena du anger för principparametrar (t.ex de platser som kan användas för distributionen) via användargränssnittet.

   ![Välj parametervärden](./media/resource-manager-policy-portal/select-parameters.png)

6. Ange värden för de andra parametrarna. Omfånget tilldelas automatiskt baserat på bladet som du valde när du startar principtilldelningen. Välj **OK** när du är klar.

   ![Definiera parametrar](./media/resource-manager-policy-portal/define-parameters.png)

  Principen har tilldelats det specificerade omfånget.

## <a name="view-policy-assignments"></a>Visa principtilldelningar

Efter att en princip, ser du den i listan med principer för detta omfång. Den **information** fliken visas en sammanfattning av tilldelning av principer.

![Visa information](./media/resource-manager-policy-portal/show-details.png)

Den **tilldelningsregel** visar JSON för principdefinitionen.

![Visa tilldelningsregel](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>Ändra en befintlig principtilldelning

Om du vill ändra en princip, Välj **Redigera tilldelning** eller **ta bort**

![Redigera eller ta bort tilldelning](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>Tilldela anpassade principer

Om du har definierat anpassade principer i din prenumeration, är dessa principer tillgängliga för tilldelning via portalen. Dessa principer inleds med **[Custom]**

![anpassade principer](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>Nästa steg
* Läs om JSON-syntax för att definiera principer i [resurs uppgifter](resource-manager-policy.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).
* Princip-schemat har publicerats på [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

