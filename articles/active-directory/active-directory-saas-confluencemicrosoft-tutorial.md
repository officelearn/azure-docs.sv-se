---
title: "Självstudier: Azure Active Directory-integrering med antal samverkande SAML SSO av Microsoft | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och växer samman SAML SSO av Microsoft."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jeedes
ms.openlocfilehash: 0409d4576b0671f60c5e3bd7e82a2a1102d3d28c
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Självstudier: Azure Active Directory-integrering med antal samverkande SAML SSO av Microsoft

I kursen får lära du att integrera växer samman SAML SSO av Microsoft Azure Active Directory (AD Azure).

Integrera växer samman SAML SSO av Microsoft med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till växer samman SAML SSO av Microsoft
- Du kan aktivera användarna att automatiskt hämta loggat in på växer samman SAML SSO av Microsoft (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med antal samverkande SAML SSO av Microsoft, behöver du följande:

- En Azure AD-prenumeration
- Antal samverkande server installerat program på en Windows 64-bitars server (lokalt eller i molnet IaaS-infrastruktur)
- Antal samverkande servern är HTTPS-aktiverade
- Observera versionerna som stöds för antal samverkande Plugin nämns i under avsnittet.
- Antal samverkande servern kan nås på internet särskilt till Azure AD inloggningssidan för autentisering och ska kunna ta emot token från Azure AD
- Admin-autentiseringsuppgifter anges i antal samverkande
- WebSudo har inaktiverats i antal samverkande
- Testanvändare som skapats i serverprogrammet växer samman

> [!NOTE]
> Om du vill testa stegen i den här självstudiekursen, rekommenderas inte med hjälp av en produktionsmiljö för växer samman. Testa integreringen först i utvecklings- eller mellanlagringsmiljön för programmet och sedan använda produktionsmiljön.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Antal samverkande versioner som stöds 

Från och med nu stöds följande antal samverkande-versioner:

- Antal samverkande: 5.0 till 5.10

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till växer samman SAML SSO av Microsoft från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Att lägga till växer samman SAML SSO av Microsoft från galleriet
Du måste lägga till växer samman SAML SSO av Microsoft från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av antal samverkande SAML SSO av Microsoft i Azure AD.

**Utför följande steg för att lägga till växer samman SAML SSO av Microsoft från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **växer samman SAML SSO av Microsoft**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. Välj i resultatpanelen **växer samman SAML SSO av Microsoft**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med antal samverkande SAML SSO av Microsoft baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i antal samverkande SAML SSO av Microsoft till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren växer samman SAML SSO av Microsoft upprättas.

Antal samverkande SAML SSO av Microsoft, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med antal samverkande SAML SSO av Microsoft, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa ett antal samverkande SAML SSO av Microsoft testanvändare](#creating-a-confluence-saml-sso-by-microsoft-test-user)**  – du har en motsvarighet för Britta Simon växer samman SAML SSO från Microsoft som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din växer samman SAML SSO av Microsoft-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med antal samverkande SAML SSO av Microsoft:**

1. I Azure-portalen på den **växer samman SAML SSO av Microsoft** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. På den **växer samman SAML SSO av URL: er och Microsoft Domain** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<domain:port>/plugins/servlet/saml/auth`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<domain:port>/`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Porten är valfria om det är en namngiven URL. Dessa värden tas emot under konfigurationen av antal samverkande plugin som beskrivs senare i självstudierna.
 

4. Att generera den **Metadata** url, utför följande steg:

    a. Klicka på **App registreringar**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/appregistrations.png)
   
    b. Klicka på **slutpunkter** att öppna **slutpunkter** dialogrutan.  
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/endpointicon.png)

    c. Klicka på kopieringsknappen för att kopiera **FEDERATION METADATADOKUMENTET** url och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/endpoint.png)
     
    d. Gå till egenskapssidan för **växer samman SAML SSO av Microsoft** och kopiera den **program-Id** med **kopiera** knappen och klistra in den i anteckningar.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/appid.png)

    e. Generera den **URL för tjänstmetadata** med hjälp av följande mönster: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` och kopiera det här värdet i anteckningar eftersom den används senare för konfiguration av plugin-programmet.

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_general_400.png)

6. I en annan webbläsarfönster loggar du in till växer samman-instans som administratör.

7. Hovra över kugge och klicka på den **tillägg**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon1.png)

8. Hämta plugin-programmet från [Microsoft Download Center](). Ladda upp plugin-programmet som tillhandahålls av Microsoft genom att använda manuellt **ladda upp tilläggsprogrammet** menyn
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon12.png)

9. När plugin-programmet installeras, visas det i **användaren installerat** tillägg avsnitt i **Hantera tillägg** avsnitt. Klicka på **konfigurera** att konfigurera nya plugin-programmet.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon13.png)

10. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon52.png)
 
    > [!TIP]
    > Kontrollera att det finns bara ett certifikat mappas mot appen så att det finns inga fel vid matchning av metadata. Om det finns flera certifikat, får administratör ett fel vid lösning av metadata.

    a. I **URL för tjänstmetadata** klistra in den **URL för tjänstmetadata** genereras från Azure AD och klicka på den **lösa** knappen. Den läser IdP metadata-URL och fyller i informationen för fält.

    b. Kopiera den **identifierare, svars-URL: en och URL: en inloggning** värden och klistra in dem i **identifierare, svars-URL: en och URL: en inloggning** textrutor respektive i **växer samman SAML SSO av URL: er och Microsoft Domain** avsnitt på Azure-portalen.

    c. I **knappen inloggningsnamnet** skriver du namnet på knappen organisationen vill att användarna ska se på inloggningsskärmen.

    d. I **SAML användar-ID platser**, väljer du antingen **användar-ID är i elementet NameIdentifier i instruktionen ämne** eller **användar-ID är i ett element med attributet**.  Detta ID måste vara växer samman användar-id. Om det användar-id inte matchas sedan kan inte användare att logga in. 

    > [!Note]
    > Standardplatsen för SAML användar-ID är namnidentifierare. Du kan ändra det till ett attributalternativ och ange lämpliga attributets namn.
    
    e. Om du väljer **användar-ID är i ett element med attributet** alternativet i **attributnamn** textrutan anger du namnet på attributet som där användar-Id förväntas. 

    f. Om du använder den federerade domänen (t.ex. AD FS etc.) med Azure AD, klicka på den **aktivera identifiering av startsfär** och konfigurera den **domännamn**.
    
    g. I **domännamn** anger du domännamnet här vid inloggningen ADFS-baserade.

    h. Kontrollera **aktivera enkel inloggning ut** om du vill logga ut från Azure AD när en användare loggar från växer samman. 

    i. Klicka på **spara** för att spara inställningarna.


> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Skapa ett antal samverkande SAML SSO av Microsoft testanvändare

Om du vill aktivera Azure AD-användare kan logga in på växer samman på lokal server, måste de etableras i antal samverkande SAML SSO av Microsoft. Antal samverkande SAML SSO av Microsoft är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt växer samman på lokal server som administratör.

2. Hovra över kugge och klicka på den **Användarhantering**.

    ![Lägga till medarbetare](./media/active-directory-saas-confluencemicrosoft-tutorial/user1.png) 

3. Under avsnittet för användare, klickar du på **lägga till användare** fliken. På den **lägga till en användare** dialogrutan utför följande steg:

    ![Lägga till medarbetare](./media/active-directory-saas-confluencemicrosoft-tutorial/user2.png) 

    a. I den **användarnamn** textruta Skriv e-postadressen för användaren som Britta Simon.

    b. I den **fullständiga namn** textruta skriver du det fullständiga namnet på användaren som Britta Simon.

    c. I den **e-post** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.

    d. I den **lösenord** textruta, Skriv in lösenordet för Britta Simon.

    e. Klicka på **Bekräfta lösenord** ange lösenordet igen.
    
    f. Klicka på **Lägg till** knappen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till växer samman SAML SSO av Microsoft.

![Tilldela användare][200] 

**Om du vill tilldela växer samman SAML SSO av Microsoft Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **växer samman SAML SSO av Microsoft**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på växer samman SAML SSO av Microsoft-panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt växer samman SAML SSO av Microsoft-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_203.png

