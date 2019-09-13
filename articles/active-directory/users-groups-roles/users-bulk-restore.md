---
title: Mass återställning borttagna användare (förhands granskning) i Azure Active Directory Portal | Microsoft Docs
description: Återställa borttagna användare i grupp i Azure AD administrations Center i Azure Active Directory
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
ms.openlocfilehash: f53ade09c5e2e7db0499122526a1de482af9378f
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901625"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Mass återställning borttagna användare (förhands granskning) i Azure Active Directory

Azure Active Directory (Azure AD) stöder Mass åtgärder för att skapa och ta bort, Gruppinbjudan för gäster och har stöd för nedladdning av listor över användare, grupper och grupp medlemmar.

## <a name="to-bulk-restore-users"></a>För Mass återställning av användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I Azure AD väljer **du användare** > **borttagna**.
1. På sidan **borttagna användare** väljer du **Mass återställning** för att ladda upp en giltig CSV-fil med egenskaper för de användare som ska återställas.

   ![Välj kommandot Mass återställning på sidan borttagna användare](./media/users-bulk-restore/bulk-restore.png)

1. När du är klar med redigeringen av CSV-filen eller om du har en av dina egna redo att ladda upp, väljer du filen under **överför din CSV-fil** som ska verifieras.

   ![Välj en lokal CSV-fil där du visar de användare som du vill lägga till](./media/users-bulk-restore/upload-button.png)

1. När fil innehållet verifieras, korrigerar du fil informationen och skickar filen igen om det finns fel. När du skickar en giltig fil startas data överförings jobbet automatiskt.
1. När CSV-filen har godkänts för verifiering väljer du **Skicka** för att starta Azure Batch-jobbet som återställer användarna. Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för Mass åtgärder. Filen innehåller orsaken för varje fel.

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![Kontrol lera uppladdnings status på sidan Mass åtgärds resultat](./media/users-bulk-restore/bulk-center.png)

Sedan kan du kontrol lera att de användare som du har återställt finns i Azure AD-organisationen antingen i Azure Portal eller genom att använda PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Visa återställda användare i Azure Portal

1. [Logga in på administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I navigerings fönstret väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa**, väljer du **alla användare** och kontrollerar att de användare som du har återställt visas.

### <a name="view-users-with-powershell"></a>Visa användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Du bör se att de användare som du har återställt visas.

## <a name="next-steps"></a>Nästa steg

- [Mass import av användare](users-bulk-add.md)
- [Mass borttagnings användare](users-bulk-delete.md)
- [Hämta lista över användare](users-bulk-download.md)
