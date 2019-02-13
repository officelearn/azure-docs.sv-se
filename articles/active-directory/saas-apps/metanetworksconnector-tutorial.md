---
title: 'Självstudier: Azure Active Directory-integrering med Meta nätverk Connector | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Meta nätverk Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4d702a33742da5501be208154cbdd593fa0524
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199722"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Självstudier: Azure Active Directory-integrering med Meta nätverk Connector

I den här självstudien får du lära dig hur du integrerar Meta nätverk anslutningen med Azure Active Directory (AD Azure).

Integrera Meta nätverk anslutningen med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till metadata nätverk Connector.
- Du kan aktivera användarna att automatiskt få loggat in på Meta nätverk Connector (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Meta nätverk Connector, behöver du följande objekt:

- En Azure AD-prenumeration
- En Meta nätverk enkel inloggning aktiverat för produktanslutning

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till metadata nätverk Connector från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Att lägga till metadata nätverk Connector från galleriet
Om du vill konfigurera integreringen av Meta nätverk anslutningen till Azure AD, som du behöver lägga till metadata nätverk Connector från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till metadata nätverk Connector från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 
    
    ![Azure Active Directory-knappen][1]
    
1. Gå till **företagsprogram**. Gå till **alla program**.
    
    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.
    
    ![Knappen Nytt program][3]
    
1. I sökrutan skriver **Meta nätverk Connector**väljer **Meta nätverk Connector** resultatet panelen klickar **Lägg till** för att lägga till programmet.
    
    ![Meta nätverk anslutningen i listan med resultat](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Meta nätverk Anslutningsapp baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Meta nätverk Connector är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Meta nätverk Connector upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Meta nätverk Connector, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Meta nätverk Connector](#create-a-meta-networks-connector-test-user)**  – du har en motsvarighet för Britta Simon i Meta nätverk koppling som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt anslutningsprogram för Meta nätverk.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Meta nätverk Connector:**

1. I Azure-portalen på den **Meta nätverk Connector** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

1. På den **Meta nätverk Connector domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Meta nätverk Connector domän och URL: er med enkel inloggning för information](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    1. I textrutan **Identifierare** anger du en URL med följande mönster: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`
    
    1. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`
    
1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Meta nätverk Connector domän och URL: er med enkel inloggning för information](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    1. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`
    
    1. I den **Vidarebefordransstatus** textrutan anger du ett URL med hjälp av följande mönster: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren svars-URL och inloggnings-URL beskrivs senare i självstudien.
    
1. Meta nätverk anslutningsprogram förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)
    
1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde | NAMNOMRÅDE|
    | ---------------| --------------- | -------- |
    | förnamn | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | namn | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    1. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

        ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)
    
        ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)   
    
    1. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
    
    1. Från den **värdet** anger attributvärdet som visas för den raden.
    
    1. I textrutan **Namnrymd** anger du det namnrymdsvärde som visas för den raden.
    
    1. Klicka på **Ok**
    
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.
    
    ![Länk för nedladdning av certifikatet](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)
    
1. På den **Meta nätverk Kopplingskonfiguration** klickar du på **konfigurera Meta nätverk Connector** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)
    
1. Klicka på knappen **Spara**.
    
    ![Konfigurera enkel inloggning – knappen Spara](./media/metanetworksconnector-tutorial/tutorial_general_400.png)
    
1. Öppna en ny flik i webbläsaren och logga in på ditt administratörskonto för Meta nätverk Connector.
    
    > [!NOTE]
    > Meta nätverk-anslutningen är en säker system. Så innan åtkomst till sina portal måste du hämta din offentliga IP-adress godkänd på sidan. För att få din offentliga IP-adress följer den länken som anges nedan [här](https://whatismyipaddress.com/). Skicka din IP-adress till den [Meta nätverk Connector-klient supportteamet](mailto:support@metanetworks.com) att hämta din IP-adress godkänd.
    
1. Gå till **administratör** och välj **inställningar**.
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure3.png)
    
1. Se till att **Log Internettrafik** och **kraft VPN MFA** har värdet off.
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure1.png)
    
1. Gå till **administratör** och välj **SAML**.
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure4.png)
    
1. Utför följande steg på den **information** sidan:
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure2.png)
    
    1. Kopiera **SSO URL** värde och klistra in den i den **logga In URL: en** -textrutan i den **Meta nätverk Connector domän och URL: er** avsnittet.
    
    1. Kopiera **mottagaren URL** värde och klistra in den i den **svars-URL** -textrutan i den **Meta nätverk Connector domän och URL: er** avsnittet.
    
    1. Kopiera **Målgrupps-URI (SP entitets-ID)** värde och klistra in den i den **identifierare (entitets-ID)** -textrutan i den **Meta nätverk Connector domän och URL: er** avsnittet.
    
    1. Aktivera SAML
    
1. På den **Allmänt** fliken utför följande steg:

    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure5.png)

    1. I den **enkel inloggnings-URL för identitetsprovider**, klistra in den **SAML enkel inloggning för tjänst-URL** värde som du har kopierat från Azure-portalen.

    1. I den **Providerutgivare**, klistra in den **SAML entitets-ID** värde som du har kopierat från Azure-portalen.

    1. Öppna det nedladdade certifikatet från Azure-portalen i anteckningar, klistra in den i den **X.509-certifikat** textrutan.

    1. Aktivera den **Just-in-Time-etablering**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.
    
![Skapa en Azure AD-testanvändare][100]
    
**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.
    
    ![Azure Active Directory-knappen](./media/metanetworksconnector-tutorial/create_aaduser_01.png)
    
1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/metanetworksconnector-tutorial/create_aaduser_02.png)
    
1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.
    
    ![Knappen Lägg till](./media/metanetworksconnector-tutorial/create_aaduser_03.png)
    
1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan Användare](./media/metanetworksconnector-tutorial/create_aaduser_04.png)
    
    1. I den **namn** skriver **BrittaSimon**.

    1. I den **användarnamn** skriver användarens Britta Simon e-postadress.
    
    1. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.
    
    1. Klicka på **Skapa**.
    
### <a name="create-a-meta-networks-connector-test-user"></a>Skapa en testanvändare Meta nätverk Connector

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Meta nätverk Connector. Meta stöder nätverk anslutningen just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt metadata nätverk anslutningen om den inte finns.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Meta nätverk Connector-klient supportteamet](mailto:support@metanetworks.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till metadata nätverk anslutningstjänsten.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Meta nätverk Connector, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.
    
    ![Tilldela användare][201]
    
1. I listan med program väljer **Meta nätverk Connector**.
    
    ![Meta nätverk Connector-länk i listan med program](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  
    
1. I menyn till vänster, klickar du på **användare och grupper**.
    
    ![Länken ”användare och grupper”][202]
    
1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.
    
    ![Fönstret Lägg till tilldelning][203]
    
1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.
    
1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.
    
1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Meta nätverk Connector i åtkomstpanelen du bör få automatiskt loggat in på ditt anslutningsprogram för Meta nätverk.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png

