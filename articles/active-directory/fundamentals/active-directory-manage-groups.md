---
title: Hantera app & resursåtkomst med hjälp av grupper - Azure AD
description: Lär dig mer om hur du hanterar åtkomst till organisationens molnbaserade appar, lokala appar och resurser med hjälp av Azure Active Directory-grupper.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 961444e15ae1c45db1fc7423a6ac3cc96cc7b3fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768018"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Hantera app- och resursåtkomst med Azure Active Directory-grupper
Med Azure Active Directory (Azure AD) kan du använda grupper för att hantera åtkomst till dina molnbaserade appar, lokala appar och dina resurser. Dina resurser kan ingå i Azure AD-organisationen, till exempel behörigheter för att hantera objekt via roller i Azure AD, eller externa till organisationen, till exempel för SaaS-appar (Software as a Service), Azure-tjänster, SharePoint-webbplatser och lokala resurser.

>[!NOTE]
>För att använda Azure Active Directory behöver du ett Azure-konto. Om du inte har ett Azure-konto kan du [registrera dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).
>
> I Azure-portalen kan du se några grupper vars medlemskap och gruppinformation som du inte kan hantera i portalen:
>
> - Grupper som synkroniseras från lokala Active Directory kan endast hanteras i lokal Active Directory.
> - Andra grupptyper, till exempel distributionslistor och e-postaktiverade säkerhetsgrupper, hanteras endast i administrationscentret för Exchange eller Microsoft 365. Du måste logga in på Administrationscenter för Exchange eller Microsoft 365 för att kunna hantera dessa grupper.

## <a name="how-access-management-in-azure-ad-works"></a>Så här fungerar åtkomsthantering i Azure AD

Azure AD hjälper dig att ge åtkomst till organisationens resurser genom att ge åtkomsträttigheter till en enskild användare eller till en hel Azure AD-grupp. Med hjälp av grupper kan resursägaren (eller Azure AD Directory-ägaren) tilldela en uppsättning åtkomstbehörigheter till alla medlemmar i gruppen, i stället för att behöva ange behörigheterna individuellt. Resurs- eller katalogägaren kan också ge hanteringsrättigheter för medlemslistan till någon annan, till exempel en avdelningschef eller en Helpdesk-administratör, så att den personen kan lägga till och ta bort medlemmar efter behov. Mer information om hur du hanterar gruppägare finns i [Hantera gruppägare](active-directory-accessmanagement-managing-group-owners.md)

![Åtkomsthanteringsdiagram i Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Olika sätt att tilldela åtkomsträttigheter

Det finns fyra sätt att tilldela resursåtkomsträttigheter till användarna:

- **Direkt uppdrag.** Resursägaren tilldelar direkt användaren till resursen.

- **Gruppuppgift.** Resursägaren tilldelar en Azure AD-grupp till resursen, vilket automatiskt ger alla gruppmedlemmar åtkomst till resursen. Gruppmedlemskap hanteras av både gruppägaren och resursägaren, så att antingen ägaren kan lägga till eller ta bort medlemmar från gruppen. Mer information om hur du lägger till eller tar bort gruppmedlemskap finns i [Så här lägger du till eller tar bort en grupp från en annan grupp med Azure Active Directory-portalen](active-directory-groups-membership-azure-portal.md). 

- **Regelbaserad tilldelning.** Resursägaren skapar en grupp och använder en regel för att definiera vilka användare som tilldelas en viss resurs. Regeln baseras på attribut som har tilldelats enskilda användare. Resursägaren hanterar regeln och avgör vilka attribut och värden som krävs för att tillåta åtkomst till resursen. Mer information finns i [Skapa en dynamisk grupp och kontrollera status](../users-groups-roles/groups-create-rule.md).

    Du kan också titta på den här korta videon för en snabb förklaring om hur du skapar och använder dynamiska grupper:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Uppdrag för externa myndigheter.** Åtkomst kommer från en extern källa, till exempel en lokal katalog eller en SaaS-app. I det här fallet tilldelar resursägaren en grupp för att ge åtkomst till resursen och sedan hanterar den externa källan gruppmedlemmarna.

   ![Översikt över åtkomsthanteringsdiagram](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Kan användare gå med i grupper utan att tilldelas?
Gruppägaren kan låta användarna hitta sina egna grupper att gå med i i stället för att tilldela dem. Ägaren kan också ställa in gruppen så att den automatiskt accepterar alla användare som går med eller kräver godkännande.

När en användare begär att gå med i en grupp vidarebefordras begäran till gruppägaren. Om det behövs kan ägaren godkänna begäran och användaren meddelas om gruppmedlemskapet. Men om du har flera ägare och en av dem inte godkänner, meddelas användaren, men läggs inte till i gruppen. Mer information och instruktioner om hur du låter dina användare begära att gå med i grupper finns i [Konfigurera Azure AD så att användarna kan begära att få gå med i grupper](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Nästa steg
Nu när du har lite av en introduktion till åtkomst till hantering med hjälp av grupper, börjar du hantera dina resurser och appar.

- [Skapa en ny grupp med Azure Active Directory](active-directory-groups-create-azure-portal.md) eller Skapa och hantera en ny grupp med [PowerShell-cmdletar](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Använda grupper för att tilldela åtkomst till en integrerad SaaS-app](../users-groups-roles/groups-saasapps.md)

- [Synkronisera en lokal grupp till Azure med Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
