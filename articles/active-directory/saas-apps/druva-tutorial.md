---
title: 'Självstudier: Azure Active Directory-integrering med Druva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 24f0a74f5abb625ba7350c2bd3a500fc2f296575
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824702"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Självstudier: Azure Active Directory-integrering med Druva

I den här självstudien får du lära dig hur du integrerar Druva med Azure Active Directory (AD Azure).

Integrera Druva med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Druva.
- Du kan aktivera användarna att automatiskt få loggat in på Druva (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Druva, behöver du följande objekt:

- En Azure AD-prenumeration
- En Druva enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Druva från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-druva-from-the-gallery"></a>Att lägga till Druva från galleriet
För att konfigurera integrering av Druva i Azure AD, som du behöver lägga till Druva från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Druva från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Druva**väljer **Druva** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Druva i resultatlistan](./media/druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Druva baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Druva är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Druva upprättas.

I Druva, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Druva, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Druva](#create-a-druva-test-user)**  – du har en motsvarighet för Britta Simon i Druva som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Druva program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Druva:**

1. I Azure-portalen på den **Druva** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/druva-tutorial/tutorial_druva_samlbase.png)

1. På den **Druva domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_druva_url.png)

    I den **identifierare** textrutan skriver strängvärdet: `druva-cloud`
    
1. Kontrollera **visa avancerade URL-inställningar**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_druva_url1.png)
    
    I den **inloggnings-URL** textrutan anger du URL: `https://cloud.druva.com/home`

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/druva-tutorial/tutorial_druva_certificate.png) 

1. Programmets Druva förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar till din **SAML-Tokenattribut** konfiguration. 

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_druva_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:

    | Attributnamn      | Attributvärde      |
    | ------------------- | -------------------- |
    | insync\_auth\_token |Ange det genererade värdet token |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_attribute_05.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden. Det genererade värdet token förklaras senare i självstudien.
    
    d. Klicka på **OK**.    

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_general_400.png)

1. På den **Druva Configuration** klickar du på **konfigurera Druva** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_druva_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen Druva företag som administratör.

1. Gå till **hantera \> inställningar**.

    ![Inställningar](./media/druva-tutorial/ic795091.png "Inställningar")

1. I dialogrutan Inställningar för enkel inloggning utför du följande steg:

    ![Enkel inloggning inställningar](./media/druva-tutorial/ic795092.png "enkel inloggnings-inställningar")
    
    a. I **inloggnings-URL för ID** textrutan klistra in värdet för **enkel inloggnings-URL för**, som du har kopierat från Azure-portalen.
        
    b. I **utloggnings-URL för ID** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen
        
    c. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **ID providern certifikat** textrutan
     
    d. Öppna den **inställningar** klickar du på **spara**.

1. På den **inställningar** klickar du på **generera SSO Token**.

    ![Inställningar](./media/druva-tutorial/ic795093.png "Inställningar")

1. På den **enkel inloggning autentiseringstoken** dialogrutan utför följande steg:

    ![SSO-Token](./media/druva-tutorial/ic795094.png "SSO-Token")
    
    a. Klickar du på **kopia**, klistra in kopieras värdet i den **värdet** -textrutan i den **lägga till attributet** avsnitt i Azure-portalen.
    
    b. Klicka på **Stäng**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/druva-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/druva-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/druva-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/druva-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-druva-test-user"></a>Skapa en Druva testanvändare

För att aktivera Azure AD-användare att logga in på Druva, måste de etableras i Druva. När det gäller Druva är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **Druva** företagets plats som administratör.

1. Gå till **hantera \> användare**.
   
   ![Hantera användare](./media/druva-tutorial/ic795097.png "hantera användare")

1. Klicka på **skapa en ny**.
   
   ![Hantera användare](./media/druva-tutorial/ic795098.png "hantera användare")

1. I dialogrutan Skapa ny användare utför du följande steg:
   
   ![Skapa ny användare](./media/druva-tutorial/ic795099.png "Skapa ny användare")
   
   a. I den **e-postadress** textrutan Ange e-postadress för användaren som **brittasimon@contoso.com**.
   
   b. I den **namn** textrutan anger du namnet på användaren som **BrittaSimon**.
   
   c. Klicka på **skapa användare**.

>[!NOTE]
>Du kan använda alla andra Druva användare konto verktyg för att skapa eller API: er som tillhandahålls av Druva för att etablera användarkonton i Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Druva.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Druva, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Druva**.

    ![Länken Druva i listan med program](./media/druva-tutorial/tutorial_druva_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Druva i åtkomstpanelen du bör få automatiskt loggat in på ditt Druva program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/druva-tutorial/tutorial_general_01.png
[2]: ./media/druva-tutorial/tutorial_general_02.png
[3]: ./media/druva-tutorial/tutorial_general_03.png
[4]: ./media/druva-tutorial/tutorial_general_04.png

[100]: ./media/druva-tutorial/tutorial_general_100.png

[200]: ./media/druva-tutorial/tutorial_general_200.png
[201]: ./media/druva-tutorial/tutorial_general_201.png
[202]: ./media/druva-tutorial/tutorial_general_202.png
[203]: ./media/druva-tutorial/tutorial_general_203.png

