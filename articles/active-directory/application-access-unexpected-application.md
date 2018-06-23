---
title: Oväntat programmet i listan med mina program | Microsoft Docs
description: Hur du ser alla program i din klient och förstå hur program visas i listan alla program under företagsprogram
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 6d8c1a251bcc4a7c0bb736df64c4c701a2fc748a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333883"
---
# <a name="unexpected-application-in-my-applications-list"></a>Oväntat programmet i listan med mina program

Den här artikeln hjälper dig att förstå hur program visas i din **alla program** listan **företagsprogram**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Hur man ser alla program i din klient

Om du vill se alla program i din klient, måste du använda den **Filter** kontroll för att kunna visa **alla program** under den **alla program** lista. Följ de här stegen:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

6.  Klicka på användningen i **Filter** kontrollen längst upp i den **listan med alla program**.

7.  På den **Filter** ställer du in den **visa** att **alla program.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Varför visas ett visst program i programlistan alla?

När filtrerats till **alla program**, **alla program** **listan** visas alla objekt för tjänstens huvudnamn i din klient. Tjänstens huvudnamn objekt som kan visas i listan på olika sätt:

1.  När du lägger till ett program från programgalleriet, inklusive:

   1. **Azure AD-galleriet program** – ett program som har varit förintegrerade för enkel inloggning med Azure AD

   2. **Application Proxy program** – ett program som körs i din lokala miljö som du vill ge säker enkel inloggning till externt

   3. **Anpassad utvecklat program** – ett program som din organisation vill utveckla på den Azure AD plattform, men som kanske inte finns ännu

   4. **Icke-galleriet program** – ta med egna program! En länk som du vill eller alla program som visas med ett användarnamn och lösenord fält har stöd för SAML- eller OpenID Connect-protokoll och stöder SCIM som du vill integrera för enkel inloggning med Azure AD.

2.  När du registrerar dig för eller loggar in på en 3<sup>rd</sup> partsprogram integrerat med Azure Active Directory. Ett exempel är [Smartsheet](https://app.smartsheet.com/b/home) eller [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  När registreringen eller att lägga till en licens till en användare eller grupp i ett partsprogram för första, som [Microsoft Office 365](http://products.office.com/)

4.  När du lägger till en ny programregistrering genom att skapa en anpassad utvecklat program med hjälp av den [registret för programmet](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5.  När du lägger till en ny programregistrering genom att skapa en anpassad utvecklat program med hjälp av den [V2.0 programregistrering portal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal)

6.  När du lägger till ett program du utvecklar med Visual Studio [ASP.net autentiseringsmetoder](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) eller [anslutna tjänster](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7.  När du skapar en huvudobjekt med hjälp av [Azure AD PowerShell-modulen](/powershell/azure/install-adv2?view=azureadps-2.0)

8.  När du [samtycker till att ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) som administratör för att kunna använda data i din klient

9.  När en [användaren godkänner till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) använda data i din klient

10. När du aktiverar vissa tjänster som lagrar data i din klient. Ett exempel är för återställning av lösenord, som modellerats som ett huvudnamn för tjänsten att lagra lösenordet Återställ principen på ett säkert sätt.

Om du vill ha mer information om hur appar har lagts till i din katalog läsa [hur och varför program läggs till Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort en specifik användare eller grupp tilldelning till ett program

Ta bort en användare eller grupptilldelning till ett program genom att följa instruktionerna i den [ta bort en användare eller grupp från en enterprise-app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikel.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användarinloggningar till ett program följer du stegen i den [inaktivera användarinloggningar för en enterprise-app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artikel.

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

Att **ta bort ett program**, gör du följande:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill ta bort.

7.  När programmet läses in klickar du på **ta bort** ikon från översta programmet **översikt** fönstret.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida användarens medgivande åtgärder för alla program

Inaktiverar användargodkännande för hela katalogen att förhindra att slutanvändare principer för alla program. Administratörer kan fortfarande medgivande på användarens behalves. Lär dig mer om programmet medgivande och varför du kanske eller kanske inte vill godkänna, läsa [förstå användare och admin medgivande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Att **inaktivera alla framtida användarens medgivande åtgärder i hela katalogen**, gör du följande:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **användarinställningar**.

6.  Inaktivera alla framtida användarens medgivande åtgärder genom att ange den **användare kan Tillåt appar att komma åt sina data** växla till **nr** och klicka på den **spara** knappen.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](manage-apps/what-is-application-management.md)
