---
title: Visa rapporter och loggar i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig hur du visar rapporten användar tilldelningar och gransknings loggar i Azure Active Directory hantering av rättigheter (för hands version).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b4751b421f5af1536af9a88d15e060ab59bdb
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489065"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Visa rapporter och loggar i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Visa resurser som en användare har åtkomst till

1. Klicka på **Azure Active Directory** och klicka sedan på **identitets styrning**.

1. På den vänstra menyn klickar du på **användar tilldelnings rapport**.

1. Klicka på **Välj användare** för att öppna fönstret Välj användare.

1. Hitta användaren i listan som du vill visa de resurser som de har åtkomst till.

1. Klicka på användaren och sedan på **Välj**.

    En lista över resurser som användaren har åtkomst till visas. Den innehåller åtkomst paketet, principen och datumen.

    ![Rapport om användartilldelningar](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Fastställa status för en användares begäran

Om du vill ha mer information om hur en användare begärde och fick åtkomst till ett Access-paket kan du använda Azure AD-gransknings loggen. I synnerhet kan du använda logg posterna i `EntitlementManagement` kategorierna och `UserManagement` för att få ytterligare information om bearbetnings stegen för varje begäran.  

1. Klicka på **Azure Active Directory** och klicka sedan på **gransknings loggar**.

1. Längst upp ändrar du **kategorin** till antingen `EntitlementManagement` eller `UserManagement`, beroende på vilken gransknings post du letar efter.  

1. Klicka på **Verkställ**.

1. Klicka på **Hämta**om du vill hämta loggarna.

När Azure AD tar emot en ny begäran skriver den en gransknings post i vilken **kategorin** `EntitlementManagement` är och **aktiviteten** är vanligt vis `User requests access package assignment`.  Om det finns en direkt tilldelning som skapats i Azure Portal, är `Administrator directly assigns user to access package` **aktivitets** fältet för gransknings posten och den användare som utför tilldelningen identifieras av **ActorUserPrincipalName**.

Azure AD skriver ytterligare gransknings poster medan begäran pågår, inklusive:

| Category | Aktivitet | Status för begäran |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Begäran kräver inte godkännande |
| `UserManagement` | `Create request approval` | Begäran kräver godkännande |
| `UserManagement` | `Add approver to request approval` | Begäran kräver godkännande |
| `EntitlementManagement` | `Approve access package assignment request` | Begäran godkänd |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Begäran godkänd eller kräver inte godkännande |

När en användare tilldelas åtkomst skriver Azure AD en gransknings post för `EntitlementManagement` kategorin med **aktivitet** `Fulfill access package assignment`.  Användaren som har tagit emot åtkomsten identifieras av **ActorUserPrincipalName** -fältet.

Om åtkomst inte har tilldelats skriver Azure `EntitlementManagement` AD en gransknings post för kategorin med **aktivitet** antingen `Deny access package assignment request`, om begäran nekades av en god kännare, eller `Access package assignment request timed out (no approver action taken)`om tids gränsen nåddes för begäran innan en god kännare kan godkänna.

När användarens åtkomst paket tilldelning går ut, annulleras av användaren eller tas bort av en administratör, skriver Azure AD en gransknings post för `EntitlementManagement` kategorin med **aktivitet** i. `Remove access package assignment`

## <a name="next-steps"></a>Nästa steg

- [Felsöka hantering av Azure AD-berättigande](entitlement-management-troubleshoot.md)
- [Vanliga scenarier](entitlement-management-scenarios.md)
