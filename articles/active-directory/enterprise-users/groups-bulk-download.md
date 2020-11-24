---
title: Hämta en lista med grupper i Azure Active Directory Portal | Microsoft Docs
description: Hämta grupp egenskaper i grupp i Azure administrations centret i Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd624abdcd358dfc1d26c3092e53691ad51cf75f
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503557"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Hämta en lista över grupper i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du hämta en lista över alla grupper i organisationen till en fil med kommaavgränsade värden (CSV).

## <a name="to-download-a-list-of-groups"></a>Hämta en lista över grupper

1. Logga in på [Azure Portal](https://portal.azure.com) med ett administratörs konto i organisationen.
1. I Azure AD väljer du **grupper**  >  **hämtnings grupper**.
1. På sidan **hämtning av grupper** väljer du **Start** för att ta emot en CSV-fil som visar dina grupper.

   ![Kommandot ladda ned grupper finns på sidan alla grupper](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Kontrol lera hämtnings status

Du kan se statusen för alla väntande Mass förfrågningar på resultat sidan för **Mass åtgärder** .

[![Kontrol lera status på sidan med Mass åtgärds resultat.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Begränsningar för Mass nedladdnings tjänst

Varje Mass aktivitet för att hämta en grupp lista kan köras i upp till en timme. På så sätt kan du ladda ned en lista med minst 300 000 grupper.

## <a name="next-steps"></a>Nästa steg

- [Mass borttagning av grupp medlemmar](groups-bulk-remove-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
