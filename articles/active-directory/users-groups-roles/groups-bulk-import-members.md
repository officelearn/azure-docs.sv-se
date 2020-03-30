---
title: Massimportöverföring för att lägga till medlemmar i en grupp – Azure Active Directory | Microsoft-dokument
description: Lägg till gruppmedlemmar i gruppmedlemmar i gruppmedlem i Azure Active Directory admincenter.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517120"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Gruppmedlemmar för massimport (förhandsversion) i Azure Active Directory

Med Azure Active Directory(Azure AD) portal kan du lägga till ett stort antal medlemmar i en grupp med hjälp av en kommaavgränsad värden (CSV) fil till massimportgruppmedlemmar.

## <a name="to-bulk-import-group-members"></a>Så här massimportgruppmedlemmar

1. Logga in [på Azure-portalen](https://portal.azure.com) med ett användarkonto för användaradministratörer i organisationen. Gruppägare kan också massimporta medlemmar i grupper som de äger.
1. I Azure AD väljer du **Grupper alla** > **grupper**.
1. Öppna gruppen som du lägger till medlemmar i och välj sedan **Medlemmar**.
1. På sidan **Medlemmar** väljer du **Importera medlemmar**.
1. På sidan **Massimportgruppmedlemmar (Förhandsgranska)** väljer du **Hämta** för att hämta CSV-filmallen med obligatoriska gruppmedlemsegenskaper.

    ![Kommandot Importera medlemmar finns på profilsidan för gruppen](./media/groups-bulk-import-members/import-panel.png)

1. Öppna CSV-filen och lägg till en rad för varje gruppmedlem som du vill importera till gruppen (obligatoriska värden är antingen **Medlemsobjekt-ID** eller **Användarnamn).** Spara sedan filen.

   ![CSV-filen innehåller namn och ID:n för medlemmarna att importera](./media/groups-bulk-import-members/csv-file.png)

1. På sidan **Massimportgruppmedlemmar (Förhandsgranska)** under **Ladda upp csv-filen**bläddrar du till filen. När du väljer filen startar valideringen av CSV-filen.
1. När filinnehållet valideras visas Fil **som har överförts**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När filen godkänns väljer du **Skicka** för att starta azure-massåtgärden som importerar gruppmedlemmarna till gruppen.
1. När importen är klar visas ett meddelande om att massåtgärden lyckades.

## <a name="check-import-status"></a>Kontrollera importstatus

Du kan se status för alla väntande massbegäranden på sidan **Massåtgärdsresultat (förhandsversion).**

   ![På sidan Resultat för massåtgärder visas status för massbegäran](./media/groups-bulk-import-members/bulk-center.png)

Om du vill ha information om varje radartikel i massåtgärden väljer du värdena under kolumnerna **# Lyckades,** **# Fel**eller Totalt **antal begäranden.** Om fel har inträffat visas orsakerna till felet.

## <a name="bulk-import-service-limits"></a>Begränsningar för massimporttjänster

Varje massaktivitet för att importera en lista över gruppmedlemmar kan köras i upp till en timme. Detta möjliggör import av en lista med minst 40 000 medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Massborttagning av gruppmedlemmar](groups-bulk-remove-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
- [Ladda ner en lista över alla grupper](groups-bulk-download.md)
