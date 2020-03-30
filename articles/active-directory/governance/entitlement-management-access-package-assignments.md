---
title: Visa, lägga till och ta bort tilldelningar för ett åtkomstpaket i Azure AD-berättigandehantering - Azure Active Directory
description: Lär dig hur du visar, lägger till och tar bort tilldelningar för ett åtkomstpaket i Azure Active Directory-berättigandehantering.
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
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262027"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Visa, lägga till och ta bort tilldelningar för ett åtkomstpaket i Azure AD-berättigandehantering

I Azure AD-berättigandehantering kan du se vem som har tilldelats åtkomstpaket, deras princip och status. Om ett åtkomstpaket har en lämplig princip kan du också direkt tilldela användaren till ett åtkomstpaket. I den här artikeln beskrivs hur du visar, lägger till och tar bort tilldelningar för åtkomstpaket.

## <a name="view-who-has-an-assignment"></a>Visa vem som har en tilldelning

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Tilldelningar** om du vill visa en lista över aktiva tilldelningar.

    ![Lista över tilldelningar till ett åtkomstpaket](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Klicka på en viss tilldelning om du vill se ytterligare information.

1. Om du vill visa en lista över tilldelningar som inte har alla resursroller korrekt etablerade klickar du på filterstatusen och väljer **Leverera**.

    Du kan se ytterligare information om leveransfel genom att hitta användarens motsvarande begäran på sidan **Begäranden.**

1. Om du vill visa utgångna tilldelningar klickar du på filterstatus och väljer **Utgånget**.

1. Om du vill hämta en CSV-fil i den filtrerade listan klickar du på **Hämta**.

### <a name="viewing-assignments-programmatically"></a>Visa tilldelningar programmässigt

Du kan också hämta tilldelningar i ett åtkomstpaket med Hjälp av Microsoft Graph.  En användare i en lämplig roll med `EntitlementManagement.ReadWrite.All` ett program som har delegerad behörighet kan anropa API:et för att [lista accessPackageAssignments](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Tilldela en användare direkt

I vissa fall kanske du vill tilldela specifika användare direkt till ett åtkomstpaket så att användarna inte behöver gå igenom processen med att begära åtkomstpaketet. För att direkt tilldela användare måste åtkomstpaketet ha en princip som tillåter administratörsdirekta tilldelningar.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Tilldelningar**på den vänstra menyn.

1. Klicka på **Ny tilldelning** om du vill öppna Lägg till användare för att komma åt paketet.

    ![Tilldelningar - Lägg till användare för att komma åt paketet](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Klicka på Lägg till **användare** om du vill välja vilka användare du vill tilldela det här åtkomstpaketet till.

1. I listan **Välj princip** väljer du en princip som användarnas framtida begäranden och livscykel ska styras och spåras av. Om du vill att de markerade användarna ska ha olika principinställningar kan du klicka på **Skapa en ny princip** för att lägga till en ny princip.

1. Ange datum och tid som du vill att de valda användarnas tilldelning ska starta och avslutas. Om inget slutdatum anges används principens livscykelinställningar.

1. Du kan också ange en motivering för din direkta tilldelning för registrering.

1. Klicka på **Lägg till** om du vill tilldela de markerade användarna direkt till åtkomstpaketet.

    Efter en stund klickar du på **Uppdatera** om du vill se användarna i listan Tilldelningar.

### <a name="directly-assigning-users-programmatically"></a>Direkt tilldela användare programmässigt

Du kan också direkt tilldela en användare till ett åtkomstpaket med Hjälp av Microsoft Graph.  En användare i en lämplig roll med `EntitlementManagement.ReadWrite.All` ett program som har delegerad behörighet kan anropa API:et för att [skapa en accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Ta bort en tilldelning

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Tilldelningar**på den vänstra menyn.
 
1. Klicka på kryssrutan bredvid den användare vars tilldelning du vill ta bort från åtkomstpaketet. 

1. Klicka på knappen **Ta bort** högst upp i den vänstra rutan. 
 
    ![Tilldelningar - Ta bort användare från åtkomstpaketet](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Ett meddelande visas som informerar dig om att tilldelningen har tagits bort. 

## <a name="next-steps"></a>Nästa steg

- [Ändra begäran och inställningarna för ett åtkomstpaket](entitlement-management-access-package-request-policy.md)
- [Visa rapporter och loggar](entitlement-management-reports.md)
