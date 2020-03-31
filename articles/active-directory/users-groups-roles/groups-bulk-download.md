---
title: Ladda ned en lista över grupper i Azure Active Directory-portalen | Microsoft-dokument
description: Hämta gruppegenskaper i grupp i Azure-administrationscentret i Azure Active Directory.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517152"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Massnedladdning av en lista över grupper (förhandsversion) i Azure Active Directory

Med Azure Active Directory(Azure AD) portal kan du massladda ner listan över alla grupper i organisationen till en kommaavgränsad värden (CSV) fil.

## <a name="to-download-a-list-of-groups"></a>Så här hämtar du en lista över grupper

1. Logga in [på Azure-portalen](https://portal.azure.com) med ett administratörskonto i organisationen.
1. I Azure AD väljer du > **Grupperhämtningsgrupper**. **Groups**
1. På **hämtningssidan för grupper** väljer du **Start** för att få en CSV-fil med dina grupper.

   ![Kommandot Hämta grupper finns på sidan Alla grupper](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Kontrollera nedladdningsstatus

Du kan se status för alla väntande massbegäranden på sidan **Massåtgärdsresultat (förhandsversion).**

   ![På sidan Resultat för massåtgärder visas status för massbegäran](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Begränsningar för masshämtningstjänsten

Varje massaktivitet för att hämta en grupplista kan köras i upp till en timme. På så sätt kan du hämta en lista med minst 300 000 grupper.

## <a name="next-steps"></a>Nästa steg

- [Massborttagning av gruppmedlemmar](groups-bulk-remove-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
