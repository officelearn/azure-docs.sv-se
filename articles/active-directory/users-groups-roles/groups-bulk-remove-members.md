---
title: Mass borttagning av grupp medlemmar genom att ladda upp en CSV-fil – Azure Active Directory | Microsoft Docs
description: Lägg till användare i grupp i Azure administrations centret.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08708c23f9c8f4c4a8fc9f2f0aa5cd20d8333a42
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146341"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Mass borttagning av grupp medlemmar (förhands granskning) i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du ta bort ett stort antal medlemmar från en grupp med hjälp av en fil med kommaavgränsade värden (CSV) för Mass borttagning av grupp medlemmar.

> [!NOTE]
> Azure AD Mass åtgärder är en offentlig för hands version av Azure AD och är tillgängliga med en betald Azure AD-licensserver. Mer information om användnings villkoren för för hands versioner finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versioner.

## <a name="to-bulk-remove-group-members"></a>För Mass borttagning av grupp medlemmar

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto i organisationen. Grupp ägare kan också massredigera medlemmar av grupper som de äger.
1. I Azure AD väljer du **grupper** > **alla grupper**.
1. Öppna den grupp som du vill ta bort medlemmar från och välj sedan **medlemmar**.
1. På sidan **medlemmar** väljer du **ta bort medlemmar**.
1. På sidan **Mass borttagning av grupp medlemmar (förhands granskning)** väljer du **Hämta** för att hämta CSV-filmallen med obligatoriska grupp medlems egenskaper.

   ![Kommandot Ta bort medlemmar finns på profil sidan för gruppen](./media/groups-bulk-remove-members/remove-panel.png)

1. Öppna CSV-filen och Lägg till en rad för varje grupp medlem som du vill ta bort från gruppen (obligatoriska värden är medlems objekt-ID eller användarens huvud namn). Spara sedan filen.

   ![CSV-filen innehåller namn och ID: n för de medlemmar som ska tas bort](./media/groups-bulk-remove-members/csv-file.png)

1. På sidan **Mass borttagning av grupp medlemmar (förhands granskning)** , under **överför din CSV-fil**, bläddrar du till filen. När du väljer filen startar valideringen av. csv-filen.
1. När fil innehållet är verifierat visar sidan Mass import att **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som tar bort grupp medlemmarna från gruppen.
1. När borttagningen är klar visas ett meddelande om att Mass åtgärden lyckades.

## <a name="check-removal-status"></a>Kontrol lera borttagnings status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![På sidan Mass åtgärds resultat visas Mass status för begäran](./media/groups-bulk-remove-members/bulk-center.png)

Om du vill ha mer information om varje rad objekt i Mass åtgärden väljer du värdena under kolumnerna **# lyckades**, **# Failure**eller **Totalt antal förfrågningar** . Om fel inträffar visas orsaken till felet.

## <a name="bulk-removal-service-limits"></a>Begränsningar för Mass borttagnings tjänst

Varje Mass aktivitet för att ta bort en lista över grupp medlemmar från kan köras i upp till en timme. Detta gör att en lista med minst 40 000 medlemmar kan tas bort.

## <a name="next-steps"></a>Nästa steg

- [Grupp medlemmar för Mass import](groups-bulk-import-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
- [Hämta en lista över alla grupper](groups-bulk-download.md)
