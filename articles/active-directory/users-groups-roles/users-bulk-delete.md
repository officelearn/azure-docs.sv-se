---
title: Massborttagningsanvändare (förhandsversion) i Azure Active Directory-portalen | Microsoft-dokument
description: Ta bort användare i grupp i Azure-administrationscentret i Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174392"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Massborttagningsanvändare (förhandsversion) i Azure Active Directory

Med Azure Active Directory (Azure AD) portal kan du ta bort ett stort antal medlemmar till en grupp med hjälp av en kommaavgränsad värden (CSV) fil för att massborttagning användare.

## <a name="to-bulk-delete-users"></a>Så här massborttagningar av användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är användaradministratör i organisationen.
1. I Azure AD väljer du **Användare** > **Massborttagning**.
1. På **sidan Massborttagningsanvändar** väljer du **Hämta** för att ta emot en giltig CSV-fil med användaregenskaper.

   ![Välj en lokal CSV-fil där du listar de användare som du vill ta bort](./media/users-bulk-delete/bulk-delete.png)

1. Öppna CSV-filen och lägg till en rad för varje användare som du vill ta bort. Det enda obligatoriska värdet är **Användarens huvudnamn**. Spara sedan filen.

   ![CSV-filen innehåller namn och ID:n för de användare som ska tas bort](./media/users-bulk-delete/delete-csv-file.png)

1. Bläddra till filen under **Ladda upp csv-filen**på sidan **Massborttagning (Förhandsgranska)** . När du markerar filen och klickar på Skicka startar valideringen av CSV-filen.
1. När filinnehållet har **validerats visas Filen uppladdad**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När filen godkänns väljer du **Skicka** för att starta azure-massåtgärden som tar bort användarna.
1. När borttagningen är klar visas ett meddelande om att massåtgärden lyckades.

Om det finns fel kan du hämta och visa resultatfilen på sidan **Massåtgärdsresultat.** Filen innehåller orsaken till varje fel.

## <a name="check-status"></a>Kontrollera status

Du kan se status för alla väntande massbegäranden på sidan **Massåtgärdsresultat (förhandsversion).**

   ![Kontrollera uppladdningsstatus på sidan Resultat för massåtgärder](./media/users-bulk-delete/bulk-center.png)

Därefter kan du kontrollera att de användare som du har tagit bort finns i Azure AD-organisationen antingen i Azure-portalen eller med PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verifiera borttagna användare i Azure-portalen

1. Logga in på Azure-portalen med ett konto som är användaradministratör i organisationen.
1. Välj Azure Active **Directory**i navigeringsfönstret .
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa**väljer du **Endast Alla användare** och kontrollerar att de användare som du har tagit bort inte längre visas.

### <a name="verify-deleted-users-with-powershell"></a>Verifiera borttagna användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Kontrollera att de användare som du har tagit bort inte längre visas.

## <a name="next-steps"></a>Nästa steg

- [Masstillägg av användare](users-bulk-add.md)
- [Ladda ner lista över användare](users-bulk-download.md)
- [Användare av massåterställning](users-bulk-restore.md)
