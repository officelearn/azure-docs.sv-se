---
title: 'Självstudier: Azure Active Directory-integrering med Phraseanet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Phraseanet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 078d84ce-e054-4ae1-a52e-46a94a6959a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49a311b74fbc48f414f530e962af39d17a346353
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175548"
---
# <a name="tutorial-azure-active-directory-integration-with-phraseanet"></a>Självstudier: Azure Active Directory-integrering med Phraseanet

I den här självstudien får du lära dig hur du integrerar Phraseanet med Azure Active Directory (AD Azure).

Integrera Phraseanet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Phraseanet.
- Du kan aktivera användarna att automatiskt få loggat in på Phraseanet (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Phraseanet, behöver du följande objekt:

- En Azure AD-prenumeration
- En Phraseanet enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Phraseanet från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-phraseanet-from-the-gallery"></a>Att lägga till Phraseanet från galleriet
För att konfigurera integrering av Phraseanet i Azure AD, som du behöver lägga till Phraseanet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Phraseanet från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/phraseanet-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/phraseanet-tutorial/a_select_app.png)
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![image](./media/phraseanet-tutorial/a_new_app.png)

4. I sökrutan skriver **Phraseanet**väljer **Phraseanet** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/phraseanet-tutorial/tutorial_phraseanet_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Phraseanet baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Phraseanet är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Phraseanet upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Phraseanet, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Phraseanet](#create-a-phraseanet-test-user)**  – du har en motsvarighet för Britta Simon i Phraseanet som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Phraseanet program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Phraseanet:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **Phraseanet** application integration markerar **enkel inloggning**.

    ![image](./media/phraseanet-tutorial/b1_b2_select_sso.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/phraseanet-tutorial/b1_b2_saml_sso.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![image](./media/phraseanet-tutorial/b1-domains_and_urlsedit.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I den **inloggnings-URL** anger en URL som:  `https://<SUBDOMAIN>.alchemyasp.com`

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_url.png)

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Phraseanet supportteamet](mailto:support@alchemy.fr) att hämta värdet.
 
5. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **Federation Metadata-XML**  och spara den på din dator.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_certificate.png) 

6. Att konfigurera enkel inloggning på **Phraseanet** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** till [Phraseanet supportteamet](mailto:support@alchemy.fr). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![image](./media/phraseanet-tutorial/d_users_and_groups.png)

2. Välj **Ny användare** överst på skärmen.

    ![image](./media/phraseanet-tutorial/d_adduser.png)

3. Genomför följande steg i Användaregenskaper.

    ![image](./media/phraseanet-tutorial/d_userproperties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-a-phraseanet-test-user"></a>Skapa en Phraseanet testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Phraseanet. Arbeta med [Phraseanet supportteamet](mailto:support@alchemy.fr) att lägga till användare i Phraseanet-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Phraseanet.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/phraseanet-tutorial/d_all_applications.png)

2. I listan med program väljer **Phraseanet**.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_app.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![image](./media/phraseanet-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/phraseanet-tutorial/d_assign_user.png)

4. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

5. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Phraseanet i åtkomstpanelen du bör få automatiskt loggat in på ditt Phraseanet program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


