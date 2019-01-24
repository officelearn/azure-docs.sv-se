---
title: 'Självstudier: Azure Active Directory-integrering med Gigya | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Gigya.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: e57943e6ee227548459c6060814165ef4fe5b337
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825620"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Självstudier: Azure Active Directory-integrering med Gigya

I den här självstudien får du lära dig hur du integrerar Gigya med Azure Active Directory (AD Azure).

Integrera Gigya med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Gigya
- Du kan aktivera användarna att automatiskt få loggat in på Gigya (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Gigya, behöver du följande objekt:

- En Azure AD-prenumeration
- En Gigya enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Gigya från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-gigya-from-the-gallery"></a>Att lägga till Gigya från galleriet
För att konfigurera integrering av Gigya i Azure AD, som du behöver lägga till Gigya från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Gigya från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Gigya**.

    ![Skapa en Azure AD-användare för testning](./media/gigya-tutorial/tutorial_gigya_search.png)

1. I resultatpanelen väljer **Gigya**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/gigya-tutorial/tutorial_gigya_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Gigya baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Gigya är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Gigya upprättas.

I Gigya, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Gigya, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Gigya](#creating-a-gigya-test-user)**  – du har en motsvarighet för Britta Simon i Gigya som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Gigya program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Gigya:**

1. I Azure-portalen på den **Gigya** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/gigya-tutorial/tutorial_gigya_samlbase.png)

1. På den **Gigya domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/gigya-tutorial/tutorial_gigya_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `http://<companyname>.gigya.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Gigya klienten supportteamet](https://www.gigya.com/support-policy/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/gigya-tutorial/tutorial_gigya_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/gigya-tutorial/tutorial_general_400.png)

1. På den **Gigya Configuration** klickar du på **konfigurera Gigya** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/gigya-tutorial/tutorial_gigya_configure.png) 

1. Logga in på webbplatsen Gigya företag som en administratör i ett annat webbläsarfönster.

1. Gå till **inställningar \> SAML-inloggningen**, och klicka sedan på den **Lägg till** knappen.
   
    ![SAML-inloggningen](./media/gigya-tutorial/ic789532.png "SAML-inloggning")

1. I den **SAML-inloggningen** avsnittet, utför följande steg:
   
    ![SAML-konfiguration](./media/gigya-tutorial/ic789533.png "SAML-konfiguration")
   
    a. I den **namn** textrutan anger du ett namn för din konfiguration.
   
    b. I **utfärdare** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen. 
   
    c. I **enkel inloggnings-URL för** textrutan klistra in värdet för **enkel inloggnings-URL för** som du har kopierat från Azure-portalen.
   
    d. I **Format för namn-ID** textrutan klistra in värdet för **Format för namn på identifierare** som du har kopierat från Azure-portalen.
   
    e. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in den i textrutan **X.509 Certificate** (X.509-certifikat).
   
    f. Klicka på **spara inställningarna för**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/gigya-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/gigya-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/gigya-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/gigya-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-gigya-test-user"></a>Skapa en Gigya testanvändare

För att aktivera Azure AD-användare att logga in på Gigya, måste de etableras i Gigya.  
När det gäller Gigya är etablering en manuell aktivitet.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Utför följande steg för att tillhandahålla ett användarkonto:

1. Logga in på din **Gigya** företagets plats som administratör.

1. Gå till **Admin \> hantera användare**, och klicka sedan på **bjuda in användare**.
   
    ![Hantera användare](./media/gigya-tutorial/ic789535.png "hantera användare")

1. I dialogrutan Bjud in användare utför du följande steg:
   
    ![Bjud in användare](./media/gigya-tutorial/ic789536.png "Bjud in användare")
   
    a. I den **e-post** textrutan skriver du e-postalias för ett giltigt Azure Active Directory-konto som du vill etablera.
    
    b. Klicka på **Bjud in användare**.
      
    > [!NOTE]
    > Azure Active Directory-kontoinnehavare får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktiv.
    > 
    

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Gigya.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Gigya, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Gigya**.

    ![Konfigurera enkel inloggning](./media/gigya-tutorial/tutorial_gigya_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Gigya i åtkomstpanelen du bör få automatiskt loggat in på ditt Gigya program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/gigya-tutorial/tutorial_general_01.png
[2]: ./media/gigya-tutorial/tutorial_general_02.png
[3]: ./media/gigya-tutorial/tutorial_general_03.png
[4]: ./media/gigya-tutorial/tutorial_general_04.png

[100]: ./media/gigya-tutorial/tutorial_general_100.png

[200]: ./media/gigya-tutorial/tutorial_general_200.png
[201]: ./media/gigya-tutorial/tutorial_general_201.png
[202]: ./media/gigya-tutorial/tutorial_general_202.png
[203]: ./media/gigya-tutorial/tutorial_general_203.png

