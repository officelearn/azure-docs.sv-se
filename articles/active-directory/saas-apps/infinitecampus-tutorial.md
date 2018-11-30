---
title: 'Självstudier: Azure Active Directory-integration med oändlig Campus | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och oändlig Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 837d18a04a0cd22f29a5cbcb8b06b7e3f5fae255
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632814"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Självstudier: Azure Active Directory-integration med oändlig Campus

I den här självstudien får du lära dig hur du integrerar oändlig Campus med Azure Active Directory (AD Azure).

Integrera oändlig Campus med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till oändlig Campus.
- Du kan aktivera användarna att automatiskt få loggat in på oändlig Campus (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med oändlig Campus, behöver du följande objekt:

- En Azure AD-prenumeration
- En oändlig Campus enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).
- Du måste vara en Azure Active Directory-administratör för att slutföra konfigurationen minimum.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till oändlig Campus från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-infinite-campus-from-the-gallery"></a>Att lägga till oändlig Campus från galleriet

För att konfigurera integrering av oändlig Campus i Azure AD, som du behöver lägga till oändlig Campus från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till oändlig Campus från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **oändlig Campus**väljer **oändlig Campus** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Oändlig Campus i resultatlistan](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med oändlig Campus baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i oändlig Campus är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i oändlig Campus upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med oändlig Campus, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en oändlig Campus testanvändare](#creating-a-infinite-campus-test-user)**  – du har en motsvarighet för Britta Simon i oändlig Campus som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt oändlig Campus-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med oändlig Campus:**

1. I Azure-portalen på den **oändlig Campus** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

5. På den **SAML grundkonfiguration** om du har **tjänstleverantör metadatafil** (steg **11.b**), utför följande steg:

    a. Klicka på **ladda upp metadatafilen**.

    ![image](common/b9_saml.png)

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    ![image](common/b9(1)_saml.png)

    c. När metadatafilen har laddats upp den **identifierare** och **svars-URL** värden får automatiskt ifylld i **SAML grundkonfiguration** avsnittet textrutan som visas nedan :

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster (domänen varierar beroende på som är värd för modellen): `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    > [!NOTE]
    > Du får den **tjänstleverantör metadatafil** på sidan oändlig Campus SSO konfiguration av ServiceProvider som beskrivs senare i självstudien.

6. Om du inte har **tjänstleverantör metadatafil**, utför följande steg:

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Oändlig Campus domän och URL: er med enkel inloggning för information](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på kopian **ikonen** att kopiera **Appfederationsmetadata**  och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. På den **konfigurera oändlig Campus** använder följande värden för att verifiera när ladda upp och använda Azure-metadata fil-URL.

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![Oändlig Campus-konfiguration](common/configuresection.png)

8. I ett annat webbläsarfönster, logga in på oändlig Campus som en administratör.

9. På vänster sida av menyn, klickar du på **systemadministration**.

    ![Administratören](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Gå till **användarsäkerhet** > **SAML Management** > **SSO konfiguration av ServiceProvider**.

    ![Saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. På den **SSO konfiguration av ServiceProvider** utför följande steg:

    ![Sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Välj **aktivera SAML enkel inloggning**.

    b. Klicka på **Metadata för Service Provider** länken för att spara den **tjänstleverantör metadatafil** på datorn och ladda upp den i **grundläggande SAML-konfiguration** avsnitt för att automatiskt polulate den **identifierare** och **svars-URL** värdena i Azure-portalen (finns i steg 5).

    c. På den **Välj ett alternativ för att hämta serverdata identitet Provider (IDP)** väljer du **Metadata_url** och klistra in den **Appfederationsmetadata** i textrutan och Klicka sedan på **synkronisering**.

    d. När du klickar på **synkronisering** värdena får fylls i **SSO konfiguration av ServiceProvider** sidan.

    e. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en _enda_ testanvändare i Azure-portalen kallas Britta Simon.

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

### <a name="creating-a-infinite-campus-test-user"></a>Skapa en oändlig Campus testanvändare

Oändlig Campus har en arkitektur för demografi centrerad. Kontakta [oändlig Campus-supportteamet](mailto:sales@infinitecampus.com) att lägga till användare i oändlig Campus-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till oändlig Campus.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **oändlig Campus**.

    ![Konfigurera enkel inloggning](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen oändlig Campus i åtkomstpanelen du bör få automatiskt loggat in på ditt oändlig Campus-program. Om du loggar in i oändlig Camnpus programmet i samma webbläsare som du administrerar Azure AD, se till att du är inloggad i Azure AD som testanvändaren. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

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
