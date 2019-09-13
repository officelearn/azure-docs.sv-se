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
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910881"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Mass borttagning av grupp medlemmar (förhands granskning) i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du ta bort ett stort antal medlemmar från en grupp med hjälp av en fil med kommaavgränsade värden (CSV) för Mass borttagning av grupp medlemmar.

> [!NOTE]
> Azure AD Mass åtgärder är en offentlig för hands version av Azure AD och är tillgängliga med en betald Azure AD-licensserver. Mer information om användnings villkoren för för hands versioner finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versioner.

## <a name="bulk-removal-service-limits"></a>Begränsningar för Mass borttagnings tjänst

Varje Mass aktivitet för att ta bort en lista över grupp medlemmar från kan köras i upp till en timme. Detta gör att en lista med minst 40 000 medlemmar kan tas bort.

## <a name="to-bulk-remove-group-members"></a>För Mass borttagning av grupp medlemmar

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto i organisationen. Grupp ägare kan också massredigera medlemmar av grupper som de äger.
1. I Azure AD väljer du **grupper** > **alla grupper**.
1. Öppna den grupp som du vill ta bort medlemmar från och välj sedan **medlemmar**.
1. På sidan **medlemmar** väljer du **ta bort medlemmar** för att ladda ned, uppdatera och ladda upp en CSV-fil som visar de medlemmar som du vill ta bort från gruppen.

   ![Kommandot Ta bort medlemmar finns på profil sidan för gruppen](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>Kontrol lera borttagnings status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![På sidan Mass åtgärds resultat visas Mass status för begäran](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>Nästa steg

- [Grupp medlemmar för Mass import](groups-bulk-import-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
