---
title: 'Självstudier: Azure Active Directory-integration med Sansan | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sansan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 9e14f923b1e25f5fbc522cd39763ac53ddf37617
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872262"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Självstudier: Azure Active Directory-integration med Sansan

I den här självstudien får du lära dig hur du integrerar Sansan med Azure Active Directory (AD Azure).

Integrera Sansan med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Sansan
- Du kan aktivera användarna att automatiskt få loggat in på Sansan (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Sansan, behöver du följande objekt:

- En Azure AD-prenumeration
- En Sansan enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Sansan från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sansan-from-the-gallery"></a>Att lägga till Sansan från galleriet
För att konfigurera integrering av Sansan i Azure AD, som du behöver lägga till Sansan från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Sansan från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Sansan**.

    ![Skapa en Azure AD-användare för testning](./media/sansan-tutorial/tutorial_sansan_search.png)

5. I resultatpanelen väljer **Sansan**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Sansan baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Sansan är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Sansan upprättas.

I Sansan, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Sansan, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Sansan](#creating-a-sansan-test-user)**  – du har en motsvarighet för Britta Simon i Sansan som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Sansan program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Sansan:**

1. I Azure-portalen på den **Sansan** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. På den **Sansan domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sansan-tutorial/tutorial_sansan_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: 
    
    | Miljö | URL |
    |:--- |:--- |
    | Dator |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Inbyggda mobilappar |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Inställningar för mobila webbläsare |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL. Kontakta [Sansan klienten supportteamet](https://www.sansan.com/form/contact) att hämta dessa värden. 
     
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/sansan-tutorial/tutorial_general_400.png)

6. Sansan program som förväntar få flera **identifierare** och **Svarswebbadresser** att stödja flera miljöer (PC webb, inbyggda mobilappar, inställningar för mobila webbläsare), som kan konfigureras med hjälp av PowerShell skript. Detaljerade anvisningar beskrivs nedan.

7. Konfigurera flera **identifierare** och **Svarswebbadresser** för Sansan program med hjälp av PowerShell-skript, gör du följande:

    ![Konfigurera enkel inloggning obj](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Gå till den **egenskaper** sidan **Sansan** programmet och kopierar den **objekt-ID** med **kopia** knappen och klistra in den i anteckningar.

    b. Den **objekt-ID**, som du har kopierat från Azure-portalen kommer att användas som **ServicePrincipalObjectId** i PowerShell-skript som används senare under kursen. 

    c. Nu ska du öppna en upphöjd Windows PowerShell-kommandotolk.
    
    >[!NOTE] 
    > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD`). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory PowerShell V2-modulen, Y och tryck på RETUR.

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

8. När installationen har slutförts på PowerShell-skript, resultatet av skriptet blir så här enligt nedan och URL-värden uppdateras men de kommer inte få visas i Azure portal. 

    ![Konfigurera enkel inloggning för skript](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. På den **Sansan Configuration** klickar du på **konfigurera Sansan** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Att konfigurera enkel inloggning på **Sansan** sida, som du behöver skicka de hämtade **certifikat**, **URL: en för utloggning**, **SAML entitets-ID**, och **SAML enkel inloggning för tjänst-URL** till [Sansan supportteamet](https://www.sansan.com/form/contact). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

>[!NOTE]
>PC webbläsarinställningen fungerar även för mobilappen och mobila webbläsare tillsammans med dator. 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/sansan-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/sansan-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/sansan-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/sansan-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sansan-test-user"></a>Skapa en Sansan testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Sansan. Sansan programmet måste användaren som ska etableras i programmet innan du gör SSO. 

>[!NOTE]
>Om du vill skapa en användare manuellt eller batch-användare, måste du kontakta den [Sansan supportteamet](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Sansan.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Sansan, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Sansan**.

    ![Konfigurera enkel inloggning](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Sansan i åtkomstpanelen du bör få automatiskt loggat in på ditt Sansan program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

