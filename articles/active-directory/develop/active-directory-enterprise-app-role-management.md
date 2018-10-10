---
title: Konfigurera rollanspråk som utfärdats i SAML-token för företagsprogram i Azure AD | Microsoft Docs
description: Lär dig att konfigurera rollanspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory
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
ms.date: 10/05/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 2bb9d69487b8576cdae60a1a613a341898495f06
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904426"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Så här: Konfigurera rollanspråk som utfärdats i SAML-token för företagsprogram

Du kan anpassa Anspråkstypen för rollen som anspråk i svarstoken du får när du godkänner en app med hjälp av Azure Active Directory (AD Azure).

## <a name="prerequisites"></a>Förutsättningar

- En Azure AD-prenumeration med katalogen installationen.
- En prenumeration som har enkel inloggning (SSO) aktiverat. Du måste konfigurera enkel inloggning med ditt program.

## <a name="when-to-use-this-feature"></a>När du ska använda den här funktionen

Om ditt program förväntar sig anpassade roller som ska skickas som en SAML-svar, måste du använda den här funktionen. Du kan skapa så många roller som du behöver som ska skickas tillbaka från Azure AD i ditt program.

## <a name="create-roles-for-an-application"></a>Skapa roller för ett program

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon.

    ![Azure Active Directory-ikon][1]

2. Välj **företagsprogram**. Välj sedan **alla program**.

    ![Fönstret för Enterprise-program][2]

3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![”Nytt program” knappen][3]

4. I sökrutan skriver du namnet på programmet och välj sedan ditt program från panelen resultatet. Välj den **Lägg till** för att lägga till programmet.

    ![Program i listan med resultat](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. När programmet har lagts till, går du till den **egenskaper** sidan och kopiera objekt-ID.

    ![Egenskapssidan](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Öppna [Azure AD Graph-testaren](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster och gör följande:

    a. Logga in till webbplatsen Graph Explorer med hjälp av global administratör eller coadmin autentiseringsuppgifterna för din klient.

    b. Du behöver behörighet att skapa rollerna. Välj **ändringsbehörigheter** att få behörigheterna.

      ![Knappen ”Ändra behörigheter”](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte har dessa redan) och välj **behörighet att ändra**.

      ![Lista över behörigheter och knappen ”Ändra behörigheter”](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Rollen Administratör för moln-App och administratören fungerar inte i det här scenariot som vi behöver de globala administratörsbehörigheter för Directory läsning och skrivning.

    d. Acceptera samtycke. Du är inloggad i systemet igen.

    e. Ändra versionen till **beta**, och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Om du använder flera kataloger, gör du det här mönstret: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Graph Explorer dialogruta, med frågan för att hämta tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Vi är redan under uppgraderingen av API: er så att kunder kan se vissa avbrott i tjänsten.

    f. Från listan över hämtade tjänstens huvudnamn, får du det som du behöver ändra. Du kan också använda Ctrl + F för att söka efter programmet från alla angivna huvudnamn. Sök efter objekt-ID som du kopierade från den **egenskaper** sidan och Använd följande fråga för att komma till tjänstens huvudnamn:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Fråga för att hämta tjänstens huvudnamn som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrahera den **appRoles** egenskap från det tjänstens huvudnamnsobjekt.

      ![Information om egenskapen appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Om du använder den anpassade appen (inte Azure Marketplace-app) måste du se två standardroller: användar- och msiam_access. Msiam_access är den enda standardrollen för Marketplace-app. Du behöver inte göra några ändringar i standardroller.

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
      > Du kan bara lägga till nya roller efter msiam_access för patch-åtgärden utfördes. Du kan också lägga till så många roller som organisationen behöver. Azure AD skickar värdet för dessa roller anspråksvärdet i SAML-svar. För att generera GUID värden för ID för nya roller använda webbverktyg som [detta](https://www.guidgenerator.com/)

    i. Gå tillbaka till Graph-testaren och ändra metod från **hämta** till **KORRIGERA**. Korrigera det tjänstens huvudnamnsobjekt om du vill ha önskade roller genom att uppdatera den **appRoles** egenskap som den som visas i föregående exempel. Välj **Kör fråga** att köra patch-åtgärden utfördes. Ett meddelande bekräftar att skapa rollen.

      ![Korrigeringsåtgärd med meddelande](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. När tjänstens huvudnamn är uppdaterad med flera roller, kan du tilldela användare till respektive roller. Du kan tilldela användarna genom att gå till portalen och bläddra till programmet. Välj den **användare och grupper** fliken. Den här fliken listar alla användare och grupper som redan har tilldelats till appen. Du kan lägga till nya användare på de nya rollerna. Du kan också välja en befintlig användare och välja **redigera** ändra rollen.

    ![Fliken ”användare och grupper”](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Om du vill tilldela rollen till alla användare, Välj den nya rollen och välj den **tilldela** knappen längst ned på sidan.

    ![Fönstret ”Redigera tilldelningen” och ”Välj roll” fönstret](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Du behöver uppdatera din session i Azure portal för att se nya roller.

8. Uppdatera den **attribut** tabell för att definiera en anpassad mappning av rollen anspråket.

9. I den **användarattribut** delen av den **enkel inloggning** dialogrutan konfigurerar attributet SAML-token som visas i bilden och utför följande steg.

    | Attributnamn | Attributvärde |
    | -------------- | ----------------|
    | Rollnamn  | User.assignedroles |

    a. Välj **Lägg till attribut** att öppna den **lägga till attributet** fönstret.

      ![Knappen ”Lägg till attribut”](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Fönstret ”Lägg till attribut”](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. I den **namn** skriver attributets namn efter behov. Det här exemplet används **rollnamn** som anspråkets namn.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna den **Namespace** lämnas omarkerad.

    e. Välj **OK**.

10. Om du vill testa ditt program i en enkel inloggning som initieras av en identitetsprovider, logga in på den [åtkomstpanelen](https://myapps.microsoft.com) och välj din program-panel. Du bör se alla tilldelade roller för användaren med namnet på anspråk som du har gett i SAML-token.

## <a name="update-an-existing-role"></a>Uppdatera en befintlig roll

Utför följande steg om du vill uppdatera en befintlig roll:

1. Öppna [Azure AD Graph-testaren](https://developer.microsoft.com/graph/graph-explorer).

2. Logga in till webbplatsen Graph Explorer med hjälp av global administratör eller coadmin autentiseringsuppgifterna för din klient.

3. Ändra versionen till **beta**, och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger, gör du det här mönstret: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer dialogruta, med frågan för att hämta tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Från listan över hämtade tjänstens huvudnamn, får du det som du behöver ändra. Du kan också använda Ctrl + F för att söka efter programmet från alla angivna huvudnamn. Sök efter objekt-ID som du kopierade från den **egenskaper** sidan och Använd följande fråga för att komma till tjänstens huvudnamn:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Fråga för att hämta tjänstens huvudnamn som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrahera den **appRoles** egenskap från det tjänstens huvudnamnsobjekt.

    ![Information om egenskapen appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Använd följande steg för att uppdatera den befintliga rollen.

    ![Brödtext i begäran för ”korrigering” med ”beskrivning” och ”displayname” markerat](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Ändra metod från **hämta** till **KORRIGERA**.

    b. Kopiera befintliga roller och klistra in dem under **Begärandetext**.

    c. Uppdatera värdet för en roll genom att uppdatera den Rollbeskrivning, rollvärde eller visningsnamn för rollen efter behov.

    d. När du uppdaterar alla nödvändiga roller, väljer **Kör fråga**.

## <a name="delete-an-existing-role"></a>Ta bort en befintlig roll

Utför följande steg för att ta bort en befintlig roll:

1. Öppna [Azure AD Graph-testaren](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

2. Logga in till webbplatsen Graph Explorer med hjälp av global administratör eller coadmin autentiseringsuppgifterna för din klient.

3. Ändra versionen till **beta**, och hämta listan över tjänstens huvudnamn från din klient med hjälp av följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger, gör du det här mönstret: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer dialogruta, med frågan för att hämta listan över tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Från listan över hämtade tjänstens huvudnamn, får du det som du behöver ändra. Du kan också använda Ctrl + F för att söka efter programmet från alla angivna huvudnamn. Sök efter objekt-ID som du kopierade från den **egenskaper** sidan och Använd följande fråga för att komma till tjänstens huvudnamn:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Fråga för att hämta tjänstens huvudnamn som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrahera den **appRoles** egenskap från det tjänstens huvudnamnsobjekt.

    ![Information om egenskapen appRoles från det tjänstens huvudnamnsobjekt](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Använd följande steg för att ta bort den befintliga rollen.

    ![Brödtext i begäran för ”korrigering” med IsEnabled inställd på false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Ändra metod från **hämta** till **KORRIGERA**.

    b. Kopiera befintliga roller från programmet och klistra in dem under **Begärandetext**.

    c. Ange den **IsEnabled** värde att **FALSKT** för den roll som du vill ta bort.

    d. Välj **Kör fråga**.

    > [!NOTE]
    > Se till att du har rollen msiam_access och ID: T matchar i rollen genererade.

7. När rollen har inaktiverats kan du ta bort detta rollen block från den **appRoles** avsnittet. Behåll metoden som **KORRIGERA**, och välj **Kör fråga**.

8. När du har kört frågan tas rollen bort.

    > [!NOTE]
    > Rollen måste inaktiveras innan den kan tas bort.

## <a name="next-steps"></a>Nästa steg

Ytterligare anvisningar finns i den [dokumentationen till app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png