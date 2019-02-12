---
title: Konfigurera grupphantering via självbetjäning – Azure Active Directory | Microsoft Docs
description: Skapa och hantera säkerhetsgrupper eller Office 365-grupper i Azure Active Directory och begära medlemskap i säkerhetsgrupper eller Office 365-grupper
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: get-started-article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.openlocfilehash: f7ea78d6082ddbeebf2cbfd70f1e1ed1a8bae664
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509617"
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>Konfigurera Azure Active Directory för grupphantering via självbetjäning

Dina användare kan skapa och hantera egna säkerhetsgrupper eller Office 365-grupper i Azure Active Directory (Azure AD). Användare kan även begära säkerhetsgruppsmedlemskap eller Office 365-gruppmedlemskap, och sedan kan gruppens ägare godkänna eller neka medlemskap. Den dagliga kontrollen av gruppmedlemskap delegeras till dem som förstår medlemskapets affärskontext. Funktioner för grupphantering via självbetjäning är bara tillgängliga för säkerhetsgrupper och Office 365-grupper, men inte för e-postaktiverade säkerhetsgrupper eller distributionslistor.

Grupphantering via självbetjäning behandlar två scenarier: 

* **Delegerad grupphantering** Ett exempel är en administratör som hanterar åtkomsten till ett SaaS-program som hennes företag använder. Eftersom det börjar bli svårt att hantera dessa åtkomstbehörigheter ber administratören företagsägaren att skapa en ny grupp. Administratören tilldelar åtkomst för programmet till den nya gruppen och lägger till alla personer som redan har åtkomst till programmet till gruppen. Företagsägaren kan sedan lägga till fler användare, som automatiskt tilldelas programmet. Företagsägaren behöver inte vänta på administratören för att kunna hantera åtkomst för användarna. Om administratören tilldelar en chef samma behörighet i en annan affärsgrupp, så kan den personen även hantera sina egna användare åtkomst. Varken företagsägaren eller chefen kan visa eller hantera varandras användare. Administratören kan fortfarande se alla användare som har åtkomst till programmet och blockera behörigheten om det behövs.
* **Grupphantering via självbetjäning** Ett exempel på det här scenariot är två användare som båda har SharePoint Online-platser som de har konfigurerat oberoende av varandra. De vill ge varandras grupper åtkomst till sina respektive webbplatser. De kan åstadkomma detta genom att först skapa en grupp i Azure AD och därefter var och en välja samma grupp i SharePoint Online, vilket ger dem åtkomst till varandras webbplatser. När någon vill ha åtkomst begär de det från åtkomstpanelen och efter godkännande får de automatiskt åtkomst till båda SharePoint Online-platserna. Senare beslutar den ena av dem att alla användare som har åtkomst till webbplatsen även ska få åtkomst till ett visst SaaS-program. SaaS-programmets administratör kan lägga till åtkomstbehörighet för programmet till SharePoint Online-webbplatsen. När det är gjort kommer alla förfrågningar som han godkänner att ge åtkomst till de två SharePoint Online-webbplatserna och även till SaaS-programmet.

## <a name="make-a-group-available-for-user-self-service"></a>Göra en grupp tillgänglig för självbetjäning av användare
1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Användare och grupper** och sedan **Gruppinställningar**.
3. Ställ in **Självbetjäning för grupphantering aktiverad** på **Ja**.
4. Ställ in **Användare kan skapa säkerhetsgrupper** eller **Användare kan skapa Office 365-grupper** på **Ja**.
  * När inställningarna är aktiverade kan alla användare i katalogen skapa nya säkerhetsgrupper och lägga till medlemmar i dessa grupper. Dessa nya grupper visas även på åtkomstpanelen för alla andra användare. Om gruppens principinställningar så tillåter kan andra användare skapa förfrågningar om att ansluta till dessa grupper. 
  * När de här inställningarna är avaktiverade kan användarna inte skapa grupper eller ändra de befintliga grupper som de äger. De kan dock fortfarande hantera medlemskap i dessa grupper och godkänna förfrågningar från andra användare om att ansluta till grupperna.

Genom **Användare som kan hantera säkerhetsgrupper** och **Användare som kan hantera Office 365-grupper** kan du få mer detaljerad åtkomstkontroll över dina användares grupphantering via självbetjäning. När **Användare kan skapa grupper** är aktiverat kan alla användare i klientorganisationen skapa nya grupper och lägga till medlemmar i dessa grupper. Om du ställer in dem på **Some** (Vissa) begränsar du grupphanteringen till enbart en begränsad grupp användare. När du har angett den här växeln till **Vissa** måste du lägga till användare i gruppen SSGMSecurityGroupsUsers innan de kan skapa nya grupper och lägga till medlemmar i dem. Om du väljer **Alla** för **Användare som kan använda självbetjäning för säkerhetsgrupper** och **Användare som kan hantera Office 365-grupper** kan alla användare i klienten skapa nya grupper.

Du kan också använda **Grupp som kan hantera säkerhetsgrupper** eller **Grupp som kan hantera Office 365-grupper** för att ange en enda grupp vars medlemmar ska kunna använda självbetjäning.

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
