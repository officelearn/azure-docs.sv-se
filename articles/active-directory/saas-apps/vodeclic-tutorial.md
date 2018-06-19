---
title: 'Självstudier: Azure Active Directory-integrering med Vodeclic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: fcb7483e714f721f61bf2170a75e06c0cf3de1ed
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966451"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Självstudier: Azure Active Directory-integrering med Vodeclic

I kursen får lära du att integrera Vodeclic med Azure Active Directory (AD Azure).

Integrera Vodeclic med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Vodeclic.
- Du kan aktivera användarna att automatiskt hämta loggat in på Vodeclic (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Vodeclic, behöver du följande:

- En Azure AD-prenumeration
- En prenumeration Vodeclic SSO-aktiverad

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö [skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Vodeclic från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-vodeclic-from-the-gallery"></a>Lägg till Vodeclic från galleriet
Du måste lägga till Vodeclic från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Vodeclic i Azure AD.

**Om du vill lägga till Vodeclic från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Vodeclic**. Välj **Vodeclic** resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Vodeclic i resultatlistan](./media/vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Vodeclic baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i Vodeclic är att en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och relaterade användaren i Vodeclic.

Ge värdet i Vodeclic, **användarnamn** samma värde som **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Vodeclic:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Vodeclic](#create-a-vodeclic-test-user) har en motsvarighet för Britta Simon Vodeclic som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Vodeclic program.

**För att konfigurera Azure AD enkel inloggning med Vodeclic, gör du följande:**

1. I Azure-portalen på den **Vodeclic** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. I den **enkel inloggning** dialogrutan under **Single-Sign-on-läge**väljer **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Om du vill konfigurera programmet i **IDP** initierade läge i den **Vodeclic domän och URL: er** avsnittet, gör du följande:

    ![URL: er och Vodeclic domän med enkel inloggning information](./media/vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. I den **identifierare** Skriv en URL med följande mönster: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. I den **Reply URL** Skriv en URL med följande mönster: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Om du vill konfigurera programmet i **SP** initierade läge, Välj den **visa avancerade inställningar för URL: en** kryssrutan och utför följande steg:

    ![URL: er och Vodeclic domän med enkel inloggning information](./media/vodeclic-tutorial/tutorial_vodeclic_url1.png)

    I den **inloggnings-URL** Skriv en URL med följande mönster: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren svara URL och inloggnings-URL. Kontakta den [Vodeclic klienten supportteamet](mailto:hotline@vodeclic.com) att hämta dessa värden.

5. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**. Spara metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Välj **Spara**.

    ![Konfigurera enkel inloggning spara](./media/vodeclic-tutorial/tutorial_general_400.png)
    
7. Konfigurera enkel inloggning på den **Vodeclic** sida, skicka den hämtade **XML-Metadata för** till den [Vodeclic supportteamet](mailto:hotline@vodeclic.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentation på [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/vodeclic-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/vodeclic-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/vodeclic-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör du följande:

    ![Dialogrutan användare](./media/vodeclic-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-vodeclic-test-user"></a>Skapa en testanvändare Vodeclic

I det här avsnittet skapar du en användare som kallas Britta Simon i Vodeclic. Arbeta med den [Vodeclic supportteamet](mailto:hotline@vodeclic.com) att lägga till användare i Vodeclic-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

> [!NOTE]
> Du kan behöva hämta din dator i listan över godkända enligt kraven för application. För att det ska ske, måste du dela din offentliga IP-adressen med den [Vodeclic supportteamet](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Vodeclic.

![Tilldela rollen][200] 

**Om du vill tilldela Vodeclic Britta Simon gör du följande:**

1. Öppna vyn program i Azure-portalen och sedan gå till vyn directory. Gå sedan till **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Vodeclic**.

    ![Länken Vodeclic i listan med program](./media/vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

6. I den **användare och grupper** dialogrutan markerar du den **Välj** knappen.

7. I den **Lägg uppdrag** dialogrutan markerar du den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Vodeclic på åtkomstpanelen hämta loggas du automatiskt till Vodeclic-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/vodeclic-tutorial/tutorial_general_203.png

