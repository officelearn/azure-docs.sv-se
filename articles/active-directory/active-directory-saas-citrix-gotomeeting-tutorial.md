---
title: "Självstudier: Azure Active Directory-integrering med GoToMeeting | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GoToMeeting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: 1c3ea5175b02e35e7c624ce936d59fd82163b0fc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>Självstudier: Azure Active Directory-integrering med GoToMeeting

I kursen får lära du att integrera GoToMeeting med Azure Active Directory (AD Azure).

Integrera GoToMeeting med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till GoToMeeting.
- Du kan aktivera användarna att automatiskt hämta loggat in på GoToMeeting (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med GoToMeeting, behöver du följande:

- En Azure AD-prenumeration
- En GoToMeeting enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till GoToMeeting från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-gotomeeting-from-the-gallery"></a>Att lägga till GoToMeeting från galleriet
Du måste lägga till GoToMeeting från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av GoToMeeting i Azure AD.

**Utför följande steg för att lägga till GoToMeeting från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **GoToMeeting**väljer **GoToMeeting** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![GoToMeeting i resultatlistan](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med GoToMeeting baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i GoToMeeting motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i GoToMeeting upprättas.

I GoToMeeting, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med GoToMeeting, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare GoToMeeting](#create-a-gotomeeting-test-user)**  – du har en motsvarighet för Britta Simon i GoToMeeting som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt GoToMeeting program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med GoToMeeting:**

1. I Azure-portalen på den **GoToMeeting** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. På den **GoToMeeting domän och URL: er** klickar du på **visa avancerade inställningar för URL: en** och utför följande åtgärder -

    ![URL: er och GoToMeeting domän med enkel inloggning information](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_ssourl.png)

    a. I den **identifierare** textruta anger du URL:`https://authentication.logmeininc.com/saml/sp`

    b. I den **Reply URL** textruta anger du URL:`https://authentication.logmeininc.com/saml/acs`

    c. I den **Relay tillstånd** textruta skriver du något av följande webbadresser:

    **För GoToMeeting**:`https://global.gotomeeting.com`
    
    **För GoToTraining**:`https://global.gototraining.com`

    **För GoToWebinar**:`https://global.gotowebinar.com`

    **För GoToAssist**:`https://app.gotoassist.com`


4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_400.png)

6. Att generera den **Metadata** url, utför följande steg:

    a. Klicka på **App registreringar**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appregistrations.png)
   
    b. Klicka på **slutpunkter** att öppna **slutpunkter** dialogrutan.  
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpointicon.png)

    c. Klicka på kopieringsknappen för att kopiera **FEDERATION METADATADOKUMENTET** url och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpoint.png)
     
    d. Gå till egenskapssidan för **GoToMeeting** och kopiera den **program-Id** med **kopiera** knappen och klistra in den i anteckningar.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appid.png)

    e. Generera den **URL för tjänstmetadata** med hjälp av följande mönster:`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`   

7. På den **GoToMeeting Configuration** klickar du på **konfigurera GoToMeeting** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![GoToMeeting konfiguration](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_configure.png) 

8. I en annan webbläsare och logga in på ditt [GoToMeeting organisation Center](https://account.citrixonline.com/organization/administration/)

9. Under **identitetsleverantör** fliken kan du konfigurera Azure inställningar antingen genom att tillhandahålla den genererade **URL för tjänstmetadata** eller den hämtade **metadatafil** eller **Manuell**.

10. För **URL för tjänstmetadata** utför följande steg:

    ![GoToMeeting konfiguration](./media/active-directory-saas-gotomeeting-tutorial/config1.png)

    a. I den **hur du vill konfigurera SAML-IDP?**väljer **automatisk** i listrutan.

    b. Klistra in den **URL för tjänstmetadata**, som du har genererat i föregående steg i den **URL för tjänstmetadata** textruta.

    c. Klicka på **Spara**.

11. För **metadatafil** utför följande steg:

    ![GoToMeeting konfiguration](./media/active-directory-saas-gotomeeting-tutorial/config2.png)

    a. I den **hur du vill konfigurera SAML-IDP?**väljer **överför SAML-metadatafil** i listrutan.

    b. Om du vill överföra din hämtade metadatafil klickar du på **överför metadatafil**.

    c. Klicka på **Spara**.

12. För **manuell** utför följande steg:

    ![GoToMeeting konfiguration](./media/active-directory-saas-gotomeeting-tutorial/config3.png)

    a.  I **inloggning Sidadress** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    b.  I **URL för utloggning** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.

    c.  I **identitet providern enhets-ID** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.

    d. Extrahera X509Certificate från hämtade metadatafilen och överföra det här certifikatet genom att klicka på **överför certifikat**.

    e. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-gotomeeting-test-user"></a>Skapa en testanvändare GoToMeeting

I det här avsnittet skapas en användare som kallas Britta Simon i GoToMeeting. GoToMeeting stöder just-in-time-allokering som är aktiverad som standard.

Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i GoToMeeting, skapas en ny när du försöker komma åt GoToMeeting.

> [!NOTE]
> Om du behöver skapa en användare manuellt, kontakta [GoToMeeting supportteamet](https://support.logmeininc.com/gotomeeting).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till GoToMeeting.

![Tilldela rollen][200] 

**Om du vill tilldela GoToMeeting Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **GoToMeeting**.

    ![Länken GoToMeeting i listan med program](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen GoToMeeting på åtkomstpanelen du bör få automatiskt loggat in på ditt GoToMeeting program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera Användaretablering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_203.png

