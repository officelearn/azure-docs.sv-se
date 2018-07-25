---
title: Roller i Azure AD Privileged Identity Management | Microsoft Docs
description: Lär dig vilka roller som används för privilegierade identiteter med Azure Privileged Identity Management-tillägget.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 07/23/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 6553fdba463144c6eda1e35c0967e92a3c44aff6
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225584"
---
# <a name="directory-roles-you-can-manage-using-azure-ad-pim"></a>Du kan hantera med hjälp av Azure AD PIM för katalogroller
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Du kan tilldela användare i din organisation för olika administrativa roller i Azure AD. De här rolltilldelningarna bestämma vilka aktiviteter, till exempel att lägga till eller ta bort användare eller ändra tjänstinställningar, användarna ska kunna utföra på Azure AD, Office 365, och andra Microsoft Online Services och anslutna program.  

En Global administratör kan uppdatera som användarna **permanent** tilldelas till roller i Azure AD via portalen, enligt beskrivningen i [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) eller med hjälp av [ PowerShell-kommandon](/powershell/module/azuread#directory_roles).

Azure AD Privileged Identity Management (PIM) hanterar principer för privilegierad åtkomst för användare i Azure AD. PIM tilldelar användare till en eller flera roller i Azure AD och du kan tilldela någon vara permanent i rollen eller berättigad för rollen. När en användare permanent tilldelade till en roll eller aktiverar en berättigad rolltilldelning och de kan hantera Azure Active Directory, Office 365 och andra program med de behörigheter som tilldelats deras roller.

Det finns ingen skillnad i åtkomst till någon med en permanent jämfört med en berättigad rolltilldelning. Den enda skillnaden är att vissa användare inte behöver som har åtkomst till hela tiden. De görs berättigad för rollen och kan aktivera och inaktivera när de behöver för att.

## <a name="roles-managed-in-pim"></a>Roller som hanteras i PIM
Privileged Identity Management kan du tilldela användare till vanliga administratörsroller, inklusive:

* **Global administratör** (även kallat företagsadministratör) har åtkomst till alla administrativa funktioner. Du kan ha fler än en global administratör i din organisation. Den person som registrerar sig för att köpa Office 365 automatiskt blir global administratör.
* **Privilegierad rolladministratör** hanterar Azure AD PIM och uppdaterar rolltilldelningar för andra användare.  
* **Faktureringsadministratör** gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstehälsa.
* **Lösenordsadministratör** återställer lösenord, hanterar tjänstbegäranden och övervakar tjänstehälsa. Lösenordsadministratörer är begränsade till återställning av lösenord för användare.
* **Tjänstadministratör** hanterar tjänstbegäranden och övervakar tjänstehälsa.
  
  > [!NOTE]
  > Om du använder Office 365 innan du tilldelar administratörsrollen till en användare först tilldela sedan användaren administrativ behörighet till en tjänst, till exempel Exchange Online.
  > 
  > 
* **Administratör för användarhantering** återställer lösenord, övervakar tjänstehälsa och hanterar användarkonton, användargrupper och tjänstbegäranden. Administratören för användarhantering kan inte ta bort en global administratör, skapa andra administratörsroller eller återställa lösenord för faktureringsadministratörer, globala administratörer och tjänstadministratörer.
* **Exchange-administratören** har administrativ åtkomst till Exchange Online via administrationscentret för Exchange (UK) och kan utföra nästan alla aktiviteter i Exchange Online.
* **SharePoint-administratör (förhandsversion)** har administrativ åtkomst till SharePoint Online via administrationscentret för SharePoint Online och kan utföra nästan alla aktiviteter i SharePoint Online. Den här rollen förhandsvisas just nu. Berättigade användare avbrott med hjälp av den här rollen i SharePoint när du har aktiverat i PIM.
* **Skype för företag-administratör** har administrativ åtkomst till Skype för företag via Skype för företag-administrationscentret och kan utföra nästan alla aktiviteter i Skype för företag Online.

Läs de här artiklarna för mer information om [Tilldela administratörsroller i Azure AD](../users-groups-roles/directory-assign-admin-roles.md) och [Tilldela administratörsroller i Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Från PIM, kan du [tilldela dessa roller till en användare](pim-how-to-add-role-to-user.md) så att användaren kan [aktivera rollen vid behov](pim-how-to-activate-role.md).

Om du vill ge en annan användare åtkomst till att hantera i PIM själva de roller som PIM kräver att användaren har beskrivs ytterligare i [ge åtkomst till PIM](pim-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Roller som inte hanteras i PIM
Roller i Exchange Online eller SharePoint Online, förutom de som nämns ovan, representeras inte i Azure AD och så visas inte i PIM. Mer information om hur du ändrar detaljerade rolltilldelningar i de här Office 365-tjänster finns i [behörigheter i Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Azure-prenumerationer och resursgrupper är också inte representeras i Azure AD. För att hantera Azure-prenumerationer, se [lägga till eller ändra Azure-administratörsroller](../../billing/billing-add-change-azure-subscription-administrator.md) och Läs mer om Azure RBAC [rollbaserad åtkomstkontroll i Azure](../../role-based-access-control/role-assignments-portal.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Användarroller och logga in
För vissa Microsoft-tjänster och program, kanske tilldela en användare till en roll inte är tillräckligt för att aktivera användaren vara administratör.

Åtkomst till Azure-portalen kräver användaren vara en tjänstadministratör eller delad administratör för en Azure-prenumeration, även om användaren inte behöver hantera Azure-prenumerationer.  Till exempel för att hantera konfigurationsinställningar för Azure AD, måste en användare vara både en global administratör i Azure AD och medadministratör för prenumerationen till en Azure-prenumeration.  Läs hur du lägger till användare till Azure-prenumerationer i [lägga till eller ändra Azure-administratörsroller](../../billing/billing-add-change-azure-subscription-administrator.md).

Åtkomst till Microsoft Online Services kan kräva att användaren också tilldelas en licens innan de kan öppna tjänstens portalen eller utföra administrativa uppgifter.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Tilldela en licens till en användare i Azure AD
1. Logga in på den [Azure-portalen](http://portal.azure.com) med ett konto som global administratör eller en delad administratör-konto.
3. Välj Azure AD och den katalog du vill arbeta med och som har licenser som är kopplade till den.
4. Välj **licenser** till vänster. Listan över tillgängliga licenser visas.
5. Välj licensplanen som innehåller de licenser som du vill distribuera.
6. Välj **tilldela användare**.
7. Välj den användare som du vill tilldela en licens till.
8. Klicka på den **tilldela** knappen.  Användaren kan nu logga in på Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

