---
title: 'Självstudier: Azure Active Directory-integrering med Thoughtworks Mingle | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: b841210121cb2091b4c4130d3353a3ab85467d4b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211391"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Självstudier: Azure Active Directory-integrering med Thoughtworks Mingle

I kursen får lära du att integrera Thoughtworks Mingle med Azure Active Directory (AD Azure).

Integrera Thoughtworks Mingle med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Thoughtworks Mingle
- Du kan aktivera användarna att automatiskt hämta loggat in på Thoughtworks Mingle (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Thoughtworks Mingle, behöver du följande:

- En Azure AD-prenumeration
- En Thoughtworks Mingle enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Thoughtworks Mingle från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Att lägga till Thoughtworks Mingle från galleriet
Du måste lägga till Thoughtworks Mingle från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Thoughtworks Mingle i Azure AD.

**Om du vill lägga till Thoughtworks Mingle från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Thoughtworks Mingle**väljer **Thoughtworks Mingle** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Thoughtworks Mingle i resultatlistan](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Thoughtworks Mingle baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Thoughtworks Mingle motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Thoughtworks Mingle upprättas.

I Thoughtworks Mingle, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Thoughtworks Mingle, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Thoughtworks Mingle](#create-a-thoughtworks-mingle-test-user)**  – har en motsvarighet för Britta Simon Thoughtworks Mingle som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Thoughtworks Mingle program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Thoughtworks Mingle:**

1. I Azure-portalen på den **Thoughtworks Mingle** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

3. På den **Thoughtworks Mingle domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Thoughtworks Mingle domän med enkel inloggning information](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med det faktiska inloggnings-URL. Kontakta [Thoughtworks Mingle klienten supportteamet](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) värdet hämtas. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

6. Logga in på ditt **Thoughtworks Mingle** företagets webbplats som administratör.

7. Klicka på den **Admin** fliken och klicka sedan på **SSO Config**.
   
    ![Fliken Administration](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO Config")

8. I den **SSO Config** avsnittet, utför följande steg:
   
    ![SSO Config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO Config")
    
    a. Om du vill överföra metadatafilen, klickar du på **Välj fil**. 

    b. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare” länkar](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Skapa en Thoughtworks Mingle testanvändare

För Azure AD-användare för att kunna logga in, måste de etableras till Thoughtworks Mingle programmet med hjälp av deras användarnamn för Azure Active Directory. När det gäller Thoughtworks Mingle är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på webbplatsen Thoughtworks Mingle företag som administratör.

2. Klicka på **profil**.
   
    ![Ditt första projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "ditt första projekt")

3. Klicka på den **Admin** fliken och klicka sedan på **användare**.
   
    ![Användare](./media/thoughtworks-mingle-tutorial/ic785161.png "användare")

4. Klicka på **ny användare**.
   
    ![Ny användare](./media/thoughtworks-mingle-tutorial/ic785162.png "ny användare")

5. På den **ny användare** dialogrutan utför följande steg:
   
    ![Dialogrutan Ny användare](./media/thoughtworks-mingle-tutorial/ic785163.png "ny användare")  
 
    a. Typ av **inloggningsnamn**, **visningsnamn**, **Välj lösenord**, **Bekräfta lösenord** av ett giltigt Azure AD-kontot som du vill etablera i relaterade textrutor. 

    b. Som **användartyp**väljer **fullständig användaren**.

    c. Klicka på **skapa den här profilen**.

>[!NOTE]
>Du kan använda något annat Thoughtworks Mingle användarens konto skapas verktyg eller API: er som tillhandahålls av Thoughtworks Mingle att etablera AAD-användarkonton.
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Thoughtworks Mingle.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Thoughtworks Mingle, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Thoughtworks Mingle**.

    ![Länken Thoughtworks Mingle i listan med program](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Thoughtworks Mingle på åtkomstpanelen du bör få automatiskt loggat in på ditt Thoughtworks Mingle program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

