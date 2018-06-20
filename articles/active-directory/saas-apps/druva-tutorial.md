---
title: 'Självstudier: Azure Active Directory-integrering med Druva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 8a274ea6a31c907060893fe128c1125b0072781e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229639"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Självstudier: Azure Active Directory-integrering med Druva

I kursen får lära du att integrera Druva med Azure Active Directory (AD Azure).

Integrera Druva med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Druva.
- Du kan aktivera användarna att automatiskt hämta loggat in på Druva (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Druva, behöver du följande:

- En Azure AD-prenumeration
- En Druva enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Druva från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-druva-from-the-gallery"></a>Att lägga till Druva från galleriet
Du måste lägga till Druva från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Druva i Azure AD.

**Utför följande steg för att lägga till Druva från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Druva**väljer **Druva** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Druva i resultatlistan](./media/druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Druva baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Druva motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Druva upprättas.

I Druva, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Druva, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Druva](#create-a-druva-test-user)**  – du har en motsvarighet för Britta Simon i Druva som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Druva program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Druva:**

1. I Azure-portalen på den **Druva** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/druva-tutorial/tutorial_druva_samlbase.png)

3. På den **Druva domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_druva_url.png)

    I den **identifierare** textruta skriver strängvärdet: `druva-cloud`
    
4. Kontrollera **visa avancerade inställningar för URL: en**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_druva_url1.png)
    
    I den **inloggnings-URL** textruta anger du URL: `https://cloud.druva.com/home`

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/druva-tutorial/tutorial_druva_certificate.png) 

6. Tillämpningsprogrammet Druva förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till attributmappningar till din **SAML-Token attribut** konfiguration. 

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_druva_attribute.png)

7. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i den föregående bilden och utför följande steg:

    | Attributnamn      | Attributvärde      |
    | ------------------- | -------------------- |
    | synkroniserad\_auth\_token |Ange token genererade värde |
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden. Token genererat värde beskrivs senare i självstudiekursen.
    
    d. Klicka på **OK**.    

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_general_400.png)

9. På den **Druva Configuration** klickar du på **konfigurera Druva** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/druva-tutorial/tutorial_druva_configure.png) 

10. I en annan webbläsarfönster loggar du in på webbplatsen Druva företag som administratör.

11. Gå till **hantera \> inställningar**.

    ![Inställningar för](./media/druva-tutorial/ic795091.png "inställningar")

12. I dialogrutan Inställningar för enkel inloggning utför du följande steg:

    ![Enkel inloggning inställningar](./media/druva-tutorial/ic795092.png "enkel inloggning inställningar")
    
    a. I **ID providern inloggnings-URL** textruta klistra in värdet för **inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
        
    b. I **ID providern logga ut URL** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen
        
    c. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **ID providern certifikat** textruta
     
    d. Öppna den **inställningar** klickar du på **spara**.

13. På den **inställningar** klickar du på **generera SSO Token**.

    ![Inställningar för](./media/druva-tutorial/ic795093.png "inställningar")

14. På den **enkel inloggning autentiseringstoken** dialogrutan, utför följande steg:

    ![SSO Token](./media/druva-tutorial/ic795094.png "SSO-Token")
    
    a. Klicka på **kopia**, klistra in kopieras värdet i den **värdet** TextBox-kontroll i den **Lägg till attribut** avsnitt i Azure-portalen.
    
    b. Klicka på **Stäng**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/druva-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/druva-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/druva-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/druva-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-druva-test-user"></a>Skapa en testanvändare Druva

För att aktivera Azure AD-användare kan logga in på Druva etableras de i Druva. När det gäller Druva är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Druva** företagets webbplats som administratör.

2. Gå till **hantera \> användare**.
   
   ![Hantera användare](./media/druva-tutorial/ic795097.png "hantera användare")

3. Klicka på **skapa nya**.
   
   ![Hantera användare](./media/druva-tutorial/ic795098.png "hantera användare")

4. I dialogrutan Skapa ny användare utför du följande steg:
   
   ![Skapa ny användare](./media/druva-tutorial/ic795099.png "Skapa ny användare")
   
   a. I den **e-postadress** textruta ange e-postadress för användaren som **brittasimon@contoso.com**.
   
   b. I den **namn** textruta anger du namnet på användaren som **BrittaSimon**.
   
   c. Klicka på **skapa användare**.

>[!NOTE]
>Du kan använda något annat Druva användarens konto skapas verktyg eller API: er som tillhandahålls av Druva att etablera Azure AD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Druva.

![Tilldela rollen][200] 

**Om du vill tilldela Druva Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Druva**.

    ![Länken Druva i listan med program](./media/druva-tutorial/tutorial_druva_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Druva på åtkomstpanelen du bör få automatiskt loggat in på ditt Druva program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/druva-tutorial/tutorial_general_01.png
[2]: ./media/druva-tutorial/tutorial_general_02.png
[3]: ./media/druva-tutorial/tutorial_general_03.png
[4]: ./media/druva-tutorial/tutorial_general_04.png

[100]: ./media/druva-tutorial/tutorial_general_100.png

[200]: ./media/druva-tutorial/tutorial_general_200.png
[201]: ./media/druva-tutorial/tutorial_general_201.png
[202]: ./media/druva-tutorial/tutorial_general_202.png
[203]: ./media/druva-tutorial/tutorial_general_203.png

