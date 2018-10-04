---
title: 'Självstudier: Azure Active Directory-integrering med Jamf Pro | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: d28e28a2c4f8144da16c4838f07c9b8bb5ce67f0
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268165"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Självstudier: Azure Active Directory-integrering med Jamf Pro

I den här självstudien får lära du att integrera Jamf Pro med Azure Active Directory (AD Azure).

Integrera Jamf Pro med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jamf Pro.
- Du kan aktivera användarna att automatiskt få loggat in på Jamf Pro (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Jamf Pro, behöver du följande objekt:

- En Azure AD-prenumeration
- En Jamf Pro enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Jamf Pro från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jamf-pro-from-the-gallery"></a>Att lägga till Jamf Pro från galleriet

Om du vill konfigurera integreringen av Jamf Pro till Azure AD, som du behöver lägga till Jamf Pro från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jamf Pro från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/jamfprosamlconnector-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/jamfprosamlconnector-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/jamfprosamlconnector-tutorial/a_new_app.png)

4. I sökrutan skriver **Jamf Pro**väljer **Jamf Pro** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/jamfprosamlconnector-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Jamf Pro utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Jamf Pro är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Jamf Pro upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jamf Pro, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i Jamf Pro](#create-a-jamf-pro-test-user)**  – du har en motsvarighet för Britta Simon i Jamf Pro som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program i Jamf Pro.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jamf Pro:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **Jamf Pro** application integration markerar **enkel inloggning**.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_select_sso.png)

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

      ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_ssso.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_sso.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/jamfprosamlconnector-tutorial/b1-domains_and_urlsedit.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    a. I den **identifierare** text skriver en URL med hjälp av följande mönster: `https://<subdomain>.jamfcloud.com/saml/metadata`.

    b. I den **svars-URL** text skriver en URL med hjälp av följande mönster: `https://<subdomain>.jamfcloud.com/saml/SSO`.

    ![image](./media/jamfprosamlconnector-tutorial//b2-domains_and_urls.png)

    c. Klicka på **ange ytterligare webbadresser**.

    d. I den **inloggnings-URL** text skriver en URL med hjälp av följande mönster: `https://<subdomain>.jamfcloud.com`.

    ![image](./media/jamfprosamlconnector-tutorial//b4-domains_and_urls.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Du får det faktiska ID-värdet från **enkel inloggning** avsnitt i Jamf Pro-portalen, som beskrivs senare i självstudien. Du kan extrahera den faktiska **underdomän** från ID-värde och använda det **underdomän** information i inloggnings-URL och svars-URL.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och spara den på din dator.

    ![image](./media/jamfprosamlconnector-tutorial/C2_certificate.png)

7. Om du vill automatisera konfigurationen i Jamf Pro, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)
 
8. När du lägger till tillägg till webbläsaren, klickar på **konfigurera Jamf Pro** omdirigerar dig till Jamf Pro-programmet. Ange administratörsautentiseringsuppgifter för att logga in på Jamf Pro därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 9 – 12.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

9. Om du vill ställa in Jamf Pro manuellt, öppna ett nytt webbläsarfönster och logga till Jamf Pro företagets webbplatsen som administratör och utför följande steg:

10. Klicka på den **inställningsikonen** från det övre högra hörnet på sidan.

    ![Jamf Pro-konfiguration](./media/jamfprosamlconnector-tutorial/configure1.png)

11. Klicka på **enkel inloggning**.

    ![Jamf Pro-konfiguration](./media/jamfprosamlconnector-tutorial/configure2.png)

12. På den **enkel inloggning** sidan utför följande steg:

    ![Jamf Pro enda](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Välj **Jamf Pro Server** att aktivera enkel inloggning för åtkomst.

    b. Genom att välja **Tillåt förbikoppling för alla användare** användare kommer inte att omdirigeras till inloggningssidan identitetsleverantör för autentisering, men kan logga in till Jamf Pro direkt i stället. När en användare försöker få åtkomst till Jamf Pro via identitetsprovidern, görs IdP-initierad SSO-autentisering och auktorisering.

    c. Välj den **NameID** för **ANVÄNDARMAPPNING: SAML**. Som standard är inställningen **NameID** men du kan definiera ett anpassat attribut.

    d. Välj **e-post** för **ANVÄNDARMAPPNING: JAMF PRO**. Jamf Pro mappar SAML-attribut som skickas av IDP: N på följande sätt: genom användare och grupper. När en användare försöker ansluta till Jamf Pro, som standard Jamf Pro hämtar information om användaren från identitetsprovidern och jämför den med Jamf Pro användarkonton. Om inkommande användarkontot inte finns i Jamf Pro, sedan inträffar grupp namnmatchning.

    e. Klistra in värdet `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` i den **GRUPPNAMN för ATTRIBUTET** textrutan.

13. På samma sida-rulla ned till och **IDENTITETSPROVIDERN** under den **enkel inloggning** avsnittet och utför följande steg:

    ![Jamf Pro-konfiguration](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Välj **andra** som ett alternativ från den **IDENTITETSPROVIDER** listrutan.

    b. I den **andra PROVIDER** textrutan Ange **Azure AD**.

    c. Välj **Metadata-URL** som ett alternativ från den **IDENTITETSKÄLLA PROVIDERN METADATA** listrutan och klistra in i textrutan följande den **Appfederationsmetadata** värde som du har kopierat från Azure-portalen.

    d. Kopiera den **entitets-ID** värde och klistra in den i den **identifierare (entitets-ID)** -textrutan i **Jamf Pro domän och URL: er** avsnittet på Azure-portalen.

    >[!NOTE]
    > Här är suddiga värdet underdomänsdelen. Använd det här värdet för att slutföra inloggnings-URL och svars-URL i den **Jamf Pro domän och URL: er** avsnittet på Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/jamfprosamlconnector-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/jamfprosamlconnector-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/jamfprosamlconnector-tutorial/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="create-a-jamf-pro-test-user"></a>Skapa en testanvändare i Jamf Pro

Om du vill aktivera Azure AD-användare att logga in till Jamf Pro, måste de etableras i Jamf Pro. När det gäller Jamf Pro är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen för företagets Jamf Pro som en administratör.

2. Klicka på den **inställningsikonen** från det övre högra hörnet på sidan.

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Klicka på **Jamf Pro-användarkonton och grupper**.

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/user1.png)

4. Klicka på **Ny**.

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/user2.png)

5. Välj **skapa standardkonto**.

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/user3.png)

6. På den **nytt konto** dailog, utför följande steg:

    ![Lägg till medarbetare](./media/jamfprosamlconnector-tutorial/user4.png)

    a. I den **användarnamn** textrutan skriver du det fullständiga namnet på BrittaSimon.

    b. Välj lämpligt alternativ enligt din organisation för **ÅTKOMSTNIVÅ**, **PRIVILEGIET värdet**, och för **ÅTKOMSTSTATUS**.

    c. I den **fullständigt namn** textrutan skriver du det fullständiga namnet för Britta Simon.

    d. I den **e-postadress** textrutan skriver du e-postadressen för Britta Simon konto.

    e. I den **lösenord** textrutan skriver du lösenordet för användaren.

    f. I den **BEKRÄFTA lösenord** textrutan skriver du lösenordet för användaren.

    g. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jamf Pro.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_applications.png)

2. I listan med program väljer **Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_proapplications.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/jamfprosamlconnector-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/jamfprosamlconnector-tutorial/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Jamf Pro i åtkomstpanelen du bör få automatiskt loggat in på ditt program i Jamf Pro.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
