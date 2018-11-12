---
title: 'Självstudier: Azure Active Directory-integration med FreshDesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010821"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Självstudier: Azure Active Directory-integration med FreshDesk

I den här självstudien får du lära dig hur du integrerar FreshDesk med Azure Active Directory (AD Azure).

Integrera FreshDesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FreshDesk.
- Du kan aktivera användarna att automatiskt få loggat in på FreshDesk (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FreshDesk, behöver du följande objekt:

- En Azure AD-prenumeration
- En FreshDesk enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till FreshDesk från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-freshdesk-from-the-gallery"></a>Att lägga till FreshDesk från galleriet

För att konfigurera integrering av FreshDesk i Azure AD, som du behöver lägga till FreshDesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till FreshDesk från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **FreshDesk**väljer **FreshDesk** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![FreshDesk i resultatlistan](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med FreshDesk baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i FreshDesk är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i FreshDesk upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med FreshDesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare FreshDesk](#creating-a-freshdesk-test-user)**  – du har en motsvarighet för Britta Simon i FreshDesk som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt FreshDesk-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FreshDesk:**

1. I Azure-portalen på den **FreshDesk** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![FreshDesk domän och URL: er med enkel inloggning för information](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.freshdesk.com` eller ett annat värde som har föreslagits Freshdesk.

    b. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.freshdesk.com` eller ett annat värde som har föreslagits Freshdesk.

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [FreshDesk klienten supportteamet](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) att hämta dessa värden.

5. FreshDesk-program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta. Standardvärdet för **användaridentifierare** är **user.userprincipalname** men **FreshDesk** förväntar sig detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller Använd rätt attribut-värde baserat på konfigurationen för din organisation.

    ![image](./media/freshdesk-tutorial/i4-attribute.png)

6. I den **användaranspråk** avsnittet på den **användarattribut och anspråk** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    a. Klicka på **redigeringsikonen** att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/freshdesk-tutorial/i2-attribute.png)

    ![image](./media/freshdesk-tutorial/i3-attribute.png)

    b. Från den **källattribut** väljer **user.mail**.

    c. Klicka på **Spara**.

7. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. Öppna **kommandotolk** och kör följande kommandon:

    a. Ange `certutil.exe -dump FreshDesk.cer` värdet i Kommandotolken.

    > [!NOTE]
    > Här **FreshDesk.cer** är det certifikat som du har hämtat från Azure-portalen.

    b. Kopiera den **Cert Hash(sha256)** värde och klistra in den i anteckningar. 

9. På den **konfigurera FreshDesk** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![FreshDesk-konfiguration](common/configuresection.png)

10. Logga in på webbplatsen Freshdesk företag som en administratör i ett annat webbläsarfönster.

11. Välj den **inställningsikonen** och i den **Security** avsnittet, utför följande steg:

    ![Enkel inloggning](./media/freshdesk-tutorial/IC776770.png "enkel inloggning")
  
    a. För **enkel inloggning (SSO)** väljer **på**.

    b. Välj **SAML SSO**.

    c. I den **inloggnings-URL för SAML** textrutan klistra in **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    d. I den **URL för utloggning** textrutan klistra in **URL för utloggning** värde, som du har kopierat från Azure-portalen.

    e. I den **Security certifikat fingeravtryck** textrutan klistra in **Cert Hash(sha256)** värde som du fått tidigare.
  
    f. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-freshdesk-test-user"></a>Skapa en FreshDesk testanvändare

För att aktivera Azure AD-användare att logga in på FreshDesk, måste de etableras i FreshDesk.  
När det gäller FreshDesk är etablering en manuell aktivitet.

**Utför följande steg för att tillhandahålla en användarkonton:**

1. Logga in på din **Freshdesk** klient.

2. Klicka på menyn längst upp **Admin**.

    ![Administratören](./media/freshdesk-tutorial/IC776772.png "Admin")

3. I den **allmänna inställningar** fliken **agenter**.
  
    ![Agenter](./media/freshdesk-tutorial/IC776773.png "agenter")

4. Klicka på **ny Agent**.

    ![Ny Agent](./media/freshdesk-tutorial/IC776774.png "ny Agent")

5. I dialogrutan agentinformation utför du följande steg:

    ![Agentinformation](./media/freshdesk-tutorial/IC776775.png "agentinformation")

    a. I den **e-post** textrutan Ange Azure AD-e-postadress för Azure AD-konto som du vill etablera.

    b. I den **fullständigt namn** textrutan skriver du namnet på Azure AD-konto som du vill etablera.

    c. I den **rubrik** textrutan skriver du namnet på Azure AD-konto som du vill etablera.

    d. Klicka på **Spara**.

    >[!NOTE]
    >Azure AD-kontoinnehavare får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det aktiveras.
    >
    >[!NOTE]
    >Du kan använda alla andra Freshdesk användare konto verktyg för att skapa eller API: er som tillhandahålls av Freshdesk att etablera AAD-användarkonton till FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till FreshDesk.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **FreshDesk**.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen FreshDesk i åtkomstpanelen du bör få automatiskt loggat in på ditt FreshDesk-program.
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
