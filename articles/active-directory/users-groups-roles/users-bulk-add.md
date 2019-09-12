---
title: Mass import för att lägga till användare (förhands granskning) i Azure Active Directory Portal | Microsoft Docs
description: Lägg till användare i grupp i Azure AD administrations Center i Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22052117448cdb71bbc16e1df7899e6d3eb764e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901495"
---
# <a name="bulk-import-users-preview-in-azure-active-directory"></a>Mass import av användare (för hands version) i Azure Active Directory

Azure Active Directory (Azure AD) stöder Mass åtgärder för att skapa och ta bort, Gruppinbjudan för gäster och har stöd för nedladdning av listor över användare, grupper och grupp medlemmar.

## <a name="bulk-import-service-limits"></a>Begränsningar för Mass import av tjänster

Varje Mass aktivitet för att skapa användare kan köras i upp till en timme. Detta möjliggör Mass skapande av minst 50 000 användare.

## <a name="required-permissions"></a>Nödvändiga behörigheter

Du måste vara inloggad som global administratör eller användar administratör för att kunna skapa användare i administrations portalen.

## <a name="to-bulk-import-users"></a>För Mass import av användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I Azure AD väljer **du användare** > **Mass skapande**.
1. På sidan **skapa användare av grupp** väljer du **Hämta** för att ta emot en giltig CSV-fil (kommaavgränsade värden) för användar egenskaper och lägger sedan till nya användare.
1. När du är klar med redigeringen av CSV-filen eller om du har en av dina egna redo att ladda upp, väljer du filen under **överför din CSV-fil** som ska verifieras.

   ![Välj en lokal CSV-fil där du visar de användare som du vill lägga till](./media/users-bulk-add/upload-button.png)

1. När fil innehållet verifieras måste du åtgärda eventuella fel innan du kan starta överförings jobbet.
1. När din fil klarar valideringen väljer du **Skicka** för att starta Azure Batch-jobbet som lägger till den nya användar informationen. Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för Mass åtgärder. Filen innehåller orsaken för varje fel.

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![Kontrol lera uppladdnings status på sidan Mass åtgärds resultat](./media/users-bulk-add/bulk-center.png)

Sedan kan du kontrol lera att de användare du skapade finns i Azure AD-organisationen antingen i Azure Portal eller med hjälp av PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifiera användare i Azure Portal

1. [Logga in på administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I navigerings fönstret väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa**, väljer du **alla användare** och kontrollerar att de användare som du har skapat visas.

### <a name="verify-users-with-powershell"></a>Verifiera användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Du bör se att de användare som du har skapat visas i listan.

## <a name="next-steps"></a>Nästa steg

- [Mass borttagnings användare](users-bulk-delete.md)
- [Hämta lista över användare](users-bulk-download.md)
- [Mass återställnings användare](users-bulk-restore.md)
