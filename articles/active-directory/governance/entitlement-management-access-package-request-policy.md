---
title: Ändra inställningar för begäran och godkännande för ett åtkomstpaket i Azure AD-berättigandehantering - Azure Active Directory
description: Lär dig hur du ändrar inställningar för begäran och godkännande för ett åtkomstpaket i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 493ba6396a7ceb11b917fbda5dd6c37c070f2fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261936"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Ändra inställningar för begäran och godkännande för ett åtkomstpaket i Azure AD-berättigandehantering

Som en åtkomstpakethanterare kan du ändra de användare som kan begära ett åtkomstpaket när som helst genom att redigera principen eller lägga till en ny princip. Du kan också ändra godkännandeinställningarna.

I den här artikeln beskrivs hur du ändrar inställningarna för begäran och godkännande för ett befintligt åtkomstpaket.

## <a name="choose-between-one-or-multiple-polices"></a>Välj mellan en eller flera poliser

Det sätt du anger vem som kan begära ett åtkomstpaket är med en princip. När du skapar ett åtkomstpaket anger du den förfrågan och godkännandeinställning som skapar en princip. De flesta åtkomstpaket har en enda princip, men ett enda åtkomstpaket kan ha flera principer. Du skulle skapa flera principer för ett åtkomstpaket om du vill tillåta att olika uppsättningar användare beviljas tilldelningar med olika inställningar för begäran och godkännande. En enskild princip kan till exempel inte användas för att tilldela interna och externa användare till samma åtkomstpaket. Du kan dock skapa två principer i samma åtkomstpaket – en för interna användare och en för externa användare. Om det finns flera principer som gäller för en användare uppmanas de vid tidpunkten för begäran att välja den princip som de vill ska tilldelas. Följande diagram visar ett åtkomstpaket med två principer.

![Flera principer i ett åtkomstpaket](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scenario | Antal policyer |
| --- | --- |
| Jag vill att alla användare i min katalog ska ha samma inställningar för begäran och godkännande för ett åtkomstpaket | En |
| Jag vill att alla användare i vissa anslutna organisationer ska kunna begära ett åtkomstpaket | En |
| Jag vill tillåta användare i min katalog och även användare utanför min katalog att begära ett åtkomstpaket | Flera |
| Jag vill ange olika godkännandeinställningar för vissa användare | Flera |
| Jag vill att vissa användare får åtkomst till pakettilldelningar ska upphöra att gälla medan andra användare kan utöka sin åtkomst | Flera |

Information om prioritetslogiken som används när flera principer gäller finns i [Flera principer](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Öppna en befintlig princip för inställningar för begäran och godkännande

Om du vill ändra inställningarna för begäran och godkännande för ett åtkomstpaket måste du öppna motsvarande princip. Följ dessa steg för att öppna inställningarna för begäran och godkännande för ett åtkomstpaket.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Principer** och sedan på den princip som du vill redigera.

    Fönstret Policyinformation öppnas längst ned på sidan.

    ![Fönstret Åtkomstpaket - informationsfönstret Principinformation](./media/entitlement-management-shared/policy-details.png)

1. Klicka på **Redigera** om du vill redigera principen.

    ![Åtkomstpaket - Redigera princip](./media/entitlement-management-shared/policy-edit.png)

1. Klicka på fliken **Begäran om** du vill öppna inställningarna för begäran och godkännande.

1. Utför stegen i något av följande avsnitt för begäran.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Lägga till en ny princip för inställningar för begäran och godkännande

Om du har en uppsättning användare som ska ha olika inställningar för begäran och godkännande måste du förmodligen skapa en ny princip. Följ dessa steg för att börja lägga till en ny princip i ett befintligt åtkomstpaket.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Principer** och lägg sedan **till princip**.

1. Skriv ett namn och en beskrivning för principen.

    ![Skapa princip med namn och beskrivning](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Klicka på **Nästa** för att öppna fliken **Begäranden.**

1. Utför stegen i något av följande avsnitt för begäran.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Om du redigerar en princip klickar du på **Uppdatera**. Om du lägger till en ny princip klickar du på **Skapa**.

## <a name="next-steps"></a>Nästa steg

- [Ändra livscykelinställningar för ett åtkomstpaket](entitlement-management-access-package-lifecycle-policy.md)
- [Visa begäranden för ett åtkomstpaket](entitlement-management-access-package-requests.md)