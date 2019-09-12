---
title: Mass borttagnings användare (för hands version) i Azure Active Directory Portal | Microsoft Docs
description: Ta bort användare i grupp i Azure administrations centret i Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c2204c572ca1f74f8060d0b6176df69359fe69a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901716"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Mass borttagnings användare (för hands version) i Azure Active Directory

Azure Active Directory (Azure AD) stöder Mass åtgärder för att skapa och ta bort, Gruppinbjudan för gäster och har stöd för nedladdning av listor över användare, grupper och grupp medlemmar.

## <a name="to-bulk-delete-users"></a>För Mass borttagning av användare

1. Logga in på din Azure AD-organisation med ett konto som är en användar administratör i organisationen.
1. I Azure AD väljer **du användare** > **Mass borttagning**.
1. På sidan **Mass borttagning av användare** väljer du **Hämta** för att ta emot en giltig CSV-fil med användar egenskaper och lägger sedan till de användare som du vill ta bort.
1. När du är klar med redigeringen av CSV-filen eller om du har en av dina egna redo att ladda upp, väljer du filen under **överför din CSV-fil** som ska verifieras.

   ![Välj en lokal CSV-fil där du visar de användare som du vill ta bort](./media/users-bulk-delete/bulk-delete.png)

1. När fil innehållet verifieras måste du åtgärda eventuella fel innan jobbet skickas.
1. När du har verifierat filen väljer du **Skicka** för att starta Azure Batch-jobbet som tar bort användarna. Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för Mass åtgärder. Filen innehåller orsaken för varje fel.

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![Kontrol lera uppladdnings status på sidan Mass åtgärds resultat](./media/users-bulk-delete/bulk-center.png)

Sedan kan du kontrol lera att de användare som du har tagit bort finns i Azure AD-organisationen antingen i Azure Portal eller med hjälp av PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verifiera borttagna användare i Azure Portal

1. Logga in på Azure Portal med ett konto som är en användar administratör i organisationen.
1. I navigerings fönstret väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa**väljer du **alla användare** och kontrollerar att de användare som du har tagit bort inte längre visas.

### <a name="verify-deleted-users-with-powershell"></a>Verifiera borttagna användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Kontrol lera att de användare som du har tagit bort inte längre visas.

## <a name="next-steps"></a>Nästa steg

- [Mass tillägg av användare](users-bulk-add.md)
- [Hämta lista över användare](users-bulk-download.md)
- [Mass återställnings användare](users-bulk-restore.md)
