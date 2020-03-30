---
title: Oväntad ansökan i min programlista | Microsoft-dokument
description: Så här visar du alla program i din klientorganisation och förstår hur program visas i listan Alla program under Företagsprogram
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1cb4eeb52d0680695bda266ad1a563b2ef5ee02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781101"
---
# <a name="unexpected-application-in-my-applications-list"></a>Oväntat program i min programlista

Den här artikeln hjälper dig att förstå hur program visas i listan **Alla program** under **Enterprise Applications**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Så här ser du alla program i din klientorganisation

Om du vill se alla program i din klientorganisation måste du använda **filterkontrollen** för att visa **alla program** under listan **Alla program.** Följ de här stegen:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på **Alla program om** du vill visa en lista över alla dina program.

6.  Klicka på använd **filterkontrollen** högst upp i **listan Alla program**.

7.  Ange alternativet **Visa** till alla **program i** **filterfönstret.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Varför visas ett visst program i listan över alla program?

När du filtreras till **Alla program**visas alla program **i listan** Alla **program** varje tjänsthuvudnamn i din klientorganisation. Huvudobjekt för tjänsten kan visas i den här listan på olika sätt:

1. När du lägger till ett program från programgalleriet, inklusive:

   1. **Azure AD Gallery Applications** – Ett program som har förintegrerats för enkel inloggning med Azure AD

   2. **Programproxyprogram** – Ett program som körs i din lokala miljö och som du vill tillhandahålla säker enkel inloggning på externt

   3. **Skräddarsydda program** – Ett program som din organisation vill utveckla på Azure AD Application Development Platform, men som kanske inte finns ännu

   4. **Icke-Galleri Applikationer** - Ta med egna program! Alla webblänkar du vill ha, eller ett program som återger ett användarnamn och lösenordsfält, stöder SAML- eller OpenID Connect-protokoll eller stöder SCIM som du vill integrera för enkel inloggning med Azure AD.

2. När du registrerar dig för eller loggar<sup>rd</sup> in på ett tredjepartsprogram som är integrerat med Azure Active Directory. Ett exempel är [Smartsheet](https://app.smartsheet.com/b/home) eller [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. När du registrerar dig för eller lägger till en licens till en användare eller en grupp i ett program från första part, till exempel [Microsoft Office 365](https://products.office.com/)

4. När du lägger till en ny programregistrering genom att skapa ett anpassat program med hjälp av [programregistret](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. När du lägger till en ny programregistrering genom att skapa ett anpassat program med hjälp av [portalen V2.0 Application Registration](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. När du lägger till ett program som du utvecklar med Visual Studios [ASP.net autentiseringsmetoder](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) eller [Anslutna tjänster](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7. När du skapar ett huvudobjekt för tjänsten med [Azure AD PowerShell-modulen](/powershell/azure/install-adv2?view=azureadps-2.0)

8. När du [samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) som administratör att använda data i din klientorganisation

9. När en [användare samtycker till ett program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) att använda data i din klientorganisation

10. När du aktiverar vissa tjänster som lagrar data i din klientorganisation. Ett exempel är Lösenordsåterställning, som modelleras som en tjänsthuvudnamn för att lagra din princip för återställning av lösenord på ett säkert sätt.

Om du vill ha mer information om hur appar läggs till i katalogen läser du [Hur och varför program läggs till i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Jag vill ta bort en viss användares eller grupps tilldelning till ett program

Om du vill ta bort en användar- eller grupptilldelning till ett program följer du stegen i artikeln [Ta bort en användare eller grupp från en företagsapp i Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Jag vill inaktivera all åtkomst till ett program för varje användare

Om du vill inaktivera alla användarloggningar till ett program följer du stegen i artikeln [Inaktivera användarloggningar för en företagsapp i Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Jag vill ta bort ett program helt

Så här tar du **bort ett program:**

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Markera det program som du vill ta bort.

7. När programmet har läses in klickar du på **Ikonen Ta bort** från det övre programmets **översiktsfönster.**

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Jag vill inaktivera alla framtida åtgärder för användarsamtycke till alla program

Om du inaktiverar användarens medgivande för hela katalogen hindra slutanvändare från att samtycka till något program. Administratörer kan fortfarande medgivande för användarens räkning. Om du vill veta mer om programmets medgivande och varför du kanske eller kanske inte vill medgivande läser [du Förstå användarens och administratörens samtycke](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Så här inaktiverar du **alla framtida åtgärder för användarmedgivande i hela katalogen:**

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Användarinställningar**.

6.  Inaktivera alla framtida åtgärder för användargodkännande genom att ange **att användare kan tillåta att appar kan komma åt sina data** växla till **Nej** och klicka på knappen **Spara.**

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
