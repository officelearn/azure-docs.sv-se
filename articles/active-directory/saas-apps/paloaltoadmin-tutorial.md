---
title: 'Självstudier: Azure Active Directory-integration med Palo Alto Networks - administratör UI | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks - administratör UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: b39879cc8548139879a3039c5e0c6b924e83c107
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046679"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integrera Azure Active Directory med Palo Alto Networks - Admin-Gränssnittet

Lär dig hur du integrerar Azure Active Directory (Azure AD) med Palo Alto Networks - administratör UI i de här självstudierna.

Av integrerande Azure AD med Palo Alto Networks - administratör UI, kan du få följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Palo Alto Networks - administratör UI.
- Du kan aktivera användarna att få automatiskt inloggad på Palo Alto Networks - administratör UI (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Läs mer om integrering av SaaS-app med Azure AD, i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Palo Alto Networks - administratör UI, behöver du följande objekt:

- En Azure AD-prenumeration
- En Palo Alto nätverk nästa generations brandvägg eller Panorama (centraliserad hanteringssystem för andra)

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du gör *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

* Att lägga till Palo Alto Networks - administratör UI från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Lägg till Palo Alto Networks - administratör UI från galleriet
Om du vill konfigurera integrering av Azure AD med Palo Alto Networks - administratör UI, lägger du till Palo Alto Networks - administratör UI från galleriet i din lista över hanterade SaaS-appar genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram** > **alla program**.

    ![Fönstret ”program”][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![”Det nya programmet” knappen][3]

4. I sökrutan skriver **Palo Alto Networks - administratör UI**väljer **Palo Alto Networks - administratör UI** i resultatlistan och välj sedan **Lägg till**.

    ![Palo Alto Networks - administratör UI i resultatlistan](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Palo Alto Networks - administratör UI, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD identifiera Palo Alto Networks - administratör UI användar- och domänkontrollanterna i Azure AD. Med andra ord etableras en länk förhållandet mellan en Azure AD-användare och samma användare på Palo Alto Networks - administratör UI.

För att upprätta länken relationen, tilldela som Palo Alto Networks - administratör UI *användarnamn* värdet för den *användarnamn* i Azure AD.

Slutför byggblocken i nästa fem avsnitt för att konfigurera och testa Azure AD enkel inloggning med Palo Alto Networks - administratör UI.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Palo Alto Networks - administratör UI-programmet genom att göra följande:

1. I Azure-portalen på den **Palo Alto Networks - administratör UI** application integration markerar **enkel inloggning**.

    ![Länken ”enkel inloggning”][4]

2. I den **enkel inloggning** fönstret i den **läge för enkel inloggning** väljer **SAML-baserad inloggning**.
 
    ![Fönstret ”enkel inloggning”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. Under **Palo Alto Networks - domänen för Admin-Gränssnittet och URL: er**, gör du följande:

    ![”Palo Alto Networks - domänen för Admin-Gränssnittet och URL: er” enkel inloggning för information](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. I den **inloggnings-URL** skriver du en URL i formatet: *https://\<kunden brandväggen FQDN > /php/login.php*.

    b. I den **identifierare** skriver du en URL i formatet: *https://\<kunden brandväggen FQDN >: 443/SAML20/SP*.
    
    c. I den **svars-URL** skriver du URL: en för Assertion konsument Service (ACS) i följande format: *https://\<kunden brandväggen FQDN >: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med faktiska inloggnings-URL och identifierare. Kontakta för att få värdena [Palo Alto Networks - Användargränssnittet administratörsklient supportteamet](https://support.paloaltonetworks.com/support). 
 
4. Eftersom Palo Alto Networks - administratör UI program förväntar sig SAML-intyg i ett visst format, konfigurera anspråk som du ser i följande bild. Hantera attributvärdena i den **användarattribut** delen av den **programintegrering** sidan genom att göra följande:
    
    ![Listan över SAML-Tokenattribut](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Eftersom attributvärdena är bara exempel, mappa lämpliga värden för *användarnamn* och *adminrole*. Det finns ett annat valfritt attribut, *accessdomain*, som används för att begränsa administratörsåtkomst till specifika virtuella datorer i brandväggen.
   >
        
    | Attributnamn | Attributvärde |
    | --- | --- |    
    | användarnamn | User.userPrincipalName |
    | adminrole | customadmin |

    a. Välj **Lägg till attribut**.  
    
    ![Knappen ”Lägg till attribut”](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    Den **lägga till attributet** öppnas.

    ![Fönstret ”Lägg till attribut”](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** skriver attributets namn som visas för den raden.
    
    c. I den **värdet** skriver attributvärdet som visas för den raden.
    
    d. Välj **OK**.

    > [!NOTE]
    > Mer information om attribut finns i följande artiklar:
    > * [Profil för administrativ roll för administratör UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Enhetsdomän åtkomst för Admin-UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. Under **SAML-signeringscertifikat**väljer **XML-Metadata för**, och välj sedan **spara**.

    ![Den XML-Metadata nedladdningslänk](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Knappen Spara](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

6. Öppna Admin-Gränssnittet Palo Alto Networks-brandväggen som en administratör i ett nytt fönster.

7. Välj den **enhet** fliken.

    ![Fliken enhet](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. I den vänstra rutan väljer **SAML-identitetsprovider**, och välj sedan **importera** att importera metadatafilen.

    ![Knappen Importera metadata-fil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. I den **SAML identifiera providern Server-profilimport** fönstret gör du följande:

    ![Fönstret ”SAML identifiera providern Server-profilimport”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. I den **profilnamn** ange ett namn (till exempel **AzureAD Admin-Gränssnittet**).
    
    b. Under **identitet providern Metadata**väljer **Bläddra**, och välj metadata.xml-fil som du tidigare hämtade från Azure-portalen.
    
    c. Rensa den **Validera providern identitetscertifikat** markerar du kryssrutan.
    
    d. Välj **OK**.
    
    e. För att genomföra konfigurationerna i brandväggen, Välj **Commit**.

10. I den vänstra rutan väljer **SAML-identitetsprovider**, och välj sedan SAML identitet Provider-profilen (till exempel **AzureAD Admin-Gränssnittet**) som du skapade i föregående steg. 

    ![SAML-profilen identitets-Provider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. I den **SAML identitet providern Server-profil** fönstret gör du följande:

    ![Fönstret ”SAML identitet providern Server-profil”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. I den **SLO-URL för identitetsprovider** rutan, ersätter den tidigare importerat SLO-URL med följande URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Välj **OK**.

12. På Palo Alto nätverk brandväggen Admin UI väljer **enhet**, och välj sedan **Administratörsrollerna**.

13. Välj den **Lägg till** knappen. 

14. I den **Admin-profil för användarroller** fönstret i den **namn** ange ett namn för administratörsrollen (till exempel **fwadmin**).  
    Rollnamnet administratör måste matcha attributnamnet SAML-administratörsroll som skickades av identitetsleverantören. Administratören rollnamn och värde skapades i steg 4.

    ![Konfigurera Palo Alto Networks-administratörsrollen](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. I brandväggen Admin UI, Välj **enhet**, och välj sedan **autentisering profil**.

16. Välj den **Lägg till** knappen. 

17. I den **autentisering profil** fönstret gör du följande: 

    ![Fönstret ”autentisering profil”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. I den **namn** ange ett namn (till exempel **AzureSAML_Admin_AuthProfile**).
    
    b. I den **typ** listrutan, väljer **SAML**. 
   
    c. I den **IdP Server-profil** listrutan väljer du en lämplig Identitetsproviderservern för SAML-profil (till exempel **AzureAD Admin-Gränssnittet**).
   
    c. Välj den **aktivera enkel utloggning** markerar du kryssrutan.
    
    d. I den **Admin Role-attributet** Anger attributets namn (till exempel **adminrole**). 
    
    e. Välj den **Avancerat** fliken och välj **listan över tillåtna**väljer **Lägg till**. 
    
    ![Knappen Lägg till på fliken Avancerat](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Välj den **alla** kryssrutan och välj de användare och grupper som kan autentisera med den här profilen.  
    När en användare autentiseras matchar brandväggen associerade användarnamn eller grupp mot posterna i den här listan. Om du inte lägger till poster kan kan inga användare autentiseras.

    g. Välj **OK**.

18. Välj för att aktivera administratörer kan använda SAML SSO med hjälp av Azure **enhet** > **installationsprogrammet**. I den **installationsprogrammet** väljer den **Management** fliken och välj **autentiseringsinställningar**väljer den **inställningar** (”kugghjulsikonen”)-knappen . 

 ![Knappen Inställningar](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Välj den SAML-autentisering-profil som du skapade i steg 17 (till exempel **AzureSAML_Admin_AuthProfile**).

 ![Fältet autentisering profil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Välj **OK**.

21. Om du vill tillämpa konfigurationen, Välj **Commit**.


> [!TIP]
> När du konfigurerar appen, du kan läsa en kortare version av föregående anvisningar i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen i den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och öppna den Embedded-dokumentation i den **Configuration** avsnittet längst ned. Mer information om funktionen embedded-dokumentation finns i [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du testanvändare Britta Simon i Azure portal genom att göra följande:

![Skapa en Azure AD-testanvändare][100]

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**.

    ![Azure Active Directory-länk](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över aktuella användare, Välj **användare och grupper** > **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

3. Överst på den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Den **användaren** öppnas.

4. I den **användaren** fönstret gör du följande:

    ![Fönstret användare](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Skapa en Palo Alto Networks - administratör UI testanvändare

Palo Alto Networks - administratör UI stöder just-in-time-användaretablering. Om en användare inte redan finns, skapas den automatiskt i systemet efter en lyckad autentisering. Ingen åtgärd krävs från dig att skapa användaren.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du användaren Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Palo Alto Networks - administratör UI. Du gör detta genom att göra följande:

![Tilldela rollen][200] 

1. I Azure-portalen öppnar du den **program** visa, gå till den **Directory** visa och välj sedan **företagsprogram** > **alla program**.

    ![”Program” och ”alla program”-länkar][201] 

2. I den **program** väljer **Palo Alto Networks - administratör UI**.

    ![Palo Alto nätverk - administratör UI-länk](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. I den vänstra rutan väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj **Lägg till** och klicka sedan på **Lägg till tilldelning** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** fönstret i den **användare** väljer **Britta Simon**.

6. Välj den **Välj** knappen.

7. I den **Lägg till tilldelning** väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer Palo Alto Networks - administratör UI-panel i åtkomstpanelen, bör du vara inloggad automatiskt till din Palo Alto Networks - administratör UI-programmet.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om integrering av SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoadmin-tutorial/tutorial_general_203.png

