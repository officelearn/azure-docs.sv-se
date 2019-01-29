---
title: 'Självstudier: Azure Active Directory-integrering med Egnyte | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeedes
ms.openlocfilehash: e33fc71e0e43864d7d70495fc5056a8acaf4ad56
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159020"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Självstudier: Azure Active Directory-integrering med Egnyte

I den här självstudien får du lära dig hur du integrerar Egnyte med Azure Active Directory (AD Azure).

Integrera Egnyte med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Egnyte.
- Du kan aktivera användarna att automatiskt få loggat in på Egnyte (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Egnyte, behöver du följande objekt:

- En Azure AD-prenumeration
- En Egnyte enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Egnyte från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-egnyte-from-the-gallery"></a>Att lägga till Egnyte från galleriet

För att konfigurera integrering av Egnyte i Azure AD, som du behöver lägga till Egnyte från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Egnyte från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Egnyte**väljer **Egnyte** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Egnyte i resultatlistan](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Egnyte baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Egnyte är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Egnyte upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Egnyte, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Egnyte](#creating-an-egnyte-test-user)**  – du har en motsvarighet för Britta Simon i Egnyte som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Egnyte program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Egnyte:**

1. I Azure-portalen på den **Egnyte** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Egnyte domän och URL: er med enkel inloggning för information](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Egnyte klienten supportteamet](https://www.egnyte.com/corp/contact_egnyte.html) att hämta det här värdet. 

5. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

6. På den **konfigurera Egnyte** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![Egnyte konfiguration](common/configuresection.png)

7. I ett annat webbläsarfönster logga du in på webbplatsen Egnyte företag som administratör.

8. Klicka på **Inställningar**.
   
    ![Inställningar](./media/egnyte-tutorial/ic787819.png "Inställningar")

9. I menyn klickar du på **inställningar**.

    ![Inställningar](./media/egnyte-tutorial/ic787820.png "Inställningar")

10. Klicka på den **Configuration** fliken och klicka sedan på **Security**.

    ![Säkerhet](./media/egnyte-tutorial/ic787821.png "Säkerhet")

11. I den **autentisering för enkel inloggning** avsnittet, utför följande steg:

    ![Enkel inloggning på autentisering](./media/egnyte-tutorial/ic787822.png "enkel inloggning för autentisering")   
    
    a. Som **autentisering för enkel inloggning**väljer **SAML 2.0**.
   
    b. Som **identitetsprovidern**väljer **AzureAD**.
   
    c. Klistra in **inloggnings-URL** kopieras från Azure-portalen till den **inloggnings-URL för identitetsprovider** textrutan.
   
    d. Klistra in **Azure AD-identifierare** som du har kopierat från Azure-portalen till den **identitet providern entitets-ID** textrutan.
      
    e. Öppna din Base64-kodat certifikat i anteckningar som hämtats från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textrutan.
   
    f. Som **standard användarmappning**väljer **e-postadress**.
   
    g. Som **använder domänspecifika utfärdarvärdet**väljer **inaktiverat**.
   
    h. Klicka på **Spara**.

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

### <a name="creating-an-egnyte-test-user"></a>Skapa en Egnyte testanvändare

Om du vill aktivera Azure AD-användare att logga in på Egnyte, måste de etableras i Egnyte. När det gäller Egnyte är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Egnyte** företagets plats som administratör.

2. Gå till **inställningar \> användare och grupper**.

3. Klicka på **Lägg till ny användare**, och välj sedan typ av användare som du vill lägga till.
   
    ![Användare](./media/egnyte-tutorial/ic787824.png "Användare")

4. I den **nya privilegierad användare** avsnittet, utför följande steg:
    
    ![Ny standardanvändare](./media/egnyte-tutorial/ic787825.png "ny standardanvändare")   

    a. I **e-post** text, ange den e-postadressen för användaren som **Brittasimon@contoso.com**.

    b. I **användarnamn** text, anger användarnamnet för användaren som **Brittasimon**.

    c. Välj **enkel inloggning** som **autentiseringstyp**.
   
    d. Klicka på **Spara**.
    
    >[!NOTE]
    >Azure Active Directory-kontoinnehavare får ett e-postmeddelande.
    >

>[!NOTE]
>Du kan använda alla andra Egnyte användare konto verktyg för att skapa eller API: er som tillhandahålls av Egnyte att etablera AAD-användarkonton.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Egnyte.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Egnyte**.

    ![Konfigurera enkel inloggning](./media/egnyte-tutorial/tutorial_egnyte_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Egnyte i åtkomstpanelen du bör få automatiskt loggat in på ditt Egnyte program.
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
