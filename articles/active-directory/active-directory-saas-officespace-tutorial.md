---
title: "Självstudier: Azure Active Directory-integrering med OfficeSpace programvara | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan OfficeSpace programvaran och Azure Active Directory."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: c7b456fe08db7c4e4cc71c635e405a20c24516cf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Självstudier: Azure Active Directory-integrering med OfficeSpace programvara

I kursen får lära du att integrera OfficeSpace programvara med Azure Active Directory (AD Azure).

Integrera OfficeSpace programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till OfficeSpace programvara.
- Du kan aktivera användarna att automatiskt hämta loggat in på OfficeSpace programvara (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med OfficeSpace programvara, behöver du följande:

- En Azure AD-prenumeration
- En OfficeSpace programvara enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till OfficeSpace programvara från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-officespace-software-from-the-gallery"></a>Lägga till OfficeSpace programvara från galleriet
Du måste lägga till OfficeSpace programvara från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av OfficeSpace programvara i Azure AD.

**Utför följande steg för att lägga till OfficeSpace programvara från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **OfficeSpace programvara**väljer **OfficeSpace programvara** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![OfficeSpace programvara i resultatlistan](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Du konfigurera och testa Azure AD enkel inloggning med OfficeSpace programvara baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till motsvarande användaren i OfficeSpace programvara till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i OfficeSpace programvara upprättas.

I OfficeSpace programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med OfficeSpace programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare OfficeSpace programvara](#create-a-officespace-software-test-user)**  – du har en motsvarighet för Britta Simon OfficeSpace programvara som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i OfficeSpace programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med OfficeSpace programvara:**

1. I Azure-portalen på den **OfficeSpace programvara** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_samlbase.png)

3. På den **OfficeSpace programvara domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och OfficeSpace programvara domän med enkel inloggning information](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [OfficeSpace Programvaruklienten supportteamet](mailto:support@officespacesoftware.com) att hämta dessa värden. 

4. OfficeSpace program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera attribut](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_attribute.png)

5. I den **användarattribut** avsnitt på den **enkel inloggning** markerar **user.mail** som **användar-ID** och för varje rad som visas i i tabellen nedan, utför följande steg:
    
    | Attributnamn | Attributvärde |
    | --- | --- |    
    | e-post | User.Mail |
    | namn | User.DisplayName |
    | Förnamn | User.givenName |
    | Efternamn | User.surname |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera Lägg till ](./media/active-directory-saas-officespace-tutorial/tutorial_attribute_04.png)

    ![Konfigurera attribut](./media/active-directory-saas-officespace-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**
 
6. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värdet för certifikatet.

    ![Länken hämta certifikatet](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. På den **OfficeSpace programvarukonfiguration** klickar du på **konfigurera OfficeSpace programvara** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfiguration av OfficeSpace programvara](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_configure.png) 

9. Logga in på din OfficeSpace programvara klient som en administratör i en annan webbläsarfönster.

10. Gå till **inställningar** och på **kopplingar**.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. Klicka på **SAML-autentisering**.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. I den **SAML-autentisering** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. I den **logga ut providern url** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.

    b. I den **klienten idp mål-url** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. Klistra in den **tumavtrycket** värde som du har kopierat från Azure-portalen i den **klienten IDP certifikat fingeravtryck** textruta. 

    d. Klicka på **Spara inställningar**.


> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-officespace-software-test-user"></a>Skapa en testanvändare OfficeSpace programvara

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon OfficeSpace programvaran. OfficeSpace programvara stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas vid ett försök att komma åt OfficeSpace programvara om det inte finns.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [OfficeSpace programvara supportteamet](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OfficeSpace programvara.

![Tilldela rollen][200] 

**Om du vill tilldela OfficeSpace programvara Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **OfficeSpace programvara**.

    ![Länken OfficeSpace programvara i listan med program](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen OfficeSpace programvara på åtkomstpanelen du bör få automatiskt loggat in på OfficeSpace programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png

