---
title: "Konfigurera Azure Active Directory för hantering av programåtkomst via självbetjäning| Microsoft Docs"
description: "Med grupphantering via självbetjäning kan användarna skapa och hantera säkerhetsgrupper eller Office 365-grupper i Azure Active Directory och begära medlemskap i säkerhetsgrupper eller Office 365-grupper"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 904d5c70-c34a-46c4-a9a7-d1efecf4821c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/3/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 4bab9f44d1c91f05618ea510b83beb06540429f2
ms.openlocfilehash: be84686351255585c3484f5ab8dad37b92462e2b


---
# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>Konfigurera Azure Active Directory för grupphantering via självbetjäning
Med självbetjäningsgrupphantering kan du skapa och hantera säkerhetsgrupper eller Office 365-grupper i Azure Active Directory (Azure AD). Användare kan även begära säkerhetsgruppsmedlemskap eller Office 365-gruppmedlemskap, och sedan kan gruppens ägare godkänna eller neka medlemskap. På så vis kan den dagliga kontrollen av gruppmedlemskap delegeras till dem som förstår medlemskapets affärskontext. Funktioner för grupphantering via självbetjäning är bara tillgängliga för säkerhetsgrupper och Office 365-grupper, men inte för e-postaktiverade säkerhetsgrupper eller distributionslistor.

Grupphantering via självbetjäning består för närvarande av två viktiga scenarier: delegerad grupphantering och grupphantering via självbetjäning.

* **Delegerad grupphantering**
   Ett exempel är en administratör som hanterar åtkomsten till ett SaaS-program som hennes företag använder. Eftersom det börjar bli svårt att hantera dessa åtkomstbehörigheter ber administratören företagsägaren att skapa en ny grupp. Administratören tilldelar åtkomst för programmet till den nya gruppen och lägger till alla personer som redan har åtkomst till programmet till gruppen. Företagsägaren kan sedan lägga till fler användare, som automatiskt tilldelas programmet. Företagsägaren behöver inte vänta på administratören för att kunna hantera åtkomst för användarna. Om administratören tilldelar en chef samma behörighet i en annan affärsgrupp, så kan den personen även hantera sina egna användare åtkomst. Varken företagsägaren eller chefen kan visa eller hantera varandras användare. Administratören kan fortfarande se alla användare som har åtkomst till programmet och blockera behörigheten om det behövs.
* **Grupphantering via självbetjäning**
   Ett exempel på det här scenariot är två användare som båda har SharePoint Online-platser som de har konfigurerat oberoende av varandra. De vill ge varandras grupper åtkomst till sina respektive webbplatser. De kan åstadkomma detta genom att först skapa en grupp i Azure AD och därefter var och en välja samma grupp i SharePoint Online, vilket ger dem åtkomst till varandras webbplatser. När någon vill ha åtkomst begär de det från åtkomstpanelen och efter godkännande får de automatiskt åtkomst till båda SharePoint Online-platserna. Senare beslutar den ena av dem att alla användare som har åtkomst till webbplatsen även ska få åtkomst till ett visst SaaS-program. SaaS-programmets administratör kan lägga till åtkomstbehörighet för programmet till SharePoint Online-webbplatsen. När det är gjort kommer alla förfrågningar som han godkänner att ge åtkomst till de två SharePoint Online-webbplatserna och även till SaaS-programmet.

## <a name="making-a-group-available-for-end-user-self-service"></a>Göra en grupp tillgänglig för självbetjäning av slutanvändare
1. Öppna din Azure AD-katalog i den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Gå till fliken **Konfigurera** och ställ in **Delegerad grupphantering** på Aktiverad.
3. Ställ in **Användare kan skapa säkerhetsgrupper** eller **Användare kan skapa Office-grupper** på Aktiverad.

När **Användare kan skapa säkerhetsgrupper** är aktiverat kan alla användare i katalogen skapa nya säkerhetsgrupper och lägga till medlemmar i dessa grupper. Dessa nya grupper visas även på åtkomstpanelen för alla andra användare. Om gruppens principinställningar så tillåter kan andra användare skapa förfrågningar om att ansluta till dessa grupper. Om du har inaktiverat **Användare kan skapa säkerhetsgrupper** så kan användarna inte skapa grupper eller ändra de befintliga grupper som de äger. De kan dock fortfarande hantera medlemskap i dessa grupper och godkänna förfrågningar från andra användare om att ansluta till grupperna.

Genom **Användare som kan använda självbetjäning för säkerhetsgrupper** kan du även få mer detaljerad åtkomstkontroll över dina användares grupphantering via självbetjäning. När **Användare kan skapa grupper** är aktiverat kan alla användare i katalogen skapa nya grupper och lägga till medlemmar i dessa grupper. Genom att även ange **Användare som kan använda självbetjäning för säkerhetsgrupper** till Vissa begränsar du grupphanteringen till endast en begränsad grupp användare. När du har angett den här växeln till Vissa måste du lägga till användare i gruppen SSGMSecurityGroupsUsers innan de kan skapa nya grupper och lägga till medlemmar i dem. Om du väljer Alla för **Användare som kan använda självbetjäning för säkerhetsgrupper** kan alla användare i katalogen skapa nya grupper.

Du kan också använda rutan **Grupp som kan använda självbetjäning för säkerhetsgrupper** om du vill ange ett eget namn för en grupp vars medlemmar kan använda självbetjäning.

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Vad är Azure Active Directory?](active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Feb17_HO2-->


