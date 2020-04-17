---
title: Massborttagning av gruppmedlemmar genom att ladda upp en CSV-fil - Azure Active Directory | Microsoft-dokument
description: Ta bort gruppmedlemmar i massåtgärder i Azure-administrationscentret.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e6d0752245e3864a8ad25efd5181d5cc1eec7ae
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533279"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Massborttagning av gruppmedlemmar i Azure Active Directory

Med Azure Active Directory(Azure AD) portal kan du ta bort ett stort antal medlemmar från en grupp med hjälp av en kommaavgränsad värden (CSV) fil för att massta bort gruppmedlemmar.

## <a name="to-bulk-remove-group-members"></a>Så här massborttagningar gruppmedlemmar

1. Logga in [på Azure-portalen](https://portal.azure.com) med ett användarkonto för användaradministratörer i organisationen. Gruppägare kan också mass ta bort medlemmar i grupper som de äger.
1. I Azure AD väljer du **Grupper alla** > **grupper**.
1. Öppna gruppen där du tar bort medlemmar och välj sedan **Medlemmar**.
1. På sidan **Medlemmar** väljer du **Ta bort medlemmar**.
1. På sidan **Massborttagning av gruppmedlemmar** väljer du **Hämta** för att hämta CSV-filmallen med obligatoriska gruppmedlemsegenskaper.

   ![Kommandot Ta bort medlemmar finns på profilsidan för gruppen](./media/groups-bulk-remove-members/remove-panel.png)

1. Öppna CSV-filen och lägg till en rad för varje gruppmedlem som du vill ta bort från gruppen (obligatoriska värden är medlemsobjekt-ID eller Användarens huvudnamn). Spara sedan filen.

   ![CSV-filen innehåller namn och ID:n för medlemmarna att ta bort](./media/groups-bulk-remove-members/csv-file.png)

1. På sidan **Mass ta bort gruppmedlemmar,** under **Ladda upp csv-filen,** bläddra till filen. När du väljer filen startar valideringen av CSV-filen.
1. När filinnehållet valideras visas Fil **som har överförts**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När filen godkänns väljer du **Skicka** för att starta azure-massåtgärden som tar bort gruppmedlemmarna från gruppen.
1. När borttagningen är klar visas ett meddelande om att massåtgärden lyckades.

## <a name="check-removal-status"></a>Kontrollera borttagningsstatus

Du kan se status för alla väntande massbegäranden på sidan **Massåtgärdsresultat.**

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

Om du vill ha information om varje radartikel i massåtgärden väljer du värdena under kolumnerna **# Lyckades,** **# Fel**eller Totalt **antal begäranden.** Om fel har inträffat visas orsakerna till felet.

## <a name="bulk-removal-service-limits"></a>Begränsningar för borttagning av bulk

Varje massaktivitet som ska tas bort en lista över gruppmedlemmar från kan köras i upp till en timme. Detta gör det möjligt att ta bort en lista med minst 40 000 medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Medlemmar i gruppen För massimportgrupp](groups-bulk-import-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
- [Ladda ner en lista över alla grupper](groups-bulk-download.md)
