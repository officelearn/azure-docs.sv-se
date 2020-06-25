---
title: Oväntat program i listan Mina program | Microsoft Docs
description: Visa alla program i din klient organisation och förstå hur program visas i listan Alla program under företags program
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97eef1f331d8e6965e378d6a76cfa7a0c50feb9a
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85317474"
---
# <a name="unexpected-application-in-my-applications-list"></a>Oväntat program i listan Mina program

Den här artikeln hjälper dig att förstå hur program visas i listan **alla program** under **företags program**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Så här visar du alla program i din klient organisation

Om du vill se alla program i din klient organisation måste du använda **filter** kontrollen för att visa **alla program** i listan **alla program** . Följ de här stegen:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på **alla program** om du vill visa en lista över alla dina program.

6.  Klicka på alternativet Använd **filter** kontrollen längst upp i **listan Alla program**.

7.  I fönstret **filter** ställer du in alternativet **Visa** för **alla program.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Varför visas ett särskilt program i listan Mina program?

När de filtreras till **alla program**, visar **listan** **alla program** alla tjänst huvud namn objekt i din klient organisation. Tjänstens huvud objekt kan visas i den här listan på olika sätt:

1. När du lägger till ett program från program galleriet, inklusive:

   1. **Azure AD Gallery-program** – ett program som har integrerats för enkel inloggning med Azure AD

   2. Program för **programproxy** – ett program som körs i din lokala miljö som du vill tillhandahålla säker enkel inloggning till externt

   3. **Anpassade program** – ett program som din organisation vill utveckla på Azure AD-program utvecklings plattformen, men som kanske inte finns ännu

   4. **Program som inte är gallerier** – ta med dina egna program! Alla webb länkar som du vill använda, eller ett program som återger ett användar namn och lösen ord, stöder SAML-eller OpenID Connect-protokoll eller stöder SCIM som du vill integrera för enkel inloggning med Azure AD.

2. När du registrerar dig för eller loggar in på, är ett 3-program<sup>för fjärr skrivbord</sup> integrerat med Azure Active Directory. Ett exempel är [Smartsheet](https://app.smartsheet.com/b/home) eller [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. När du registrerar dig för eller lägger till en licens till en användare eller en grupp till ett program i den första parten, t. ex. [Microsoft Office 365](https://products.office.com/)

4. När du lägger till en ny program registrering genom att skapa ett anpassat program som har utvecklats med [program registret](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. När du lägger till en ny program registrering genom att skapa ett anpassat program som har utvecklats med [v 2.0-program registrerings portalen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. När du lägger till ett program som du utvecklar med hjälp av Visual Studios [ASP.net-verifieringsmetoder](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) eller [anslutna tjänster](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)

7. När du skapar ett huvud namns objekt för tjänsten med hjälp av [Azure AD PowerShell-modulen](/powershell/azure/install-adv2?view=azureadps-2.0)

8. När du [samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) som administratör för att använda data i din klient organisation

9. När en [användare samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) för att använda data i din klient organisation

10. När du aktiverar vissa tjänster som lagrar data i din klient organisation. Ett exempel är lösen ords återställning, som modelleras som tjänstens huvud namn för att lagra din princip för lösen ords återställning på ett säkert sätt.

Om du vill ha mer information om hur appar läggs till i din katalog läser du [hur och varför program läggs till i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort tilldelningen för en specifik användare eller grupp till ett program

Om du vill ta bort en användare eller grupp tilldelning till ett program följer du stegen i artikeln [ta bort en användare eller grupp tilldelning från en företags app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikeln.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användar inloggningar till ett program följer du stegen i avsnittet [Inaktivera användar inloggningar för en Enterprise-App i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artikeln.

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

Följ dessa steg om du vill **ta bort ett program**:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill ta bort.

7. När programmet har lästs in klickar du på **ta bort** ikon i det översta programmets **översikts** fönster.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida åtgärder för användar medgivande till alla program

Om du inaktiverar användar medgivande för hela katalogen kan slutanvändarna inte godkänna något program. Administratörer kan fortfarande godkänna användarens räkning. Om du vill veta mer om program medgivande och varför du kanske inte vill godkänna, Läs [förstå användar-och administratörs medgivande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Följ dessa steg om du vill **inaktivera alla framtida användar medgivande åtgärder i hela katalogen**:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **användar inställningar**.

6.  Inaktivera alla framtida medgivande åtgärder genom att ange att **användarna ska kunna tillåta att appar kommer åt sina data** genom att växla till **Nej** och klicka på knappen **Spara** .

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
