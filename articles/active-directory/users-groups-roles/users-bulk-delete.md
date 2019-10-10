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
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174392"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Mass borttagnings användare (för hands version) i Azure Active Directory

Med hjälp av Azure Active Directory (Azure AD)-portalen kan du ta bort ett stort antal medlemmar till en grupp med hjälp av en fil med kommaavgränsade värden (CSV) för Mass borttagning av användare.

## <a name="to-bulk-delete-users"></a>För Mass borttagning av användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I Azure AD väljer **du användare** > **Mass borttagning**.
1. På sidan **Mass borttagning av användare** väljer du **Hämta** för att ta emot en giltig CSV-fil med användar egenskaper.

   ![Välj en lokal CSV-fil där du visar de användare som du vill ta bort](./media/users-bulk-delete/bulk-delete.png)

1. Öppna CSV-filen och Lägg till en rad för varje användare som du vill ta bort. Det enda obligatoriska värdet är **användarens huvud namn**. Spara sedan filen.

   ![CSV-filen innehåller namn och ID: n för de användare som ska tas bort](./media/users-bulk-delete/delete-csv-file.png)

1. På sidan **Mass borttagning av användare (förhands granskning)** , under **överför din CSV-fil**, bläddrar du till filen. När du väljer filen och klickar på Skicka, startar verifieringen av CSV-filen.
1. När fil innehållet verifieras visas **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som tar bort användarna.
1. När borttagnings åtgärden har slutförts visas ett meddelande om att Mass åtgärden har slutförts.

Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för **Mass åtgärder** . Filen innehåller orsaken för varje fel.

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
