---
title: 'Självstudier: Azure Active Directory-integration med 4me | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 4me.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 983eecc6-41f8-49b7-b7f6-dcf833dde121
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: jeedes
ms.openlocfilehash: c33edf13a8bcafd4a6c3d4885553fc856ec941d8
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158133"
---
# <a name="tutorial-azure-active-directory-integration-with-4me"></a>Självstudier: Azure Active Directory-integration med 4me

I den här självstudien får du lära dig hur du integrerar 4me med Azure Active Directory (AD Azure).

Integrera 4me med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till 4me.
- Du kan aktivera användarna att automatiskt få loggat in på 4me (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med 4me, behöver du följande objekt:

- En Azure AD-prenumeration
- En 4me enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till 4me från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-4me-from-the-gallery"></a>Att lägga till 4me från galleriet
För att konfigurera integrering av 4me i Azure AD, som du behöver lägga till 4me från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till 4me från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **4me**väljer **4me** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![4me i resultatlistan](./media/4me-tutorial/tutorial_4me_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med 4me baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i 4me är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i 4me upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med 4me, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare 4me](#create-a-4me-test-user)**  – du har en motsvarighet för Britta Simon i 4me som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt 4me program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med 4me:**

1. I Azure-portalen på den **4me** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/4me-tutorial/tutorial_4me_samlbase.png)

3. På den **4me domän och URL: er** avsnittet, utför följande steg:

    ![4me domän och URL: er med enkel inloggning för information](./media/4me-tutorial/tutorial_4me_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster:

    | Miljö| URL|
    |---|---|
    | PRODUKTION | `https://<SUBDOMAIN>.4me.com`|
    | KVALITETSKONTROLL| `https://<SUBDOMAIN>.4me.qa`|
  
    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:
    
    | Miljö| URL|
    |---|---|
    | PRODUKTION | `https://<SUBDOMAIN>.4me.com`|
    | KVALITETSKONTROLL| `https://<SUBDOMAIN>.4me.qa`|

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [4me klienten supportteamet](mailto:support@4me.com) att hämta dessa värden. 
 
4. 4me program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/4me-tutorial/tutorial_4me_attribute.png)

5. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | Förnamn | User.givenName |
    | Efternamn | User.surname |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/4me-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/4me-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna den **Namespace** tom.
    
    d. Klicka på **Ok**

6. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet på datorn.

    ![Länk för hämtning av certifikat](./media/4me-tutorial/tutorial_4me_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/4me-tutorial/tutorial_general_400.png)

8. På den **4me Configuration** klickar du på **konfigurera 4me** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![4me konfiguration](./media/4me-tutorial/tutorial_4me_configure.png) 

9. I ett annat webbläsarfönster, logga in på 4me som administratör.

10. På upp till vänster, klickar du på **inställningar** logotyp och på fältet i vänster **enkel inloggning**.

    ![4me inställningar](./media/4me-tutorial/tutorial_4me_settings.png)

11. På den **enkel inloggning** utför följande steg:

    ![4me singleasignon](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. Välj den **aktiverad** alternativet.

    b. I den **Remote utloggnings-URL** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    c. Under **SAML** avsnittet i den **URL för SAML SSO** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    d. I den **certifikat fingeravtryck** textrutan klistra in den **TUMAVTRYCK** värdet avgränsade med kolon i duplets ordning (AA:BB:CC:DD:EE:FF:GG:HH:II), som du har kopierat från Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/4me-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/4me-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/4me-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/4me-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-4me-test-user"></a>Skapa en 4me testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i 4me. 4me stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt 4me om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [4me supportteamet](mailto:support@4me.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till 4me.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon 4me, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **4me**.

    ![Länken 4me i listan med program](./media/4me-tutorial/tutorial_4me_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen 4me i åtkomstpanelen du bör få automatiskt loggat in på ditt 4me program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/4me-tutorial/tutorial_general_01.png
[2]: ./media/4me-tutorial/tutorial_general_02.png
[3]: ./media/4me-tutorial/tutorial_general_03.png
[4]: ./media/4me-tutorial/tutorial_general_04.png

[100]: ./media/4me-tutorial/tutorial_general_100.png

[200]: ./media/4me-tutorial/tutorial_general_200.png
[201]: ./media/4me-tutorial/tutorial_general_201.png
[202]: ./media/4me-tutorial/tutorial_general_202.png
[203]: ./media/4me-tutorial/tutorial_general_203.png

