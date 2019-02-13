---
title: 'Självstudier: Azure Active Directory-integrering med Peakon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e32f0ac6fcc6839bad973c3d014436d08ae43896
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211317"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Självstudier: Azure Active Directory-integrering med Peakon

I den här självstudien får du lära dig hur du integrerar Peakon med Azure Active Directory (AD Azure).

Integrera Peakon med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Peakon.
- Du kan aktivera användarna att automatiskt få loggat in på Peakon (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Peakon, behöver du följande objekt:

- En Azure AD-prenumeration
- En Peakon enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte produktionsmiljö, om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Peakon från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-peakon-from-the-gallery"></a>Att lägga till Peakon från galleriet

För att konfigurera integrering av Peakon i Azure AD, som du behöver lägga till Peakon från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Peakon från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Peakon**väljer **Peakon** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Peakon i resultatlistan](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Peakon baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Peakon är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Peakon upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Peakon, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Peakon](#creating-a-peakon-test-user)**  – du har en motsvarighet för Britta Simon i Peakon som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Peakon program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Peakon:**

1. I Azure-portalen på den **Peakon** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Peakon domän och URL: er med enkel inloggning för information](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://app.peakon.com/saml/<companyid>/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://app.peakon.com/saml/<companyid>/assert`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Peakon domän och URL: er med enkel inloggning för information](./media/peakon-tutorial/tutorial_peakon_url1.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL som är förklaras latert i självstudien.

6. I den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. På den **konfigurera Peakon** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![Peakon konfiguration](common/configuresection.png)

8. I ett annat webbläsarfönster, loggar du in Peakon som administratör.

9. I menyraden till vänster på sidan klickar du på **Configuration**, gå sedan till **integreringar**.

    ![Konfig](./media/peakon-tutorial/tutorial_peakon_config.png)

10. På **integreringar** klickar du på **enkel inloggning**.

    ![Enda](./media/peakon-tutorial/tutorial_peakon_single.png)

11. Under **enkel inloggning** klickar du på **aktivera**.

    ![Aktivera](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. På den **enkel inloggning för anställda som använder SAML** avsnittet, utför följande steg:

    ![Saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. I den **inloggnings-URL för enkel inloggning** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. I den **utloggnings-URL för enkel inloggning** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    c. Klicka på **Välj fil** att ladda upp det certifikat som du har hämtat från Azure-portalen i rutan certifikat.

    d. Klicka på den **ikonen** att kopiera den **entitets-ID** och klistra in i **identifierare** -textrutan i **grundläggande SAML-konfiguration** avsnittet på Azure-portalen.

    e. Klicka på den **ikonen** att kopiera den **svars-URL (ACS)** och klistra in i **svars-URL** -textrutan i **grundläggande SAML-konfiguration**   avsnittet om Azure-portalen.

    f. Klicka på **Spara**

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-peakon-test-user"></a>Skapa en Peakon testanvändare

För att aktivera Azure AD-användare att logga in på Peakon, måste de etableras i Peakon.  
När det gäller Peakon är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Peakon företag som administratör.

2. I menyraden till vänster på sidan klickar du på **Configuration**, gå sedan till **anställda**.

    ![Medarbetaren](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. På upp till höger på sidan, klickar du på **Lägg till medarbetare**.

      ![Lägg till medarbetare](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. På den **nyanställd** dialogrutan utför följande steg:

     ![Den nya medarbetaren](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. I den **namn** textrutan typ förnamn som **Britta** - och efternamn som **simon**.

    b. I den **e-post** textrutan, skriver du in den e-postadressen som **Brittasimon@contoso.com**.

    c. Klicka på **skapa medarbetare**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Peakon.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Peakon**.

    ![Konfigurera enkel inloggning](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Peakon i åtkomstpanelen du bör få automatiskt loggat in på ditt Peakon program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
