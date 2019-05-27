---
title: Avsluta ditt arbets- eller skolkonto konto i en ohanterad katalog - Azure Active Directory | Microsoft Docs
description: Så här stänger du ditt arbets- eller skolkonto konto i en ohanterad Azure Active Directory.
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
ms.openlocfilehash: 39b359ef7feeaec541ba17e98a5d1e9b74c6403a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65958007"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Avsluta ditt arbets- eller skolkonto konto i en ohanterad katalog

Om du är en användare i en ohanterad Azure Active Directory (Azure AD)-organisation och du inte längre behöver använda appar från den organisationen eller underhålla eventuella kopplingar med är att du kan avsluta ditt konto när som helst. En ohanterad katalog har inte en Global administratör. Användare i en ohanterad katalog kan stänga sina konton på egen hand, utan att behöva kontakta en administratör.

Användare i en ohanterad katalog skapas ofta under självbetjäning registrering. Ett exempel kan vara en informationsanställd i en organisation som registrerar sig för en kostnadsfri tjänst. Läs mer om självanmälan [vad är självbetjäning registrera dig för Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan stänga ditt konto, bör du kontrollera följande:

* Kontrollera att du är en användare av en ohanterad Azure AD-katalog. Du kan inte stänga ditt konto om du tillhör en hanterad katalog. Om du tillhör en hanterad katalog och vill avsluta ditt konto, måste du kontakta din administratör. Information om hur du avgör om du tillhör en ohanterad katalog finns i [ta bort användaren från ohanterad klient](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Spara data som du vill behålla. Information om hur du skickar en begäran om export finns i [åtkomst till och export av systemgenererade loggar för ohanterade klienter](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Stänga ditt konto kan inte ångras. När du avslutar kontot tas tas alla personliga data bort. Du kommer inte längre har åtkomst till ditt konto och data som är associerade med ditt konto.

## <a name="close-your-account"></a>Avsluta ditt konto

Stäng ett ohanterade arbets- eller skolkonto genom att följa dessa steg:

1. Logga in på [avsluta ditt konto](https://go.microsoft.com/fwlink/?linkid=873123), med hjälp av det konto som du vill stänga.

1. På **Mina dataförfrågningar**väljer **avsluta kontot**.

    ![Mina dataförfrågningar - Stäng konto](./media/users-close-account/close-account.png)

1. Granska bekräftelsemeddelandet och markera **Ja**.

    ![Mina dataförfrågningar - bekräfta att Stäng](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Nästa steg

- [Vad är självbetjäning registrera dig för Azure Active Directory?](directory-self-service-signup.md)
- [Ta bort användaren från ohanterad klient](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Åtkomst till och export av systemgenererade loggar för ohanterade klienter](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
