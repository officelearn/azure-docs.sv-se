---
title: 'Självstudier: Azure Active Directory-integrering med SAP Business objektet Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Business objektet molnet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: ffd4480a13549caba17becff27a43f51fcaa1988
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041746"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Självstudier: Azure Active Directory-integrering med SAP Business objektet Cloud

I den här självstudien får du lära dig hur du integrerar SAP Business objektet Cloud med Azure Active Directory (AD Azure).

När du integrerar SAP Business objektet Cloud med Azure AD får du följande fördelar:

- Du kan styra vem som har åtkomst till SAP Business objektet moln i Azure AD.
- Du kan logga in automatiskt dina användare till SAP Business objektet moln med hjälp av enkel inloggning och en användares Azure AD-konto.
- Du kan hantera dina konton i en, central plats, Azure-portalen.

Läs mer om programvara som en tjänst (SaaS) app-integrering med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP Business objektet Cloud, behöver du följande objekt:

- En Azure AD-prenumeration
- En SAP Business objektet molnet, med enkel inloggning aktiverat

> [!NOTE]
> Om du testar stegen i den här självstudien, rekommenderar vi att du inte testa dem i en produktionsmiljö.

Rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [skaffa en månads kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till molnet för SAP Business objekt från galleriet.
2. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Lägg till molnet för SAP Business objekt från galleriet
Om du vill konfigurera integreringen av SAP Business objektet Cloud med Azure AD i galleriet, lägger du till SAP Business objektet molnet i din lista över hanterade SaaS-appar.

Lägga till molnet för SAP Business objekt från galleriet:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra menyn väljer du **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Sidan för Enterprise-program][2]
    
3. Om du vill lägga till ett nytt program, Välj **nytt program**.

    ![Knappen Nytt program][3]

4. I sökrutan anger **SAP Business objektet Cloud**.

    ![Sökrutan](./media/sapboc-tutorial/tutorial_sapboc_search.png)

5. I resultatpanelen väljer **SAP Business objektet Cloud**, och välj sedan **Lägg till**.

    ![SAP Business objektet Cloud i resultatlistan](./media/sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP Business objektet molnet baserat på en användare med namnet *Britta Simon*.

För enkel inloggning att fungera, behöver Azure AD veta Azure AD-användare för motsvarighet i SAP Business objektet molnet. En länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP Business objektet Cloud etableras.

Att upprätta länken-relationen i SAP Business objektet molnet för **användarnamn**, tilldela värdet för den **användarnamn** i Azure AD.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP Business objektet Cloud, utför du följande uppgifter:

1. [Konfigurera Azure AD enkel inloggning](#set-up-azure-ad-single-sign-on). Ställer in en användare ska använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user). Testerna Azure AD enkel inloggning med användaren Britta Simon.
3. [Skapa en testanvändare för SAP Business objektet Cloud](#create-an-sap-business-object-cloud-test-user). Skapar en motsvarighet för Britta Simon i SAP Business objekt-moln som är länkad till en Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user). Ställer in Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on). Kontrollerar att konfigurationen fungerar.

### <a name="set-up-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska aktivera du Azure AD enkel inloggning i Azure-portalen. Sedan kan ställa du in enkel inloggning i ditt SAP Business objektet molnprogram.

Så här skapar Azure AD enkel inloggning med SAP Business objektet Cloud:

1. I Azure-portalen på den **SAP Business objektet Cloud** application integration markerar **enkel inloggning**.

    ![Välj enkel inloggning][4]

2. På den **enkel inloggning** sidan för **läge**väljer **SAML-baserad inloggning**.
 
    ![Välj SAML-baserad inloggning](./media/sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. Under **SAP Business objektet molnet domän och URL: er**, gör du följande:

    1. I den **inloggnings-URL** anger en URL som har följande mönster: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. I den **identifierare** anger en URL som har följande mönster:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![SAP Business objektet molnet domän och URL: er sida URL: er](./media/sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Värdena i dessa URL: er är bara exempel. Uppdatera värden med de faktiska inloggnings-URL och URL-identifierare. För att få inloggnings-URL: en kan kontakta den [SAP Business objektet Cloud klienten supportteamet](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/en-US). Du kan hämta ID-URL genom att ladda ned molnmetadata för SAP Business objekt från administratörskonsolen. Detta förklaras senare under kursen. 

4. Under **SAML-signeringscertifikat**väljer **XML-Metadata för**. Spara sedan metadatafilen på datorn.

    ![Välj XML-Metadata](./media/sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Välj **Spara**.

    ![Klicka på Spara](./media/sapboc-tutorial/tutorial_general_400.png)

6. I ett annat webbläsarfönster, loggar du in din SAP Business objektet Cloud företagets webbplats som administratör.

7. Välj **menyn** > **System** > **Administration**.
    
    ![Välj menyn och sedan systemet och Administration](./media/sapboc-tutorial/config1.png)

8. På den **Security** fliken den **redigera** (pennikonen).
    
    ![Välj redigeringsikonen på fliken Säkerhet](./media/sapboc-tutorial/config2.png)  

9. För **autentiseringsmetod**väljer **SAML enkel inloggning (SSO)**.

    ![Välj SAML enkel inloggning för den aktuella autentiseringsmetoden](./media/sapboc-tutorial/config3.png)  

10. För att hämta metadata för service provider (steg 1), Välj **hämta**. I metadatafilen, söka och kopiera den **entityID** värde. I Azure-portalen under **SAP Business objektet molnet domän och URL: er**, klistra in värdet i den **identifierare** box.

    ![Kopiera och klistra in värdet för entityID](./media/sapboc-tutorial/config4.png)  

11. Ladda upp service provider metadata (steg 2) i filen som du hämtade från Azure-portalen under **överför identitetsprovider metadata**väljer **överför**.  

    ![Under överför identitetsprovider metadata, väljer du ladda upp](./media/sapboc-tutorial/config5.png)

12. I den **användarattribut** väljer användarattribut (steg3) som du vill använda för din implementering. Det här användarattributet mappar till identitetsleverantören. Om du vill ange ett anpassat attribut på användarens sida, använda den **mappning av anpassade SAML** alternativet. Du kan också välja antingen **e-post** eller **ANVÄNDAR-ID** som användarattributet. I vårt exempel har vi valt **e-post** har mappats vi identifierare användaranspråk med den **userprincipalname** attribut i den **användarattribut** avsnitt i Azure portalen. Detta ger en unik användare email, som skickas till molnprogrammet för SAP Business objekt i varje lyckade SAML-svar.

    ![Välj användarattribut](./media/sapboc-tutorial/config6.png)

13. Att verifiera kontot med identitetsleverantör (steg 4), i den **inloggning autentiseringsuppgifter (e-post)** anger användarens e-postadress. Välj **verifiera kontot**. Systemet lägger till autentiseringsuppgifter till användarkontot.

    ![Ange e-postadress och välj verifiera konto](./media/sapboc-tutorial/config7.png)

14. Välj den **spara** ikon.

    ![Spara ikon](./media/sapboc-tutorial/save.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in din app! När du har lagt till appen genom att välja **Active Directory** > **företagsprogram**väljer den **enkel inloggning** fliken. Du kan komma åt embedded-dokumentation i den **Configuration** avsnittet längst ned på sidan. Mer information finns i [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

Skapa en testanvändare i Azure AD:

1. Azure-portalen på den vänstra menyn, Välj **Azure Active Directory**.

    ![Skapa en Azure AD-användare för testning](./media/sapboc-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, Välj **användare och grupper**, och välj sedan **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/sapboc-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan **Lägg till**.
 
    ![Skapa en Azure AD-användare för testning](./media/sapboc-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan rutan, gör du följande:
 
    1. I den **namn** anger **BrittaSimon**.

    2. I den **användarnamn** anger du e-postadressen för användaren Britta Simon.

    3. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    4. Välj **Skapa**.

        ![Dialogrutan användare](./media/sapboc-tutorial/create_aaduser_04.png) 

    ![Skapa en Azure AD-användare][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Skapa en SAP Business objektet Cloud testanvändare

Azure AD-användare måste etableras i SAP Business objektet molnet innan de kan logga in till SAP Business objektet moln. I SAP Business objektet Cloud är etablering en manuell aktivitet.

Att etablera ett användarkonto:

1. Logga in till SAP Business objektet Cloud företagets platsen som en administratör.

2. Välj **menyn** > **Security** > **användare**.

    ![Lägg till medarbetare](./media/sapboc-tutorial/user1.png)

3. På den **användare** att lägga till information om nya användare, väljer du **+**. 

    ![Lägg till sidan användare](./media/sapboc-tutorial/user4.png)

    Utför följande steg:

    1. I den **ANVÄNDAR-ID** anger användar-ID för användaren som **Britta**.

    2. I den **Förnamn** anger det första namnet för användaren som **Britta**.

    3. I den **efternamn** anger efternamn för användaren som **Simon**.

    4. I den **VISNINGSNAMN** anger det fullständiga namnet på användaren som **Britta Simon**.

    5. I den **e** anger e-postadressen för användaren som **brittasimon@contoso.com**.

    6. På den **Välj roller** , väljer rätt roll för användaren och välj sedan **OK**.

      ![Välj roll](./media/sapboc-tutorial/user3.png)

    7. Välj den **spara** ikon.    


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska låta du användare Britta Simon att använda Azure AD enkel inloggning genom att bevilja användarkontoåtkomst till SAP Business objektet Cloud.

Så här tilldelar Britta Simon till SAP Business objektet moln:

1. Öppna vyn program i Azure-portalen och sedan gå till vyn directory. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SAP Business objektet Cloud**.

    ![Konfigurera enkel inloggning](./media/sapboc-tutorial/tutorial_sapboc_app.png) 

3. I den vänstra menyn väljer du **användare och grupper**.

    ![Välj användare och grupper][202] 

4. Välj **Lägg till**. Klicka på den **Lägg till tilldelning** väljer **användare och grupper**.

    ![Sidan Lägg till tilldelning][203]

5. På den **användare och grupper** sidan i listan över användare väljer **Britta Simon**.

6. På den **användare och grupper** väljer **Välj**.

7. På den **Lägg till tilldelning** väljer **tilldela**.

![Tilldela rollen][200] 
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen SAP Business objektet Cloud i åtkomstpanelen, bör du vara automatiskt inloggad på ditt SAP Business objektet molnprogram.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/sapboc-tutorial/tutorial_general_01.png
[2]: ./media/sapboc-tutorial/tutorial_general_02.png
[3]: ./media/sapboc-tutorial/tutorial_general_03.png
[4]: ./media/sapboc-tutorial/tutorial_general_04.png

[100]: ./media/sapboc-tutorial/tutorial_general_100.png

[200]: ./media/sapboc-tutorial/tutorial_general_200.png
[201]: ./media/sapboc-tutorial/tutorial_general_201.png
[202]: ./media/sapboc-tutorial/tutorial_general_202.png
[203]: ./media/sapboc-tutorial/tutorial_general_203.png

