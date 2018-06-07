---
title: 'Självstudier: Azure Active Directory-integrering med Sansan | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sansan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: f0739c821f1521eb761912e5092661c7b5c0fd78
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591263"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Självstudier: Azure Active Directory-integrering med Sansan

I kursen får lära du att integrera Sansan med Azure Active Directory (AD Azure).

Integrera Sansan med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Sansan
- Du kan aktivera användarna att automatiskt hämta loggat in på Sansan (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Sansan, behöver du följande:

- En Azure AD-prenumeration
- En Sansan enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Sansan från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sansan-from-the-gallery"></a>Att lägga till Sansan från galleriet
Du måste lägga till Sansan från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Sansan i Azure AD.

**Utför följande steg för att lägga till Sansan från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Sansan**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_search.png)

5. Välj i resultatpanelen **Sansan**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Sansan baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Sansan motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Sansan upprättas.

I Sansan, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Sansan, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Sansan](#creating-a-sansan-test-user)**  – du har en motsvarighet för Britta Simon i Sansan som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Sansan program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Sansan:**

1. I Azure-portalen på den **Sansan** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_samlbase.png)

3. På den **Sansan domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: 
    
    | Miljö | URL |
    |:--- |:--- |
    | Dator |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Inbyggda mobila appen |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Inställningar för mobila webbläsare |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL. Kontakta [Sansan klienten supportteamet](https://www.sansan.com/form/contact) att hämta dessa värden. 
     
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-sansan-tutorial/tutorial_general_400.png)

6. Sansan program förväntar sig flera **identifierare** och **Reply URL: er** att stödja flera miljöer (dator, interna mobilapp, inställningar för mobila webbläsare), som kan konfigureras med hjälp av PowerShell skript. Detaljerade anvisningar beskrivs nedan.

7. Konfigurera flera **identifierare** och **Reply URL: er** för Sansan program med hjälp av PowerShell-skript, gör du följande:

    ![Konfigurera enkel inloggning obj](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_objid.png)    

    a. Gå till den **egenskaper** sidan **Sansan** programmet och kopierar den **objekt-ID** med **kopiera** knappen och klistra in den i anteckningar.

    b. Den **objekt-ID**, som du har kopierat från Azure-portalen kommer att användas som **ServicePrincipalObjectId** i PowerShell-skript används senare under kursen. 

    c. Öppna en kommandotolk i Windows PowerShell.
    
    >[!NOTE] 
    > Du måste installera modulen AzureAD (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory PowerShell V2-modulen, Skriv Y och tryck på RETUR.

    d. Kör `Connect-AzureAD` och logga in med ett användarkonto för Global administratör.

    e. Använd följande skript för att uppdatera flera URL: er till ett program:

    ```poweshell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. Resultatet av skriptet kommer att så här enligt nedan efter slutförande av PowerShell-skript och URL-värden uppdateras men inte hämta visas i Azure-portalen. 

    ![Konfigurera enkel inloggning skript](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_powershell.png)


9. På den **Sansan Configuration** klickar du på **konfigurera Sansan** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_configure.png) 

10. Konfigurera enkel inloggning på **Sansan** sida, måste du skicka den hämtade **certifikat**, **Sign-Out URL**, **SAML enhets-ID**, och **SAML enkel inloggning Tjänstwebbadress** till [Sansan supportteamet](https://www.sansan.com/form/contact). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

>[!NOTE]
>PC webbläsarinställningen fungerar även för mobila appar och mobila webbläsare tillsammans med dator. 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sansan-test-user"></a>Skapa en testanvändare Sansan

I det här avsnittet skapar du en användare som kallas Britta Simon i Sansan. Sansan programmet måste användaren som ska etableras i programmet innan du utför enkel inloggning. 

>[!NOTE]
>Om du behöver skapa en användare manuellt eller batch-användare, måste du kontakta den [Sansan supportteamet](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Sansan.

![Tilldela användare][200] 

**Om du vill tilldela Sansan Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Sansan**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Sansan på åtkomstpanelen du bör få automatiskt loggat in på ditt Sansan program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_203.png

