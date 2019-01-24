---
title: 'Självstudier: Azure Active Directory-integrering med Agiloft | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Agiloft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: aca13814-cdbd-46b8-93dc-1578099c5ee4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: jeedes
ms.openlocfilehash: f851bd260c18509a3233bb3401383e0e04ba6259
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813669"
---
# <a name="tutorial-azure-active-directory-integration-with-agiloft"></a>Självstudier: Azure Active Directory-integrering med Agiloft

I den här självstudien får du lära dig hur du integrerar Agiloft med Azure Active Directory (AD Azure).

Integrera Agiloft med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Agiloft.
- Du kan aktivera användarna att automatiskt få loggat in på Agiloft (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Agiloft, behöver du följande objekt:

- En Azure AD-prenumeration
- En Agiloft enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Agiloft från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-agiloft-from-the-gallery"></a>Att lägga till Agiloft från galleriet
För att konfigurera integrering av Agiloft i Azure AD, som du behöver lägga till Agiloft från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Agiloft från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Agiloft**väljer **Agiloft** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Agiloft i resultatlistan](./media/agiloft-tutorial/tutorial_agiloft_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Agiloft baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Agiloft är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Agiloft upprättas.

I Agiloft, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Agiloft, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Agiloft](#create-an-agiloft-test-user)**  – du har en motsvarighet för Britta Simon i Agiloft som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Agiloft program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Agiloft:**

1. I Azure-portalen på den **Agiloft** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/agiloft-tutorial/tutorial_agiloft_samlbase.png)

3. På den **Agiloft domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![Agiloft domän och URL: er med enkel inloggning för information](./media/agiloft-tutorial/tutorial_agiloft_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: 
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com/project/<KB_NAME>` |
    | `https://<subdomain>.agiloft.com/project/<KB_NAME>` |

    b. I textrutan **Svars-URL** anger du en URL med följande mönster:
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com:443/gui2/spsamlsso?project=<KB_NAME>` |
    | `https://<subdomain>.agiloft.com:443/gui2/spsamlsso?project=<KB_NAME>` |

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Agiloft domän och URL: er med enkel inloggning för information](./media/agiloft-tutorial/tutorial_agiloft_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: 
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com/gui2/samlssologin.jsp?project=<KB_NAME>` |
    | `https://<subdomain>.agiloft.com/gui2/samlssologin.jsp?project=<KB_NAME>` |
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Agiloft klienten supportteamet](https://www.agiloft.com/support-login.htm) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/agiloft-tutorial/tutorial_agiloft_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/agiloft-tutorial/tutorial_general_400.png)
    
7. På den **Agiloft Configuration** klickar du på **konfigurera Agiloft** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Agiloft konfiguration](./media/agiloft-tutorial/tutorial_agiloft_configure.png) 

8. I ett annat webbläsarfönster logga du in på webbplatsen Agiloft företag som administratör.

9. Klicka på **installationsprogrammet** (i det vänstra fönstret) och sedan **åtkomst**.

    ![Agiloft konfiguration](./media/agiloft-tutorial/setup1.png) 

10. Klicka på knappen **”konfigurera SAML 2.0 enkel inloggning”**. 
    
    ![Agiloft konfiguration](./media/agiloft-tutorial/setup2.png) 

11. Guiden visas. I dialogrutan klickar du på FLIKEN **”providern uppgifterna om autentiseringsidentitet som”** och Fyll i följande fält:  
    
    ![Agiloft konfiguration](./media/agiloft-tutorial/setup4.png) 

    a. I **IdP entitets-Id / utfärdare** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    b. I **inloggnings-URL för IDP: N** textrutan klistra in värdet för **enkel inloggnings-URL för**, som du har kopierat från Azure-portalen.

    c. I **utloggnings-URL för IDP: N** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    d. Öppna din **Base64-kodade certifikat** i anteckningar som hämtats från Azure-portalen, kopierar du innehållet i den till Urklipp och klistra in den till den **IdP tillhandahålls X.509 certifikatinnehåll**  textrutan.

    e. Klicka på **Slutför**.


> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/agiloft-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/agiloft-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/agiloft-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/agiloft-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-agiloft-test-user"></a>Skapa en Agiloft testanvändare

Programmet stöder just-in-time-användaretablering, och efter autentiseringen skapas användare automatiskt i programmet. Det finns ingen åtgärd för dig i det här avsnittet.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Agiloft.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Agiloft, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Agiloft**.

    ![Länken Agiloft i listan med program](./media/agiloft-tutorial/tutorial_agiloft_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Agiloft i åtkomstpanelen du bör få automatiskt loggat in på ditt Agiloft program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/agiloft-tutorial/tutorial_general_01.png
[2]: ./media/agiloft-tutorial/tutorial_general_02.png
[3]: ./media/agiloft-tutorial/tutorial_general_03.png
[4]: ./media/agiloft-tutorial/tutorial_general_04.png

[100]: ./media/agiloft-tutorial/tutorial_general_100.png

[200]: ./media/agiloft-tutorial/tutorial_general_200.png
[201]: ./media/agiloft-tutorial/tutorial_general_201.png
[202]: ./media/agiloft-tutorial/tutorial_general_202.png
[203]: ./media/agiloft-tutorial/tutorial_general_203.png

