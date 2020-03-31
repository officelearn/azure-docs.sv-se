---
title: Lägga till eller ta bort användare – Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du lägger till nya användare eller tar bort befintliga användare med Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262118"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Lägga till eller ta bort användare med Azure Active Directory

Lägg till nya användare eller ta bort befintliga användare från din Azure Active Directory-organisation (Azure AD). Om du vill lägga till eller ta bort användare måste du vara användaradministratör eller global administratör.

## <a name="add-a-new-user"></a>Lägga till en ny användare

Du kan skapa en ny användare med Azure Active Directory-portalen.

Så här lägger du till en ny användare:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som användaradministratör för organisationen.

1. Sök efter och välj *Azure Active Directory* från valfri sida.

1. Välj **Användare**och välj sedan **Ny användare**.

    ![Lägga till en användare via Användare – Alla användare i Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Ange information för den här användaren på sidan **Användare:**

   - **Namn**. Krävs. Den nya användarens för- och efternamn. Till exempel *Mary Parker*.

   - **Användarnamn**. Krävs. Användarnamnet för den nya användaren. Till exempel `mary@contoso.com`.

     Domändelen av användarnamnet måste använda antingen det ursprungliga standarddomännamnet, * \<ditt domännamn>.onmicrosoft.com*eller ett anpassat domännamn, till exempel *contoso.com*. Mer information om hur du skapar ett anpassat domännamn finns i [Lägga till ditt eget domännamn med Azure Active Directory-portalen](add-custom-domain.md).

   - **Grupper**. Du kan också lägga till användaren i en eller flera befintliga grupper. Du kan också lägga till användaren i grupper vid ett senare tillfälle. Mer information om hur du lägger till användare i grupper finns i [Skapa en grundläggande grupp och lägga till medlemmar med Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Katalogroll**: Om du kräver administrativa Azure AD-behörigheter för användaren kan du lägga till dem i en Azure AD-roll. Du kan tilldela användaren att vara global administratör eller en eller flera av de begränsade administratörsrollerna i Azure AD. Mer information om hur du tilldelar roller finns i [Så här tilldelar du roller till användare](active-directory-users-assign-role-azure-portal.md).

   - **Jobbinformation**: Du kan lägga till mer information om användaren här, eller göra det senare. Mer information om hur du lägger till användarinformation finns i [Så här lägger du till eller ändrar information om användarprofil](active-directory-users-profile-azure-portal.md).

1. Kopiera det lösenord som skapas automatiskt i rutan **Lösenord.** Du måste ge det här lösenordet till användaren för att logga in för första gången.

1. Välj **Skapa**.

Användaren skapas och läggs till i din Azure AD-organisation.

## <a name="add-a-new-guest-user"></a>Lägga till en ny gästanvändare

Du kan också bjuda in nya gästanvändare att samarbeta med din organisation genom att välja **Bjud in användare** på sidan Ny **användare.** Om organisationens externa samarbetsinställningar är konfigurerade så att du får bjuda in gäster får användaren skicka en inbjudan som de måste acceptera för att kunna börja samarbeta via e-post. Mer information om hur du bjuder in användare av B2B-samarbete finns i [Bjuda in B2B-användare till Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Lägga till en konsumentanvändare

Det kan finnas scenarier där du manuellt vill skapa konsumentkonton i din Azure Active Directory B2C -katalog (Azure AD B2C). Mer information om hur du skapar konsumentkonton finns [i Skapa och ta bort konsumentanvändare i Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Lägga till en ny användare i en hybridmiljö

Om du har en miljö med både Azure Active Directory (moln) och Windows Server Active Directory (lokalt) kan du lägga till nya användare genom att synkronisera befintliga användarkontodata. Mer information om hybridmiljöer och användare finns i [Integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Ta bort en användare

Du kan ta bort en befintlig användare med Azure Active Directory-portalen.

Så här tar du bort en användare:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett användaradministratörskonto för organisationen.

1. Sök efter och välj *Azure Active Directory* från valfri sida.

1. Sök efter och välj den användare som du vill ta bort från din Azure AD-klientorganisation. Till exempel _Mary Parker_.

1. Välj **Ta bort användare**.

    ![Användare – Sidan Alla användare med Ta bort användare markerad](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

Användaren tas bort och visas inte längre på sidan **Användare – Alla användare.** Användaren kan ses på sidan **Borttagna användare** under de kommande 30 dagarna och kan återställas under den tiden. Mer information om hur du återställer en användare finns i [Återställa eller ta bort en nyligen borttagen användare med Azure Active Directory](active-directory-users-restore.md).

När en användare tas bort görs alla licenser som förbrukas av användaren tillgängliga för andra användare.

>[!Note]
>Du måste använda Active Directory i Windows Server för att uppdatera identitets-, kontaktinformations- eller jobbinformationen för användare vars behörighetskälla är Active Directory för Windows Server. När du har slutfört uppdateringen måste du vänta tills nästa synkroniseringscykel har slutförts innan ändringarna visas.

## <a name="next-steps"></a>Nästa steg

När du har lagt till användarna kan du göra följande grundläggande processer:

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

- [Arbeta med dynamiska grupper och användare](../users-groups-roles/groups-create-rule.md)

Du kan också utföra andra användarhanteringsuppgifter, till exempel [lägga till gästanvändare från en annan katalog](../b2b/what-is-b2b.md) eller återställa en [borttagen användare](active-directory-users-restore.md). Mer information om andra tillgängliga åtgärder finns i Dokumentationen för [Azure Active Directory-användarhantering](../users-groups-roles/index.yml).
