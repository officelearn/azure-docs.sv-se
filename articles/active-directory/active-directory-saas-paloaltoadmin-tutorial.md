---
title: "Självstudier: Azure Active Directory-integrering med Palo Alto nätverk - Admin UI | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto nätverk - Admin UI."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 5dca976449ee856cc61407d0eae831fc1e1e7a50
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Självstudier: Azure Active Directory-integrering med Palo Alto nätverk - Admin UI

I kursen får du lära dig hur du integrerar Palo Alto - Admin UI med Azure Active Directory (AD Azure).

Integrera Palo Alto nätverk - Admin UI med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Palo Alto nätverk - Admin UI.
- Du kan aktivera användarna att automatiskt hämta loggat in på Palo Alto nätverk - Admin UI (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Palo Alto nätverk - Admin UI, behöver du följande:

- En Azure AD-prenumeration
- En Palo Alto nätverk – Admin UI enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till nätverk Palo Alto - Admin UI från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Lägga till nätverk Palo Alto - Admin UI från galleriet
Du måste lägga till Palo Alto nätverk - Admin UI från galleriet i listan över hanterade SaaS-appar för att konfigurera nätverk Palo Alto - Admin UI till Azure AD-integrering.

**Utför följande steg för att lägga till Palo Alto nätverk - Admin UI från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Palo Alto nätverk - Admin UI**väljer **Palo Alto nätverk - Admin UI** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Palo Alto nätverk - Admin UI i resultatlistan](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Palo Alto nätverk - Admin UI baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Palo Alto nätverk - Admin UI till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Palo Alto nätverk - Admin UI måste upprättas.

I nätverk med Palo Alto - Admin UI, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Palo Alto nätverk - Admin UI, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa ett nätverk för Palo Alto - Admin UI testanvändare](#create-a-palo-alto-networks---admin-ui-test-user)**  – du har en motsvarighet för Britta Simon i Palo Alto nätverk - Admin UI som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i nätverket Palo Alto - Admin UI-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Palo Alto nätverk - Admin UI:**

1. I Azure-portalen på den **Palo Alto nätverk - Admin UI** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. På den **Palo Alto nätverk - URL: er och domänen för Admin-Användargränssnittet** avsnittet, utför följande steg:

    ![Palo Alto nätverk - domänen för Admin-Användargränssnittet och URL: er enkel inloggning information](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<Customer Firewall URL>/php/login.php`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Palo Alto nätverk - Användargränssnittet administratörsklient supportteamet](https://support.paloaltonetworks.com/support) att hämta dessa värden. 
 
4. Palo Alto nätverk - Admin UI program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg: Observera att attributvärden är endast, exempel mappa lämpliga värden för användarnamn och adminrole. Det finns ett annat valfritt attribut ”accessdomain” som används för att begränsa administratörsåtkomst till specifika virtuella datorer i brandväggen.
        
    | Attributnamn | Attributvärde |
    | --- | --- |    
    | användarnamn | User.userPrincipalName |
    | adminrole | customadmin |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**

    > [!NOTE]
    > Du kan se följande artiklar för mer information om attribut.
    > 1. Administrativa profilen för användarrollen för administratör UI (adminrole): https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile
    > 2. Enheten åtkomst domän för Admin UI (accessdomain): https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain

6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

8. Öppna Palo Alto-platsen som en administratör i ett nytt webbläsarfönster.

9. Klicka på **enhet**.

    ![Konfigurera Palo Alto enkel inloggning](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Välj **SAML-identitetsprovider** från det vänstra navigeringsfönstret menyraden och klicka på ”Importera” om du vill importera metadatafilen.

    ![Konfigurera Palo Alto enkel inloggning](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Utför följande åtgärder i fönstret Import

    ![Konfigurera Palo Alto enkel inloggning](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. I den **profilnamn** textruta, ange ett namn t.ex Azure AD Admin-Användargränssnittet.
    
    b. I **identitet providern Metadata**, klickar du på **Bläddra** och välj metadata.xml-fil som du har hämtat från Azure-portalen
    
    c. Klicka på **OK**

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Skapa ett nätverk för Palo Alto - Admin UI testanvändare

Palo Alto nätverk - Admin UI stöder Just-in-time användaretablering så att en användare skapas automatiskt i systemet efter en lyckad autentisering om den inte redan finns. Du behöver inte göra någonting här.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Palo Alto nätverk - Admin UI.

![Tilldela rollen][200] 

**Utför följande steg om du vill tilldela Britta Simon Palo Alto nätverk - Admin UI:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Palo Alto nätverk - Admin UI**.

    ![Palo Alto nätverk - Admin UI-länken i listan med program](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på Palo Alto nätverk - Admin UI-panelen på panelen åtkomst du ska hämta automatiskt loggat in på nätverket Palo Alto - Admin UI-programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

