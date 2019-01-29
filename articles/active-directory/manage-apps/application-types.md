---
title: Ett oväntat program i listan över program | Microsoft Docs
description: Hur du visar alla program i din klient och förstå hur program visas i listan alla program under företagsprogram
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 441821ae422fb4cbddbca5b4c30d68484ce6bc1f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55186985"
---
# <a name="unexpected-application-in-my-applications-list"></a>Ett oväntat program i listan över program

Den här artikeln hjälper dig att förstå hur program visas i din **alla program** listan **företagsprogram**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Hur du visar alla program i din klient

Om du vill se alla program i din klient, måste du använda den **Filter** så att **alla program** under den **alla program** lista. Följ de här stegen:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

6.  Klicka på användningen den **Filter** kontroll högst upp på den **listan över alla program**.

7.  På den **Filter** fönstret, ange den **visa** alternativet att **alla program.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Varför visas ett visst program i listan över alla program?

När filtrerat till **alla program**, **alla program** **lista** visar alla objekt för tjänstens huvudnamn i din klient. Objekt för tjänstens huvudnamn kan visas i listan på olika sätt:

1.  När du lägger till alla program från programgalleriet, inklusive:

   1. **Azure AD-Galleriprogram** – ett program som har varit förintegrerade för enkel inloggning med Azure AD

   2. **Application Proxy-program** – ett program som körs i din lokala miljö som du vill ge säker enkel inloggning till externt

   3. **Egenutvecklat program** – ett program som din organisation vill utveckla på det Azure AD plattform, men som kanske inte finns ännu

   4. **Icke-Galleriprogram** – Använd dina egna program! Alla webblänk som du vill eller alla program som återger ett fält för användarnamn och lösenord, har stöd för SAML- eller OpenID Connect-protokoll eller stöder SCIM som du vill integrera för enkel inloggning med Azure AD.

2.  När du registrerar dig för eller logga in på en 3<sup>rd</sup> partsprogram integrerad med Azure Active Directory. Ett exempel är [Smartsheet](https://app.smartsheet.com/b/home) eller [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  När registreringen eller att lägga till en licens till en användare eller grupp i ett partsprogram för första, som [Microsoft Office 365](https://products.office.com/)

4.  När du lägger till en ny programregistrering genom att skapa ett egenutvecklat program som använder den [programregistret](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5.  När du lägger till en ny programregistrering genom att skapa ett egenutvecklat program som använder den [V2.0 programregistreringsportalen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal)

6.  När du lägger till ett program du håller på att utveckla med Visual Studio [ASP.net autentiseringsmetoder](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) eller [Connected Services](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7.  När du skapar ett objekt med den [Azure AD PowerShell-modulen](/powershell/azure/install-adv2?view=azureadps-2.0)

8.  När du [samtycka till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) som administratör för att använda data i din klient

9.  När en [användare godkänner ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) att använda data i din klient

10. När du aktiverar vissa tjänster som lagrar data i din klient. Ett exempel är återställning av lösenord, som modellerats som princip för återställning av tjänstens huvudnamn för att lagra lösenordet på ett säkert sätt.

Om du vill ha mer information om hur appar läggs till i din katalog, läsa [hur och varför program läggs till Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort en specifik användare eller grupp-tilldelning till ett program

Ta bort en användare eller grupptilldelning till ett program genom att följa instruktionerna i den [ta bort en användare eller grupp från en företagsapp i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikeln.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användarinloggningar till ett program följer du stegen i den [inaktivera användarinloggningar för en företagsapp i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artikeln.

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

Att **ta bort ett program**, Följ dessa steg:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill ta bort.

7.  När programmet har lästs in klickar du på **ta bort** ikonen från övre programmets **översikt** fönstret.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida medgivande användaråtgärder för program

Inaktiverar användarens medgivande för hela katalogen förhindra slutanvändare från principer för alla program. Administratörer kan fortfarande ge samtycke på användarens behalves. Mer information om programtillåtelser och varför du kanske eller kanske inte vill ge samtycke, läsa [Förstå användar- och administratörens godkännande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Att **inaktivera alla framtida medgivande användaråtgärder i hela katalogen**, Följ dessa steg:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **användarinställningar**.

6.  Inaktivera alla framtida medgivande användaråtgärder genom att ange den **användare kan bevilja appar åtkomst till sina data** växla till **nr** och klicka på den **spara** knappen.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
