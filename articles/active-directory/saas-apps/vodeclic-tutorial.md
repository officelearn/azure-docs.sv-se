---
title: 'Självstudier: Azure Active Directory-integrering med Vodeclic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 14dc205857a6a067421fbb51dbbbf0c35c556e0e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150605"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Självstudier: Azure Active Directory-integrering med Vodeclic

I den här självstudien får du lära dig hur du integrerar Vodeclic med Azure Active Directory (AD Azure).

Integrera Vodeclic med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Vodeclic.
- Du kan aktivera användarna att automatiskt få loggat in på Vodeclic (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Vodeclic, behöver du följande objekt:

- En Azure AD-prenumeration
- En Vodeclic SSO-aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö [skaffa en månads kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Vodeclic från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-vodeclic-from-the-gallery"></a>Lägg till Vodeclic från galleriet
För att konfigurera integrering av Vodeclic i Azure AD, som du behöver lägga till Vodeclic från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Vodeclic från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Företagsprogram][2]
    
1. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Vodeclic**. Välj **Vodeclic** i resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Vodeclic i resultatlistan](./media/vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Vodeclic baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilken användaren motsvarighet i Vodeclic är att en användare i Azure AD. Med andra ord måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren i Vodeclic.

I Vodeclic, ger värdet **användarnamn** samma värde som **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna.

Om du vill konfigurera och testa Azure AD enkel inloggning med Vodeclic, utför du följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
1. [Skapa en testanvändare Vodeclic](#create-a-vodeclic-test-user) har en motsvarighet för Britta Simon i Vodeclic som är länkad till en Azure AD-representation av användaren.
1. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
1. [Testa enkel inloggning](#test-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Vodeclic program.

**Om du vill konfigurera Azure AD enkel inloggning med Vodeclic, gör du följande:**

1. I Azure-portalen på den **Vodeclic** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. I den **enkel inloggning** dialogrutan **Single-Sign-on-läge**väljer **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

1. Om du vill konfigurera programmet i **IDP** har initierat läge, den **Vodeclic domän och URL: er** avsnittet, gör följande:

    ![Vodeclic domän och URL: er med enkel inloggning för information](./media/vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. I den **identifierare** skriver du en URL med följande mönster: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. I den **svars-URL** skriver du en URL med följande mönster: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

1. Om du vill konfigurera programmet i **SP** initierad läge, väljer den **visa avancerade URL-inställningar** och utför du följande steg:

    ![Vodeclic domän och URL: er med enkel inloggning för information](./media/vodeclic-tutorial/tutorial_vodeclic_url1.png)

    I den **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren svars-URL och inloggnings-URL. Kontakta den [Vodeclic klienten supportteamet](mailto:hotline@vodeclic.com) att hämta dessa värden.

1. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**. Spara metadatafilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

1. Välj **Spara**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/vodeclic-tutorial/tutorial_general_400.png)
    
1. Att konfigurera enkel inloggning på den **Vodeclic** sida, skicka de hämtade **XML-Metadata för** till den [Vodeclic supportteamet](mailto:hotline@vodeclic.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och komma åt den inbäddade dokumentation genom den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen embedded-dokumentation på [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen, i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/vodeclic-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](./media/vodeclic-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/vodeclic-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan rutan, gör följande:

    ![Dialogrutan Användare](./media/vodeclic-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-vodeclic-test-user"></a>Skapa en Vodeclic testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Vodeclic. Arbeta med den [Vodeclic supportteamet](mailto:hotline@vodeclic.com) att lägga till användare i Vodeclic-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

> [!NOTE]
> Du kan behöva hämta dator-godkänd enligt kraven för programmet. För att det ska ske, måste du dela din offentliga IP-adress med det [Vodeclic supportteamet](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Vodeclic.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Vodeclic, gör du följande:**

1. Öppna vyn program i Azure-portalen och sedan gå till vyn directory. Gå sedan till **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Vodeclic**.

    ![Länken Vodeclic i listan med program](./media/vodeclic-tutorial/tutorial_vodeclic_app.png)  

1. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”][202]

1. Välj knappen **Lägg till**. Välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

1. I den **användare och grupper** dialogrutan den **Välj** knappen.

1. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Vodeclic i åtkomstpanelen, får du automatiskt inloggad på programmets Vodeclic.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

