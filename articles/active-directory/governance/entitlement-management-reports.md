---
title: Visa loggar och rapporter i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Lär dig hur du visar rapporten användaren tilldelningar och granskningsloggar i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541547"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Visa rapporter och loggar i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Visa en användare har åtkomst till resurser

1. Klicka på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **tilldelningar rapport**.

1. Klicka på **Välj användare** att öppna fönstret Välj användare.

1. Hitta användaren i listan som du vill visa de resurser som de har åtkomst till.

1. Klicka på användaren och klicka sedan på **Välj**.

    En lista över resurser som användaren har åtkomst till visas. Den innehåller den åtkomst-paketet, principer och datum.

    ![Rapport om användartilldelningar](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Bestämma tillståndet hos en användares begäran

Du kan använda Azure AD-spårningsloggen för att få mer information om hur en användare begärde och fick åtkomst till en åtkomst-paketet. Framför allt, du kan använda loggposter i den `EntitlementManagement` och `UserManagement` kategorier för att få ytterligare information om bearbetningssteg för varje begäran.  

1. Klicka på **Azure Active Directory** och klicka sedan på **granskningsloggar**.

1. Högst upp, ändra den **kategori** antingen `EntitlementManagement` eller `UserManagement`, beroende på granskningsposten du letar efter.  

1. Klicka på **Verkställ**.

1. För att hämta loggarna, klickar du på **hämta**.

När Azure AD får en ny begäran, skriver en granskningspost där den **kategori** är `EntitlementManagement` och **aktivitet** är vanligtvis `User requests access package assignment`.  När det gäller en direkttilldelning som skapats i Azure-portalen i **aktivitet** fält i granskningsposten är `Administrator directly assigns user to access package`, och användaren som utför tilldelningen har identifierats av den **ActorUserPrincipalName**.

Azure AD skrivs ytterligare granskningsposter medan begäran pågår, inklusive:

| Category | Aktivitet | Status för begäran |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Begäran kräver inte godkännande |
| `UserManagement` | `Create request approval` | Begäran kräver godkännande |
| `UserManagement` | `Add approver to request approval` | Begäran kräver godkännande |
| `EntitlementManagement` | `Approve access package assignment request` | Begäran godkänd |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Begäran godkändes eller kräver inte godkännande |

När en användare har tilldelats åtkomst, Azure AD skrivs en granskningspost för den `EntitlementManagement` kategori med **aktivitet** `Fulfill access package assignment`.  Den användare som fick åtkomst har identifierats av **ActorUserPrincipalName** fält.

Om åtkomst inte har tilldelats Azure AD och skriver sedan en granskningspost för den `EntitlementManagement` kategori med **aktivitet** antingen `Deny access package assignment request`om förfrågan nekades av en godkännare eller `Access package assignment request timed out (no approver action taken)`om begäran orsakade timeout innan en godkännare kan godkänna.

När användarens åtkomst pakettilldelningar upphör att gälla är avbröts av användaren eller tas bort av en administratör, och Azure AD skriver en granskningspost för sedan den `EntitlementManagement` kategori med **aktivitet** av `Remove access package assignment`.

## <a name="next-steps"></a>Nästa steg

- [Felsök hantering av Azure AD-behörighet](entitlement-management-troubleshoot.md)
- [Vanliga scenarier](entitlement-management-scenarios.md)
