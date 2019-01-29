---
title: Hantera appen och resurs åtkomst med hjälp av grupper – Azure Active Directory | Microsoft Docs
description: Läs mer om hur du hanterar åtkomst till din organisations molnbaserade appar, lokala appar och resurser med hjälp av Azure Active Directory-grupper.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: lizross
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.openlocfilehash: d5a05db0ab69a1cb9f123bb73c0cb4151f1104d6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103555"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Hantera appen och resurs åtkomst med hjälp av Azure Active Directory-grupper
Azure Active Directory (Azure AD) hjälper dig att hantera dina molnbaserade appar, lokala appar och dina resurser med hjälp av organisationens grupper. Dina resurser kan inte ingå i katalogen, till exempel behörighet att hantera objekt via roller i katalogen eller externa för katalogen, t.ex. för programvara som en tjänst (SaaS)-appar, Azure-tjänster eller SharePoint-webbplatser och lokala resurser.

>[!NOTE]
>För att använda Azure Active Directory behöver du ett Azure-konto. Om du inte har ett Azure-konto kan du [registrera dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

## <a name="how-does-access-management-in-azure-ad-work"></a>Hur har tillgång till hantering i Azure AD-arbete?
Azure AD kan du ge åtkomst till organisationens resurser genom att tillhandahålla åtkomsträttigheter till en enskild användare eller till en hel Azure AD-gruppen. Med hjälp av grupper kan den resurs ägare (eller Azure AD directory ägare), tilldela en uppsättning behörigheter för åtkomst till alla medlemmar i gruppen, utan att behöva tilldela rättigheter i taget. Resurs- eller directory ägaren kan också låta rights management för medlemslistan till någon annan, till exempel en avdelningschef eller supportavdelningsadministratör, så att den personen lägga till och ta bort medlemmar, efter behov. Läs mer om hur du hanterar gruppägare [hantera gruppägare](active-directory-accessmanagement-managing-group-owners.md)

![Åtkomsthanteringsdiagram i Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Sätt att tilldela behörighet
Det finns fyra sätt att tilldela behörighet till dina användare för resursen:

- **Direkttilldelning.** Resursägaren tilldelar användaren direkt till resursen.

- **Tilldelning av gruppen.** Resursägaren tilldelar en Azure AD-grupp till en resurs, som automatiskt ger alla medlemmar gruppåtkomst till resursen. Gruppmedlemskap hanteras av både gruppägare och resursägare, så att antingen ägaren lägga till eller ta bort medlemmar från gruppen. Läs mer om att lägga till eller ta bort gruppmedlemskap [så här: Lägg till eller ta bort en grupp från någon annan grupp med hjälp av Azure Active Directory-portalen](active-directory-groups-membership-azure-portal.md). 

- **Regelbaserad tilldelning.** Resursägaren skapar en grupp och använder en regel för att definiera vilka användare som har tilldelats en viss resurs. Regeln är baserat på attribut som är kopplade till enskilda användare. Resursägaren hanterar regeln, avgör vilka attribut och värden som krävs för att tillåta åtkomst till resursen. Mer information finns i [skapa en dynamisk grupp och kontrollera status för](../users-groups-roles/groups-create-rule.md).

    Du kan också se den här korta videon för en snabb förklaring om att skapa och använda dynamiska grupper:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Tilldelning av extern utfärdare.** Åtkomst kommer från en extern källa, till exempel en lokal katalog eller en SaaS-app. I det här fallet resursägaren tilldelar en grupp för att ge åtkomst till resursen och sedan den externa källan hanterar gruppmedlemmarna.

   ![Översikt över åtkomsthanteringsdiagram](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Kan användarna ansluta till grupper utan att ha tilldelats?
Gruppägare kan låta användarna hitta sina egna grupper att ansluta till, i stället för att tilldela dem. Ägare kan också ställa in gruppen att automatiskt godkänna alla användare som ansluter till eller krav på godkännande.

När en användare begär att ansluta till en grupp, vidarebefordras begäran till gruppägare. Om det krävs ägare godkänna denna begäran och användaren meddelas om gruppmedlemskap. Om du har flera ägare och en av dem disapproves, användaren meddelas, men har lagts till inte i gruppen. Mer information och instruktioner om hur du ger användarna begära att ansluta till grupper finns i [konfigurera Azure AD så att användarna kan begära att ansluta till grupper](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Nästa steg
Nu när du har lite en introduktion till hantering med hjälp av grupper kan börja du hantera dina resurser och appar.

- [Skapa en ny grupp med Azure Active Directory](active-directory-groups-create-azure-portal.md) eller [skapa och hantera en ny grupp med PowerShell-cmdletar](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Använda grupper för att tilldela åtkomst till en integrerad SaaS-app](../users-groups-roles/groups-saasapps.md)

- [Synkronisera en lokal grupp till Azure med Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
