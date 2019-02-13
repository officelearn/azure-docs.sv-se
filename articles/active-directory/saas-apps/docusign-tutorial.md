---
title: 'Självstudier: Azure Active Directory-integrering med DocuSign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb6e130b772afabe939516ffa37ae292e2db5859
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178965"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Självstudier: Azure Active Directory-integrering med DocuSign

I den här självstudien får du lära dig hur du integrerar DocuSign med Azure Active Directory (AD Azure).

Integrera DocuSign med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till DocuSign.
- Du kan aktivera användarna att automatiskt få loggat in på DocuSign (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med DocuSign, behöver du följande objekt:

- En Azure AD-prenumeration
- En DocuSign enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till DocuSign från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-docusign-from-the-gallery"></a>Att lägga till DocuSign från galleriet

För att konfigurera integrering av DocuSign i Azure AD, som du behöver lägga till DocuSign från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till DocuSign från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **DocuSign**väljer **DocuSign** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![DocuSign i resultatlistan](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med DocuSign baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i DocuSign är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i DocuSign upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med DocuSign, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare DocuSign](#creating-a-docusign-test-user)**  – du har en motsvarighet för Britta Simon i DocuSign som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt DocuSign-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med DocuSign:**

1. I Azure-portalen på den **DocuSign** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![DocuSign domän och URL: er med enkel inloggning för information](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare som beskrivs senare **visa SAML 2.0 slutpunkter** avsnitt i självstudien.

5. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. På den **konfigurera DocuSign** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![DocuSign-konfiguration](common/configuresection.png)

7. I ett annat webbläsarfönster, logga in på din **DocuSign administrationsportalen** som administratör.

8. Längst upp till höger på sidan klickar du på profilen **logotyp** och klicka sedan på **gå till administratör**.
  
    ![Konfigurerar enkel inloggning][51]

9. På sidan med lösningar domän klickar du på **domäner**

    ![Konfigurerar enkel inloggning][50]

10. Under den **domäner** klickar du på **anspråk domän**.

    ![Konfigurerar enkel inloggning][52]

11. På den **gör anspråk på en domän** dialogrutan i den **domännamn** textruta, ange din företagsdomän och klicka sedan på **anspråk**. Se till att du verifiera domänen och att statusen är aktiv.

    ![Konfigurerar enkel inloggning][53]

12. På sidan med lösningar domän klickar du på **Identitetsprovidrar**.
  
    ![Konfigurerar enkel inloggning][54]

13. Under **Identitetsprovidrar** klickar du på **Lägg till IDENTITETSPROVIDER**. 

    ![Konfigurerar enkel inloggning][55]

14. På den **identitet providerinställningar** utför följande steg:

    ![Konfigurerar enkel inloggning][56]

    a. I den **namn** textrutan skriver du ett unikt namn för din konfiguration. Använd inte blanksteg.

    b. I den **Providerutgivare textrutan**, klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    c. I den **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    d. I den **utloggnings-URL för identitetsprovider** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    e. Välj **logga AuthN begäran**.

    f. Som **skicka AuthN-begäran från**väljer **POST**.

    g. Som **skicka utloggningsbegäran från**väljer **hämta**.

    h. I den **anpassad attributmappning** klickar du på **Lägg till ny MAPPNING**.

    ![Konfigurerar enkel inloggning][62]

    i. Välj fältet som du vill mappa med Azure AD-anspråk. I det här exemplet på **e-postadress** anspråk har mappats med värdet för **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Det är standardnamnet för anspråk från Azure AD för e-anspråk och klicka sedan på **spara**.

    ![Konfigurerar enkel inloggning][57]

    > [!NOTE]
    > Använd lämplig **användaridentifierare** att mappa användaren från Azure AD att DocuSign användarmappning. Välj rätt fältet och ange rätt värde baserat på Organisationsinställningarna för din.

    j. I den **identitet providern certifikat** klickar du på **Lägg till certifikat**, och sedan ladda upp det certifikat som du har hämtat från Azure AD-portalen och klicka på **spara**.

    ![Konfigurerar enkel inloggning][58]

    k. I den **Identitetsprovidrar** klickar du på **åtgärder**, och klicka sedan på **slutpunkter**.

    ![Konfigurerar enkel inloggning][59]

    l. I den **visa SAML 2.0 slutpunkter** avsnittet på **DocuSign administrationsportalen**, utför följande steg:

    ![Konfigurerar enkel inloggning][60]

    * Kopiera den **utfärdar-URL för Service Provider**, och klistra in den i den **identifierare** -textrutan i **DocuSign domän och URL: er** avsnittet på Azure portal.

    * Kopiera den **inloggnings-URL för tjänsten**, och klistra in den i den **inloggning på URL: en** -textrutan i **DocuSign domän och URL: er** avsnittet på Azure portal.

    * Klicka på **Stäng**

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

### <a name="creating-a-docusign-test-user"></a>Skapa en testanvändare DocuSign

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i DocuSign. DocuSign stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt DocuSign om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [DocuSign-supportteamet](https://support.docusign.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till DocuSign.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **DocuSign**.

    ![Konfigurera enkel inloggning](./media/docusign-tutorial/tutorial_docusign_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen DocuSign i åtkomstpanelen du bör få automatiskt loggat in på ditt DocuSign-program.
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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
