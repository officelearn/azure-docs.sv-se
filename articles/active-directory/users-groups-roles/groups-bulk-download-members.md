---
title: Gruppmedlemskapslista för masshämtning – Azure Active Directory-portal | Microsoft-dokument
description: Lägg till användare i grupp i Azure-administrationscentret.
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
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517157"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Masshämtningsmedlemmar i en grupp (förhandsversion) i Azure Active Directory

Med Azure Active Directory(Azure AD) portal kan du massladda ned medlemmarna i en grupp i organisationen till en CSV-fil (kommaavgränsade värden).

## <a name="to-bulk-download-group-membership"></a>Så här massnedladdningsgruppmedlemskap

1. Logga in [på Azure-portalen](https://portal.azure.com) med ett användarkonto för användaradministratörer i organisationen. Gruppägare kan också massnedladdning medlemmar i grupper som de äger.
1. I Azure AD väljer du **Grupper alla** > **grupper**.
1. Öppna gruppen vars medlemskap du vill hämta och välj sedan **Medlemmar**.
1. På sidan **Medlemmar** väljer du **Hämta medlemmar** för att hämta en CSV-fil med gruppmedlemmarna.

   ![Kommandot Hämta medlemmar finns på gruppens profilsida](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Kontrollera nedladdningsstatus

Du kan se status för alla väntande massbegäranden på sidan **Massåtgärdsresultat (förhandsversion).**

   ![På sidan Resultat för massåtgärder visas status för massbegäran](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Begränsningar för masshämtningstjänsten

Varje massaktivitet för att hämta en lista över gruppmedlemmar kan köras i upp till en timme. På så sätt kan du hämta en lista med minst 500 000 medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Medlemmar i gruppen För massimportgrupp](groups-bulk-import-members.md)
- [Massborttagning av gruppmedlemmar](groups-bulk-download-members.md)
