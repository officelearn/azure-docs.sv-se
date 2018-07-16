---
title: 'Självstudier: Azure Active Directory-integration med båge Publishing - SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och båge Publishing - enkel inloggning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: eafd7998e5bc21a539b6709794fe3cd70d9e3179
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054326"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Självstudier: Azure Active Directory-integration med båge Publishing - enkel inloggning

I den här självstudien får du lära dig hur du integrerar båge Publishing - enkel inloggning med Azure Active Directory (AD Azure).

Integrera båge publicering - enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Båge Publishing - enkel inloggning.
- Du kan aktivera användarna att automatiskt få loggat in på båge Publishing - SSO (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med båge Publishing - SSO, behöver du följande objekt:

- En Azure AD-prenumeration
- En båge publicering - SSO enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Båge publicering - enkel inloggning från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Att lägga till Båge publicering - enkel inloggning från galleriet
Om du vill konfigurera integreringen av båge Publishing - enkel inloggning till Azure AD som du behöver lägga till Båge Publishing - enkel inloggning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Båge Publishing - enkel inloggning från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **båge Publishing - SSO**väljer **båge Publishing - SSO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Båge publicering - enkel inloggning i listan med resultat](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med båge Publishing - SSO baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD behöver du veta vilka motsvarande användaren i båge Publishing - SSO är att en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i båge Publishing - enkel inloggning måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med båge Publishing - SSO, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en båge-Publishing - SSO testanvändare](#create-an-arc-publishing---sso-test-user)**  – du har en motsvarighet för Britta Simon i båge Publishing - enkel inloggning som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i programmet för enkel inloggning din båge - publicering.

**Om du vill konfigurera Azure AD utför enkel inloggning med båge Publishing - SSO, du följande steg:**

1. I Azure-portalen på den **båge Publishing - SSO** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/arc-tutorial/tutorial_arc_samlbase.png)

1. På den **båge Publishing - SSO-domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Båge publicering - SSO-domän och URL: er enkel inloggning för information](./media/arc-tutorial/tutorial_arc_url.png)

    1. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    1. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Båge publicering - SSO-domän och URL: er enkel inloggning för information](./media/arc-tutorial/tutorial_arc_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [båge Publishing - klientfilerna supportteamet](mailto:inf@washpost.com) att hämta dessa värden. 

1. Båge publicera – SSO-program förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/arc-tutorial/tutorial_arc_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | Förnamn | User.givenName |
    | Efternamn | User.surname |
    | e-post | User.Mail |
    | grupper | User.assignedroles |

    1. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

     ![Konfigurera enkel inloggning](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Konfigurera enkel inloggning](./media/arc-tutorial/tutorial_attribute_05.png)
    
    1. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    1. Från den **värdet** anger attributvärdet som visas för den raden.

    1. Lämna den **Namespace** tom.
    
    1. Klicka på **Ok**

    > [!NOTE]
    > Här den **grupper** attributet mappas med **user.assignedroles**. Dessa är anpassade roller som skapas i Azure AD för att mappa gruppnamn tillbaka i programmet. Du kan hitta mer vägledning [här](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) om hur du skapar anpassade roller i Azure AD. 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/arc-tutorial/tutorial_arc_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/arc-tutorial/tutorial_general_400.png)
    
1. På den **båge Publishing - konfiguration för enkel inloggning** klickar du på **konfigurera båge Publishing - SSO** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Båge publicering - SSO-konfiguration](./media/arc-tutorial/tutorial_arc_configure.png) 

1. Att konfigurera enkel inloggning på **båge Publishing - SSO** sida, som du behöver skicka de hämtade **certifikat (Base64), URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [båge Publicering - SSO-supportteamet](mailto:inf@washpost.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/arc-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/arc-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/arc-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/arc-tutorial/create_aaduser_04.png)

    1. I den **namn** skriver **BrittaSimon**.

    1. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    1. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    1. Klicka på **Skapa**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Skapa testanvändare för enkel inloggning en båge - publicering

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i båge Publishing - enkel inloggning. Båge publicera – SSO stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att få åtkomst till Båge Publishing - enkel inloggning om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [båge Publishing - SSO-supportteamet](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Båge Publishing - enkel inloggning.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon båge Publishing - SSO, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **båge Publishing - SSO**.

    ![Båge publiceringen - SSO länk i listan med program](./media/arc-tutorial/tutorial_arc_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen för enkel inloggning i åtkomstpanelen i båge - publicering, du bör få automatiskt loggat in på programmet för enkel inloggning din båge - publicering.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png

