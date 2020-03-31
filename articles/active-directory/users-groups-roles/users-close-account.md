---
title: Stänga arbets- eller skolkonto i en ohanterad Azure AD-katalog
description: Så här stänger du ditt arbets- eller skolkonto i en ohanterat Azure Active Directory.
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
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815724"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Stänga ditt arbets- eller skolkonto i en ohanterad katalog

Om du är en användare i en ohanterat Azure Active Directory-organisation (Azure AD) och du inte längre behöver använda appar från den organisationen eller underhålla någon koppling till den, kan du stänga ditt konto när som helst. En ohanterat katalog har ingen global administratör. Användare i en ohanterlig katalog kan stänga sina konton på egen hand, utan att behöva kontakta en administratör.

Användare i en ohanterlig katalog skapas ofta under självbetjäningsanmälning. Ett exempel kan vara en informationsarbetare i en organisation som registrerar sig för en kostnadsfri tjänst. Mer information om självbetjäningsanmälning finns i [Vad är självbetjäningsanmäla för Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan stänga ditt konto bör du bekräfta följande:

* Kontrollera att du är användare av en ohanterat Azure AD-katalog. Du kan inte stänga ditt konto om du tillhör en hanterad katalog. Om du tillhör en hanterad katalog och vill stänga ditt konto måste du kontakta administratören. Information om hur du tar reda på om du tillhör en ohanterade katalog finns i [Ta bort användaren från Ohanterade klient.](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)

* Spara alla data som du vill behålla. Information om hur du skickar en exportbegäran finns i [Komma åt och exportera systemgenererade loggar för ohanterade klienter](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Att stänga ditt konto är oåterkalleligt. När du avslutar ditt konto tas alla personuppgifter bort. Du kommer inte längre att ha tillgång till ditt konto och data som är kopplade till ditt konto.

## <a name="close-your-account"></a>Stänga konto

Så här stänger du ett ohanterat arbets- eller skolkonto:

1. Logga in för att [stänga ditt konto](https://go.microsoft.com/fwlink/?linkid=873123)med det konto som du vill stänga.

1. På **Mina databegäranden**väljer du **Stäng konto**.

    ![Mina dataförfrågningar - Stäng konto](./media/users-close-account/close-account.png)

1. Granska bekräftelsemeddelandet och välj sedan **Ja**.

    ![Mina dataförfrågningar - Bekräfta stängning](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Nästa steg

- [Vad är självbetjäningsanmäla för Azure Active Directory?](directory-self-service-signup.md)
- [Ta bort användaren från en ohanterad klientorganisation](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Åtkomst till och export av systemgenererade loggar för ohanterade klientorganisationer](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
