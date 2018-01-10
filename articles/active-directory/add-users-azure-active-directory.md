---
title: "Lägg till eller ta bort användare i Azure Active Directory | Microsoft Docs"
description: "Beskriver hur du lägger till nya användare eller ta bort befintliga användare i Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: f0f18f377f194e78c05e63e9b6cbc31c1b945335
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Snabbstart: Lägga till nya användare i Azure Active Directory
Den här artikeln beskriver hur du tar bort eller lägga till användare i din organisation till din orgnization Azure Active Directory (Azure AD)-klient med hjälp av Azure-portalen eller genom att synkronisera dina lokala Windows Server AD-kontot användardata. 

## <a name="add-cloud-based-users"></a>Lägga till molnbaserade användare
1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Azure Active Directory** och sedan **användare och grupper**.
3. På **användare och grupper**väljer **alla användare**, och välj sedan **ny användare**.
   ![Att välja kommandot Lägg till](./media/add-users-azure-active-directory/add-user.png)
4. Ange information för användaren, till exempel **namn** och **användarnamn**. Domännamndelen för användarnamn måste antingen vara den första standard domänen namnet ”[namn].onmicrosoft.com” eller en verifierad ofedererad [domännamn](add-custom-domain.md) , till exempel ”contoso.com”.
5. Kopiera eller annars Observera genererade användarens lösenord så att du kan ange den till användaren när processen har slutförts.
6. Du kan också öppna och fylla i informationen i **profil**, **grupper**, eller **Directory rollen** för användaren. Mer information om användar- och administratörsroller finns i [Tilldela administratörsroller i Azure AD](active-directory-assign-admin-roles-azure-portal.md).
7. På **användare**väljer **skapa**.
8. På ett säkert sätt distribuera genererade lösenordet till den nya användaren så att användaren kan logga in.

> [!TIP]
> Du kan också synkronisera konto användardata från lokala Windows Server AD. Microsofts identitetslösningar omfattar lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering för alla resurser, oavsett plats. Vi kallar detta Hybrididentitet. [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) kan användas för att integrera dina lokala kataloger med Azure Active Directory hybrid identity scenarier. På så sätt kan du erbjuda en gemensam identitet för dina användare för Office 365- och Azure SaaS-program som är integrerade med Azure AD. 

## <a name="delete-users-from-azure-ad"></a>Ta bort användare från Azure AD
1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **användare och grupper**.
3. På den **användare och grupper** bladet Välj användare för att ta bort från listan. 
4. På bladet för den valda användaren väljer **översikt**, och välj sedan i kommandofältet **ta bort**.
   ![Att välja kommandot Lägg till](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Läs mer 
* [Lägg till gästanvändare från en annan katalog](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Tilldela en användare till en roll i din Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
* [Återställa en borttagen användare](active-directory-users-restore.md)



## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du lägger till nya användare till Azure AD Premium i denna Snabbstart. 

Du kan använda följande länk för att skapa en ny användare i Azure AD från Azure-portalen.

> [!div class="nextstepaction"]
> [Lägga till användare i Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 