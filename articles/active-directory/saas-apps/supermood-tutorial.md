---
title: 'Självstudier: Azure Active Directory-integrering med Supermood | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Supermood.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60efdd5b509045ce2398720d650894983c2cfc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639894"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>Självstudier: Azure Active Directory-integrering med Supermood

I den här självstudien får du lära dig hur du integrerar Supermood med Azure Active Directory (AD Azure).

Integrera Supermood med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Supermood.
- Du kan aktivera användarna att automatiskt få loggat in på Supermood (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Supermood, behöver du följande objekt:

- En Azure AD-prenumeration
- En Supermood enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Supermood från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-supermood-from-the-gallery"></a>Att lägga till Supermood från galleriet
Om du vill konfigurera integreringen av Supermood i Azure AD, som du behöver lägga till Supermood från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Supermood från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Supermood**väljer **Supermood** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Supermood i resultatlistan](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Supermood baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Supermood är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Supermood upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Supermood, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Supermood](#create-a-supermood-test-user)**  – du har en motsvarighet för Britta Simon i Supermood som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Supermood program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Supermood:**

1. I Azure-portalen på den **Supermood** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

1. På den **Supermood domän och URL: er** avsnittet, utför följande steg:

    ![Supermood domän och URL: er med enkel inloggning för information](./media/supermood-tutorial/tutorial_supermood_url.png)

    a. Kontrollera **visa avancerade URL-inställningar**.

    b. Om du vill konfigurera programmet i **IDP** har initierat läge, den **Vidarebefordransstatus** textrutan anger du ett URL: `https://supermood.co/auth/sso/saml20`

    c. Om du vill konfigurera programmet i **SP** har initierat läge, den **inloggnings-URL** textrutan anger du ett URL: `https://supermood.co/app/#!/loginv2`

1. Supermood program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera enkel inloggning](./media/supermood-tutorial/tutorial_supermood_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna **Namnrymd** tom.
    
    d. Klicka på **Ok**

1. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för nedladdning av certifikatet](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/supermood-tutorial/tutorial_general_400.png)

1. Gå till din Supermood.co administrationspanel som säkerhetsadministratör.

1. Klicka på **mitt konto** (längst ned till vänster) och **enkel inloggning (SSO)**.

    ![Det enda certifikatet](./media/supermood-tutorial/tutorial_supermood_single.png)
1. På **Your SAML 2.0-konfigurationer**, klickar du på **lägga till en SAML 2.0-konfiguration för en e-postdomän**.

    ![Lägga till certifikatet](./media/supermood-tutorial/tutorial_supermood_add.png)

1. På **lägga till en SAML 2.0-konfiguration för en e-postdomän**. avsnittet, utför följande steg:

    ![Certifikat-saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. I den **e-postdomän för den här identitetsprovidern** textrutan skriver din domän.

    b. I den **använder en URL för metadata** textrutan klistra in den **Appfederationsmetadata** som du har kopierat från Azure-portalen.

    c. Klicka på **Lägg till**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/supermood-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/supermood-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/supermood-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/supermood-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-supermood-test-user"></a>Skapa en Supermood testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Supermood. Supermood stöder just-in-time-etablering, vilket är som standard aktiverad för användare vars e-postmeddelanden som hör till de domäner som har lagts till under konfigurationen Supermood slutet. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Supermood om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Supermood supportteamet](mailto:hello@supermood.fr).


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Supermood.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Supermood, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Supermood**.

    ![Länken Supermood i listan med program](./media/supermood-tutorial/tutorial_supermood_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Supermood i åtkomstpanelen du bör få automatiskt loggat in på ditt Supermood program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png

