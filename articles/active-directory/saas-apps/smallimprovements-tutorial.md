---
title: 'Självstudier: Azure Active Directory-integrering med små förbättringar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och små förbättringar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 27f94ef7fb41315ea11f87adf8a4f7f026d584af
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818667"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Självstudier: Azure Active Directory-integrering med små förbättringar

I den här självstudien får du lära dig hur du integrerar små förbättringar med Azure Active Directory (AD Azure).

Integrera små förbättringar med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till små förbättringar
- Du kan aktivera användarna att automatiskt få loggat in på små förbättringar (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med små förbättringar, behöver du följande objekt:

- En Azure AD-prenumeration
- En liten förbättringar av enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du kan få en månads utvärdering här [– prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till små förbättringar från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-small-improvements-from-the-gallery"></a>Att lägga till små förbättringar från galleriet
För att konfigurera integrering av små förbättringar i Azure AD, som du behöver lägga till små förbättringar från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till små förbättringar från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **små förbättringar**.

    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/tutorial_smallimprovements_search.png)

1. I resultatpanelen väljer **små förbättringar**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med små förbättringar som baseras på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarande små förbättringar i är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren små förbättringar i upprättas.

Tilldela värdet för små förbättringar i den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med små förbättringar, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för små förbättringar](#creating-a-small-improvements-test-user)**  – du har en motsvarighet för Britta Simon i små förbättringar som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för små förbättringar.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med små förbättringar:**

1. I Azure-portalen på den **små förbättringar** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

1. På den **mindre förbättringar domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.small-improvements.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [små förbättringar klienten supportteamet](mailto:support@small-improvements.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_general_400.png)

1. På den **små förbättringar Configuration** klickar du på **konfigurera små förbättringar** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

1. I ett nytt webbläsarfönster inloggning till webbplatsen förbättringar av små företag som administratör.

1. Huvudinstrumentpanel-sidan klickar du på **Administration** knappen till vänster.
   
    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klicka på den **SAML SSO** knappen **integreringar** avsnittet.
   
    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. På konfigurationssidan för enkel inloggning utför du följande steg:
   
    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. I den **HTTP-slutpunkt** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    b. Öppna din nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **x509 certifikat** textrutan. 

    c. Om du vill ha enkel inloggning och logga in formuläret autentiseringsalternativet tillgänglig för användare, kontrollerar du den **Aktivera åtkomst via inloggning för** alternativet.  

    d. Ange rätt värde för att nämna knappen SSO-inloggning i den **SAML fråga** textrutan.  

    e. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-small-improvements-test-user"></a>Skapa en testanvändare för små förbättringar

Om du vill aktivera Azure AD-användare att logga in på små förbättringar, måste de etableras i små förbättringar. När det gäller små förbättringar är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Inloggning till webbplatsen förbättringar av små företag som administratör.

1. Från startsidan går du till menyn på vänster och sedan på **Administration**.

1. Klicka på den **användarkatalog** knappen Användarhantering avsnitt. 
   
    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klicka på **lägga till användare**.

    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. På den **Lägg till användare** dialogrutan utför följande steg: 

    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. Ange den **Förnamn** för användare som **Britta**.

    b. Ange den **efternamn** för användare som **Simon**.

    c. Ange den **e-post** för användare som <strong>brittasimon@contoso.com</strong>. 

    d. Du kan också välja att ange personligt meddelande i den **skicka e-postmeddelande** box. Om du inte vill skicka meddelandet, avmarkerar du den här kryssrutan.

    e. Klicka på **skapa användare**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till små förbättringar.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon små förbättringar, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **små förbättringar**.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.  

När du klickar på panelen små förbättringar i åtkomstpanelen du bör få automatiskt loggat in på ditt program för små förbättringar.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/smallimprovements-tutorial/tutorial_general_203.png

