---
title: Samlings lista över grupp medlemskap – Azure Active Directory Portal | Microsoft Docs
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
ms.openlocfilehash: 94c2ac1d662851b5a0b44ec475becb5f5e0403c4
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146385"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Mass nedladdning av medlemmar i en grupp (för hands version) i Azure Active Directory

Med hjälp av Azure Active Directory (Azure AD)-portalen kan du hämta medlemmarna i en grupp i din organisation till en fil med kommaavgränsade värden (CSV).

> [!NOTE]
> Azure AD Mass åtgärder är en offentlig för hands version av Azure AD och är tillgängliga med en betald Azure AD-licensserver. Mer information om användnings villkoren för för hands versioner finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versioner.

## <a name="to-bulk-download-group-membership"></a>Till grupp medlemskap för grupp hämtning

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto i organisationen. Grupp ägare kan också massredigera medlemmar av grupper som de äger.
1. I Azure AD väljer du **grupper** > **alla grupper**.
1. Öppna den grupp vars medlemskap du vill hämta och välj sedan **medlemmar**.
1. På sidan **medlemmar** väljer du **Hämta medlemmar** för att ladda ned en CSV-fil som visar grupp medlemmarna.

   ![Kommandot ladda ned medlemmar finns på profil sidan för gruppen](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Kontrol lera hämtnings status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![På sidan Mass åtgärds resultat visas Mass status för begäran](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Begränsningar för Mass nedladdnings tjänst

Varje Mass aktivitet för att ladda ned en lista över grupp medlemmar kan köras i upp till en timme. På så sätt kan du ladda ned en lista med minst 500 000 medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Grupp medlemmar för Mass import](groups-bulk-import-members.md)
- [Mass borttagning av grupp medlemmar](groups-bulk-download-members.md)
