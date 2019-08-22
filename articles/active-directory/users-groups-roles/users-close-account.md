---
title: Stäng ditt arbets-eller skol konto i en ohanterad katalog – Azure Active Directory | Microsoft Docs
description: Hur du stänger ditt arbets-eller skol konto i en ohanterad Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da4c6b1c1434dae564ab4876a3ab3f341a87097
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891959"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Stäng ditt arbets-eller skol konto i en ohanterad katalog

Om du är en användare i en ohanterad Azure Active Directory (Azure AD)-organisation och du inte längre behöver använda appar från den organisationen eller upprätthålla någon koppling till den, kan du när som helst stänga ditt konto. En ohanterad katalog har ingen global administratör. Användare i en ohanterad katalog kan stänga sina konton på egen hand, utan att behöva kontakta en administratör.

Användare i en ohanterad katalog skapas ofta under självbetjänings registrering. Ett exempel kan vara en informations anställd i en organisation som registrerar sig för en kostnads fri tjänst. Mer information om självbetjänings registrering finns i [Vad är självbetjänings registrering för Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan avsluta ditt konto bör du bekräfta följande objekt:

* Se till att du är en användare av en ohanterad Azure AD-katalog. Du kan inte stänga ditt konto om du tillhör en hanterad katalog. Om du tillhör en hanterad katalog och vill avsluta ditt konto måste du kontakta administratören. Information om hur du avgör om du tillhör en ohanterad katalog finns i [ta bort användaren från en ohanterad klient](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Spara alla data som du vill behålla. Information om hur du skickar en export förfrågan finns i [komma åt och exportera systemgenererade loggar för ohanterade klienter](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Om du stänger ditt konto går det inte att ångra. När du stänger ditt konto tas all personlig information bort. Du kommer inte längre att ha åtkomst till ditt konto och dina data som är kopplade till ditt konto.

## <a name="close-your-account"></a>Avsluta ditt konto

Följ dessa steg om du vill stänga ett ohanterat arbets-eller skol konto:

1. Logga in för att [stänga ditt konto](https://go.microsoft.com/fwlink/?linkid=873123)med det konto som du vill stänga.

1. På **mina data förfrågningar**väljer du **Stäng konto**.

    ![Mina data förfrågningar – Stäng konto](./media/users-close-account/close-account.png)

1. Granska bekräftelse meddelandet och välj sedan **Ja**.

    ![Mina data förfrågningar – Bekräfta stängning](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Nästa steg

- [Vad är självbetjänings registrering för Azure Active Directory?](directory-self-service-signup.md)
- [Ta bort användaren från ohanterad klient](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Åtkomst till och export av systemgenererade loggar för ohanterade klienter](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
