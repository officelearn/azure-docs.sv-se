---
title: Visa rapporter & loggar in i rättighetshantering – Azure AD
description: Lär dig hur du visar rapporten för användartilldelningar och granskningsloggar i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128425"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Visa rapporter och loggar i Azure AD-berättigandehantering

Azure AD-rättighetshanteringsrapporter och Azure AD-granskningsloggen innehåller ytterligare information om vilka resurser användare har åtkomst till. Som administratör kan du visa åtkomstpaket och resurstilldelningar för en användare och visa begärandeloggar i granskningssyfte eller för att fastställa status för en användares begäran. I den här artikeln beskrivs hur du använder rättighetshanteringsrapporter och Azure AD-granskningsloggar.

Titta på följande video om du vill lära dig hur du visar vilka resurser användarna har åtkomst till i rättighetshantering:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Visa åtkomstpaket för en användare

Med den här rapporten kan du lista alla åtkomstpaket som en användare kan begära och de åtkomstpaket som för närvarande har tilldelats användaren.

**Viktig roll:** Global administratör eller användaradministratör

1. Klicka på **Azure Active Directory** och sedan på **Identitetsstyrning**.

1. Klicka på **Rapporter**på den vänstra menyn .

1. Klicka på **Access-paket för en användare**.

1. Klicka på **Välj användare** om du vill öppna fönstret Välj användare.

1. Leta reda på användaren i listan och klicka sedan på **Välj**.

    På fliken **Kan begära** visas en lista över de åtkomstpaket som användaren kan begära. Den här listan bestäms av de [begärandeprinciper](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) som definierats för åtkomstpaketen. 

    ![Åtkomstpaket för en användare](./media/entitlement-management-reports/access-packages-report.png)

1. Om det finns fler än en resursroll eller principer för ett åtkomstpaket klickar du på resursroller eller principer för att se urvalsinformation.

1. Klicka på fliken **Tilldelad** om du vill visa en lista över de åtkomstpaket som för närvarande har tilldelats användaren. När ett åtkomstpaket tilldelas en användare innebär det att användaren har åtkomst till alla resursroller i åtkomstpaketet.

## <a name="view-resource-assignments-for-a-user"></a>Visa resurstilldelningar för en användare

Med den här rapporten kan du visa de resurser som för närvarande har tilldelats en användare i rättighetshantering. Observera att den här rapporten är för resurser som hanteras med rättighetshantering. Användaren kan ha åtkomst till andra resurser i katalogen utanför rättighetshanteringen.

**Viktig roll:** Global administratör eller användaradministratör

1. Klicka på **Azure Active Directory** och sedan på **Identitetsstyrning**.

1. Klicka på **Rapporter**på den vänstra menyn .

1. Klicka på **Resurstilldelningar för en användare**.

1. Klicka på **Välj användare** om du vill öppna fönstret Välj användare.

1. Leta reda på användaren i listan och klicka sedan på **Välj**.

    En lista över de resurser som för närvarande tilldelats användaren visas. Listan visar också det åtkomstpaket och den princip som de fick resursrollen från, tillsammans med start- och slutdatum för åtkomst.
    
    Om en användare har åtkomst till samma resurs i två eller flera paket kan du klicka på en pil för att se varje paket och princip.

    ![Resurstilldelningar för en användare](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Fastställa status för en användares begäran

Om du vill ha ytterligare information om hur en användare har begärt och tagit emot åtkomst till ett åtkomstpaket kan du använda Granskningsloggen för Azure AD. I synnerhet kan du använda loggposterna i `EntitlementManagement` kategorierna och `UserManagement` för att få ytterligare information om bearbetningsstegen för varje begäran.  

1. Klicka på **Azure Active Directory** och sedan på **Granskningsloggar**.

1. Högst upp ändrar **Category** du `EntitlementManagement` kategorin `UserManagement`till antingen eller , beroende på vilken granskningspost du letar efter.  

1. Klicka på **Använd**.

1. Om du vill hämta loggarna klickar du på **Hämta**.

När Azure AD tar emot en ny begäran, skriver **Category** den `EntitlementManagement` en granskningspost, där kategorin är och **aktiviteten** vanligtvis `User requests access package assignment`.  När det gäller en direkttilldelning som **Activity** skapats i Azure-portalen är `Administrator directly assigns user to access package`fältet Aktivitet i granskningsposten och användaren som utför tilldelningen identifieras av **ActorUserPrincipalName**.

Azure AD skriver ytterligare granskningsposter medan begäran pågår, inklusive:

| Kategori | Aktivitet | Status för begäran |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Begäran kräver inte godkännande |
| `UserManagement` | `Create request approval` | Begäran kräver godkännande |
| `UserManagement` | `Add approver to request approval` | Begäran kräver godkännande |
| `EntitlementManagement` | `Approve access package assignment request` | Begäran godkänd |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Begäran godkänd, eller kräver inte godkännande |

När en användare tilldelas åtkomst skriver Azure AD en `EntitlementManagement` granskningspost för kategorin med **Aktivitet** `Fulfill access package assignment`.  Användaren som tog emot åtkomsten identifieras av fältet **ActorUserPrincipalName.**

Om åtkomsten inte har tilldelats skriver Azure AD `EntitlementManagement` en granskningspost för kategorin med **Aktivitet** antingen `Deny access package assignment request` `Access package assignment request timed out (no approver action taken)`, om begäran nekades av en godkännare eller om begäran tog time out innan en godkännare kunde godkänna.

När användarens tilldelning av åtkomstpaket upphör att gälla, avbryts av användaren eller tas bort av `EntitlementManagement` en administratör, `Remove access package assignment`skriver Azure AD en granskningspost för kategorin med **Aktivitet** i .

## <a name="next-steps"></a>Nästa steg

- [Arkivera rapporter och loggar](entitlement-management-logs-and-reporting.md)
- [Felsöka hantering av Azure AD-berättigande](entitlement-management-troubleshoot.md)
- [Vanliga scenarier](entitlement-management-scenarios.md)
