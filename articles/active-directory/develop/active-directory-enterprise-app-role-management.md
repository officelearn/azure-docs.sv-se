---
title: Konfigurera rollen anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar rollen anspråket utfärdats i SAML-token för företagsprogram i Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 88a9f5988d1fe3f4de4fe10da23a5f713e3f3370
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Konfigurera rollen anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory

Den här funktionen kan du anpassa Anspråkstyp för 'roller' anspråk i svaret token togs emot på auktorisera en app med Azure AD.

## <a name="prerequisites"></a>Förutsättningar
- En Azure AD-prenumeration med katalogen installationen
- En enkel inloggning aktiverad prenumeration
- Du måste konfigurera enkel inloggning med ditt program

## <a name="when-to-use-this-feature"></a>När du ska använda den här funktionen

Om ditt program förväntar anpassade roller som ska skickas i SAML-svar, måste du använda den här funktionen. Den här funktionen kan du skapa så många roller som du behöver för att skickas tillbaka från Azure AD till ditt program.

## <a name="steps-to-use-this-feature"></a>Steg för att använda den här funktionen

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver du namnet på ditt program, Välj ditt program resultatet panelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Program i resultatlistan](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. När programmet har lagts till, gå till **egenskaper** och kopiera den **objekt-ID**

    ![Egenskapssidan](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    a. Logga in på webbplatsen Explorer diagram med autentiseringsuppgifterna som global administratör/medadministratör för din klient.

    b. Ändra versionen som ska **beta** och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Om du använder flera kataloger, ska sedan du följa det här mönstret `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. Hämta det måste du ändra listan över tjänstens huvudnamn hämtas och. Du kan också använda Ctrl + F för att söka efter programmet från de angivna ServicePrincipals. Sök efter den **objekt-id**, som du har kopierat från egenskapssidan och Använd följande fråga för att få till respektive tjänstens huvudnamn.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Extrahera appRoles egenskapen från tjänsten huvudobjektet.

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. Du måste skapa nya roller för programmet nu. Du kan ladda ned Azure AD-roll Generator från [här](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Öppna Azure AD-generatorn och utföra följande steg-

    ![Azure AD Generator](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Ange **rollnamn**, **Rollbeskrivning**, och **roll värdet**. Klicka på **Lägg till** att lägga till rollen
    
    När du har lagt till alla nödvändiga roller, klickar du på **Generera**
    
    Kopiera innehållet genom att klicka på **kopiera innehåll**

    > [!NOTE] 
    > Kontrollera att du har **msiam_access** användarroll och id matchar i rollen skapas.

    g. Gå tillbaka till ditt diagram Explorer. Ändra metod från **hämta** till **korrigering**. Korrigera service principal objekt om du vill ha önskad appRoles genom att uppdatera appRoles egenskapen med de kopierade värdena. Klicka på **Kör fråga**.

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > Följande är ett exempel på appRoles objekt. 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. När tjänstens huvudnamn har korrigerats med flera roller, tilldela vi användare till respektive roller. Detta kan göras genom att gå till portalen och gå till respektive appen. Sedan, klicka på den **användare och grupper** fliken längst upp. Den här processen visar en lista över alla användare eller grupper.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. Om du vill tilldela en roll till någon användare bara markera viss användargrupp och klicka på **tilldela** knappen längst ned på sidan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. Klicka på som öppnar ett popup-fönster för att välja en roll i olika roller som har definierats för den respektive tjänsten huvudnamn.

    c. Välj de rolltjänster som behövs och klicka på Skicka.

8. Vi behöver uppdatera efter tilldela roller till användare, **attribut** kan definiera anpassade mappningen av **rollen** anspråk.

9. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | -------------- | ----------------|    
    | Rollnamn      | User.assignedrole |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Konfigurera attribut för enkel inloggning](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    e. Klicka på **OK**.

10. Att testa ditt program i IDP initieras för enkel inloggning på loggen till åtkomstpanelen (https://myapps.microsoft.com) och klicka på panelen för ditt program. Du bör se alla tilldelade roller för användaren med anspråk namn du gav i SAML-token.

## <a name="update-existing-role"></a>Uppdatera en befintlig roll

1. Utför följande steg - för att uppdatera en befintlig roll

    a. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    b. Logga in på webbplatsen Explorer diagram med autentiseringsuppgifterna som global administratör/medadministratör för din klient.
    
    c. Ändra versionen som ska **beta** och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Om du använder flera kataloger, ska sedan du följa det här mönstret `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Hämta det måste du ändra listan över tjänstens huvudnamn hämtas och. Du kan också använda Ctrl + F för att söka efter programmet från de angivna ServicePrincipals. Sök efter den **objekt-id**, som du har kopierat från egenskapssidan och Använd följande fråga för att få till respektive tjänstens huvudnamn.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Extrahera appRoles egenskapen från tjänsten huvudobjektet.
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. Följ nedanstående steg för att uppdatera den befintliga rollen:

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Ändra metod från **hämta** till **korrigering**.

    * Kopiera de befintliga rollerna från programmet och klistra in dem i den **brödtext i begäran**.
    
    * Uppdatera värdet för rollen genom att ersätta den **Rollbeskrivning**, **roll värdet**, och **roll displayname** enligt krav för din organisation.
    
    * När du har uppdaterat alla nödvändiga roller, klickar du på **Kör fråga**.
        
## <a name="delete-existing-role"></a>Ta bort befintlig roll

1. Utför följande steg - för att ta bort en befintlig roll

    a. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    b. Logga in på webbplatsen Explorer diagram med autentiseringsuppgifterna som global administratör/medadministratör för din klient.

    c. Ändra versionen som ska **beta** och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Om du använder flera kataloger, ska sedan du följa det här mönstret `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Hämta det måste du ändra listan över tjänstens huvudnamn hämtas och. Du kan också använda Ctrl + F för att söka efter programmet från de angivna ServicePrincipals. Sök efter den **objekt-id**, som du har kopierat från egenskapssidan och Använd följande fråga för att få till respektive tjänstens huvudnamn.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Extrahera appRoles egenskapen från tjänsten huvudobjektet.
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. Följ nedanstående steg för att ta bort den befintliga rollen:

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    Ändra metod från **hämta** till **korrigering**.

    Kopiera de befintliga rollerna från programmet och klistra in dem i den **brödtext i begäran**.
    
    Ange den **IsEnabled** värde till **FALSKT** för den roll som du vill ta bort

    Klicka på **Kör fråga**.
    
    > [!NOTE] 
    > Kontrollera att du har **msiam_access** användarroll och id matchar i rollen skapas.
    
    g. När du har gjort ovanstående procedur hålla metoden som **korrigering** och klistra in rollen remianing innehåll i den **brödtext i begäran** och klicka på **Kör fråga**.
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. När du har kört frågan tas rollen bort.
    
    > [!NOTE]
    > Rollen måste inaktiveras först innan den kan tas bort. 

## <a name="next-steps"></a>Nästa steg

Se [dokumentationen till App ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list) ytterligare anvisningar.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
