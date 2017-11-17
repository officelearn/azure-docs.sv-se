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
ms.openlocfilehash: 591fadb7a218802ec5685d3bd5886ecded1f9920
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Självstudier: Azure Active Directory-integrering med SAP Cloud Platform identitetsverifiering

Lär dig hur du integrerar SAP Cloud Platform identitetsverifiering med Azure Active Directory (AD Azure) i den här självstudiekursen. SAP Cloud Platform identitetsverifiering används som en proxy IdP åtkomst till SAP-program som använder Azure AD som huvudsakliga IdP.

När du integrerar SAP Cloud Platform identitetsverifiering med Azure AD kan du få följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP-program.
- Du kan aktivera användarna att logga in automatiskt SAP-program med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns i artikeln [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med SAP Cloud Platform identitetsverifiering, behöver du följande:

- En Azure AD-prenumeration.
- En enkel inloggning på aktiverad-prenumeration för SAP Cloud Platform identitetsverifiering.

> [!NOTE]
> Vi rekommenderar inte använda en produktionsmiljö för att testa stegen i den här självstudiekursen.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö [skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SAP Cloud Platform identitetsverifiering från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

Innan du fördjupa dig i de tekniska detaljerna, är det viktigt att förstå de begrepp som du ska titta på. SAP Cloud Platform identitetsverifiering och Active Directory Federation Services kan du implementera enkel inloggning över program eller tjänster som skyddas av Azure AD (som en IdP) med SAP-program och tjänster som skyddas av SAP-moln Plattform identitetsverifiering.

För närvarande fungerar SAP Cloud Platform identitetsverifiering som en Proxy-identitetsprovider till SAP-program. Azure Active Directory fungerar i sin tur som inledande identitetsleverantören i den här installationen. 

Följande diagram illustrerar den här relationen:

![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Din SAP Cloud Platform identitetsverifiering-klient är konfigurerad som ett betrodda program i Azure Active Directory med den här installationen. 

Alla SAP-program och tjänster som du vill skydda det här sättet konfigureras senare i hanteringskonsolen för SAP Cloud Platform identitetsverifiering.

Tillståndet för att bevilja åtkomst till SAP-program och tjänster måste äga rum i SAP Cloud Platform identitetsverifiering (i stället för Azure Active Directory).

Genom att konfigurera SAP Cloud Platform identitetsverifiering som ett program via Azure Active Directory Marketplace, behöver du inte konfigurera enskilda anspråk eller SAML intyg.

>[!NOTE] 
>För närvarande endast webb-SSO har testats av båda parter. Flöden som är nödvändiga för App-API eller API-API-kommunikation ska fungera men har inte testats. De kommer att testas under efterföljande aktiviteter.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Lägg till SAP Cloud Platform identitetsverifiering från galleriet
Du måste lägga till SAP Cloud Platform identitetsverifiering från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SAP Cloud Platform identitetsverifiering i Azure AD.

**Om du vill lägga till SAP Cloud Platform identitetsverifiering från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), på det vänstra navigeringsfönstret väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya det nya programmet, Välj den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP Cloud Platform identitetsverifiering**. 

5. Välj **SAP Cloud Platform identitetsverifiering** resultatrutan och välj sedan den **Lägg till** knappen.

    ![SAP Cloud Platform identitetsverifiering i resultatlistan](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med SAP Cloud Platform identitetsverifiering. Du kan konfigurera och testa med en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i SAP Cloud Platform identitetsverifiering. Med andra ord måste du skapa en länk mellan en Azure AD-användare och relaterade användaren i SAP Cloud Platform identitetsverifiering.

I identitetsverifiering SAP Cloud-plattformen, ger värdet **användarnamn** samma värde som **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med SAP Cloud Platform identitetsverifiering:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare SAP Cloud Platform identitetsverifiering](#create-an-sap-cloud-platform-identity-authentication-test-user) har en motsvarighet för Britta Simon SAP-plattformen identitetsautentisering i molnet som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för SAP Cloud Platform identitetsverifiering.

**För att konfigurera Azure AD enkel inloggning med identitetsverifiering för SAP Cloud-plattformen, gör du följande:**

1. I Azure-portalen på den **SAP Cloud Platform identitetsverifiering** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. I den **enkel inloggning** dialogrutan under **SAML-baserade inloggning**väljer **läge** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Om du vill konfigurera programmet i **IDP** initierade läge i den **SAP Cloud Platform identitet autentisering domän och URL: er** avsnittet i den **identifierare** rutan Ange en URL med följande mönster: `https://<entity-id>.accounts.ondemand.com`.  

    ![URL: er och SAP Cloud Platform identitet autentisering domän med enkel inloggning information](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    > [!NOTE] 
    > Det här värdet inte existerar. Uppdatera det här värdet med den faktiska identifieraren. Kontakta den [klientautentisering för SAP Cloud Platform identitet supportteamet](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) att hämta det här värdet. Om du inte förstår det här värdet Läs i dokumentationen för SAP Cloud Platform identitetsverifiering om [klient SAML 2.0 configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Om du vill konfigurera programmet i **SP** initierade läge, Välj **visa avancerade inställningar för URL: en**. 

    ![URL: er och SAP Cloud Platform identitet autentisering domän med enkel inloggning information](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    I den **logga URL** Skriv en URL med följande mönster: `https://<entity-id>.accounts.ondemand.com/admin`.

    > [!NOTE] 
    > Det här värdet inte existerar. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta den [klientautentisering för SAP Cloud Platform identitet supportteamet](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) att hämta det här värdet.

5. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**. Spara metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. SAP Cloud Platform identitetsverifiering program förväntar SAML-intyg i ett specifikt format. Hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan program integrering. Följande skärmbild visar ett exempel på format. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. Om din SAP-program förväntar sig ett attribut som **Förnamn**, lägga till den **Förnamn** attribut i den **användarattribut** avsnitt. Det här alternativet är tillgängligt i den **enkel inloggning** dialogruta i **SAML-token attribut** dialogrutan...

    a. Öppna den **lägga till attributet** dialogrutan **Lägg till attributet**. 
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** Skriv attributnamnet **firstName**.
    
    c. Från den **värdet** väljer attributvärdet **user.givenname**.
    
    d. Välj **Ok**.

8. Välj knappen **Spara**.

    ![Konfigurera enkel inloggning spara knappen](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. I den **SAP-plattformen identitet autentisering Molnkonfigurationen** väljer **konfigurera SAP Cloud Platform identitetsverifiering** att öppna den **konfigurera inloggning**fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![SAP-plattformen identitet autentisering Molnkonfigurationen](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Gå till SAP Cloud Platform identitetsverifiering administrationskonsolen för att få SSO konfigurerats för ditt program. URL som har följande mönster: `https://<tenant-id>.accounts.ondemand.com/admin`. Läs dokumentationen om SAP Cloud Platform identitetsverifiering på [integrering med Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. I Azure portal, väljer du den **spara** knappen.

12. Fortsätt med följande endast om du vill lägga till och aktivera enkel inloggning för ett annat SAP-program. Upprepa stegen i avsnittet **lägger till SAP Cloud Platform identitetsverifiering från galleriet**.

13. I Azure-portalen på den **SAP Cloud Platform identitetsverifiering** programmet integration anger **inloggning länkade**.

    ![Konfigurera länkad inloggning](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Spara konfigurationen.

>[!NOTE] 
>Det nya programmet använder enkel inloggning konfigurationen av det tidigare SAP-programmet. Kontrollera att du använder samma företagets identitetsleverantörer i administrationskonsolen för SAP Cloud Platform identitetsverifiering.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) när du ställer in appen!  När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentation på [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör du följande:

    ![Dialogrutan användare](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Skapa en testanvändare SAP Cloud Platform identitetsverifiering

Du behöver inte skapa en användare i SAP Cloud Platform identitetsverifiering. Användare i arkivet för Azure AD-användare kan använda funktionen för enkel inloggning.

SAP Cloud Platform identitetsverifiering har stöd för identitetsfederation-alternativet. Det här alternativet innebär att du kan kontrollera om det finns användare som autentiseras av företagets identitetsleverantören i den store av SAP Cloud Platform identitet användarautentisering. 

Identitetsfederation alternativet är inaktiverat som standard. Om identitetsfederation är aktiverad kan endast användare som importeras i SAP Cloud Platform identitetsverifiering kan komma åt programmet. 

Mer information om hur du aktiverar eller inaktiverar identitetsfederation med SAP Cloud Platform identitetsverifiering finns i ”Aktivera identitetsfederation med SAP Cloud Platform identitetsautentisering” i [konfigurera identitetsfederation med den Store SAP Cloud Platform identitet användarautentisering](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Cloud Platform identitetsverifiering.

![Tilldela rollen][200] 

**Om du vill tilldela SAP Cloud Platform identitetsverifiering Britta Simon gör du följande:**

1. Öppna vyn program i Azure-portalen och sedan gå till vyn directory. Gå sedan till **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAP Cloud Platform identitetsverifiering**.

    ![Länken SAP Cloud Platform identitetsverifiering i listan med program](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan.

6. Klicka på den **Välj** knappen i den **användare och grupper** dialogrutan.

7. Välj den **tilldela** knappen i den **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen SAP Cloud Platform identitetsverifiering på åtkomstpanelen hämta loggas du automatiskt till SAP Cloud Platform identitetsverifiering programmet.

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

