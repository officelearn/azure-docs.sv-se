---
title: "Självstudier: Azure Active Directory-integrering med arbetsplats av Facebook | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbetsplats med Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 27e62a00832484667117d8718db9f2fc05e2f4e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Självstudier: Azure Active Directory-integrering med arbetsplats av Facebook

I kursen får lära du att integrera arbetsplats av Facebook med Azure Active Directory (AD Azure).

Integrera arbetsplats av Facebook med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till arbetsplats med Facebook.
- Du kan aktivera användarna att automatiskt hämta loggat in på arbetsplatsen av Facebook (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats: Azure-portalen.

Mer information om programvara som en tjänst (SaaS) appintegrering med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med arbetsplats av Facebook, behöver du följande:

- En Azure AD-prenumeration
- En arbetsplats av Facebook enkel inloggning (SSO) aktiverat prenumeration

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD SSO i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till arbetsplatsen av Facebook från galleriet.
2. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-workplace-by-facebook-from-the-gallery"></a>Lägg till arbetsplatsen av Facebook från galleriet
För att konfigurera integrering av arbetsplatsen av Facebook i Azure AD, lägga till arbetsplatsen av Facebook från galleriet i listan över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Bläddra till **företagsprogram** > **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till det nya programmet, Välj **nytt program** överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **arbetsplats av Facebook**, och välj **arbetsplats av Facebook** resultaten. Välj sedan **Lägg till**, för att lägga till programmet.

    ![Arbetsplats av Facebook i resultatlistan](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD SSO till arbetsplats av Facebook, baserat på en testanvändare som kallas ”Britta Simon”.

För SSO ska fungera måste Azure AD du känna till motsvarande användaren i arbetsplats av Facebook till en användare i Azure AD. Med andra ord ska ett länkat förhållande mellan en Azure AD-användare och relaterade användaren arbetsplatsen av Facebook upprättas.

Etablera den här relationen genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** arbetsplatsen av Facebook.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

Du aktiverar Azure AD SSO i Azure portal och du konfigurerar enkel inloggning i din arbetsplats Facebook-programmet i det här avsnittet.

1. I Azure-portalen på den **arbetsplats av Facebook** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. I den **enkel inloggning** dialogrutan **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. I den **arbetsplats Facebook-domänen och URL: er** avsnittet, gör du följande:

    a. I den **inloggnings-URL** text skriver en URL som använder följande mönster:`https://<company subdomain>.facebook.com`

    b. I den **identifierare** text skriver en URL som använder följande mönster:`https://www.facebook.com/company/<scim company id>`

    > [!NOTE]
    > Dessa värden är bara ett exempel. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta den [arbetsplats av Facebook klienten supportteamet](https://workplace.fb.com/faq/) att hämta dessa värden. 

4. I den **SAML-signeringscertifikat** väljer **certifikat (Base64)**, och sedan spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Välj **Spara**.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. I den **arbetsplats Facebook konfigurationen** väljer **konfigurera arbetsplats av Facebook** att öppna den **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens** avsnitt.

    ![Arbetsplats av Facebook-konfiguration](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. I en annan webbläsarfönster inloggningen till din arbetsplats av Facebook företagets plats som administratör.
  
   > [!NOTE] 
   > Som en del av processen för SAML-autentisering, kan arbetsplats använda frågesträngar upp till 2,5 kilobyte i storlek för att kunna skicka parametrar till Azure AD.

8. I den **företagets instrumentpanelen**, gå till den **autentisering** fliken.

9. Under **SAML-autentisering**väljer **SSO endast** från den nedrullningsbara listan.

10. Ange de värden som kopieras från den **arbetsplats Facebook konfigurationen** på Azure portal till motsvarande fält:

    *   I den **SAML URL** text klistra in värdet för **inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
    *   I den **utfärdar-URL för SAML** text klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.
    *   I **SAML logga ut omdirigera (valfritt)**, klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.
    *   Öppna din **Base64-kodat certifikat** i anteckningar, som hämtas från Azure-portalen. Kopiera innehållet i den till Urklipp och klistra in den till den **SAML certifikat** textruta.

11. Du kan behöva ange Webbadressen till målgruppen mottagaren URL: en och URL för ACS (Assertion konsumenten Service), som visas under den **SAML-konfiguration** avsnitt.

12. Bläddra längst ned i avsnittet och välj **Test SSO**. Ett popup-fönster visas med Azure AD-inloggningssida. Ange dina autentiseringsuppgifter för att autentisera, som vanligt. Kontrollera den e-postadress som returnerades från Azure AD är samma som arbetsplatskontot som du är inloggad med.

13. Om testet har slutförts, bläddra till längst ned på sidan och välj **spara**.

14. Alla som använder arbetsplats kan nu se med Azure AD-inloggningssida för autentisering.

Du kan välja att konfigurera en SAML-logga ut URL, vilket kan användas för att peka på sidan Azure AD utloggning. När den här inställningen har aktiverats och konfigurerats, omdirigeras inte längre användaren till sidan Arbetsyta utloggning. I stället omdirigeras användaren till den URL som har lagts till i inställningen SAML utloggning omdirigera.


> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** bara väljer den **enkel inloggning** fliken och åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen i den [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="configure-reauthentication-frequency"></a>Konfigurera omautentisering frekvens

Du kan konfigurera arbetsplats för att fråga efter en SAML kontroll varje dag, tre dagar, en vecka, två veckor, en månad eller aldrig.

> [!NOTE] 
>Minsta värde för SAML-kontroll av mobila program anges till en vecka.

Du kan också tvinga en SAML återställa för alla användare. Det gör du genom att använda den **kräver SAML-autentisering för alla användare nu** knappen.


### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

1. I den **Azure-portalen**, i den vänstra rutan, Välj **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj **alla användare**.
    
    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan **Lägg till**.
 
    ![Knappen Lägg till](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan Gör följande:
 
    ![Dialogrutan användare](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet**, och Skriv ned.

    d. Välj **Skapa**.
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Skapa en arbetsplats av Facebook testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i arbetsplats med Facebook. Arbetsplats av Facebook stöder just-in-time-allokering som är aktiverad som standard.

Det finns inga åtgärder i det här avsnittet. Om en användare inte finns i arbetsplats av Facebook, skapas en ny när du försöker komma åt arbetsplatsen av Facebook.

>[!Note]
>Om du behöver skapa en användare manuellt kan kontakta den [arbetsplats av Facebook klienten supportteamet](https://workplace.fb.com/faq/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure SSO genom att bevilja åtkomst till arbetsplats med Facebook.

![Tilldela användare][200] 

1. Öppna program i Azure-portalen. Gå till vyn directory genom att gå till **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **arbetsplats av Facebook**.

    ![Arbetsplatsen av Facebook-länken i listan med program](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Välj **Lägg till**. I den **Lägg uppdrag** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan.

6. I den **användare och grupper** dialogrutan **Välj**.

7. I den **Lägg uppdrag** dialogrutan **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Om du vill testa inställningarna för enkel inloggning, öppna åtkomstpanelen.
Mer information finns i [Introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).


## <a name="next-steps"></a>Nästa steg

* Finns det [lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md).
* Läs [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).
* Mer information om hur du [konfigurera användaretablering](active-directory-saas-facebook-at-work-provisioning-tutorial.md).



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png

