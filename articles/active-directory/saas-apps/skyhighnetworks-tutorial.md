---
title: 'Självstudier: Azure Active Directory-integrering med Skyhigh nätverk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Skyhigh nätverk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 48d6ddd1-4d3e-4019-8234-5e5212684d9c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: jeedes
ms.openlocfilehash: cdea99a7e2814673e5ba533fbfd58dbb01ad240b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35968918"
---
# <a name="tutorial-azure-active-directory-integration-with-skyhigh-networks"></a>Självstudier: Azure Active Directory-integrering med Skyhigh nätverk

I kursen får lära du att integrera Skyhigh nätverk med Azure Active Directory (AD Azure).

Integrera Skyhigh nätverk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Skyhigh nätverk.
- Du kan aktivera användarna att automatiskt hämta loggat in på Skyhigh nätverk (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Skyhigh nätverk, behöver du följande:

- En Azure AD-prenumeration
- En Skyhigh nätverk enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Skyhigh nätverk från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-skyhigh-networks-from-the-gallery"></a>Att lägga till Skyhigh nätverk från galleriet
Du måste lägga till Skyhigh nätverk från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Skyhigh nätverk i Azure AD.

**Utför följande steg för att lägga till Skyhigh nätverk från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Skyhigh nätverk**väljer **Skyhigh nätverk** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Skyhigh nätverk i resultatlistan](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Skyhigh nätverk baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Skyhigh nätverk till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Skyhigh nätverk upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Skyhigh nätverk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Skyhigh nätverk](#create-a-skyhigh-networks-test-user)**  – du har en motsvarighet för Britta Simon i Skyhigh nätverk som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Skyhigh nätverk.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Skyhigh nätverk:**

1. I Azure-portalen på den **Skyhigh nätverk** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_samlbase.png)

3. På den **Skyhigh nätverk domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Skyhigh nätverk domän med enkel inloggning information](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url.png)

    a. I den **identifierare (enhets-ID)** textruta Skriv en URL med följande mönster: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Skyhigh nätverk domän med enkel inloggning information](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [Skyhigh nätverk klienten supportteamet](mailto:support@skyhighnetworks.com) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/skyhighnetworks-tutorial/tutorial_general_400.png)
    
7. På den **Skyhigh nätverk Configuration** klickar du på **konfigurera Skyhigh nätverk** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Skyhigh nätverk konfiguration](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_configure.png) 

8. Konfigurera enkel inloggning på **Skyhigh nätverk** sida, måste du skicka den hämtade **certifikat (Base64), Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** till [ Skyhigh nätverk stöder team](mailto:support@skyhighnetworks.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/skyhighnetworks-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/skyhighnetworks-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/skyhighnetworks-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/skyhighnetworks-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-skyhigh-networks-test-user"></a>Skapa en testanvändare Skyhigh nätverk

I det här avsnittet skapar du en användare som kallas Britta Simon i Skyhigh nätverk. Arbeta med [Skyhigh nätverk stöder team](mailto:support@skyhighnetworks.com) att lägga till användare i Skyhigh nätverk-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Skyhigh nätverk.

![Tilldela rollen][200] 

**Om du vill tilldela Skyhigh nätverk Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Skyhigh nätverk**.

    ![Länken Skyhigh nätverk i listan med program](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Skyhigh nätverk på åtkomstpanelen du ska hämta automatiskt loggat in på ditt nätverk Skyhigh program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skyhighnetworks-tutorial/tutorial_general_01.png
[2]: ./media/skyhighnetworks-tutorial/tutorial_general_02.png
[3]: ./media/skyhighnetworks-tutorial/tutorial_general_03.png
[4]: ./media/skyhighnetworks-tutorial/tutorial_general_04.png

[100]: ./media/skyhighnetworks-tutorial/tutorial_general_100.png

[200]: ./media/skyhighnetworks-tutorial/tutorial_general_200.png
[201]: ./media/skyhighnetworks-tutorial/tutorial_general_201.png
[202]: ./media/skyhighnetworks-tutorial/tutorial_general_202.png
[203]: ./media/skyhighnetworks-tutorial/tutorial_general_203.png

