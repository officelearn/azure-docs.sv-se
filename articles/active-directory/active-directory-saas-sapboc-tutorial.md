---
title: 'Självstudier: Azure Active Directory-integrering med SAP Business objektet molntjänster | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Business objektet moln.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 5a56a892ac3b28c4e90ec2ea6360da3d2eff2581
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349496"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Självstudier: Azure Active Directory-integrering med SAP Business objektet moln

Lär dig hur du integrerar SAP Business objektet moln med Azure Active Directory (AD Azure) i den här självstudiekursen.

När du integrerar SAP Business objektet moln med Azure AD får du följande fördelar:

- Du kan styra vem som har åtkomst till SAP Business objektet moln i Azure AD.
- Du kan logga in automatiskt användarna till SAP Business objektet moln med hjälp av enkel inloggning och användarens Azure AD-kontot.
- Du kan hantera dina konton i en enda central plats och Azure-portalen.

Läs mer om programvara som en tjänst (SaaS) appintegrering med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP Business objektet molnet, behöver du följande:

- En Azure AD-prenumeration
- En SAP Business objektet molnet, med enkel inloggning aktiverad

> [!NOTE]
> Om du testar stegen i den här kursen rekommenderar vi att du inte testa dem i en produktionsmiljö.

Rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till SAP Business objektet moln från galleriet.
2. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Lägg till SAP Business objektet moln från galleriet
Lägg till SAP Business objektet moln i listan över hanterade SaaS-appar för att ställa in SAP Business objektet moln med Azure AD-integrering i galleriet.

Lägg till SAP Business objektet moln från galleriet:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra menyn, Välj **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Sidan Enterprise program][2]
    
3. Om du vill lägga till ett nytt program, Välj **nytt program**.

    ![Knappen Nytt program][3]

4. I sökrutan anger **SAP Business objektet molnet**.

    ![Sökrutan](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. Välj i resultatpanelen **SAP Business objektet molnet**, och välj sedan **Lägg till**.

    ![SAP Business objektet moln i resultatlistan](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning till SAP Business objektet moln baserat på en användare med namnet *Britta Simon*.

För enkel inloggning ska fungera, Azure AD som behöver veta Azure AD motsvarighet användaren i molnet för SAP Business-objektet. En länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP Business objektet molnet måste upprättas.

Upprätta länk-relationen i SAP Business objektet molnet för **användarnamn**, tilldela värdet för den **användarnamn** i Azure AD.

Om du vill konfigurera och testa Azure AD enkel inloggning till SAP Business objektet moln, utför följande uppgifter:

1. [Konfigurera Azure AD enkel inloggning](#set-up-azure-ad-single-sign-on). Ställer in en användare att använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user). Testerna Azure AD enkel inloggning med användaren Britta Simon.
3. [Skapa en SAP Business objektet molnet testanvändare](#create-an-sap-business-object-cloud-test-user). Skapar en motsvarighet för Britta Simon i SAP Business objektet moln som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user). Ställer in Britta Simon använda Azure AD för enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on). Kontrollerar att konfigurationen fungerar.

### <a name="set-up-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera du Azure AD enkel inloggning i Azure-portalen. Sedan kan ställa du in enkel inloggning i ditt program för SAP Business objektet molnet.

Konfigurera Azure AD enkel inloggning med SAP Business objektet molnet:

1. I Azure-portalen på den **SAP Business objektet molnet** programmet integration anger **enkel inloggning**.

    ![Välja enkel inloggning][4]

2. På den **enkel inloggning** sidan för **läge**väljer **SAML-baserade inloggning**.
 
    ![Välj SAML-baserade inloggning](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. Under **SAP Business objektet molnet domän och URL: er**, gör du följande:

    1. I den **inloggnings-URL** ange en URL som har följande mönster: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. I den **identifierare** ange en URL som har följande mönster:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![URL: er och SAP Business objektet molnet domän webbadresser](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Värdena i dessa URL: er är bara exempel. Uppdatera värdena med faktiska inloggnings-URL och Identifierare. För att få den inloggnings-URL kan kontakta den [SAP Business objektet Cloud klienten supportteamet](https://www.sap.com/product/analytics/cloud-analytics.support.html). Du kan hämta ID-URL genom att hämta molnmetadata för SAP Business objekt från administrationskonsolen. Detta beskrivs senare under kursen. 

4. Under **SAML-signeringscertifikat**väljer **XML-Metadata för**. Spara metadatafilen på datorn.

    ![Välj XML-Metadata](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Välj **Spara**.

    ![Klicka på Spara](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. I en annan webbläsarfönster loggar du in på din SAP Business objektet molnet företagets webbplats som administratör.

7. Välj **menyn** > **System** > **Administration**.
    
    ![Välj menyn och sedan på System och Administration](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. På den **säkerhet** väljer den **redigera** (penna) ikon.
    
    ![På fliken säkerhet, väljer du ikonen Redigera](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. För **autentiseringsmetod**väljer **SAML enkel inloggning (SSO)**.

    ![Välj SAML enkel inloggning för autentiseringsmetod](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. För att hämta metadata för service provider (steg 1), Välj **hämta**. Hitta i metadatafilen och kopiera den **ID för entiteterna** värde. I Azure-portalen under **SAP Business objektet molnet domän och URL: er**, klistra in värdet i den **identifierare** rutan.

    ![Kopiera och klistra in värdet för ID för entiteterna](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Att överföra metadata för service provider (steg 2) i filen som du hämtade från Azure-portalen under **överför identitetsleverantör metadata**väljer **överför**.  

    ![Välj överför under överför identitetsleverantör metadata](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. I den **användarattribut** väljer användarattribut (steg3) som du vill använda för din implementering. Den här användarattribut mappar till identitetsleverantören. Om du vill ange ett anpassat attribut på användarens sida, använda den **mappning av anpassade SAML** alternativet. Du kan också välja antingen **e-post** eller **ANVÄNDAR-ID** som användarattribut. Vi valde i vårt exempel **e-post** som vi har mappats användaranspråk för identifierare med den **userprincipalname** attribut i den **användarattribut** avsnitt i Azure portalen. Detta ger ett unikt e-post, som skickas till molnapp för SAP Business objekt i varje lyckat SAML-svar.

    ![Välj användarattribut](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Att verifiera kontot med identitetsprovider (steg 4) i den **inloggningen autentiseringsuppgifter (e-post)** ange användarens e-postadress. Markera **verifiera kontot**. Inloggningsuppgifter läggs till användarkontot.

    ![Ange e-postadress och välj verifiera konto](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Välj den **spara** ikon.

    ![Spara ikon](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen! När du har lagt till appen genom att välja **Active Directory** > **företagsprogram**, Välj den **enkel inloggning** fliken. Du kan komma åt inbäddade dokumentation i den **Configuration** avsnittet längst ned på sidan. Mer information finns i [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

Skapa en testanvändare i Azure AD:

1. Välj i Azure-portalen på den vänstra menyn **Azure Active Directory**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, Välj **användare och grupper**, och välj sedan **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan **Lägg till**.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan rutan, gör du följande:
 
    1. I den **namn** ange **BrittaSimon**.

    2. I den **användarnamn** ange e-postadressen för användaren Britta Simon.

    3. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    4. Välj **Skapa**.

        ![Dialogrutan användare](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Skapa Azure AD-användare][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Skapa en SAP Business objektet molnet testanvändare

Azure AD-användare måste etableras i SAP Business objektet moln innan de kan logga in till SAP Business objektet moln. I SAP Business objektet moln är etablering en manuell aktivitet.

Att tillhandahålla ett användarkonto:

1. Logga in på webbplatsen SAP Business objektet molnet företag som administratör.

2. Välj **menyn** > **säkerhet** > **användare**.

    ![Lägga till medarbetare](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. På den **användare** sidan om du vill lägga till nya användarinformation, Välj **+**. 

    ![Lägg till användare på sidan](./media/active-directory-saas-sapboc-tutorial/user4.png)

    Utför följande steg:

    1. I den **ANVÄNDAR-ID** ange användar-ID för användaren som **Britta**.

    2. I den **Förnamn** Ange först namnet på användaren som **Britta**.

    3. I den **efternamn** Ange efternamn för användaren som **Simon**.

    4. I den **VISNINGSNAMN** ange det fullständiga namnet på användaren som **Britta Simon**.

    5. I den **e-post** ange e-postadressen för användaren som **brittasimon@contoso.com**.

    6. På den **Välj roller** väljer sedan rätt roll för användaren, och välj sedan **OK**.

      ![Välja en roll](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. Välj den **spara** ikon.    


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet tillåter användaren Britta Simon att använda Azure AD enkel inloggning genom att bevilja användarkontoåtkomst till SAP Business objektet molnet.

Tilldela Britta Simon SAP Business objektet molnet:

1. Öppna vyn program i Azure-portalen och sedan gå till vyn directory. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAP Business objektet molnet**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. Välj i den vänstra menyn **användare och grupper**.

    ![Välj användare och grupper][202] 

4. Välj **Lägg till**. Klicka sedan på den **Lägg uppdrag** väljer **användare och grupper**.

    ![Sidan Lägg till tilldelning][203]

5. På den **användare och grupper** sida i listan över användare, Välj **Britta Simon**.

6. På den **användare och grupper** väljer **Välj**.

7. På den **Lägg uppdrag** väljer **tilldela**.

![Tilldela rollen][200] 
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen SAP Business objektet molnet på åtkomstpanelen bör du vara loggas in automatiskt i tillämpningsprogrammet SAP Business objektet molnet.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png

