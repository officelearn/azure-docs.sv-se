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
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e52c37e293941a767621cf56ef75f8cc83b1925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298011"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Konfigurera grupphantering med självbetjäning i Azure Active Directory 

Du kan göra det möjligt för användare att skapa och hantera sina egna säkerhetsgrupper eller Office 365-grupper i Azure Active Directory (Azure AD). Gruppens ägare kan godkänna eller neka medlemsförfrågningar och delegera kontroll över gruppmedlemskap. Grupphanteringsfunktioner för självbetjäning är inte tillgängliga för e-postaktiverade säkerhetsgrupper eller distributionslistor.

## <a name="self-service-group-membership-defaults"></a>Standardvärden för självbetjäningsgruppmedlemskap

När säkerhetsgrupper skapas i Azure-portalen eller med Azure AD PowerShell kan endast gruppens ägare uppdatera medlemskapet. Säkerhetsgrupper som skapas av självbetjäning på [åtkomstpanelen](https://account.activedirectory.windowsazure.com/r#/joinGroups) och alla Office 365-grupper är tillgängliga för alla användare, oavsett om de är ägargodkända eller automatiskt godkända. På Access-panelen kan du ändra medlemskapsalternativen när du skapar gruppen.

Grupper som skapats i | Standardbeteende för säkerhetsgrupp | Standardbeteende för Office 365-grupp
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Endast ägare kan lägga till medlemmar<br>Synlig men inte tillgänglig för att ansluta till Access-panelen | Öppen för anslutning för alla användare
[Azure-portal](https://portal.azure.com) | Endast ägare kan lägga till medlemmar<br>Synlig men inte tillgänglig för att ansluta till Access-panelen<br>Ägaren tilldelas inte automatiskt när gruppen skapas | Öppen för anslutning för alla användare
[Åtkomstpanel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Öppen för anslutning för alla användare<br>Medlemskapsalternativ kan ändras när gruppen skapas | Öppen för anslutning för alla användare<br>Medlemskapsalternativ kan ändras när gruppen skapas

## <a name="self-service-group-management-scenarios"></a>Scenarier för grupphantering med självbetjäning

* **Delegerad grupphantering** Ett exempel är en administratör som hanterar åtkomsten till ett SaaS-program som hennes företag använder. Eftersom det börjar bli svårt att hantera dessa åtkomstbehörigheter ber administratören företagsägaren att skapa en ny grupp. Administratören tilldelar åtkomst för programmet till den nya gruppen och lägger till alla personer som redan har åtkomst till programmet i gruppen. Företagsägaren kan sedan lägga till fler användare, som automatiskt tilldelas programmet. Företagsägaren behöver inte vänta på administratören för att kunna hantera åtkomst för användarna. Om administratören ger samma behörighet till en chef i en annan företagsgrupp kan den personen också hantera åtkomst för sina egna gruppmedlemmar. Varken företagets ägare eller chef kan visa eller hantera varandras gruppmedlemskap. Administratören kan fortfarande se alla användare som har åtkomst till programmet och blockera behörigheten om det behövs.
* **Grupphantering via självbetjäning** Ett exempel på det här scenariot är två användare som båda har SharePoint Online-platser som de har konfigurerat oberoende av varandra. De vill ge varandras team tillgång till sina webbplatser. De kan åstadkomma detta genom att först skapa en grupp i Azure AD och därefter var och en välja samma grupp i SharePoint Online, vilket ger dem åtkomst till varandras webbplatser. När någon vill ha åtkomst begär de det från åtkomstpanelen och efter godkännande får de automatiskt åtkomst till båda SharePoint Online-platserna. Senare beslutar den ena av dem att alla användare som har åtkomst till webbplatsen även ska få åtkomst till ett visst SaaS-program. SaaS-programmets administratör kan lägga till åtkomstbehörighet för programmet till SharePoint Online-webbplatsen. När det är gjort kommer alla förfrågningar som han godkänner att ge åtkomst till de två SharePoint Online-webbplatserna och även till SaaS-programmet.

## <a name="make-a-group-available-for-user-self-service"></a>Göra en grupp tillgänglig för självbetjäning av användare

1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **Grupper**och välj sedan **Allmänna** inställningar.
1. Ange **ägare kan hantera gruppmedlemskapsförfrågningar på åtkomstpanelen** till **Ja**.
1. Ange **Begränsa åtkomst till grupper på åtkomstpanelen** till **Nej**.
1. Om du anger **att användare kan skapa säkerhetsgrupper i Azure-portaler** eller användare kan skapa Office **365-grupper i Azure-portaler** till

    - **Ja**: Alla användare i din Azure AD-organisation tillåts skapa nya säkerhetsgrupper och lägga till medlemmar i dessa grupper. Dessa nya grupper visas även på åtkomstpanelen för alla andra användare. Om principinställningen för gruppen tillåter det kan andra användare skapa begäranden om att gå med i dessa grupper
    - **Nej:** Användare kan inte skapa grupper och kan inte ändra befintliga grupper som de är ägare till. De kan dock fortfarande hantera medlemskap i dessa grupper och godkänna förfrågningar från andra användare om att ansluta till grupperna.

Du kan också använda **Ägare som kan tilldela medlemmar som gruppägare i Azure-portaler** och ägare som kan tilldela medlemmar som **gruppägare i Azure-portaler** för att uppnå mer detaljerad åtkomstkontroll över självbetjäningsgrupphantering för dina användare.

När användare kan skapa grupper får alla användare i organisationen skapa nya grupper och kan sedan, som standardägare, lägga till medlemmar i dessa grupper. Du kan inte ange personer som kan skapa sina egna grupper. Du kan bara ange personer för att göra en annan gruppmedlem till gruppägare.

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
