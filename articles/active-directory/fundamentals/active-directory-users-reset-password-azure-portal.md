---
title: Återställa en användares lösenord – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du återställer en användares lösenord med Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db6554e86cef61f2fc8e7a466919d2ce723f0e5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492710"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Återställa en användares lösenord med Azure Active Directory

Som administratör kan återställa du en användares lösenord om du har glömt lösenordet, om användaren hämtar utelåst från en enhet, eller om du inte fått ett lösenord.

>[!Note]
>Om inte din Azure AD-klient är arbetskatalogen för en användare kan du inte återställa sina lösenord. Det innebär att om användaren logga in på din organisation med ett konto från en annan organisation, ett Microsoft-konto eller ett Google-konto kan du inte att återställa sina lösenord.<br><br>Om dina användare har en auktoritetskälla som Windows Server Active Directory, kommer du bara att kunna återställa lösenordet om du har aktiverat tillbakaskrivning av lösenord.<br><br>Om dina användare har en auktoritetskälla som externa Azure AD kan du inte återställa lösenordet. Endast användare eller en administratör i externa Azure AD kan återställa lösenordet.

>[!Note]
>Om du inte är administratör och letar i stället för instruktioner om hur du återställer ditt eget lösenord för arbetet eller skolan, se [återställa lösenordet för arbets- eller skolkonto](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Lösenordsåterställning

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som en användare med rollen eller lösenordsadministratör. Läs mer om de tillgängliga rollerna [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Välj **Azure Active Directory**väljer **användare**, söka efter och välj den användare som behöver återställningen och välj sedan **Återställ lösenord**.

    Den **Alain Charon - profil** visas med den **Återställ lösenord** alternativet.

    ![Användarens profilsida med återställning av lösenordsalternativet är markerat](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. I den **Återställ lösenord** väljer **Återställ lösenord**.

    Ett tillfälligt lösenord genereras automatiskt för användaren.

4. Kopiera lösenord och ge den till användaren. Användaren kommer att behöva ändra lösenordet vid nästa inloggningsprocess.

    >[!Note]
    >Det tillfälliga lösenordet upphör aldrig att gälla. Nästa gång användaren loggar in, fungerar lösenordet fortfarande, oavsett hur lång tid har passerat sedan det tillfälliga lösenordet har genererats.

## <a name="next-steps"></a>Nästa steg

När du har återställt dina användares lösenord, kan du utföra följande basic-processer:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

Eller du kan utföra mer komplexa användarscenarier, till exempel tilldela ombud, med hjälp av principer och dela användarkonton. Läs mer om andra tillgängliga åtgärder, [Azure Active Directory management supportdokumentation](../users-groups-roles/index.yml).
