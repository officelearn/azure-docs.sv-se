---
title: Det gick inte att logga in på Azure AD Gallery-appen som kon figurer ATS för SSO | Microsoft Docs
description: Så här felsöker du problem med ett Azure AD Gallery-program som är konfigurerat för enkel inloggning med lösen ord.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eebde3a7c6163b7faf92be193fe442cd5b74d2c
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2020
ms.locfileid: "84759121"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Inloggnings problem med en Azure AD Gallery-app som kon figurer ATS för SSO

Åtkomst panelen är en webbaserad portal. Det gör det möjligt för användare som har Azure Active Directory (Azure AD) arbets-eller skol konton att komma åt molnbaserade appar som de har behörighet för. Användare som har Azure AD-versioner kan också använda funktioner för självbetjänings grupp och app-hantering via åtkomst panelen.

Åtkomst panelen är separat från Azure Portal. Användare behöver ingen Azure-prenumeration för att använda åtkomst panelen.

Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomst panelen måste åtkomst panels tillägget vara installerat i webbläsaren. Fil namns tillägget hämtas automatiskt när du väljer en app som är konfigurerad för lösenordsbaserad SSO.

## <a name="browser-requirements-for-access-panel"></a>Webb läsar krav för åtkomst panelen

Åtkomst panelen kräver en webbläsare som stöder Java Script och har CSS aktiverat.

Följande webbläsare har stöd för lösenordsbaserad SSO:

- Internet Explorer 8, 9, 10 och 11 i Windows 7 eller senare

- Chrome på Windows 7 eller senare eller på MacOS X eller senare

- Firefox 26,0 eller senare i Windows XP SP2 eller senare eller på Mac OS X 10,6 eller senare

>[!NOTE]
>Det lösenordsbaserade SSO-tillägget blir tillgängligt för Microsoft Edge i Windows 10 när stöd för webb läsar tillägg har lagts till i Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Installera åtkomst panelens webb läsar tillägg

Följ de här stegen:

1. Öppna [åtkomst panelen](https://myapps.microsoft.com) i en webbläsare som stöds och logga in som en användare i Azure AD.

2. Välj en lösen ords-SSO-aktiverad app i åtkomst panelen.

3. När du uppmanas väljer du **Installera nu**.

4. Du dirigeras till en nedladdnings länk baserat på din webbläsare. Välj **Lägg till** för att installera webb läsar tillägget.

5. Om du uppmanas väljer du **Aktivera** eller **Tillåt**.

6. Starta om webbläsaren efter installationen.

7.  Logga in på åtkomst panelen och se om du kan starta dina appar för inloggning med lösen ord.

Du kan också hämta tilläggen för Chrome och Firefox direkt genom följande länkar:

-   [Tillägg för Chrome Access panel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Åtkomst panels tillägg för Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grup princip för Internet Explorer

Du kan konfigurera en grup princip som gör att du kan fjärrinstallera åtkomst panels tillägget för Internet Explorer på användarnas datorer.

Följande är förutsättningarna:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) måste konfigureras och användarnas datorer måste vara anslutna till din domän.

-   Du har behörighet att redigera inställningar för att redigera grupprincip-objektet (GPO). Som standard har medlemmar i följande säkerhets grupper den här behörigheten: domän administratörer, företags administratörer och grupprincip Skapare ägare. [Läs mer](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Information om hur du konfigurerar grup principen och distribuerar den till användare finns i [så här distribuerar du åtkomst panels tillägget för Internet Explorer med hjälp av grup princip](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Felsöka åtkomst panelen i Internet Explorer

Information om hur du konfigurerar tillägget finns i [Felsöka åtkomst panelens tillägg för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Konfigurera SSO för lösen ord för en Azure AD-Galleri-app

Om du vill konfigurera en app från Azure AD-galleriet måste du göra följande saker:

-   Lägg till appen från Azure AD-galleriet
-   [Konfigurera appen för enkel inloggning med lösen ord](#configure-the-app-for-password-sso)
-   [Tilldela användare till appen](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Lägg till appen från Azure AD-galleriet

Följ de här stegen:

1. Öppna [Azure Portal](https://portal.azure.com) och logga in som global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigerings fönstret på vänster sida för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **Lägg till** i det övre högra hörnet i fönstret **företags program** .

6. I avsnittet **Lägg till från galleriet** , anger du namnet på appen i rutan **Ange ett namn** .

7. Välj den app som du vill konfigurera för SSO.

8. *Valfritt:* Innan du lägger till appen kan du ändra dess namn i rutan **namn** .

9. Klicka på **Lägg till** för att lägga till appen.

   Efter en kort fördröjning kommer du att kunna se appens konfigurations fönster.

### <a name="configure-the-app-for-password-sso"></a>Konfigurera appen för inloggning med lösen ord

Följ de här stegen:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigerings fönstret på vänster sida för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **alla program** om du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filter** kontrollen längst upp i **listan Alla program**. Ange alternativet **Visa** som "alla program".

6. Välj den app som du vill konfigurera för SSO.

7. När appen har lästs in väljer du **enkel inloggning** i rutan till vänster i appen.

8. Välj **lösenordsbaserad inloggnings** läge.

9. Tilldela användare till appen.

10. Du kan också ange autentiseringsuppgifter för användare. (Annars uppmanas användarna att ange autentiseringsuppgifter när appen startas.) Det gör du genom att välja rader för användarna. Välj sedan **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord.

### <a name="assign-users-to-the-app"></a>Tilldela användare till appen

Följ dessa steg om du vill tilldela användare till en app direkt:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som global administratör.

2. Välj **alla tjänster** i navigerings perspektivet på vänster sida för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory**.

4. Välj **företags program** i navigerings fönstret i Azure AD.

5. Välj **alla program** om du vill visa en lista över dina program.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filter** kontrollen längst upp i **listan Alla program**. Ange alternativet **Visa** som "alla program".

6. I listan väljer du den app som du vill tilldela en användare till.

7. När programmet har lästs in väljer **du användare och grupper** i appens navigerings fönster på den vänstra sidan.

8. Välj **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9. Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. I rutan **Sök efter namn eller e-postadress** anger du det fullständiga namnet eller e-postadressen för den användare som du vill tilldela.

11. Hovra över användaren i listan. Markera kryss rutan bredvid användarens profil bild eller logo typ för att lägga till användaren i den **markerade** listan.

12. *Valfritt:* Om du vill lägga till en annan användare skriver du ett annat namn eller en e-postadress i rutan **Sök efter namn eller e-** postadress och markerar sedan kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du är klar med att välja användare klickar du på **Välj** för att lägga till dem i listan över användare och grupper som är tilldelade till appen.

14. *Valfritt:* Klicka på **Välj roll** i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de användare som du har valt.

15. Välj **tilldela** för att tilldela appen till de valda användarna.

    Efter en kort fördröjning kommer användarna att kunna komma åt dessa appar från åtkomst panelen.

## <a name="request-support"></a>Support för begäran 
Om du får ett fel meddelande när du konfigurerar SSO och tilldelar användare, öppnar du ett support ärende. Inkludera så mycket som möjligt av följande information:

-   Korrelations fel-ID
-   UPN (användarens e-postadress)
-   TenantID
-   Typ av webbläsare
-   Tidszon och tid/tidsram när felet uppstod
-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
