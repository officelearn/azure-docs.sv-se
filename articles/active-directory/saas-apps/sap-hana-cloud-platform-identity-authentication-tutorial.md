---
title: 'Självstudier: Azure Active Directory-integration med SAP Cloud Platform identitetsautentisering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud Platform identitetsautentisering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 97e77fdbb352e1fcf66044f48e17cb19a8aa2e1d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042154"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Självstudier: Azure Active Directory-integrering med identitetsautentisering för SAP Cloud Platform

Lär dig hur du integrerar SAP Cloud Platform identitetsautentisering med Azure Active Directory (AD Azure) i den här självstudien. SAP Cloud Platform identitetsautentisering används som en proxy IdP åtkomst till SAP-program som använder Azure AD som den huvudsakliga IDP: N.

När du integrerar SAP Cloud Platform identitetsautentisering med Azure AD, får du följande förmåner:

- Du kan styra i Azure AD som har åtkomst till SAP-program.
- Du kan aktivera användarna att logga in automatiskt till SAP-program med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i artikeln [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP Cloud Platform identitetsautentisering, behöver du följande objekt:

- En Azure AD-prenumeration.
- En enkel inloggning på aktiverad-prenumeration för SAP Cloud Platform identitetsautentisering.

> [!NOTE]
> Vi rekommenderar inte att du använder en produktionsmiljö för att testa stegen i den här självstudien.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö [skaffa en månads kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAP Cloud Platform identitetsautentisering från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

Innan du gör en djupdykning i de tekniska detaljerna, är det viktigt att förstå de begrepp som du ska titta på. SAP Cloud Platform identitetsautentisering och Active Directory Federation Services kan du implementera enkel inloggning i program eller tjänster som skyddas av Azure AD (som en IdP) med SAP-program och tjänster som skyddas av SAP-moln Plattformen identitetsautentisering.

För närvarande fungerar SAP Cloud Platform identitetsautentisering som en Proxy identitetsprovider till SAP-program. Azure Active Directory fungerar i sin tur som ledande identitetsprovider i den här installationen. 

Följande diagram illustrerar den här relationen:

![Skapa en Azure AD-användare för testning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Med den här konfigurationen kan är din identitetsautentisering för SAP Cloud Platform-klient konfigurerad som ett betrodda program i Azure Active Directory. 

Alla SAP-program och tjänster som du vill skydda det här sättet konfigureras senare i hanteringskonsolen för SAP Cloud Platform identitetsautentisering.

Auktorisering för att bevilja åtkomst till SAP-program och tjänster måste äga rum i SAP Cloud Platform identitetsautentisering (i stället för Azure Active Directory).

Genom att konfigurera SAP Cloud Platform identitetsautentisering som ett program via Azure Active Directory Marketplace, behöver du inte konfigurera enskilda anspråk eller SAML intyg.

>[!NOTE] 
>För närvarande endast webb-SSO har testats av båda parter. Flöden som är nödvändiga för App-API- eller API för API-kommunikation bör fungera men har inte testats. De testas under efterföljande aktiviteter.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Lägg till SAP Cloud Platform identitetsautentisering från galleriet
Om du vill konfigurera integreringen av SAP Cloud Platform identitetsautentisering i Azure AD, som du behöver lägga till SAP Cloud Platform identitetsautentisering från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till SAP Cloud Platform identitetsautentisering från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), på den vänstra navigeringspanelen väljer den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Om du vill lägga till nya det nya programmet, Välj den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP Cloud Platform identitetsautentisering**. 

5. Välj **SAP Cloud Platform identitetsautentisering** i resultatrutan och välj sedan den **Lägg till** knappen.

    ![SAP Cloud Platform identitetsautentisering i resultatlistan](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP Cloud Platform identitetsautentisering. Du konfigurerar och testar med en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vem användaren motsvarighet i SAP Cloud Platform identitetsautentisering är. Med andra ord måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren i SAP Cloud Platform identitetsautentisering.

I SAP Cloud Platform identitetsautentisering, ger värdet **användarnamn** samma värde som **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP Cloud Platform identitetsautentisering, utför du följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare SAP Cloud Platform identitetsautentisering](#create-an-sap-cloud-platform-identity-authentication-test-user) har en motsvarighet för Britta Simon i SAP Cloud Platform med identitetsautentisering som är länkad till en Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SAP Cloud Platform identitetsautentisering-program.

**Om du vill konfigurera Azure AD enkel inloggning med SAP Cloud Platform identitetsautentisering, gör du följande:**

1. I Azure-portalen på den **SAP Cloud Platform identitetsautentisering** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. I den **enkel inloggning** dialogrutan **SAML-baserad inloggning**väljer **läge** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

3. Om du vill konfigurera programmet i **IDP** har initierat läge, den **SAP Cloud Platform identitet autentisering domän och URL: er** avsnittet, utför följande steg:  

    ![SAP Cloud Platform identitet autentisering domän och URL: er med enkel inloggning för information](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. I den **identifierare** skriver du en URL med följande mönster: `<IAS-tenant-id>.accounts.ondemand.com`

    b. I den **svars-URL** skriver du en URL med följande mönster: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL. Kontakta den [SAP Cloud Platform Identity-klientautentisering supportteamet](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) att hämta dessa värden. Om du inte känner till ID-värde, Läs i dokumentationen för SAP Cloud Platform identitetsautentisering om [klient SAML 2.0-konfiguration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Om du vill konfigurera programmet i **SP** initierades väljer läget **visa avancerade URL-inställningar**.

    ![SAP Cloud Platform identitet autentisering domän och URL: er med enkel inloggning för information](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    I den **inloggning på URL: en** skriver du en URL med följande mönster: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Använd din specifika program inloggnings-URL. Kontakta den [SAP Cloud Platform Identity-klientautentisering supportteamet](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) om du har några osäkra.

5. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**. Spara metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

6. SAP Cloud Platform identitetsautentisering program som förväntar SAML-intyg i ett visst format. Hantera värdena för dessa attribut från den **användarattribut** på sidan för program-integrering. Följande skärmbild visar ett exempel på formatet. 

    ![Konfigurera enkel inloggning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

7. Om ditt SAP-program förväntar sig ett attribut som **firstName**, lägga till den **firstName** attribut i den **användarattribut** avsnittet. Det här alternativet är tillgängligt i den **enkel inloggning** egenskapsdialogrutan för den **SAML-tokenattribut** dialogrutan...

    a. Öppna den **lägga till attributet** dialogrutan **Lägg till attribut**. 
    
    ![Konfigurera enkel inloggning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** skriver attributnamnet **firstName**.
    
    c. Från den **värdet** väljer attributvärdet **user.givenname**.
    
    d. Välj **Ok**.

8. Välj knappen **Spara**.

    ![Konfigurera enkel inloggning spara knappen](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

9. I den **SAP Cloud Platform identitet Autentiseringskonfiguration** väljer **konfigurera SAP Cloud Platform identitetsautentisering** att öppna den **konfigurerarinloggning**fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Autentiseringskonfiguration för SAP Cloud Platform identitet](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

10. SSO konfigurerats för ditt program, gå till SAP Cloud Platform identitetsautentisering-administrationskonsolen. URL: en har följande mönster: `https://<tenant-id>.accounts.ondemand.com/admin`. Läs i dokumentationen om identitetsautentisering på SAP Cloud Platform för [integrering med Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. I Azure-portalen väljer du den **spara** knappen.

12. Fortsätt med följande endast om du vill lägga till och aktivera enkel inloggning för ett annat SAP-program. Upprepa stegen i avsnittet **att lägga till SAP Cloud Platform identitetsautentisering från galleriet**.

13. I Azure-portalen på den **SAP Cloud Platform identitetsautentisering** application integration markerar **inloggning länkade**.

    ![Konfigurera länkad inloggning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

14. Spara konfigurationen.

>[!NOTE] 
>Det nya programmet använder enkel inloggning för konfigurationen av föregående SAP-program. Kontrollera att du använder samma företagets Identitetsproviders i identitetsautentisering för SAP Cloud Platform-administrationskonsolen.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) när du ställer in appen!  När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och komma åt den inbäddade dokumentation genom den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen embedded-dokumentation på [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen, i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör följande:

    ![Dialogrutan användare](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Skapa en testanvändare identitetsautentisering för SAP Cloud Platform

Du behöver inte skapa en användare i SAP Cloud Platform identitetsautentisering. Användare som ingår i arkivet för Azure AD-användare kan använda SSO-funktioner.

SAP Cloud Platform identitetsautentisering har stöd för identitetsfederation-alternativet. Det här alternativet gör att kontrollera om användare som autentiseras av företagets identitetsleverantören finns i den store av SAP Cloud Platform identitet användarautentisering. 

Identitetsfederation-alternativet är inaktiverat som standard. Om identitetsfederation är aktiverad kan endast användare som importeras i SAP Cloud Platform identitetsautentisering åtkomst till programmet. 

Mer information om hur du aktiverar eller inaktiverar identitetsfederation med SAP Cloud Platform identitetsautentisering finns i ”Aktivera Federation med SAP Cloud Platform identitet identitetsautentisering” i [konfigurera identitetsfederation med den Användaren Store SAP Cloud Platform identitetsautentisering](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Cloud Platform identitetsautentisering.

![Tilldela rollen][200] 

**Om du vill tilldela SAP Cloud Platform identitetsautentisering Britta Simon gör du följande:**

1. Öppna vyn program i Azure-portalen och sedan gå till vyn directory. Gå sedan till **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SAP Cloud Platform identitetsautentisering**.

    ![Länken SAP Cloud Platform identitetsautentisering i listan med program](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

3. I menyn till vänster väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan.

6. Klicka på den **Välj** knappen i den **användare och grupper** dialogrutan.

7. Välj den **tilldela** knappen i den **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen SAP Cloud Platform identitetsautentisering på åtkomstpanelen, loggas du automatiskt till SAP Cloud Platform identitetsautentisering programmet.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png
