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
ms.openlocfilehash: 43a4db9114cd47da5bef98ed634847b547589b47
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
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

5. När programmet har lagts till, gå till **egenskaper** och kopiera den **objekt-ID**.

<!-- ![Properties Page](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png) Note: Image is missing. -->

6. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    a. Logga in på webbplatsen Explorer diagram med autentiseringsuppgifterna som global administratör/medadministratör för din klient.

    b. Du måste ha behörighet att skapa roller. Klicka på **ändringsbehörighet** få behörigheterna som krävs. 

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte har dessa redan) och klicka på ”behörighet att ändra” 

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Detta blir du ombedd att logga in igen och acceptera samtycke. När du godkänt samtycke är du inloggad i systemet igen.

    e. Ändra versionen som ska **beta** och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Om du använder flera kataloger, ska sedan du följa det här mönstret `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Hämta det måste du ändra listan över tjänstens huvudnamn hämtas och. Du kan också använda Ctrl + F för att söka efter programmet från de angivna ServicePrincipals. Sök efter den **objekt-id**, som du har kopierat från egenskapssidan och Använd följande fråga för att få till respektive tjänstens huvudnamn.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrahera appRoles egenskapen från tjänsten huvudobjektet. 

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > Om du använder anpassade (icke-galleriet) appen finns två standardrollerna - användar- och msiam_access. Vid galleriet app är msiam_access endast standardroll. Du behöver inte göra några ändringar i standardroller.

    h. Nu måste du generera nya roller för programmet. 

    i. Är ett exempel på appRoles objekt nedan JSON. Skapa en liknande objekt om du vill lägga till roller som du vill använda för ditt program. 

    ```
    {
       "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Administrators Only",
            "displayName": "Admin",
            "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "Administrator"
        }
    ]
    }
    ```
    > [!Note]
    > Du kan bara lägga till nya roller efter den **msiam_access** för åtgärden korrigering. Dessutom kan du lägga till så många roller som du vill per behöver din organisation. Azure AD skickar den **värdet** av dessa roller som anspråksvärdet SAML-svar.
    
    j. Gå tillbaka till ditt diagram Explorer och ändra metod från **hämta** till **korrigering**. Korrigera service principal objekt om du vill ha önskad roller genom att uppdatera appRoles egenskapen liknar det i exemplet ovan. Klicka på **Kör fråga** att köra åtgärden korrigering. Ett meddelande bekräftar skapandet av rollen.

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. När tjänstens huvudnamn har korrigerats med flera roller, kan du tilldela respektive roller användare. Detta kan göras genom att gå till portalen och gå till respektive appen. Klicka på den **användare och grupper** fliken längst upp. Detta visar alla användare och grupper som redan är tilldelade till programmet. Du kan lägga till nya användare på nya rollen kan även välja den befintliga användaren och klicka på **redigera** ändra rollen.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     Om du vill tilldela rollen för alla användare, Välj den nya rollen och klicka på **tilldela** knappen längst ned på sidan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Observera att du behöver uppdatera din session i Azure portal och se nya roller.

8. Vi behöver uppdatera efter tilldela roller till användare, **attribut** kan definiera anpassade mappningen av **rollen** anspråk.

9. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | -------------- | ----------------|    
    | Rollnamn      | User.assignedrole |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Konfigurera attribut för enkel inloggning](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn efter behov. I det här exemplet har vi använt **rollnamn** som anspråk namn.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    e. Klicka på **OK**.

10. Att testa ditt program i IDP initieras för enkel inloggning på loggen till åtkomstpanelen (https://myapps.microsoft.com) och klicka på panelen för ditt program. Du bör se alla tilldelade roller för användaren med anspråk namn du gav i SAML-token.

## <a name="update-existing-role"></a>Uppdatera en befintlig roll

Utför följande steg - för att uppdatera en befintlig roll

1. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Logga in på webbplatsen Explorer diagram med autentiseringsuppgifterna som global administratör/medadministratör för din klient.
    
3. Ändra versionen som ska **beta** och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Om du använder flera kataloger, ska sedan du följa det här mönstret `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Hämta det måste du ändra listan över tjänstens huvudnamn hämtas och. Du kan också använda Ctrl + F för att söka efter programmet från de angivna ServicePrincipals. Sök efter den **objekt-id**, som du har kopierat från egenskapssidan och Använd följande fråga för att få till respektive tjänstens huvudnamn.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extrahera appRoles egenskapen från tjänsten huvudobjektet.
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Följ nedanstående steg för att uppdatera den befintliga rollen:

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Ändra metod från **hämta** till **korrigering**.

    * Kopiera de befintliga rollerna och klistra in dem i den **brödtext i begäran**.

    * Uppdatera värdet för rollen genom att uppdatera den **Rollbeskrivning**, **roll värdet** eller **roll displayname** efter behov.

    * När du har uppdaterat alla nödvändiga roller, klickar du på **Kör fråga**.
        
## <a name="delete-existing-role"></a>Ta bort befintlig roll

Om du vill ta bort en befintlig roll, gör du följande:

1. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

2. Logga in på webbplatsen Explorer diagram med autentiseringsuppgifterna som global administratör/medadministratör för din klient.

3. Ändra versionen som ska **beta** och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Om du använder flera kataloger, ska sedan du följa det här mönstret `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Hämta det måste du ändra listan över tjänstens huvudnamn hämtas och. Du kan också använda Ctrl + F för att söka efter programmet från de angivna ServicePrincipals. Sök efter den **objekt-id**, som du har kopierat från egenskapssidan och Använd följande fråga för att få till respektive tjänstens huvudnamn.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extrahera appRoles egenskapen från tjänsten huvudobjektet.
    
    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Följ nedanstående steg för att ta bort den befintliga rollen:

    ![Dialogrutan för diagram explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * Ändra metod från **hämta** till **korrigering**.

    * Kopiera de befintliga rollerna från programmet och klistra in dem i den **brödtext i begäran**.
        
    * Ange den **IsEnabled** värde till **FALSKT** för rollen som du vill ta bort

    * Klicka på **Kör fråga**.
    
    > [!NOTE] 
    > Kontrollera att du har **msiam_access** användarroll och id matchar i rollen skapas.
    
7. När rollen har inaktiverats kan ta bort detta rollen block från avsnittet appRoles, hålla metoden som **korrigering** och på **Kör fråga**.
    
8. När du har kört frågan tas rollen bort.
    
    > [!NOTE]
    > Rollen måste inaktiveras först innan den kan tas bort. 

## <a name="next-steps"></a>Nästa steg

Se [dokumentationen till App ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) ytterligare anvisningar.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
