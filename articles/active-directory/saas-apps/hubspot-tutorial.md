---
title: 'Självstudier: Azure Active Directory-integrering med HubSpot | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fd70b80dd512b530d367d57cd9fc04ec28f27b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211707"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Självstudier: Azure Active Directory-integrering med HubSpot

I den här självstudien får du lära dig hur du integrerar HubSpot med Azure Active Directory (AD Azure).

Integrera HubSpot med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till HubSpot
- Du kan aktivera användarna att automatiskt få loggat in på HubSpot (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med HubSpot, behöver du följande objekt:

- En Azure AD-prenumeration
- En HubSpot enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till HubSpot från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-hubspot-from-the-gallery"></a>Att lägga till HubSpot från galleriet

För att konfigurera integrering av HubSpot i Azure AD, som du behöver lägga till HubSpot från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HubSpot från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

4. I sökrutan skriver **HubSpot**. Välj **HubSpot** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med HubSpot baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i HubSpot är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i HubSpot upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med HubSpot, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **Skapa en testanvändare HubSpot** – du har en motsvarighet för Britta Simon i HubSpot som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt HubSpot program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med HubSpot:**

1. I Azure-portalen på den **HubSpot** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/tutorial_general_301.png)

3. Om du vill ändra till **SAML** -läge från ett annat ett annat läge, klickar du på **ändra enkel inloggning läge** på skärmen.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/tutorial_general_300.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/tutorial_general_302.png)

5. På den **SAML grundkonfiguration** avsnittet utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![HubSpot domän och URL: er med enkel inloggning för information](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. I den **identifierare** textrutan anger du URL med hjälp av följande mönster: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. I den **svars-URL** textrutan anger du URL med hjälp av följande mönster: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL som beskrivs senare i den här självstudien. Kontakta [HubSpot klienten supportteamet](https://help.hubspot.com/) att hämta dessa värden.

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![HubSpot domän och URL: er med enkel inloggning för information](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    I den **inloggnings-URL** textrutan anger du URL: `https://app.hubspot.com/login`

6. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. På den **konfigurera HubSpot** klickar du på kopieringsknappen för att kopiera den **inloggnings-URL** och **Azure AD-identifierare** värden.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. Öppna en ny flik i webbläsaren och logga in på ditt administratörskonto för HubSpot.

9. Klicka på **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config1.png)

10. Klicka på **konto standardvärden**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config2.png)

11. Rulla ned till den **Security** och klicka på **konfigurera**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config3.png)

12. På den **Konfigurera enkel inloggning** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config4.png)

    a. Klickar du på **kopia** för att kopiera den **målgrupp URl(Service Provider Entity ID)** värde och klistra in den i den **identifierare** -textrutan i den **grundläggande SAML Konfigurationen** avsnitt i Azure-portalen.

    b. Klickar du på **kopia** för att kopiera den **logga på URL: en, ACS, mottagaren eller omdirigering** värde och klistra in den i den **svars-URL** -textrutan i den **grundläggande SAML Konfigurationen** avsnitt i Azure-portalen.

    c. I den **identitet identifierare eller utfärdar-URL** textrutan klistra in den **Azure AD-identifierare** värde som du har kopierat från Azure-portalen.

    d. I den **enkel inloggnings-URL för identitetsprovider** textrutan klistra in den **inloggnings-URL** värde som du har kopierat från Azure-portalen.

    e. Öppna din hämtade **Certificate(Base64)** -filen i anteckningar. Kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** box.

    f. Klicka på **Verifiera**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/hubspot-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/hubspot-tutorial/create_aaduser_02.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-hubspot-test-user"></a>Skapa en HubSpot testanvändare

Om du vill aktivera Azure AD-användare att logga in på HubSpot, måste de etableras i HubSpot.
När det gäller HubSpot är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **HubSpot** företagets plats som administratör.

2. Klicka på **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/config1.png)

3. Klicka på **användare och team**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user1.png)

4. Klicka på **Skapa användare**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user2.png)

5. Ange e-postadress för användaren som **brittasimon@contoso.com** i den **Lägg till e-addess(es)** textrutan och klicka på **nästa**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user3.png)

6. På den **skapa användare** ,. Gå igenom varje enskild fliken och väljer lämpliga inställningar och behörigheter för användaren och klicka på **nästa**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user4.png)

7. Klicka på **skicka** att skicka inbjudan till användaren.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Användaren kommer aktiveras efter att acceptera inbjudan.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till HubSpot.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **HubSpot**.

    ![Konfigurera enkel inloggning](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen HubSpot i åtkomstpanelen, får du automatiskt inloggningssidan i HubSpot program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png
