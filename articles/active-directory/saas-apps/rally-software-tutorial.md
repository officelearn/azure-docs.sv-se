---
title: 'Självstudier: Azure Active Directory-integrering med Rally programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Rally programvaran och Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: 7e6c3c217621a12e862167eac78e161db9fbc147
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35965864"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Självstudier: Azure Active Directory-integrering med Rally programvara

I kursen får lära du att integrera Rally programvara med Azure Active Directory (AD Azure).

Integrera Rally programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Rally programvara.
- Du kan aktivera användarna att automatiskt hämta loggat in på Rally programvara (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Rally programvara, behöver du följande:

- En Azure AD-prenumeration
- En Rally programvara enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till Rally programvara från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-rally-software-from-the-gallery"></a>Lägga till Rally programvara från galleriet
Du måste lägga till Rally programvara från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Rally programvara i Azure AD.

**Utför följande steg för att lägga till Rally programvara från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Rally programvara**väljer **Rally programvara** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Rally programvara i resultatlistan](./media/rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Rally programvara baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Rally programvara till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Rally programvara upprättas.

I Rally programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Rally programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Rally programvara](#create-a-rally-software-test-user)**  – du har en motsvarighet för Britta Simon Rally programvara som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Rally programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Rally programvara:**

1. I Azure-portalen på den **Rally programvara** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

3. På den **Rally programvara domän och URL: er** avsnittet, utför följande steg:

    ![Rally programvara domän och URL: er enkel inloggning information](./media/rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenant-name>.rally.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Rally Programvaruklienten supportteamet](https://help.rallydev.com/) att hämta dessa värden. 
 


4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/rally-software-tutorial/tutorial_general_400.png)

6. På den **Rally programvarukonfiguration** klickar du på **konfigurera Rally programvara** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![Rally konfiguration av programvara](./media/rally-software-tutorial/tutorial_rallysoftware_configure.png) 

7. Logga in på ditt **Rally programvara** klient.

8. Klicka på i verktygsfältet högst upp **installationsprogrammet**, och välj sedan **prenumeration**.
   
    ![Prenumerationen](./media/rally-software-tutorial/ic769531.png "prenumeration")

9. Klicka på den **åtgärd** knappen. Välj **redigera prenumeration** på upp till höger i verktygsfältet.

10. På den **prenumeration** dialogrutan sida, utför följande steg och klicka sedan på **spara och Stäng**:
   
    ![Autentisering](./media/rally-software-tutorial/ic769542.png "autentisering")
   
    a. Välj **autentisering Rally eller SSO** autentisering listrutan.

    b. I den **identitet providern URL** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen. 

    c. I den **SSO logga ut** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/rally-software-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/rally-software-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/rally-software-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/rally-software-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-rally-software-test-user"></a>Skapa en testanvändare Rally programvara

För Azure AD-användare för att kunna logga in, måste de etableras till Rally-programmet med hjälp av deras användarnamn för Azure Active Directory.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din klient Rally programvara.

2. Gå till **installationsprogrammet \> användare**, och klicka sedan på **+ Lägg till ny**.
   
    ![Användare](./media/rally-software-tutorial/ic781039.png "användare")

3. Ange namnet i textrutan ny användare och klicka sedan på **Lägg till med uppgifter**.

4. I den **skapa användare** avsnittet, utför följande steg:
   
    ![Skapa användare](./media/rally-software-tutorial/ic781040.png "skapa användare")

    a. I den **användarnamn** textruta, ange namnet på användaren som **Brittsimon**.
   
    b. I **e-postadress** textruta ange e-postadress för användaren som **brittasimon@contoso.com**.

    c. I **Förnamn** text Ange först namnet på användaren som **Britta**.

    d. I **efternamn** text Ange efternamn för användaren som **Simon**.

    e. Klicka på **spara och Stäng**.

   >[!NOTE]
   >Du kan använda andra Rally användarens konto skapas verktyg eller API: er som tillhandahålls av Rally programvara för att etablera Azure AD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Rally programvara.

![Tilldela rollen][200] 

**Om du vill tilldela Rally programvara Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Rally programvara**.

    ![Länken Rally programvara i listan med program](./media/rally-software-tutorial/tutorial_rallysoftware_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Rally programvara på åtkomstpanelen du bör få automatiskt loggat in på Rally programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rally-software-tutorial/tutorial_general_01.png
[2]: ./media/rally-software-tutorial/tutorial_general_02.png
[3]: ./media/rally-software-tutorial/tutorial_general_03.png
[4]: ./media/rally-software-tutorial/tutorial_general_04.png

[100]: ./media/rally-software-tutorial/tutorial_general_100.png

[200]: ./media/rally-software-tutorial/tutorial_general_200.png
[201]: ./media/rally-software-tutorial/tutorial_general_201.png
[202]: ./media/rally-software-tutorial/tutorial_general_202.png
[203]: ./media/rally-software-tutorial/tutorial_general_203.png

