---
title: 'Självstudier: Azure Active Directory-integrering med Allbound SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Allbound SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 15011ddf-941f-4da2-b993-40ad94a08e42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: e0658841e121dc8ea5bd4c704d99fd6bac98a32f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171396"
---
# <a name="tutorial-azure-active-directory-integration-with-allbound-sso"></a>Självstudier: Azure Active Directory-integrering med Allbound SSO

I den här självstudien får du lära dig hur du integrerar Allbound SSO med Azure Active Directory (AD Azure).

Integrera Allbound SSO med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Allbound SSO.
- Du kan aktivera användarna att automatiskt få loggat in på Allbound SSO (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Allbound SSO, behöver du följande objekt:

- En Azure AD-prenumeration
- En Allbound SSO enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Allbound SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-allbound-sso-from-the-gallery"></a>Att lägga till Allbound SSO från galleriet

Om du vill konfigurera integreringen av Allbound SSO till Azure AD, som du behöver lägga till Allbound SSO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Allbound SSO från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Allbound SSO**väljer **Allbound SSO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Allbound SSO i resultatlistan](./media/allbound-sso-tutorial/tutorial_allbound-sso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Allbound SSO utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Allbound SSO är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Allbound SSO upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Allbound SSO, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Allbound SSO](#creating-an-allbound-sso-test-user)**  – du har en motsvarighet för Britta Simon i Allbound SSO som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Allbound SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Allbound SSO:**

1. I Azure-portalen på den **Allbound SSO** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Allbound SSO domän och URL: er med enkel inloggning för information](./media/allbound-sso-tutorial/tutorial_allbound-sso_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<SUBDOMAIN>.allbound.com/`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.allbound.com/acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Allbound SSO domän och URL: er med enkel inloggning för information](./media/allbound-sso-tutorial/tutorial_allbound-sso_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<SUBDOMAIN>.allbound.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Allbound klientfilerna supportteamet](mailto:engineering@allbound.com) att hämta dessa värden.

6. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned **Federation Metadata XML** och spara för metadatafilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/allbound-sso-tutorial/tutorial_allbound-sso_certificate.png) 

7. På den **konfigurera Allbound SSO** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![Allbound SSO-konfiguration](common/configuresection.png)

8. Att konfigurera enkel inloggning på **Allbound SSO** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** till [Allbound SSO-supportteamet](mailto:engineering@allbound.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

### <a name="creating-an-allbound-sso-test-user"></a>Skapa en testanvändare Allbound SSO

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Allbound SSO. Allbound SSO stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Allbound SSO om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Allbound SSO-supportteamet](mailto:engineering@allbound.com).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Allbound SSO.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Allbound SSO**.

    ![Konfigurera enkel inloggning](./media/allbound-sso-tutorial/tutorial_allbound-sso_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Allbound SSO i åtkomstpanelen du bör få automatiskt loggat in på ditt Allbound SSO-program.
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
