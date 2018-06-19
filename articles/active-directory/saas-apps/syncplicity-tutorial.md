---
title: 'Självstudier: Azure Active Directory-integrering med Syncplicity | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 65b58a1a995b09e3905962f41ccdaa6360692006
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35965824"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Självstudier: Azure Active Directory-integrering med Syncplicity

I kursen får lära du att integrera Syncplicity med Azure Active Directory (AD Azure).

Integrera Syncplicity med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Syncplicity
- Du kan aktivera användarna att automatiskt hämta loggat in på Syncplicity (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Syncplicity, behöver du följande:

- En Azure AD-prenumeration
- En Syncplicity enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Syncplicity från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-syncplicity-from-the-gallery"></a>Att lägga till Syncplicity från galleriet
Du måste lägga till Syncplicity från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Syncplicity i Azure AD.

**Utför följande steg för att lägga till Syncplicity från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Syncplicity**.

    ![Skapa en testanvändare i Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

5. Välj i resultatpanelen **Syncplicity**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Syncplicity baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Syncplicity motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Syncplicity upprättas.

I Syncplicity, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Syncplicity, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Syncplicity](#creating-a-syncplicity-test-user)**  – du har en motsvarighet för Britta Simon i Syncplicity som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Syncplicity program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Syncplicity:**

1. I Azure-portalen på den **Syncplicity** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. På den **Syncplicity domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.syncplicity.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Syncplicity klienten supportteamet](https://www.syncplicity.com/contact-us) att hämta dessa värden. 
 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_general_400.png)

6. På den **Syncplicity Configuration** klickar du på **konfigurera Syncplicity** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. Logga in på ditt **Syncplicity** klient.

8. Klicka på menyn högst upp **admin**väljer **inställningar**, och klicka sedan på **anpassad domän och enkel inloggning**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

9. På den **enkel inloggning (SSO)** dialogrutan utför följande steg:
   
    ![Enkel inloggning \(enkel inloggning\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. I den **anpassad domän** textruta skriver du namnet på din domän.
  
    b. Välj **aktiverat** som **enkel inloggning Status**.

    c. I den **enhets-Id** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.

    d. I den **inloggning Sidadress** textruta klistra in den **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    e. I den **logga ut Sidadress** textruta klistra in den **Sign-Out URL** som du har kopierat från Azure-portalen.

    f. I **providern identitetscertifikat**, klickar du på **Välj fil**, och sedan ladda upp certifikatet som du har hämtat från Azure-portalen. 

    g. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/syncplicity-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/syncplicity-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/syncplicity-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-syncplicity-test-user"></a>Skapa en testanvändare Syncplicity
För AAD-användare för att kunna logga in, måste de etableras till Syncplicity program. Det här avsnittet beskrivs hur du skapar användarkonton i AAD i Syncplicity.

**Utför följande steg för att etablera ett användarkonto för Syncplicity:**

1. Logga in på ditt **Syncplicity** klient (till exempel: `https://company.Syncplicity.com`).

2. Klicka på **admin** och välj **användarkonton**.

3. Klicka på **lägga till en användare**.
   
    ![Hantera användare](./media/syncplicity-tutorial/ic769764.png "hantera användare")

4. Typ av **e-adresser** ett AAD-konto som du vill etablera, väljer du **användare** som **rollen**, och klicka sedan på **nästa**.
   
    ![Kontoinformation](./media/syncplicity-tutorial/ic769765.png "kontoinformation")
   
    >[!NOTE]
    >AAD kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta och aktivera kontot. 
    > 

5. Välj en grupp i företaget som de nya användarna ska bli medlem i och klicka sedan på **nästa**.
   
    ![Gruppmedlemskap](./media/syncplicity-tutorial/ic769772.png "gruppmedlemskap")
   
    >[!NOTE]
    >Om det finns inga grupper visas, klickar du på **nästa**. 
    > 

6. Välj mapparna du vill placera under Syncplicitys kontroll på användarens dator och klicka sedan på **nästa**.
   
    ![Syncplicity mappar](./media/syncplicity-tutorial/ic769773.png "Syncplicity mappar")

>[!NOTE]
>Du kan använda något annat Syncplicity användarens konto skapas verktyg eller API: er som tillhandahålls av Syncplicity att etablera AAD-användarkonton. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Syncplicity.

![Tilldela användare][200] 

**Om du vill tilldela Syncplicity Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Syncplicity**.

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Syncplicity på åtkomstpanelen du bör få automatiskt loggat in på ditt Syncplicity program.
## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

