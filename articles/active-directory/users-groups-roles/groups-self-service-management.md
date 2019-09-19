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
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097703"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Konfigurera hantering av självbetjänings grupper i Azure Active Directory 

Du kan göra det möjligt för användare att skapa och hantera egna säkerhets grupper eller Office 365-grupper i Azure Active Directory (Azure AD). Ägare av gruppen kan godkänna eller neka medlemskaps begär Anden och kan delegera kontrollen över grupp medlemskap. Funktioner för grupp hantering via självbetjäning är inte tillgängliga för e-postaktiverade säkerhets grupper eller distributions listor.

## <a name="self-service-group-membership-defaults"></a>Standardinställningar för grupp medlemskap för självbetjäning

När säkerhets grupper skapas i Azure Portal eller med hjälp av Azure AD PowerShell kan endast gruppens ägare uppdatera medlemskap. Säkerhets grupper som skapas i [åtkomst panelen](https://account.activedirectory.windowsazure.com/r#/joinGroups) och alla Office 365-grupper är tillgängliga för att ansluta till alla användare, oavsett om de är godkända eller automatiskt godkända. I åtkomst panelen kan du ändra medlemskaps alternativ när du skapar gruppen.

Grupper som skapats i | Standard beteende för säkerhets grupp | Standard beteende för Office 365-grupper
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Endast ägare kan lägga till medlemmar<br>Synlig men inte tillgänglig för anslutning i åtkomst panelen | Öppna för att ansluta till alla användare
[Azure Portal](https://portal.azure.com) | Endast ägare kan lägga till medlemmar<br>Synlig men inte tillgänglig för anslutning i åtkomst panelen<br>Ägaren tilldelas inte automatiskt när gruppen skapas | Öppna för att ansluta till alla användare
[Åtkomst panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Öppna för att ansluta till alla användare<br>Medlemskaps alternativ kan ändras när gruppen skapas | Öppna för att ansluta till alla användare<br>Medlemskaps alternativ kan ändras när gruppen skapas

## <a name="self-service-group-management-scenarios"></a>Självbetjänings grupp hanterings scenarier

* **Delegerad grupphantering** Ett exempel är en administratör som hanterar åtkomsten till ett SaaS-program som hennes företag använder. Eftersom det börjar bli svårt att hantera dessa åtkomstbehörigheter ber administratören företagsägaren att skapa en ny grupp. Administratören tilldelar åtkomst till programmet till den nya gruppen och lägger till gruppen alla personer som redan har åtkomst till programmet. Företagsägaren kan sedan lägga till fler användare, som automatiskt tilldelas programmet. Företagsägaren behöver inte vänta på administratören för att kunna hantera åtkomst för användarna. Om administratören ger samma behörighet till en chef i en annan affärs grupp, kan den personen även hantera åtkomsten för sina egna grupp medlemmar. Varken företags ägaren eller chefen kan visa eller hantera vart och ett av grupp medlemskapen. Administratören kan fortfarande se alla användare som har åtkomst till programmet och blockera behörigheten om det behövs.
* **Grupphantering via självbetjäning** Ett exempel på det här scenariot är två användare som båda har SharePoint Online-platser som de har konfigurerat oberoende av varandra. De vill ge varandras grupper åtkomst till sina respektive webbplatser. De kan åstadkomma detta genom att först skapa en grupp i Azure AD och därefter var och en välja samma grupp i SharePoint Online, vilket ger dem åtkomst till varandras webbplatser. När någon vill ha åtkomst begär de det från åtkomstpanelen och efter godkännande får de automatiskt åtkomst till båda SharePoint Online-platserna. Senare beslutar den ena av dem att alla användare som har åtkomst till webbplatsen även ska få åtkomst till ett visst SaaS-program. SaaS-programmets administratör kan lägga till åtkomstbehörighet för programmet till SharePoint Online-webbplatsen. När det är gjort kommer alla förfrågningar som han godkänner att ge åtkomst till de två SharePoint Online-webbplatserna och även till SaaS-programmet.

## <a name="make-a-group-available-for-user-self-service"></a>Göra en grupp tillgänglig för självbetjäning av användare

1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Användare och grupper** och sedan **Gruppinställningar**.
3. Ställ in **Självbetjäning för grupphantering aktiverad** på **Ja**.
4. Ställ in **Användare kan skapa säkerhetsgrupper** eller **Användare kan skapa Office 365-grupper** på **Ja**.
   * När inställningarna är aktiverade kan alla användare i katalogen skapa nya säkerhetsgrupper och lägga till medlemmar i dessa grupper. Dessa nya grupper visas även på åtkomstpanelen för alla andra användare. Om gruppens principinställningar så tillåter kan andra användare skapa förfrågningar om att ansluta till dessa grupper. 
   * När de här inställningarna är avaktiverade kan användarna inte skapa grupper eller ändra de befintliga grupper som de äger. De kan dock fortfarande hantera medlemskap i dessa grupper och godkänna förfrågningar från andra användare om att ansluta till grupperna.

Genom **Användare som kan hantera säkerhetsgrupper** och **Användare som kan hantera Office 365-grupper** kan du få mer detaljerad åtkomstkontroll över dina användares grupphantering via självbetjäning. När **Användare kan skapa grupper** är aktiverat kan alla användare i klientorganisationen skapa nya grupper och lägga till medlemmar i dessa grupper. Du kan inte ange personer som kan skapa sina egna grupper. Du kan bara ange individer för att göra en annan grupp medlem till en grupp ägare.

Genom att ställa in **användare som kan använda självbetjäning för säkerhets grupper** och **användare som kan hantera Office 365-grupper** till **Ja**, så kan du aktivera alla användare i din klient organisation för att skapa nya grupper.

Du kan också använda **Grupp som kan hantera säkerhetsgrupper** eller **Grupp som kan hantera Office 365-grupper** för att ange en enda grupp vars medlemmar ska kunna använda självbetjäning.

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
