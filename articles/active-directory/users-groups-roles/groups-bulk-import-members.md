---
title: Mass import – uppladdning för att lägga till medlemmar i en grupp-Azure Active Directory | Microsoft Docs
description: Lägg till grupp medlemmar i grupp i Azure Active Directory administrations centret.
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
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517120"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Grupp medlemmar för Mass import (för hands version) i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du lägga till ett stort antal medlemmar i en grupp genom att använda en fil med kommaavgränsade värden (CSV) för grupp medlemmar i Mass import.

## <a name="to-bulk-import-group-members"></a>Till Mass import av grupp medlemmar

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto i organisationen. Grupp ägare kan också Mass import medlemmar av grupper som de äger.
1. I Azure AD väljer du **grupper**  > **alla grupper**.
1. Öppna den grupp som du vill lägga till medlemmar i och välj sedan **medlemmar**.
1. På sidan **medlemmar** väljer du **Importera medlemmar**.
1. På sidan **grupp medlemmar för Mass import (förhands granskning)** väljer du **Hämta** för att hämta CSV-filmallen med obligatoriska grupp medlems egenskaper.

    ![Kommandot Importera medlemmar finns på profil sidan för gruppen](./media/groups-bulk-import-members/import-panel.png)

1. Öppna CSV-filen och Lägg till en rad för varje grupp medlem som du vill importera till gruppen (obligatoriska värden är antingen **medlems objekt-ID** eller **användarens huvud namn**). Spara sedan filen.

   ![CSV-filen innehåller namn och ID: n för de medlemmar som ska importeras](./media/groups-bulk-import-members/csv-file.png)

1. På sidan **grupp medlemmar för Mass import (förhands granskning)** , under **överför din CSV-fil**, bläddrar du till filen. När du väljer filen startar valideringen av CSV-filen.
1. När fil innehållet är verifierat visar sidan Mass import att **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som importerar grupp medlemmarna till gruppen.
1. När importen är klar visas ett meddelande om att Mass åtgärden lyckades.

## <a name="check-import-status"></a>Kontrol lera import status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![På sidan Mass åtgärds resultat visas Mass status för begäran](./media/groups-bulk-import-members/bulk-center.png)

Om du vill ha mer information om varje rad objekt i Mass åtgärden väljer du värdena under kolumnerna **# lyckades**, **# Failure**eller **Totalt antal förfrågningar** . Om fel inträffar visas orsaken till felet.

## <a name="bulk-import-service-limits"></a>Begränsningar för Mass import av tjänster

Varje Mass aktivitet för att importera en lista över grupp medlemmar kan köras i upp till en timme. På så sätt kan du importera en lista med minst 40 000 medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Mass borttagning av grupp medlemmar](groups-bulk-remove-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
- [Hämta en lista över alla grupper](groups-bulk-download.md)
