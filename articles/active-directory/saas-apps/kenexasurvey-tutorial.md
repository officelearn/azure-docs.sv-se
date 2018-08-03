---
title: 'Självstudier: Azure Active Directory-integrering med IBM Kenexa undersökningen Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IBM Kenexa undersökningen Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 6828617e0ae61a3784e4db3d1c2ecf4ce9862ce2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449517"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Självstudier: Azure Active Directory-integrering med IBM Kenexa undersökningen Enterprise

Lär dig hur du integrerar IBM Kenexa undersökningen Enterprise med Azure Active Directory (AD Azure) i den här självstudien.

Integrera IBM Kenexa undersökningen Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till IBM Kenexa undersökningen Enterprise.
- Du kan aktivera användarna att logga in automatiskt till IBM Kenexa undersökningen Enterprise med hjälp av enkel inloggning (SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats: Azure-portalen.

Om du vill veta mer om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med IBM Kenexa undersökningen Enterprise, behöver du följande objekt:

- En Azure AD-prenumeration
- En prenumeration på IBM Kenexa undersökningen Enterprise SSO-aktiverad

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du inte använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i kursen består av två viktigaste byggstenarna:

* Att lägga till IBM Kenexa undersökningen Enterprise från galleriet
* Konfigurera och testa Azure AD SSO

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Lägg till IBM Kenexa undersökningen Enterprise från galleriet
Lägg till IBM Kenexa undersökningen Enterprise från galleriet i din lista över hanterade SaaS-appar för att konfigurera integrering av IBM Kenexa undersökningen Enterprise i Azure AD.

Om du vill lägga till IBM Kenexa undersökningen Enterprise från galleriet, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan klickar du på den **Azure Active Directory** knappen. 

    ![Azure Active Directory-knappen][1]

1. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett program, klicka på den **nytt program** knappen.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **IBM Kenexa undersökningen Enterprise**.

    ![Skapa en Azure AD-användare för testning](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

1. I resultatlistan väljer **IBM Kenexa undersökningen Enterprise**, och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![IBM Kenexa undersökningen Enterprise i resultatlistan](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med IBM Kenexa undersökningen Enterprise baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste identifiera IBM Kenexa undersökningen Enterprise användaren motsvarigheten i Azure AD för SSO ska fungera. Azure AD måste alltså upprätta en länk relation mellan Azure AD-användare och en tillhörande användare i IBM Kenexa undersökningen företag.

För att upprätta länken relationen, tilldela värdet för den **användarnamn** i IBM Kenexa undersökningen företag som värde för den **användarnamn** i Azure AD.

Om du vill konfigurera och testa Azure AD enkel inloggning med IBM Kenexa undersökningen Enterprise, slutför du byggstenarna i följande två avsnitt.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt IBM Kenexa undersökningen Enterprise-program genom att göra följande:

1. I Azure-portalen på den **IBM Kenexa undersökningen Enterprise** program integration-sidan klickar du på **enkel inloggning**.

    ![IBM Kenexa undersökningen Enterprise Konfigurera enkel inloggning för länk][4]

1. I den **enkel inloggning** i dialogrutan den **läge** väljer **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

1. I den **IBM Kenexa undersökningen Enterprise domän och URL: er** avsnittet, utför följande steg:

    ![IBM Kenexa undersökningen Enterprise domän och URL: er med enkel inloggning för information](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. I den **identifierare** textrutan anger du ett URL med följande mönster: `https://surveys.kenexa.com/<companycode>`

    b. I den **svars-URL** textrutan anger du ett URL med följande mönster: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med det faktiska ID: t och svars-URL. För att få de faktiska värdena kan kontakta den [IBM Kenexa undersökningen Enterprise support-teamet](https://www.ibm.com/support/home/?lnk=fcw).

1. Under **SAML-signeringscertifikat**, klickar du på **certifikat (Base64)**, och sedan spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat (Base64)](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    IBM Kenexa undersökningen Enterprise-programmet förväntas ta emot Security intyg Markup Language (SAML) intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar konfigurationen av SAML-tokenattribut. Värdet för användar-ID-anspråk i svaret måste matcha SSO-ID som har konfigurerats i Kenexa-system. Om du vill mappa lämplig användaridentifieraren i din organisation som SSO Internet Datagram Protocol (IDP) som fungerar med den [IBM Kenexa undersökningen Enterprise support-teamet](https://www.ibm.com/support/home/?lnk=fcw). 

    Som standard Anger användar-ID som värde för användarens huvudnamn (UPN) i Azure AD. Du kan ändra värdet på den **attributet** fliken enligt följande skärmbild. Integrationen fungerar bara när du har slutfört mappningen korrekt.
    
    ![Dialogrutan användarattribut](./media/kenexasurvey-tutorial/tutorial_attribute.png) 

1. Klicka på **Spara**.

    ![Den Konfigurera enkel inloggning spara knappen](./media/kenexasurvey-tutorial/tutorial_general_400.png)

1. Öppna den **konfigurera inloggning** fönstret under **företagskonfiguration för IBM Kenexa undersökningen**, klickar du på **konfigurera IBM Kenexa undersökningen Enterprise**. 
 
    ![Konfigurera IBM Kenexa undersökningen Enterprise-länk](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

1. Kopiera den **URL: en för utloggning**, **SAML entitets-ID**, och **SAML enkel inloggning för tjänst-URL** värden från den **Snabbreferens** avsnittet.

1. I den **konfigurera inloggning** fönstret under **Snabbreferens**, kopiera den **URL: en för utloggning**, **SAML entitets-ID**, och **SAML enkel inloggning för tjänst-URL** värden.

1. Att konfigurera enkel inloggning på den **IBM Kenexa undersökningen Enterprise** sida, skicka de hämtade **certifikat (Base64)**, **URL: en för utloggning**, **SAML entitets-ID**, och **SAML enkel inloggning för tjänst-URL** värden till den [IBM Kenexa undersökningen Enterprise support-teamet](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Du kan referera till en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du har lagt till appen från den **Active Directory** > **företagsprogram** bara klickar du på den **enkel inloggning** fliken och öppna den Embedded-dokumentation via den **Configuration** i slutet. Mer information om funktionen embedded-dokumentation finns [embedded-dokumentation för Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
I det här avsnittet skapar du testanvändare Britta Simon i Azure portal genom att göra följande:

![Skapa en Azure AD-testanvändare][100]

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/kenexasurvey-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/kenexasurvey-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.
 
    ![Knappen Lägg till](./media/kenexasurvey-tutorial/create_aaduser_03.png) 

1. I den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/kenexasurvey-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Skapa en testanvändare för IBM Kenexa undersökningen Enterprise

I det här avsnittet skapar du en användare som kallas Britta Simon i IBM Kenexa undersökningen företag. 

För att skapa användare i systemet för IBM Kenexa undersökningen Enterprise och mappa SSO-ID för dem, kan du arbeta med den [IBM Kenexa undersökningen Enterprise support-teamet](https://www.ibm.com/support/home/?lnk=fcw). Det här SSO-ID-värdet bör också mappas till användar-ID-värde från Azure AD. Du kan ändra denna standardinställning på den **attributet** fliken.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du användaren Britta Simon att använda enkel inloggning för Azure genom att bevilja åtkomst till IBM Kenexa undersökningen Enterprise.

![Tilldela rollen][200] 

Om du vill tilldela användaren Britta Simon IBM Kenexa undersökningen Enterprise, gör du följande:

1. I Azure-portalen öppnar du den **program** visa, gå till den **Directory** väljer **företagsprogram**, och klicka sedan på **alla program** .

    ![”Program” och ”alla program”-länkar][201] 

1. I den **program** väljer **IBM Kenexa undersökningen Enterprise**.

    ![IBM Kenexa undersökningen Enterprise-länk i listan med program](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

1. I den vänstra rutan klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

1. Klicka på den **Lägg till** knappen och klicka sedan på **Lägg till tilldelning** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

1. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**.

1. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

1. I den **Lägg till tilldelning** dialogrutan klickar du på den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på den **IBM Kenexa undersökningen Enterprise** panelen i åtkomstpanelen, du bör vara loggas in automatiskt dina IBM Kenexa undersökningen företagsprogram.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/kenexasurvey-tutorial/tutorial_general_203.png

 
