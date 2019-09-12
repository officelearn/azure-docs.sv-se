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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911023"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Grupp medlemmar för Mass import (för hands version) i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du lägga till ett stort antal medlemmar i en grupp genom att använda en fil med kommaavgränsade värden (CSV) för grupp medlemmar i Mass import.

> [!NOTE]
> Azure AD Mass åtgärder är en offentlig för hands version av Azure AD och är tillgängliga med en betald Azure AD-licensserver. Mer information om användnings villkoren för för hands versioner finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versioner.

## <a name="bulk-import-service-limits"></a>Begränsningar för Mass import av tjänster

Varje Mass aktivitet för att importera en lista över grupp medlemmar kan köras i upp till en timme. På så sätt kan du importera en lista med minst 40 000 medlemmar.

## <a name="to-bulk-import-group-members"></a>Till Mass import av grupp medlemmar

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto i organisationen. Grupp ägare kan också Mass import medlemmar av grupper som de äger.
1. I Azure AD väljer du **grupper** > **alla grupper**.
1. Öppna den grupp som du vill lägga till medlemmar i och välj sedan **medlemmar**.
1. På sidan **medlemmar** väljer du **Importera medlemmar** för att ladda ned, uppdatera och ladda upp en CSV-fil som visar de medlemmar som du vill importera till gruppen.

   ![Kommandot Importera medlemmar finns på profil sidan för gruppen](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>Kontrol lera import status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![På sidan Mass åtgärds resultat visas Mass status för begäran](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>Nästa steg

- [Mass borttagning av grupp medlemmar](groups-bulk-remove-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
