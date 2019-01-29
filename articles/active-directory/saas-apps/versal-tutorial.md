---
title: 'Självstudier: Azure Active Directory-integrering med Versal | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Länkövergång.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeedes
ms.openlocfilehash: 91f069da5572394934da41039d7e9a5950089ea1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185115"
---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>Självstudier: Azure Active Directory-integrering med Versal

I den här självstudien får du lära dig hur du integrerar Länkövergång med Azure Active Directory (AD Azure).

Integrera Länkövergång med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Versal.
- Du kan aktivera användarna att automatiskt få loggat in på Länkövergång (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Versal, behöver du följande objekt:

- En Azure AD-prenumeration
- En Versal enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Länkövergång från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-versal-from-the-gallery"></a>Att lägga till Länkövergång från galleriet
Om du vill konfigurera integreringen av Länkövergång i Azure AD, som du behöver lägga till Versal från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Länkövergång från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Länkövergång**väljer **Länkövergång** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Länkövergång i resultatlistan](./media/versal-tutorial/tutorial_versal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Länkövergång baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till användaren i Versal motsvarighet till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Länkövergång upprättas.

I Länkövergång, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Versal, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en Versal testanvändare](#create-a-versal-test-user)**  – du har en motsvarighet för Britta Simon i Länkövergång som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program med Versal.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Versal:**

1. I Azure-portalen på den **Länkövergång** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/versal-tutorial/tutorial_versal_samlbase.png)

1. På den **Länkövergång domän och URL: er** avsnittet, utför följande steg:

    ![Versal domän och URL: er med enkel inloggning för information](./media/versal-tutorial/tutorial_versal_url.png)

    a. I den **identifierare** textrutan angett värde: `VERSAL`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE] 
    > Svars-URL-värdet är inte verkliga. Uppdatera det här värdet med faktiska svars-URL. Kontakta [Länkövergång supportteamet](https://support.versal.com/hc/) att hämta det här värdet.
    
1. Ditt program förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel på detta. Standardvärdet för **användaridentifierare** är **user.userprincipalname** men **Länkövergång** förväntar sig detta mappas med användarens e-postadress. Till det kan du använda **user.mail**-attributet från listan eller rätt attributvärde baserat på organisationens konfiguration.
    
    ![Listrutan för användar-ID](./media/versal-tutorial/tutorial_versal_attribute.png)

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/versal-tutorial/tutorial_versal_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/versal-tutorial/tutorial_general_400.png)
    
1. Att konfigurera enkel inloggning på **Länkövergång** sida, som du behöver skicka de hämtade **XML-Metadata för** och **SAML-signeringscertifikat** till [Länkövergång support-teamet ](https://support.versal.com/hc/). De konfigurerar din Länkövergång organisation med SAML SSO-anslutningen korrekt inställda på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/versal-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/versal-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/versal-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/versal-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-versal-test-user"></a>Skapa en Versal testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Versal. Följ den [skapar en SAML testanvändare](https://support.versal.com/hc/en-us/articles/115011672887-Creating-a-SAML-test-user) supportguide för att skapa användaren Britta Simon inom din organisation. Användare måste skapas och aktiveras i Länkövergång innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Versal.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Länkövergång, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Länkövergång**.

    ![Länkövergång länken i listan med program](./media/versal-tutorial/tutorial_versal_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av en Versal kurs som bäddats in i din webbplats.
Finns det [bädda in organisationens kurser](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses) **SAML enkel inloggning** stöd guide för instruktioner om hur du bäddar in en Versal kurs med stöd för Azure AD enkel inloggning. 

Du måste skapa en kurs, dela den med din organisation och publicera det för att testa inbäddning av kursen. Se [skapar en kurs](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course), [publicerar en kurs](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course), och [kursen och learner](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management) för mer information.  
                     

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/versal-tutorial/tutorial_general_01.png
[2]: ./media/versal-tutorial/tutorial_general_02.png
[3]: ./media/versal-tutorial/tutorial_general_03.png
[4]: ./media/versal-tutorial/tutorial_general_04.png

[100]: ./media/versal-tutorial/tutorial_general_100.png

[200]: ./media/versal-tutorial/tutorial_general_200.png
[201]: ./media/versal-tutorial/tutorial_general_201.png
[202]: ./media/versal-tutorial/tutorial_general_202.png
[203]: ./media/versal-tutorial/tutorial_general_203.png

