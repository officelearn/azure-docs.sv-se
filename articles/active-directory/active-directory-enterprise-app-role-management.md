---
title: Konfigurera roll-anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory | Microsoft Docs
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
ms.date: 05/30/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: c9a1d605f6cf2ef9dae3a5549e3848931d508394
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082751"
---
# <a name="configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Konfigurera roll-anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory

Du kan anpassa Anspråkstypen för rollen anspråk i token som svar på att du får när du godkänner en app med hjälp av Azure Active Directory (AD Azure).

## <a name="prerequisites"></a>Förutsättningar
- En Azure AD-prenumeration med katalogen installationen.
- En prenumeration som har enkel inloggning (SSO) aktiverat. Du måste konfigurera enkel inloggning med ditt program.

## <a name="when-to-use-this-feature"></a>När du ska använda den här funktionen

Om ditt program förväntar anpassade roller som ska skickas som en SAML-svar, måste du använda den här funktionen. Du kan skapa så många roller som du behöver för att skickas tillbaka från Azure AD till ditt program.

## <a name="create-roles-for-an-application"></a>Skapa roller för ett program

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-ikonen][1]

2. Välj **företagsprogram**. Välj sedan **alla program**.

    ![Rutan för Enterprise-program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![”Nya program” knappen][3]

4. I sökrutan skriver du namnet på programmet och välj sedan dina program från panelen resultat. Välj den **Lägg till** för att lägga till programmet.

    ![Program i resultatlistan](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. När programmet har lagts till, går du till den **egenskaper** och kopiera objekt-ID.

    ![Egenskapssidan](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Öppna den [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster och utför följande steg:

    a. Logga in på webbplatsen diagrammet Explorer med hjälp av de globala administratören eller coadmin autentiseringsuppgifterna för din klient.

    b. Du behöver behörighet att skapa roller. Välj **ändringsbehörighet** få behörigheter. 

      ![Knappen ”Ändra”](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte har dessa redan) och välj **behörighet att ändra**. 

      ![Lista över behörigheter och knappen ”Ändra behörigheter”](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Acceptera samtycke. Du är inloggad på datorn igen.

    e. Ändra versionen som ska **beta**, och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
      Om du använder flera kataloger Följ detta mönster: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
      ![Diagrammet Explorer dialogrutan med frågan för hämtning av tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Hämta det som du behöver ändra från listan över hämtade tjänstens huvudnamn. Du kan också använda Ctrl + F om du vill söka programmet från alla listade tjänstens huvudnamn. Sök efter objekt-ID som du kopierade från den **egenskaper** sidan och Använd följande fråga för att komma till tjänstens huvudnamn:
    
      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Frågan för att hämta huvudnamn för tjänsten som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrahera den **appRoles** egenskapen från tjänsten UPN-objektet. 

      ![Information om egenskapen appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Om du använder en anpassad app (inte Azure Marketplace-app) måste du se två standardroller: användar- och msiam_access. Marknadsplats-app är msiam_access endast standardroll. Du behöver inte göra några ändringar i standardrollerna.

    h. Generera nya roller för programmet. 

      Följande JSON är ett exempel på den **appRoles** objekt. Skapa ett liknande objekt för att lägga till de roller som du vill använda för ditt program. 

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
      > Du kan bara lägga till nya roller efter msiam_access för åtgärden korrigering. Du kan också lägga till så många roller som organisationen behöver. Azure AD skickar värdet för dessa roller som anspråksvärdet i SAML-svaret. För att generera GUID-värden för ID för nya roller använder webbverktyg som [detta](https://www.guidgenerator.com/)
    
    i. Gå tillbaka till diagrammet Explorer och ändra metod från **hämta** till **korrigering**. Korrigering av service principal objekt om du vill ha önskade roller genom att uppdatera den **appRoles** egenskap som visas i föregående exempel. Välj **Kör fråga** att köra åtgärden korrigering. Ett meddelande bekräftar skapandet av rollen.

      ![Korrigering av åtgärden med meddelande](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. När tjänstens huvudnamn har korrigerats med flera roller, kan du tilldela respektive roller användare. Du kan tilldela användare genom att gå till portalen och bläddra till programmet. Välj den **användare och grupper** fliken. Den här fliken listar alla användare och grupper som redan har tilldelats till appen. Du kan lägga till nya användare på de nya rollerna. Du kan också välja en befintlig användare och välja **redigera** ändra rollen.

    ![”Användare och grupper”](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Om du vill tilldela rollen för alla användare, Välj den nya rollen och välj den **tilldela** knappen längst ned på sidan.

    ![”Redigera tilldelning” och ”Välj roll”](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Du behöver uppdatera din session i Azure portal och se nya roller.

8. Uppdatering av **attribut** tabellen för att definiera en anpassad mappning av roll-anspråk.

9. I den **användarattribut** avsnitt i den **enkel inloggning** dialogrutan konfigurerar attributet SAML-token som visas i bilden och utför följande steg.
    
    | Attributets namn | Attributvärdet |
    | -------------- | ----------------|    
    | Rollnamn      | User.assignedrole |

    a. Välj **Lägg till attributet** att öppna den **lägga till attributet** fönstret.

      ![Knappen ”Lägg till attributet”](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![”Lägg till attributet” fönstret](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. I den **namn** Skriv attributnamnet efter behov. Det här exemplet används **rollnamn** som anspråkets namn.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tom.
    
    e. Välj **Ok**.

10. Om du vill testa ditt program i en enkel inloggning som initieras av en identitetsleverantör, logga in på den [åtkomstpanelen](https://myapps.microsoft.com) och välj panelen för ditt program. Du bör se alla tilldelade roller för användare med anspråk namn som du har angett i SAML-token.

## <a name="update-an-existing-role"></a>Uppdatera en befintlig roll

Utför följande steg om du vill uppdatera en befintlig roll:

1. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Logga in på webbplatsen diagrammet Explorer med hjälp av de globala administratören eller coadmin autentiseringsuppgifterna för din klient.
    
3. Ändra versionen som ska **beta**, och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Om du använder flera kataloger Följ detta mönster: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Diagrammet Explorer dialogrutan med frågan för hämtning av tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Hämta det som du behöver ändra från listan över hämtade tjänstens huvudnamn. Du kan också använda Ctrl + F om du vill söka programmet från alla listade tjänstens huvudnamn. Sök efter objekt-ID som du kopierade från den **egenskaper** sidan och Använd följande fråga för att komma till tjänstens huvudnamn:
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Frågan för att hämta huvudnamn för tjänsten som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extrahera den **appRoles** egenskapen från tjänsten UPN-objektet.
    
    ![Information om egenskapen appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Använd följande steg om du vill uppdatera den befintliga rollen.

    ![Begäran för ”KORRIGERINGSFIL” med ”beskrivning” och ”visningsnamn” markerat](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    a. Ändra metod från **hämta** till **korrigering**.

    b. Kopiera de befintliga rollerna och klistra in dem under **brödtext i begäran**.

    c. Uppdatera värdet för en roll genom att uppdatera Rollbeskrivning, rollvärde eller rollnamn efter behov.

    d. När du uppdaterar alla nödvändiga roller, Välj **Kör fråga**.
        
## <a name="delete-an-existing-role"></a>Ta bort en befintlig roll

Utför följande steg för att ta bort en befintlig roll:

1. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

2. Logga in på webbplatsen diagrammet Explorer med hjälp av de globala administratören eller coadmin autentiseringsuppgifterna för din klient.

3. Ändra versionen som ska **beta**, och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Om du använder flera kataloger Följ detta mönster: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Diagrammet Explorer dialogrutan med frågan för hämtning av lista över tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Hämta det som du behöver ändra från listan över hämtade tjänstens huvudnamn. Du kan också använda Ctrl + F om du vill söka programmet från alla listade tjänstens huvudnamn. Sök efter objekt-ID som du kopierade från den **egenskaper** sidan och Använd följande fråga för att komma till tjänstens huvudnamn:
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Frågan för att hämta huvudnamn för tjänsten som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extrahera den **appRoles** egenskapen från tjänsten UPN-objektet.
    
    ![Information om egenskapen appRoles från service principal objektet](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Använd följande steg om du vill ta bort de befintliga rollerna.

    ![Begäran för ”KORRIGERINGSFIL” med IsEnabled inställd på false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Ändra metod från **hämta** till **korrigering**.

    b. Kopiera de befintliga rollerna från programmet och klistra in dem under **brödtext i begäran**.
        
    c. Ange den **IsEnabled** värde till **FALSKT** för den roll som du vill ta bort.

    d. Välj **Kör fråga**.
    
    > [!NOTE] 
    > Kontrollera att du har rollen msiam_access och ID: T matchar genererade rollen.
    
7. När rollen har inaktiverats kan du ta bort detta rollen block från den **appRoles** avsnitt. Behåll metoden som **korrigering**, och välj **Kör fråga**.
    
8. När du kör frågan bort rollen.
    
    > [!NOTE]
    > Rollen måste inaktiveras innan den kan tas bort. 

## <a name="next-steps"></a>Nästa steg

Ytterligare anvisningar finns i [dokumentationen till app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png