---
title: 'Självstudier: Azure Active Directory-integrering med Vidyard | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 0934ec2443d240234b2120056894921f2e30c81c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213173"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Självstudier: Azure Active Directory-integrering med Vidyard

I kursen får lära du att integrera Vidyard med Azure Active Directory (AD Azure).

Integrera Vidyard med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Vidyard.
- Du kan aktivera användarna att automatiskt hämta loggat in på Vidyard (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Vidyard, behöver du följande:

- En Azure AD-prenumeration
- En Vidyard enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Vidyard från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-vidyard-from-the-gallery"></a>Att lägga till Vidyard från galleriet
Du måste lägga till Vidyard från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Vidyard i Azure AD.

**Utför följande steg för att lägga till Vidyard från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Vidyard**väljer **Vidyard** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Vidyard i resultatlistan](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Vidyard baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Vidyard motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Vidyard upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Vidyard, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Vidyard](#create-a-vidyard-test-user)**  – du har en motsvarighet för Britta Simon i Vidyard som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Vidyard program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Vidyard:**

1. I Azure-portalen på den **Vidyard** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

3. På den **Vidyard domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Vidyard domän med enkel inloggning information](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Vidyard domän med enkel inloggning information](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Dessa värden uppdateras med den faktiska identifierare Reply URL och inloggnings-URL, som beskrivs senare i självstudierna

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/vidyard-tutorial/tutorial_general_400.png)

7. På den **Vidyard Configuration** klickar du på **konfigurera Vidyard** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Vidyard konfiguration](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

8. I en annan webbläsarfönster loggar du in på webbplatsen för företagets Vidyard programvara som en administratör.

9. Infopanelen Vidyard Välj **grupp** > **säkerhet**

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure1.png)

1. Klicka på **ny profil** fliken.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure2.png)

11. I den **SAML-konfiguration** avsnittet, utför följande steg:

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure3.png)

    a. Ange allmänna profilnamn i den **profilnamn** textruta.

    b. Kopiera **SSO användaren inloggningssidan** värdet och klistrar in det i **inloggning URL** TextBox-kontroll i **Vidyard domän och URL: er** på Azure-portalen.

    c. Kopiera **ACS URL** värdet och klistrar in det i **Reply URL** TextBox-kontroll i **Vidyard domän och URL: er avsnittet** på Azure-portalen.

    d. Kopiera **utfärdaren/Metadata URL** värdet och klistrar in det i **identifierare** TextBox-kontroll i **Vidyard domän och URL: er avsnittet** på Azure-portalen.

    e. Öppna filen hämtat certifikat från Azure-portalen i anteckningar och klistra in det i den **X.509-certifikat** textruta.

    f. I den **slutpunkts-URL för SAML** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** kopieras från Azure-portalen.

    g. Klicka på **Bekräfta**.

12. Välj fliken enkel inloggning **tilldela** bredvid en befintlig profil

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > När du har skapat en profil för enkel inloggning måste du tilldela den till alla grupper som användarna måste ha åtkomst via Azure. Om användaren inte finns i den grupp som de har tilldelats, kommer Vidyard automatiskt skapa ett användarkonto och tilldela rollen i realtid.

13. Välj din organisation, som visas i den **tillgängliga grupper att tilldela**.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure5.png)

14. Du kan se de tilldelade grupperna under den **grupper tilldelade för tillfället**. Välj en roll för en grupp i din organisation och klickar på **Bekräfta**.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Mer information finns i [det här dokumentet](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/vidyard-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/vidyard-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/vidyard-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/vidyard-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-vidyard-test-user"></a>Skapa en testanvändare Vidyard

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Vidyard. Vidyard stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Vidyard om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [Vidyard supportteamet](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Vidyard.

![Tilldela rollen][200] 

**Om du vill tilldela Vidyard Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Vidyard**.

    ![Länken Vidyard i listan med program](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Vidyard på åtkomstpanelen du bör få automatiskt loggat in på ditt Vidyard program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

