---
title: Publicera appar med Azure AD Application Proxy | Microsoft Docs
description: "Publicera lokala program till molnet med Azure AD Application Proxy på den klassiska portalen."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 96490c0d060fe5486a7235a5aa76380c8d9b5d4f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicera program med Azure AD Application Proxy

> [!div class="op_single_selector"]
> * [Azure Portal](application-proxy-publish-azure-portal.md)
> * [Klassisk Azure-portal](active-directory-application-proxy-publish.md)

Azure AD-programproxyn hjälper dig att stödja fjärranvändare genom att publicera lokala program som kan nås via Internet. I det här läget bör du redan ha [aktiverat programproxyn i den klassiska Azure-portalen](active-directory-application-proxy-enable.md). Den här artikeln beskriver hur du publicerar program som körs i nätverket och hur du tillhandahåller säker fjärråtkomst utanför nätverket. När du är klar med den här artikeln kan du konfigurera programmet med personlig information eller säkerhetskrav.

> [!NOTE]
> Application Proxy är en funktion som bara är tillgänglig om du har uppgraderat till Premium- eller Basic-versionen av Azure Active Directory. Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md). Om du vill använda Application Proxy kan du [Publicera program i Azure Portal](application-proxy-publish-azure-portal.md).

## <a name="publish-an-app-using-the-wizard"></a>Publicera en app med hjälp av guiden
1. Logga in som administratör på [den klassiska Azure-portalen](https://manage.windowsazure.com/).
2. Gå till Active Directory och välj den katalog där du aktiverade Application Proxy.
   
    ![Active Directory – ikon](./media/active-directory-application-proxy-publish/ad_icon.png)
3. Klicka på fliken **Program** och sedan på knappen **Lägg till** längst ned på skärmen
   
    ![Lägga till ett program](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
4. Välj **Publicera ett program som ska vara tillgängligt utanför ditt nätverk**.
   
    ![Publicera ett program som ska vara tillgängligt utanför ditt nätverk](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)
5. Ange följande information om ditt program:
   
   * **Namn**: Programmets användarvänliga namn. Det måste vara unikt i din katalog.
   * **Intern URL**: Adressen som Application Proxy Connector använder för att komma åt programmet inifrån ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika webbplatser på samma server och ge varje webbplats sitt eget namn och sina egna åtkomstregler.
     
     > [!TIP]
     > Om du publicerar en sökväg, så se till att den innehåller alla bilder, skript och formatmallar som krävs för ditt program. Om din app t.ex. finns på https://yourapp/app och använder bilder som finns på https://yourapp/media, så bör du publicera https://yourapp/ som sökväg.
     > 
     > 
   * **Förautentiseringsmetoden**: Hur programproxyn verifierar användare innan de ges åtkomst till ditt program. Välj något av alternativen i den nedrullningsbara menyn.
     
     * Azure Active Directory: Programproxyn omdirigerar användarna till att logga in med Azure AD, som autentiserar deras katalog- och programbehörigheter.
     * Genomströmning: Användarna behöver inte autentisera sig för att få åtkomst till programmet.
     
     ![Egenskaper för program](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  
6. Slutför guiden genom att klicka på bockmarkeringen längst ned på skärmen. Nu har programmet definierats i Azure AD.

## <a name="assign-users-and-groups-to-the-application"></a>Tilldela användare och grupper till programmet
För att dina användare ska få åtkomst till ditt publicerade program måste du tilldela dem antingen individuellt eller i grupper. (Kom ihåg att även tilldela dig själv åtkomst.) Varje användare som du tilldelar behöver en licens för Azure Basic eller högre. Du kan tilldela licenser individuellt eller till grupper. Mer information finns i [Tilldela användare till ett program](active-directory-applications-guiding-developers-assigning-users.md). 

För appar som kräver förautentisering ger användartilldelningen behörighet att använda programmet. För appar som inte kräver förautentisering innebär användartilldelningen att användaren kan komma åt programmet via åtkomstpanelen.

1. När du har slutfört guiden Lägg till app visas sidan Snabbstart för ditt program. Du kan hantera vem som har tillgång till appen genom att välja **Användare och grupper**.
   
    ![Snabbstart för att tilldela användare för Application Proxy – skärmbild](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)
2. Sök efter specifika grupper i katalogen, eller visa alla användare. Visa sökresultatet genom att klicka på kryssmarkeringen.
   
      ![Söka efter grupper eller användare – skärmbild](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)
3. Välj alla användare eller grupper som du vill tilldela den här appen och klicka på **Tilldela**. Du ombeds att bekräfta åtgärden.

> [!NOTE]
> För appar med integrerad Windows-autentisering kan du endast tilldela användare och grupper som synkroniseras från din lokala Active Directory. Användare som loggar in med ett Microsoft-konto och gäster kan inte tilldelas appar som publiceras med Azure Active Directory Application Proxy. Se till att dina användare loggar in med autentiseringsuppgifter för samma domän som appen som du publicerar.
> 
> 

## <a name="test-your-published-application"></a>Testa ditt publicerade program
När du har publicerat programmet kan du testa det genom att navigera till den URL som du har publicerat. Kontrollera att du har åtkomst till det, att det återges korrekt och att allt fungerar som förväntat. Om det uppstår problem eller om ett felmeddelande visas, så gå till [felsökningsguiden](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Konfigurera ditt program
Du kan ändra publicerade appar eller konfigurera avancerade alternativ på sidan Konfigurera. På den här sidan kan du anpassa din app genom att ändra namnet eller genom att ladda upp en logotyp. Du kan också hantera åtkomstregler som förautentiseringsmetoden eller Multi-Factor Authentication.

![Avancerad konfiguration](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)

När du har publicerat program med hjälp av Azure Active Directory Application Proxy visas de i programlistan i Azure AD och du kan hantera dem där.

Om du inaktiverar programproxytjänsterna när du har publicerat program, så är programmen inte längre tillgängliga utanför ditt privata nätverk. Användarna kan fortfarande komma åt programmen lokalt precis som vanligt.

Om du vill visa ett program och förvissa dig om att det är tillgängligt dubbelklickar du på programnamnet. Om Application Proxy-tjänsten är inaktiverad och programmet inte är tillgängligt visas ett varningsmeddelande längst upp på skärmen.

Om du vill ta bort ett program markerar du programmet i listan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg
* [Publicera program med ditt domännamn](active-directory-application-proxy-custom-domains.md)
* [Aktivera enkel inloggning](active-directory-application-proxy-sso-using-kcd.md)
* [Aktivera villkorlig åtkomst](active-directory-application-proxy-conditional-access.md)
* [Arbeta med anspråksmedvetna program](active-directory-application-proxy-claims-aware-apps.md)

Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

