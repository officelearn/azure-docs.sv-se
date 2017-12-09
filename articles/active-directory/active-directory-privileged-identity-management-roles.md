---
title: Roller i Azure AD Privileged Identity Management | Microsoft Docs
description: "Information om vilka roller används för privilegierade identiteter med Azure Privileged Identity Management-tillägget."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ac812ccc-cf4e-4ac2-b981-69598056c9ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/31/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: e3f67b978ff66cbb71709f2f8d66986a33149ae6
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Olika administrativa roll i Azure Active Directory-PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Du kan tilldela användare i din organisation för olika administrativa roller i Azure AD. Dessa rolltilldelningar bestämma vilka uppgifter som att lägga till eller ta bort användare eller ändra inställningar för tjänsten, användarna ska kunna utföra på Azure AD och Office 365 och andra Microsoft Online Services och kopplade program.  

> [!IMPORTANT]
> Microsoft rekommenderar att du hanterar Azure AD via [Azure AD administratörscenter](https://aad.portal.azure.com) på Azure Portal istället för via den klassiska Azure-portalen som nämns i den här artikeln.

En global administratör kan uppdatera som användare kan **permanent** tilldelade roller i Azure AD, med hjälp av PowerShell-cmdlets som `Add-MsolRoleMember` och `Remove-MsolRoleMember`, eller via den klassiska portalen som beskrivs i [ Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Azure AD Privileged Identity Management (PIM) hanterar principer för privilegierad åtkomst för användare i Azure AD. PIM tilldelar användare till en eller flera roller i Azure AD och du kan tilldela någon till permanent i rollen, eller är tillämpliga för rollen. När en användare är permanent tilldelade till en roll eller aktiverar berättigade rolltilldelning och de kan hantera Azure Active Directory Office 365 och andra program med behörigheter som tilldelats deras roller.

Det finns ingen skillnad i åtkomst till någon med ett permanent jämfört med en berättigad rolltilldelning. Den enda skillnaden är att vissa användare inte behöver som hela tiden. De görs tillgängliga för rollen och kan aktivera det och inaktivera när de behöver.

## <a name="roles-managed-in-pim"></a>Roller som hanteras i PIM
Privileged Identity Management kan du tilldela användare till vanliga administratörsroller, inklusive:

* **Global administratör** (även kallat företagsadministratör) har åtkomst till alla administrativa funktioner. Du kan ha fler än en global administratör i din organisation. Den person som registrerar sig för att köpa Office 365 automatiskt blir en global administratör.
* **Administratör av Privilegierade roller** hanterar Azure AD PIM och uppdaterar rolltilldelningar för andra användare.  
* **Faktureringsadministratör** gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstens hälsa.
* **Lösenordsadministratör** återställer lösenord, hanterar tjänstbegäranden och övervakar tjänstens hälsa. Lösenord för administratörer kan bara återställa lösenord för användare.
* **Tjänstadministratör** hanterar tjänstbegäranden och övervakar tjänstens hälsa.
  
  > [!NOTE]
  > Om du använder Office 365 innan du tilldelar rollen Administratör till en användare först tilldela sedan användaren administrativ behörighet till en tjänst som Exchange Online.
  > 
  > 
* **Administratör för användarhantering** återställer lösenord, övervakar tjänstens hälsa och hanterar användarkonton, användargrupper och tjänstbegäranden. Användaren management admin kan inte ta bort en global administratör, skapa andra administratörsroller eller återställa lösenord för faktureringsadministratörer, globala och tjänsten administratörer.
* **Exchange-administratören** har administrativ åtkomst till Exchange Online via administrationscentret för Exchange (EAC) och kan utföra nästan alla uppgifter i Exchange Online.
* **SharePoint-administratör** har administrativ åtkomst till SharePoint Online via administrationscentret för SharePoint Online och kan utföra nästan alla uppgifter i SharePoint Online.
* **Skype för företag administratör** har administrativ åtkomst till Skype för företag genom Skype för företag administrationscentret och kan utföra nästan alla uppgifter i Skype för företag – Online.

Läs följande artiklar innehåller mer information om [Tilldela administratörsroller i Azure AD](active-directory-assign-admin-roles-azure-portal.md) och [Tilldela administratörsroller i Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Från PIM, kan du [tilldelar dessa roller till en användare](active-directory-privileged-identity-management-how-to-add-role-to-user.md) så att användaren kan [aktivera rollen vid behov](active-directory-privileged-identity-management-how-to-activate-role.md).

Om du vill ge en annan användare tillgång till att hantera i PIM själva roller som PIM kräver att användaren har beskrivs ytterligare i [ge åtkomst till PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Roller som inte hanteras i PIM
Roller i Exchange Online eller SharePoint Online, förutom de som nämns ovan, visas inte i Azure AD och så visas inte i PIM. Mer information om hur du ändrar detaljerade rolltilldelningar i dessa tjänster för Office 365 finns [behörigheter i Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Azure-prenumerationer och resursgrupper också visas inte i Azure AD. Om du vill hantera Azure-prenumerationer, finns [lägga till eller ändra Azure-administratörsroller](../billing/billing-add-change-azure-subscription-administrator.md) och för mer information om Azure RBAC finns [rollbaserad åtkomstkontroll i](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Användarroller och logga in
För vissa Microsoft-tjänster och program kanske tilldela en användare till en roll inte tillräckliga för att användaren är administratör.

Åtkomst till den klassiska Azure-portalen måste användaren vara en tjänstadministratör eller en medadministratör på en Azure-prenumeration, även om användaren inte behöver hantera Azure-prenumerationer.  Till exempel för att hantera konfigurationsinställningar för Azure AD i den klassiska portalen, måste en användare vara både en global administratör i Azure AD och en medadministratör för prenumerationen på en Azure-prenumeration.  Information om hur du lägger till användare till Azure-prenumerationer finns [lägga till eller ändra Azure-administratörsroller](../billing/billing-add-change-azure-subscription-administrator.md).

Åtkomst till Microsoft Online Services kan kräva att användaren också tilldelas en licens innan de kan öppna tjänstens portalen eller utföra administrativa uppgifter.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Tilldela en licens till en användare i Azure AD
1. Logga in på den [klassiska Azure-portalen](http://manage.windowsazure.com) med ett konto med global administratör eller en medadministratör.
2. Välj **alla objekt** på huvudmenyn.
3. Välj den katalog som du vill arbeta med och som har licenser som är associerade med den.
4. Välj **licenser**. Listan över tillgängliga licenser visas.
5. Välj licensplan som innehåller de licenser som du vill distribuera.
6. Välj **tilldela användare**.
7. Välj den användare som du vill tilldela en licens till.
8. Klicka på den **tilldela** knappen.  Användaren kan nu logga in på Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

