---
title: Inte att logga in till en app för Azure AD-galleriet som konfigurerats för enkel inloggning med lösenord | Microsoft Docs
description: Så här felsöker du problem med en Azure AD-galleriprogram som konfigurerats för lösenord för enkel inloggning.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190332"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Inloggningsproblem med en app för Azure AD-galleriet som konfigurerats för enkel inloggning

Åtkomstpanelen är en webbaserad portal. Det gör det möjligt för användare som har Azure Active Directory (Azure AD) arbets- eller skolkonton för att komma åt molnbaserade appar som de har behörighet för. Användare som har Azure AD-versioner kan också använda självbetjäning och app-hanteringsfunktioner via åtkomstpanelen.

Åtkomstpanelen är separat från Azure-portalen. Användarna behöver inte en Azure-prenumeration du använder åtkomstpanelen.

Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomstpanelen, måste tillägget åtkomstpanelen installeras i webbläsaren. Tillägget hämtar automatiskt när du väljer en app som är konfigurerad för lösenordsbaserad SSO.

## <a name="browser-requirements-for-access-panel"></a>Webbläsarkrav för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och CSS aktiverat.

Följande webbläsare har stöd för lösenordsbaserad SSO:

- Internet Explorer 8, 9, 10 och 11 på Windows 7 eller senare

- Chrome på Windows 7 eller senare eller i Mac OS X eller senare

- Firefox 26.0 eller senare på Windows XP SP2 eller senare eller på Mac OS X 10.6 eller senare

>[!NOTE]
>Tillägget lösenordsbaserad SSO blir tillgängliga för Microsoft Edge i Windows 10 när stödet för webbläsartillägg har lagts till i Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Installera Access Panel webbläsartillägg

Följ de här stegen:

1. Öppna [åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds och logga in som en användare i Azure AD.

2. Välj en lösenord-SSO-aktiverad app i åtkomstpanelen.

3. När du uppmanas väljer **installera nu**.

4. Du omdirigeras till en nedladdningslänk baserat på din webbläsare. Välj **Lägg till** att installera webbläsartillägget.

5. Om du uppmanas väljer **aktivera** eller **Tillåt**.

6. Starta om webbläsaren efter installationen.

7.  Logga in på åtkomstpanelen och se om du startar dina lösenord-SSO-aktiverade appar.

Du kan också direkt hämta tillägg för Chrome och Firefox via följande länkar:

-   [Chrome Access Panel-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel-tillägg](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurera en grupprincip för Internet Explorer

Du kan konfigurera en grupprincip som gör det möjligt att fjärrinstallera Access Panel-tillägg för Internet Explorer på användarnas datorer.

Det här är kraven:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) måste ställas in, och dina användares datorer måste vara ansluten till din domän.

-   Du har ”redigera inställningar” behörighet att redigera det grupprincipobjekt (GPO). Som standard har medlemmar i de följande säkerhetsgrupperna denna behörighet: Domänadministratörer, Företagsadministratörer och skapare och ägare av Grupprincip. [Läs mer](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Om du vill konfigurera en Grupprincip och distribuera till användare, se [hur du distribuerar tillägget åtkomstpanelen för Internet Explorer med hjälp av en Grupprincip](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Felsöka åtkomstpanelen i Internet Explorer

Du hittar ett verktyg för diagnostik och anvisningar för att konfigurera tillägget i [Felsök Access Panel-tillägg för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Konfigurera enkel inloggning med lösenord för en app för Azure AD-galleriet

Om du vill konfigurera en app från Azure AD-galleriet, måste du göra allt detta:

-   Lägg till appen från Azure AD-galleriet
-   [Konfigurera appen för lösenord för enkel inloggning](#configure-the-app-for-password-sso)
-   [Tilldela användare till appen](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Lägg till appen från Azure AD-galleriet

Följ de här stegen:

1. Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigeringsfönstret till vänster för att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Lägg till** i det övre högra hörnet av den **företagsprogram** fönstret.

6. I den **Lägg till från galleriet** Skriv namnet på appen i den **anger du ett namn** box.

7. Välj den app som du vill konfigurera för enkel inloggning.

8. *Valfritt:* Innan du lägger till appen, kan du ändra dess namn i den **namn** box.

9. Klicka på **Lägg till** lägger du till appen.

   Efter en liten stund kommer du att kunna se appens konfigurationsruta.

### <a name="configure-the-app-for-password-sso"></a>Konfigurera appen för enkel inloggning med lösenord

Följ de här stegen:

1. Öppna den [Azure-portalen](https://portal.azure.com/) och logga in som en global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigeringsfönstret till vänster för att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret för Azure AD.

5. Välj **alla program** att visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app som du vill använda den **Filter** kontroll högst upp på den **listan över alla program**. Ange den **visa** alternativet ”alla program”.

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har lästs in väljer **enkel inloggning** i fönstret till vänster i appen.

8. Välj **lösenordsbaserad inloggning** läge.

9. Tilldela användare till appen.

10. Du kan också ange autentiseringsuppgifter för användare. (Annars kan användare uppmanas att ange dem vid start av appen.) Gör detta genom att markera vilka rader av användare. Välj sedan **uppdaterade autentiseringsuppgifter** och ange sina användarnamn och lösenord.

### <a name="assign-users-to-the-app"></a>Tilldela användare till appen

Följ dessa steg om du vill tilldela användare till en app direkt:

1. Öppna den [Azure-portalen](https://portal.azure.com/) och logga in som global administratör.

2. Välj **alla tjänster** i navigeringen enkelt på vänster sida för att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret för Azure AD.

5. Välj **alla program** att visa en lista över dina program.

   > [!NOTE]
   > Om du inte ser den app som du vill använda den **Filter** kontroll högst upp på den **listan över alla program**. Ange den **visa** alternativet ”alla program”.

6. Välj den app som du vill tilldela en användare i listan.

7. När programmet har lästs in väljer **användare och grupper** från appens navigeringsfönstret till vänster.

8. Välj **Lägg till** överst i den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9. Välj **användare och grupper** i den **Lägg till tilldelning** fönstret.

10. I den **Sök efter namn eller e-postadress** skriver du det fullständiga namnet eller e-postadress för den användare som du vill tilldela.

11. Hovra över användare i listan. Markera kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till den **valda** lista.

12. *Valfritt:* Om du vill lägga till en annan användare, skriver du ett annat namn eller e-postadress i den **Sök efter namn eller e-postadress** rutan och välj sedan kryssrutan för att lägga till den **valda** lista.

13. När du är klar att välja användare klickar du på **Välj** att lägga till dem i listan över användare och grupper som är tilldelade till appen.

14. *Valfritt:* Klicka på **Välj roll** i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Välj **tilldela** att tilldela appen till de valda användarna.

    Efter en kort fördröjning kommer användare kunna komma åt apparna från åtkomstpanelen.

## <a name="request-support"></a>Begär support 
Om du får ett felmeddelande när du ställer in enkel inloggning och tilldela användare kan öppna ett supportärende. Inkludera följande information som möjligt:

-   Korrelations-ID för fel
-   UPN (användarens e-postadress)
-   TenantID
-   Typ av webbläsare
-   Tidszon och tid/tidsram när felet uppstod
-   Fiddler-spårningar

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
