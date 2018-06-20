---
title: 'Självstudier: Azure Active Directory-integrering med IBM Kenexa undersökning Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IBM Kenexa undersökning Enterprise.
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
ms.openlocfilehash: bca5d5111f5cfc3bcf2720af855f44d1928bcfd6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221024"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Självstudier: Azure Active Directory-integrering med IBM Kenexa undersökning Enterprise

I kursen får lära du att integrera IBM Kenexa undersökning Enterprise med Azure Active Directory (AD Azure).

Integrera IBM Kenexa undersökning Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till IBM Kenexa undersökning Enterprise.
- Du kan aktivera användarna att logga in automatiskt till IBM Kenexa undersökning företaget med hjälp av enkel inloggning (SSO) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats: Azure-portalen.

Om du vill veta mer om programvara som en tjänst (SaaS) appintegrering med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med IBM Kenexa undersökning Enterprise, behöver du följande:

- En Azure AD-prenumeration
- En prenumeration för IBM Kenexa undersökning Enterprise SSO aktiverad

> [!NOTE]
> När du testar stegen i den här självstudiekursen, rekommenderar vi att du inte använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD SSO i en testmiljö. Det scenario som beskrivs i kursen består av två huvudsakliga byggblock:

* Att lägga till IBM Kenexa undersökning Enterprise från galleriet
* Konfigurera och testa Azure AD-SSO

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Lägg till IBM Kenexa undersökning Enterprise från galleriet
För att konfigurera integrering av IBM Kenexa undersökning Enterprise i Azure AD, lägga till IBM Kenexa undersökning Enterprise från galleriet i listan över hanterade SaaS-appar.

Lägg till IBM Kenexa undersökning Enterprise från galleriet genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan klickar du på den **Azure Active Directory** knappen. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till ett program, klickar du på den **nytt program** knappen.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **IBM Kenexa undersökning Enterprise**.

    ![Skapa en testanvändare i Azure AD](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Välj i resultatlistan **IBM Kenexa undersökning Enterprise**, och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![IBM Kenexa undersökning Enterprise i resultatlistan](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD SSO med IBM Kenexa undersökning Enterprise baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste identifiera IBM Kenexa undersökning Enterprise användaren motsvarighet i Azure AD för SSO ska fungera. Med andra ord upprätta Azure AD en länk förhållandet mellan en Azure AD-användare och en relaterad användare i IBM Kenexa undersökning företag.

Om du vill upprätta en länk relation, tilldela värdet för den **användarnamn** i IBM Kenexa undersökning företag som värde för den **användarnamn** i Azure AD.

Slutför byggblock i följande två avsnitt för att konfigurera och testa Azure AD SSO med IBM Kenexa undersökning Enterprise.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD för enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i IBM Kenexa undersökning Enterprise-programmet genom att göra följande:

1. I Azure-portalen på den **IBM Kenexa undersökning Enterprise** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![IBM Kenexa undersökning Enterprise Konfigurera enkel inloggning länk][4]

2. I den **enkel inloggning** i dialogrutan den **läge** väljer **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. I den **IBM Kenexa undersökning företagsdomänen och URL: er** avsnittet, utför följande steg:

    ![IBM Kenexa undersökning företagsdomänen och URL: er med enkel inloggning information](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. I den **identifierare** textruta typ en URL med följande mönster: `https://surveys.kenexa.com/<companycode>`

    b. I den **Reply URL** textruta typ en URL med följande mönster: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med den faktiska identifieraren och reply URL. För att få de faktiska värdena kan kontakta den [IBM Kenexa undersökning Enterprise supportteamet](https://www.ibm.com/support/home/?lnk=fcw).

4. Under **SAML-signeringscertifikat**, klickar du på **certifikat (Base64)**, och sedan spara certifikatfilen på datorn.

    ![Länken Hämta certifikat (Base64)](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    IBM Kenexa undersökning affärsprogrammet förväntas ta emot Security intyg Markup Language (SAML) intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen av din token SAML-attribut. Värdet för användar-ID-anspråk i svaret måste matcha SSO-ID som har konfigurerats i Kenexa system. Om du vill mappa lämpliga användar-ID i din organisation som SSO Internet Datagram Protocol (IDP) som fungerar med den [IBM Kenexa undersökning Enterprise supportteamet](https://www.ibm.com/support/home/?lnk=fcw). 

    Som standard Anger användar-ID i Azure AD som värde för användarens huvudnamn (UPN). Du kan ändra värdet på den **attributet** fliken som visas i följande skärmbild. Integrationen fungerar endast när du har slutfört mappningen korrekt.
    
    ![Dialogrutan användarattribut](./media/kenexasurvey-tutorial/tutorial_attribute.png) 

5. Klicka på **Spara**.

    ![Den Konfigurera enkel inloggning spara knappen](./media/kenexasurvey-tutorial/tutorial_general_400.png)

6. Öppna den **konfigurera inloggning** fönstret under **företagskonfiguration för IBM Kenexa undersökning**, klickar du på **konfigurera IBM Kenexa undersökning Enterprise**. 
 
    ![Konfigurera IBM Kenexa undersökning Enterprise-länk](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Kopiera den **Sign-Out URL**, **SAML enhets-ID**, och **SAML enkel inloggning Tjänstwebbadress** värden från den **Snabbreferens** avsnitt.

8. I den **konfigurera inloggning** fönstret under **Snabbreferens**, kopiera den **Sign-Out URL**, **SAML enhets-ID**, och **SAML inloggning tjänst-URL för enkel** värden.

9. Konfigurera enkel inloggning på den **IBM Kenexa undersökning Enterprise** sida, skicka den hämtade **certifikat (Base64)**, **Sign-Out URL**, **SAML enhets-ID**, och **SAML inloggning tjänst-URL för enkel** värden och den [IBM Kenexa undersökning Enterprise supportteamet](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Du kan referera till en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du har lagt till appen från den **Active Directory** > **företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och komma åt inbäddade dokumentationen via den **Configuration** avsnittet i slutet. Mer information om funktionen inbäddade dokumentation finns [Azure AD inbäddade dokumentationen](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
I det här avsnittet skapar du testanvändare Britta Simon i Azure-portalen genom att göra följande:

![Skapa en testanvändare i Azure AD][100]

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/kenexasurvey-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare” länkar](./media/kenexasurvey-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.
 
    ![Knappen Lägg till](./media/kenexasurvey-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/kenexasurvey-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Skapa en testanvändare IBM Kenexa undersökning Enterprise

I det här avsnittet kan du skapa en användare som kallas Britta Simon i IBM Kenexa undersökning Enterprise. 

För att skapa användare i systemet IBM Kenexa undersökning Enterprise och mappa SSO-ID för dem, kan du arbeta med den [IBM Kenexa undersökning Enterprise supportteamet](https://www.ibm.com/support/home/?lnk=fcw). Det här värdet för SSO-ID ska också mappas till identifierarvärde användaren från Azure AD. Du kan ändra denna standardinställning på den **attributet** fliken.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du låta användare Britta Simon att använda Azure SSO genom att bevilja åtkomst till IBM Kenexa undersökning Enterprise.

![Tilldela rollen][200] 

Om du vill tilldela användaren Britta Simon IBM Kenexa undersökning Enterprise, gör du följande:

1. I Azure-portalen öppnar den **program** visa, gå till den **Directory** väljer **företagsprogram**, och klicka sedan på **alla program**.

    ![”Företagsprogram” och ”alla program” länkar][201] 

2. I den **program** väljer **IBM Kenexa undersökning Enterprise**.

    ![IBM Kenexa undersökning Enterprise-länken i listan med program](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. I den vänstra rutan klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Klicka på den **Lägg till** knappen och sedan, i den **Lägg uppdrag** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

7. I den **Lägg uppdrag** dialogrutan klickar du på den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du konfigurationen av Azure AD SSO med hjälp av åtkomstpanelen.

När du klickar på den **IBM Kenexa undersökning Enterprise** panelen i panelen åtkomst ska vara loggas du automatiskt till IBM Kenexa undersökning Enterprise-programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

 
