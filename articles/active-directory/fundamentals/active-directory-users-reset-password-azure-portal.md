---
title: Återställa en användares lösen ord – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du återställer en användares lösen ord med hjälp av Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69da6aa0253f92f9242a988c6b46de873df0677e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87797211"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Återställa en användares lösenord med hjälp av Azure Active Directory

Som administratör kan du återställa en användares lösen ord om lösen ordet är bortglömt, om användaren blir utelåst från en enhet eller om användaren aldrig har fått ett lösen ord.

>[!Note]
>Om inte din Azure AD-klient är arbets katalogen för en användare kan du inte återställa lösen ordet. Det innebär att om din användare loggar in i din organisation med ett konto från en annan organisation, ett Microsoft-konto eller ett Google-konto, kan du inte återställa lösen ordet.<br><br>Om användaren har en behörighets källa som Windows Server Active Directory kan du bara återställa lösen ordet om du har aktiverat tillbakaskrivning av lösen ord.<br><br>Om användaren har en behörighets källa som extern Azure AD kan du inte återställa lösen ordet. Endast användaren eller en administratör i extern Azure AD kan återställa lösen ordet.

>[!Note]
>Om du inte är administratör och vill ha anvisningar om hur du återställer ditt eget arbets-eller skol lösen ord, se [återställa lösen ordet för ditt arbets-eller skol](../user-help/active-directory-passwords-update-your-own-password.md)konto.

## <a name="to-reset-a-password"></a>Återställa ett lösen ord

1. Logga in på [Azure Portal](https://portal.azure.com/) som användar administratör eller lösen ords administratör. Mer information om tillgängliga roller finns [i tilldela administratörs roller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Välj **Azure Active Directory**, Välj **användare**, Sök efter och välj den användare som behöver återställas och välj sedan **Återställ lösen ord**.

    Sidan **Alain Charon-profil** visas med alternativet **Återställ lösen ord** .

    ![Användarens profil sida med alternativet Återställ lösen ord markerat](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. På sidan **Återställ lösen ord** väljer du **Återställ lösen ord**.

    > [!Note]
    > När du använder Azure Active Directory genereras ett tillfälligt lösen ord automatiskt för användaren. När du använder Active Directory lokalt skapar du lösen ordet för användaren.

4. Kopiera lösen ordet och ge det till användaren. Användaren kommer att behöva ändra lösen ordet under nästa inloggnings process.

    >[!Note]
    >Det tillfälliga lösen ordet upphör aldrig att gälla. Nästa gången användaren loggar in, kommer lösen ordet fortfarande att fungera, oavsett hur lång tid det tar att använda sedan det tillfälliga lösen ordet genererades.

## <a name="next-steps"></a>Nästa steg

När du har återställt användarens lösen ord kan du utföra följande grundläggande processer:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Lägga till eller ändra profil information](active-directory-users-profile-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

Eller så kan du utföra mer komplexa användar scenarier, till exempel tilldela ombud, använda principer och dela användar konton. Mer information om andra tillgängliga åtgärder finns i [Azure Active Directory User Management-dokumentation](../users-groups-roles/index.yml).
