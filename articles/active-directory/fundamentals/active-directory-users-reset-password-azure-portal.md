---
title: Återställa en användares lösenord - Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du återställer en användares lösenord med Hjälp av Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4fdbbd4d71a9c97259678413cd9e59ee8aeae6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69032672"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Återställa en användares lösenord med Azure Active Directory

Som administratör kan du återställa en användares lösenord om lösenordet glöms bort, om användaren blir utelåst från en enhet eller om användaren aldrig har fått ett lösenord.

>[!Note]
>Om inte din Azure AD-klient är en användares arbetskatalog kan du inte återställa lösenordet. Det innebär att om användaren loggar in på din organisation med ett konto från en annan organisation, ett Microsoft-konto eller ett Google-konto kan du inte återställa lösenordet.<br><br>Om användaren har en fullmaktskälla som Active Directory för Windows Server kan du bara återställa lösenordet om du har aktiverat tillbakaskrivning av lösenord.<br><br>Om din användare har en auktoritetskälla som Extern Azure AD kan du inte återställa lösenordet. Endast användaren, eller en administratörssitrator i Extern Azure AD, kan återställa lösenordet.

>[!Note]
>Om du inte är administratör och i stället letar efter instruktioner om hur du återställer ditt eget arbets- eller skollösenord läser du [Återställ ditt arbets- eller skollösenord](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Så här återställer du ett lösenord

1. Logga in på [Azure-portalen](https://portal.azure.com/) som användaradministratör eller lösenordsadministratör. Mer information om tillgängliga roller finns i [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Välj **Azure Active Directory**, välj **Användare**, sök efter och välj den användare som behöver återställningen och välj sedan Återställ **lösenord**.

    Sidan **Alain Charon - Profile** visas med alternativet Återställ **lösenord.**

    ![Användarens profilsida med alternativet Återställ lösenord markerat](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. På sidan **Återställ lösenord** väljer du **Återställ lösenord**.

    > [!Note]
    > När du använder Azure Active Directory genereras ett tillfälligt lösenord automatiskt för användaren. När du använder Active Directory lokalt skapar du lösenordet för användaren.

4. Kopiera lösenordet och ge det till användaren. Användaren måste ändra lösenordet under nästa inloggningsprocess.

    >[!Note]
    >Det tillfälliga lösenordet upphör aldrig att gälla. Nästa gång användaren loggar in fungerar lösenordet fortfarande, oavsett hur mycket tid som har gått sedan det tillfälliga lösenordet genererades.

## <a name="next-steps"></a>Nästa steg

När du har återställt användarens lösenord kan du utföra följande grundläggande processer:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

Du kan också utföra mer komplexa användarscenarier, till exempel tilldela ombud, använda principer och dela användarkonton. Mer information om andra tillgängliga åtgärder finns i Dokumentationen för [Azure Active Directory-användarhantering](../users-groups-roles/index.yml).
