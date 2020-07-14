---
title: Inställningar för ändring av begäran och godkännande för ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du ändrar inställningar för begäran och godkännande för ett Access-paket i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ef2faf2a1d1a131dc5f2a01d3fa46cc61a06fb6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078809"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Inställningar för ändring av begäran och godkännande för ett Access-paket i Azure AD-hantering av rättigheter

Som en Access Package Manager kan du när som helst ändra vilka användare som kan begära ett åtkomst paket genom att redigera principen eller lägga till en ny princip. Du kan också ändra inställningarna för godkännande.

I den här artikeln beskrivs hur du ändrar inställningarna för begäran och godkännande för ett befintligt Access-paket.

## <a name="choose-between-one-or-multiple-polices"></a>Välj mellan en eller flera principer

Hur du anger vem som kan begära ett åtkomst paket är med en princip. När du skapar ett Access-paket anger du inställningen för begäran och godkännande som skapar en princip. De flesta åtkomst paket kommer att ha en enda princip, men ett enda åtkomst paket kan ha flera principer. Du kan skapa flera principer för ett Access-paket om du vill att olika uppsättningar med användare ska kunna tilldelas tilldelningar med olika inställningar för begäran och godkännande. En enda princip kan till exempel inte användas för att tilldela interna och externa användare till samma Access-paket. Du kan dock skapa två principer i samma Access-paket – ett för interna användare och en för externa användare. Om det finns flera principer som gäller för en användare, uppmanas de vid tidpunkten för begäran att välja den princip som de vill tilldelas. Följande diagram visar ett Access-paket med två principer.

![Flera principer i ett Access-paket](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scenario | Antal principer |
| --- | --- |
| Jag vill att alla användare i min katalog ska ha samma inställningar för begäran och godkännande för ett Access-paket | En |
| Jag vill att alla användare i vissa anslutna organisationer ska kunna begära ett Access-paket | En |
| Jag vill tillåta användare i min katalog och även användare utanför katalogen för att begära ett Access-paket | Flera |
| Jag vill ange olika godkännande inställningar för vissa användare | Flera |
| Jag vill att vissa användare får åtkomst till paket tilldelningar så att andra användare kan utöka sin åtkomst | Flera |

Information om prioritets logiken som används när flera principer tillämpas finns i [flera principer](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Öppna en befintlig princip för inställningar för begäran och godkännande

Om du vill ändra inställningarna för begär ande och godkännande för ett Access-paket måste du öppna motsvarande princip. Följ de här stegen för att öppna inställningarna för begäran och godkännande för ett Access-paket.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **principer** och sedan på den princip som du vill redigera.

    Fönstret princip information visas längst ned på sidan.

    ![Åtkomst paket – princip informations fönstret](./media/entitlement-management-shared/policy-details.png)

1. Redigera principen genom att klicka på **Redigera** .

    ![Åtkomst paket – redigera princip](./media/entitlement-management-shared/policy-edit.png)

1. Klicka på fliken **förfrågningar** för att öppna inställningarna för begäran och godkännande.

1. Utför stegen i något av följande avsnitt om begäran.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Lägg till en ny princip för inställningar för begäran och godkännande

Om du har en uppsättning användare som ska ha olika inställningar för begäran och godkännande måste du förmodligen skapa en ny princip. Följ dessa steg om du vill börja lägga till en ny princip i ett befintligt Access-paket.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **principer** och **Lägg sedan till princip**.

1. Ange ett namn och en beskrivning för principen.

    ![Skapa en princip med namn och beskrivning](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Klicka på **Nästa** för att öppna fliken **förfrågningar** .

1. Utför stegen i något av följande avsnitt om begäran.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Om du redigerar en princip klickar du på **Uppdatera**. Om du lägger till en ny princip klickar du på **skapa**.

## <a name="next-steps"></a>Nästa steg

- [Ändra livs cykel inställningar för ett Access-paket](entitlement-management-access-package-lifecycle-policy.md)
- [Visa begär Anden för ett Access-paket](entitlement-management-access-package-requests.md)