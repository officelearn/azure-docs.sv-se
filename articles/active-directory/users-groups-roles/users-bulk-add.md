---
title: Massskapa användare i Azure Active Directory-portalen | Microsoft-dokument
description: Lägga till användare i grupp i Azure AD-administrationscentret i Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a8b9cb9701288d24534ab08940f6dbd4a698ad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532957"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Massskapa användare i Azure Active Directory

Azure Active Directory (Azure AD) stöder massanvändare skapa och ta bort åtgärder, massinbjudan för gäster och stöder nedladdning av listor över användare, grupper och gruppmedlemmar.

## <a name="required-permissions"></a>Nödvändiga behörigheter

För att kunna skapa användare i administrationsportalen måste du vara inloggad som global administratör eller användaradministratör.

## <a name="to-create-users-in-bulk"></a>Så här skapar du användare i grupp

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är användaradministratör i organisationen.
1. I Azure AD väljer du **Användare** > **Mass skapa**.
1. På **sidan Mass skapa användare** väljer du **Hämta** för att ta emot en giltig CSV-fil (kommaavgränsade värden) med användaregenskaper och lägger sedan till användare som du vill skapa.

   ![Välj en lokal CSV-fil där du listar de användare du vill lägga till](./media/users-bulk-add/upload-button.png)

1. Öppna CSV-filen och lägg till en rad för varje användare som du vill skapa. De enda obligatoriska värdena är **Namn,** **Användarnamn,** **Ursprungligt lösenord** och **Blockera inloggning (Ja/Nej).** Spara sedan filen.

   ![CSV-filen innehåller namn och ID:n för de användare som ska skapas](./media/users-bulk-add/add-csv-file.png)

1. På **sidan Mass skapa användare,** under Ladda upp csv-filen, bläddra till filen. När du markerar filen och klickar på **Skicka**startar valideringen av CSV-filen.
1. När filinnehållet har **validerats visas Filen uppladdad**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När filen godkänns väljer du **Skicka** för att starta azure-massåtgärden som importerar de nya användarna.
1. När importåtgärden är klar visas ett meddelande om statusen för massoperationsjobbet.

Om det finns fel kan du hämta och visa resultatfilen på sidan **Massåtgärdsresultat.** Filen innehåller orsaken till varje fel.

## <a name="check-status"></a>Kontrollera status

Du kan se status för alla väntande massbegäranden på sidan **Massåtgärdsresultat.**

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

Därefter kan du kontrollera att de användare som du har skapat finns i Azure AD-organisationen antingen i Azure-portalen eller med PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifiera användare i Azure-portalen

1. [Logga in på Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som är användaradministratör i organisationen.
1. Välj Azure Active **Directory**i navigeringsfönstret .
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa**väljer du **Alla användare** och kontrollerar att de användare du har skapat visas.

### <a name="verify-users-with-powershell"></a>Verifiera användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Du bör se att de användare som du har skapat visas.

## <a name="bulk-import-service-limits"></a>Begränsningar för massimporttjänster

Varje massaktivitet för att skapa användare kan köras i upp till en timme. Detta möjliggör massskapande av minst 50 000 användare.

## <a name="next-steps"></a>Nästa steg

- [Massborttagning användare](users-bulk-delete.md)
- [Ladda ner lista över användare](users-bulk-download.md)
- [Användare av massåterställning](users-bulk-restore.md)
