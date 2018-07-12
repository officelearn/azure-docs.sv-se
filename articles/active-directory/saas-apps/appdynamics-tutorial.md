---
title: 'Självstudier: Azure Active Directory-integrering med AppDynamics | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AppDynamics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: jeedes
ms.openlocfilehash: 3600e83d18f8cabd03c46af2ef47445c588cbdb5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548294"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Självstudier: Azure Active Directory-integrering med AppDynamics

I den här självstudien får du lära dig hur du integrerar AppDynamics med Azure Active Directory (AD Azure).

Integrera AppDynamics med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AppDynamics
- Du kan aktivera användarna att automatiskt få loggat in på AppDynamics (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med AppDynamics, behöver du följande objekt:

- En Azure AD-prenumeration
- En AppDynamics enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till AppDynamics från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-appdynamics-from-the-gallery"></a>Att lägga till AppDynamics från galleriet
För att konfigurera integrering av AppDynamics i Azure AD, som du behöver lägga till AppDynamics från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till AppDynamics från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **AppDynamics**.

    ![Skapa en Azure AD-användare för testning](./media/appdynamics-tutorial/tutorial_appdynamics_search.png)

5. I resultatpanelen väljer **AppDynamics**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/appdynamics-tutorial/tutorial_appdynamics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med AppDynamics baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i AppDynamics är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i AppDynamics upprättas.

I AppDynamics, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med AppDynamics, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare AppDynamics](#creating-an-appdynamics-test-user)**  – du har en motsvarighet för Britta Simon i AppDynamics som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program med AppDynamics.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med AppDynamics:**

1. I Azure-portalen på den **AppDynamics** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/appdynamics-tutorial/tutorial_appdynamics_samlbase.png)

3. På den **AppDynamics domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/appdynamics-tutorial/tutorial_appdynamics_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.saas.appdynamics.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [AppDynamics klienten supportteamet](https://www.appdynamics.com/support/) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/appdynamics-tutorial/tutorial_appdynamics_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/appdynamics-tutorial/tutorial_general_400.png)

6. På den **AppDynamics Configuration** klickar du på **konfigurera AppDynamics** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/appdynamics-tutorial/tutorial_appdynamics_configure.png)

7. I ett annat webbläsarfönster logga du in på webbplatsen AppDynamics företag som administratör.

8. I verktygsfältet högst upp, klickar du på **inställningar**, och klicka sedan på **Administration**.

    ![Administration](./media/appdynamics-tutorial/ic790216.png "Administration")

9. Klicka på den **autentiseringsprovider** fliken.

    ![Autentiseringsprovider](./media/appdynamics-tutorial/ic790224.png "autentiseringsprovider")

10. I den **autentiseringsprovider** avsnittet, utför följande steg:

    ![SAML-konfiguration](./media/appdynamics-tutorial/ic790225.png "SAML-konfiguration")

    a. Som **autentiseringsprovider**väljer **SAML**.

    b. I den **inloggnings-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. I den **URL för utloggning** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.

    d. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat** textrutan

    e. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/appdynamics-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/appdynamics-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/appdynamics-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/appdynamics-tutorial/create_aaduser_04.png)

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-an-appdynamics-test-user"></a>Skapa en testanvändare AppDynamics

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i AppDynamics. AppDynamics stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt AppDynamics om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [AppDynamics klienten supportteamet](https://www.appdynamics.com/support/).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AppDynamics.

![Tilldela användare][200]

**Om du vill tilldela Britta Simon AppDynamics, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **AppDynamics**.

    ![Konfigurera enkel inloggning](./media/appdynamics-tutorial/tutorial_appdynamics_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen AppDynamics i åtkomstpanelen du bör få automatiskt loggat in på ditt AppDynamics-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appdynamics-tutorial/tutorial_general_01.png
[2]: ./media/appdynamics-tutorial/tutorial_general_02.png
[3]: ./media/appdynamics-tutorial/tutorial_general_03.png
[4]: ./media/appdynamics-tutorial/tutorial_general_04.png

[100]: ./media/appdynamics-tutorial/tutorial_general_100.png

[200]: ./media/appdynamics-tutorial/tutorial_general_200.png
[201]: ./media/appdynamics-tutorial/tutorial_general_201.png
[202]: ./media/appdynamics-tutorial/tutorial_general_202.png
[203]: ./media/appdynamics-tutorial/tutorial_general_203.png
