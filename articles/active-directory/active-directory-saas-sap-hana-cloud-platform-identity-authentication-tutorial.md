---
title: "Självstudier: Azure Active Directory-integrering med SAP Cloud Platform identitetsverifiering | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud Platform identitetsverifiering."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Självstudier: Azure Active Directory-integrering med SAP Cloud Platform identitetsverifiering

Lär dig hur du integrerar SAP Cloud Platform identitetsverifiering med Azure Active Directory (AD Azure) i den här självstudiekursen. SAP Cloud Platform identitetsverifiering används som en proxy IdP åtkomst SAP-program med Azure AD som huvudsakliga IdP.

Integrera SAP Cloud Platform identitetsverifiering med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP-program.
- Du kan aktivera användarna att automatiskt hämta loggat in på SAP-program enkel inloggning (SSO)) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med SAP Cloud Platform identitetsverifiering, behöver du följande:

- En Azure AD-prenumeration
- En SAP Cloud Platform identitetsverifiering enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SAP Cloud Platform identitetsverifiering från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

Innan du dyker till teknisk information, är det viktigt att förstå de begrepp som du ska titta på. SAP Cloud Platform identitetsverifiering och Azure Active Directory federation gör att du kan implementera enkel inloggning över program eller tjänster som skyddas av AAD (som en IdP) med SAP-program och tjänster som skyddas av SAP Molnidentitet plattform Autentisering.

För närvarande fungerar SAP Cloud Platform identitetsverifiering som en Proxy-identitetsprovider till SAP-program. Azure Active Directory fungerar i sin tur som inledande identitetsleverantören i den här installationen. 

Följande diagram illustrerar detta:

![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Med den här installationen konfigureras din SAP Cloud Platform identitetsverifiering klient som ett betrodda program i Azure Active Directory. 

Alla SAP-program och tjänster som du vill skydda via det här sättet konfigureras senare i hanteringskonsolen för SAP Cloud Platform identitetsverifiering!

Det innebär att auktorisering för att bevilja åtkomst till SAP-program och tjänster måste äga rum i SAP Molnautentisering plattform identitet för en sådan konfiguration (i stället konfigurerar auktorisering i Azure Active Directory).

Genom att konfigurera SAP Cloud Platform identitetsverifiering som ett program via Azure Active Directory Marketplace, behöver du inte ta hand om att konfigurera nödvändiga enskilda anspråk / SAML intyg och omvandlingar som behövs för att producera ett giltigt Autentiseringstoken för SAP-program.

>[!NOTE] 
>För närvarande har Web SSO testats av båda parter endast. Flöden som krävs för App-API och API-API-kommunikation ska fungera men har inte testats, ännu. De kommer att testas som en del av efterföljande aktiviteter.
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Att lägga till SAP Cloud Platform identitetsverifiering från galleriet
Du måste lägga till SAP Cloud Platform identitetsverifiering från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SAP Cloud Platform identitetsverifiering i Azure AD.

**Utför följande steg för att lägga till SAP Cloud Platform identitetsverifiering från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP Cloud Platform identitetsverifiering**väljer **SAP Cloud Platform identitetsverifiering** resultatet-panelen klickar **Lägg till** för att Lägg till programmet.

    ![SAP Cloud Platform identitetsverifiering i resultatlistan](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med SAP Cloud Platform identitetsverifiering baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SAP Cloud Platform identitetsverifiering motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP Cloud Platform identitetsverifiering upprättas.

I SAP Cloud Platform identitetsverifiering, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP Cloud Platform identitetsverifiering måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SAP Cloud Platform identitetsverifiering](#create-an-sap-cloud-platform-identity-authentication-test-user)**  – du har en motsvarighet för Britta Simon i SAP-plattformen identitetsautentisering i molnet som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för SAP Cloud Platform identitetsverifiering.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP Cloud Platform identitetsverifiering:**

1. I Azure-portalen på den **SAP Cloud Platform identitetsverifiering** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. På den **SAP Cloud Platform identitet autentisering domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och SAP Cloud Platform identitet autentisering domän med enkel inloggning information](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    I den **identifierare** textruta Skriv en URL med följande mönster:`https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [klientautentisering för SAP Cloud Platform identitet supportteamet](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) att hämta det här värdet. Om du inte vet det här värdet, följ dokumentationen SAP Cloud Platform identitetsverifiering på [klientkonfiguration SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Kontrollera **visa avancerade inställningar för URL: en**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och SAP Cloud Platform identitet autentisering domän med enkel inloggning information](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    I den **logga URL** textruta Skriv en URL med följande mönster:`https://<entity-id>.accounts.ondemand.com/admin`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [klientautentisering för SAP Cloud Platform identitet supportteamet](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) att hämta det här värdet.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. SAP Cloud Platform identitetsverifiering program förväntar SAML-intyg i ett specifikt format. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan om tillämpningsprogrammet SAP förväntar sig ett attribut till exempel ”förnamn”. Lägg till attributet ”förnamn” i dialogrutan för SAML-token attribut.

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributet name ”förnamn”.
    
    c. Från den **värdet** väljer attributvärdet ”user.givenname”.
    
    d. Klicka på **OK**.

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. På den **SAP-plattformen identitet autentisering Molnkonfigurationen** klickar du på **konfigurera SAP Cloud Platform identitetsverifiering** att öppna **konfigurera inloggning** fönstret. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![SAP-plattformen identitet autentisering Molnkonfigurationen](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Gå till SAP Cloud Platform identitet autentisering-administratörskonsolen för att få SSO konfigurerats för ditt program. URL som har följande mönster: `https://<tenant-id>.accounts.ondemand.com/admin`. Följ dokumentationen på SAP Cloud Platform identitetsverifiering till [konfigurera Microsoft Azure AD som företagets identitetsleverantör vid SAP Cloud Platform identitetsverifiering](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. I Azure-portalen klickar du på **spara** knappen.

12. Fortsätt följande steg om du vill lägga till och aktivera enkel inloggning för ett annat SAP-program. Upprepa steg under avsnittet ”lägger till SAP Cloud Platform identitetsautentisering från galleriet” att lägga till en annan instans av SAP Cloud Platform identitetsverifiering.

13. I Azure-portalen på den **SAP Cloud Platform identitetsverifiering** integreringssidan för programmet, klickar du på **inloggning länkade**.

    ![Konfigurera länkad inloggning](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Spara konfigurationen.

>[!NOTE] 
>Det nya programmet använder SSO-konfigurationen för det tidigare SAP-programmet. Kontrollera att du använder samma företagets identitetsleverantörer i administrationskonsolen för SAP Cloud Platform identitet autentisering.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Skapa en testanvändare SAP Cloud Platform identitetsverifiering

Du behöver inte skapa en användare på SAP Cloud Platform identitetsverifiering. Användare i arkivet för Azure AD-användare kan använda funktionen för enkel inloggning.

SAP Cloud Platform identitetsverifiering har stöd för identitetsfederation-alternativet. Det här alternativet innebär att du kan kontrollera om de användare som autentiseras av företagets identitetsleverantören finns i den store av SAP Cloud Platform identitet användarautentisering. 

I standardinställningen, är identitetsfederation-alternativet inaktiverat. Om identitetsfederation är aktiverad kan endast användare som importeras i SAP Cloud Platform identitetsverifiering åtkomst till programmet. 

Mer information om hur du aktiverar eller inaktiverar identitetsfederation med SAP Cloud Platform identitetsverifiering finns i Aktivera identitetsfederation med SAP Cloud Platform identitetsverifiering i [konfigurera identitetsfederation med den Store SAP Cloud Platform identitet användarautentisering](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Cloud Platform identitetsverifiering.

![Tilldela rollen][200] 

**Om du vill tilldela SAP Cloud Platform identitetsverifiering Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAP Cloud Platform identitetsverifiering**.

    ![Länken SAP Cloud Platform identitetsverifiering i listan med program](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SAP Cloud Platform identitetsverifiering på åtkomstpanelen du ska hämta automatiskt loggat in i tillämpningsprogrammet SAP Cloud Platform identitetsverifiering.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png

