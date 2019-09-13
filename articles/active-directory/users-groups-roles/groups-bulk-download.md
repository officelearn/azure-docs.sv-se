---
title: Hämta en lista med grupper i Azure Active Directory Portal | Microsoft Docs
description: Hämta grupp egenskaper i grupp i Azure administrations centret i Azure Active Directory.
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
ms.openlocfilehash: b1d0f94e9cf9b91bc365586dafc147dc73b17876
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914761"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Hämta en lista över grupper (förhands granskning) i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du hämta en lista över alla grupper i organisationen till en fil med kommaavgränsade värden (CSV).

> [!NOTE]
> Azure AD Mass åtgärder är en offentlig för hands version av Azure AD och är tillgängliga med en betald Azure AD-licensserver. Mer information om användnings villkoren för för hands versioner finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versioner.

## <a name="bulk-download-service-limits"></a>Begränsningar för Mass nedladdnings tjänst

Varje Mass aktivitet för att hämta en grupp lista kan köras i upp till en timme. På så sätt kan du ladda ned en lista med minst 300 000 grupper.

## <a name="to-download-a-list-of-groups"></a>Hämta en lista över grupper

1. Logga in på [Azure Portal](https://portal.azure.com) med ett administratörs konto i organisationen.
1. I Azure AD väljer du **grupper** > **hämtnings grupper**.
1. På sidan **hämtning av grupper** väljer du **Start** för att ta emot en CSV-fil som visar dina grupper.

   ![Kommandot ladda ned grupper finns på sidan alla grupper](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Kontrol lera hämtnings status

Du kan se statusen för alla väntande Mass begär Anden på sidan för **Mass åtgärds resultat (för hands version)** .

   ![På sidan Mass åtgärds resultat visas Mass status för begäran](./media/groups-bulk-download/bulk-center.png)

## <a name="next-steps"></a>Nästa steg

- [Mass borttagning av grupp medlemmar](groups-bulk-remove-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
