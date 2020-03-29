---
title: Konfigurera rollanspråk för Azure AD-appar för företag | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar rollanspråket som utfärdas i SAML-token för företagsprogram i Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: a70abd1cddb866037926bbbc881682d50599366b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699265"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Så här konfigurerar du rollanspråket som utfärdas i SAML-token för företagsprogram

Genom att använda Azure Active Directory (Azure AD) kan du anpassa anspråkstypen för rollanspråket i svarstoken som du får när du har auktoriserat en app.

## <a name="prerequisites"></a>Krav

- En Azure AD-prenumeration med katalogkonfiguration.
- En prenumeration som har enkel inloggning (SSO) aktiverad. Du måste konfigurera SSO med ditt program.

## <a name="when-to-use-this-feature"></a>När ska den här funktionen användas

Om ditt program förväntar sig att anpassade roller ska skickas i ett SAML-svar måste du använda den här funktionen. Du kan skapa så många roller som du behöver skickas tillbaka från Azure AD till ditt program.

## <a name="create-roles-for-an-application"></a>Skapa roller för ett program

1. Välj Azure Active Directory-ikonen i **Azure Active Directory** [Azure-portalen](https://portal.azure.com)i den vänstra rutan.

    ![Ikon för Azure Active Directory][1]

2. Välj **Företagsprogram**. Välj sedan **Alla program**.

    ![Fönstret Företagsprogram][2]

3. Om du vill lägga till ett nytt program markerar du knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen "Ny ansökan"][3]

4. Skriv namnet på programmet i sökrutan och välj sedan programmet på resultatpanelen. Välj knappen **Lägg** till om du vill lägga till programmet.

    ![Ansökan i resultatlistan](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. När programmet har lagts till går du till sidan **Egenskaper** och kopierar objekt-ID:n.

    ![Sidan Egenskaper](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Öppna [Utforskaren](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster och gör följande:

    a. Logga in på Graph Explorer-webbplatsen med hjälp av de globala administratörs- eller coadmin-autentiseringsuppgifterna för din klientorganisation.

    b. Du behöver tillräcklig behörighet för att skapa rollerna. Välj **ändra behörigheter** för att hämta behörigheterna.

      ![Knappen "Ändra behörigheter"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Markera följande behörigheter i listan (om du inte redan har dessa) och välj **Ändra behörigheter**.

      ![Lista över behörigheter och knappen "Ändra behörigheter"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Rollen Cloud App Administrator och App Administrator fungerar inte i det här scenariot eftersom vi behöver behörigheterna för global administratör för läsa och skrivkatalog.

    d. Acceptera samtycket. Du är inloggad i systemet igen.

    e. Ändra versionen till **beta**och hämta listan över tjänsthuvudnamn från din klient med hjälp av följande fråga:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Om du använder flera kataloger följer du det här mönstret:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Dialogrutan Graph Explorer, med frågan om hämtning av tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Vi håller redan på att uppgradera API:erna så att kunderna kan se vissa störningar i tjänsten.

    f. Hämta de senaste tjänsthuvudnamnen i listan över hämtade tjänsthuvudnamn. Du kan också använda Ctrl+F för att söka i programmet från alla angivna tjänsthuvudnamn. Sök efter det objekt-ID som du kopierade från sidan **Egenskaper** och använd följande fråga för att komma åt tjänstens huvudnamn:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Fråga för att hämta tjänstens huvudnamn som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrahera egenskapen **appRoles** från tjänstens huvudobjekt.

      ![Information om egenskapen appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Om du använder den anpassade appen (inte Azure Marketplace-appen) visas två standardroller: användare och msiam_access. För Marketplace-appen är msiam_access den enda standardrollen. Du behöver inte göra några ändringar i standardrollerna.

    h. Generera nya roller för ditt program.

      Följande JSON är ett exempel på **appRoles-objektet.** Skapa ett liknande objekt för att lägga till de roller som du vill använda för ditt program.

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
      > Du kan bara lägga till nya roller efter msiam_access för korrigeringsåtgärden. Du kan också lägga till så många roller som din organisation behöver. Azure AD skickar värdet för dessa roller som anspråksvärde i SAML-svaret. Om du vill generera GUID-värdena för ID för nya roller använder du webbverktygen så [här](https://www.guidgenerator.com/)

    i. Gå tillbaka till Graph Explorer och ändra metoden från **GET** till **PATCH**. Korrigera tjänstens huvudobjekt om du vill ha önskade roller genom att uppdatera egenskapen **appRoles** som den som visas i föregående exempel. Välj **Kör fråga** om du vill köra korrigeringsåtgärden. Ett lyckat meddelande bekräftar skapandet av rollen.

      ![Korrigeringsåtgärd med meddelande om lyckade meddelanden](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. När tjänstens huvudnamn har korrigerats med fler roller kan du tilldela användare till respektive roller. Du kan tilldela användarna genom att gå till portalen och surfa till programmet. Välj fliken **Användare och grupper.** På den här fliken visas alla användare och grupper som redan har tilldelats appen. Du kan lägga till nya användare i de nya rollerna. Du kan också välja en befintlig användare och välja **Redigera** för att ändra rollen.

    ![Fliken "Användare och grupper"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Om du vill tilldela rollen till alla användare väljer du den nya rollen och väljer knappen **Tilldela** längst ned på sidan.

    ![Fönstret "Redigera tilldelning" och fönstret "Välj roll"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Du måste uppdatera din session i Azure-portalen för att se nya roller.

8. Uppdatera tabellen **Attribut** för att definiera en anpassad mappning av rollanspråket.

9. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Attributnamn | Attributvärde |
    | -------------- | ----------------|
    | Rollnamn  | user.assignedroles |

    >[!NOTE]
    >Om rollanspråksvärdet är null skickar Azure AD inte det här värdet i token och det här är standard enligt design.

    a. Klicka på **Redigera** ikon om du vill öppna dialogrutan **Användarattribut & anspråk.**

      ![Knappen "Lägg till attribut"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. Lägg till ATTRIBUTEt SAML-token i dialogrutan **Hantera användaranspråk** genom att klicka på **Lägg till nya anspråk**.

      ![Knappen "Lägg till attribut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Fönstret "Lägg till attribut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Skriv attributnamnet efter behov i rutan **Namn.** I det här exemplet används **rollnamn** som anspråksnamn.

    d. Lämna **rutan Namnområde** tom.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Välj **Spara**.

10. Om du vill testa ditt program i en enda inloggning som initieras av en identitetsprovider loggar du in på [åtkomstpanelen](https://myapps.microsoft.com) och väljer programpanelen. I SAML-token bör du se alla tilldelade roller för användaren med anspråksnamnet som du har angett.

## <a name="update-an-existing-role"></a>Uppdatera en befintlig roll

Så här uppdaterar du en befintlig roll:

1. Öppna [Utforskaren](https://developer.microsoft.com/graph/graph-explorer)i Microsoft Graph .

2. Logga in på Graph Explorer-webbplatsen med hjälp av de globala administratörs- eller coadmin-autentiseringsuppgifterna för din klientorganisation.

3. Ändra versionen till **beta**och hämta listan över tjänsthuvudnamn från din klient med hjälp av följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger följer du det här mönstret:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogrutan Graph Explorer, med frågan om hämtning av tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Hämta de senaste tjänsthuvudnamnen i listan över hämtade tjänsthuvudnamn. Du kan också använda Ctrl+F för att söka i programmet från alla angivna tjänsthuvudnamn. Sök efter det objekt-ID som du kopierade från sidan **Egenskaper** och använd följande fråga för att komma åt tjänstens huvudnamn:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Fråga för att hämta tjänstens huvudnamn som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrahera egenskapen **appRoles** från tjänstens huvudobjekt.

    ![Information om egenskapen appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Om du vill uppdatera den befintliga rollen följer du stegen nedan.

    ![Begär brödtext för "PATCH", med "beskrivning" och "visningsnamn" markerat](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Ändra metoden från **GET** till **PATCH**.

    b. Kopiera befintliga roller och klistra in dem under **Begäran brödtext**.

    c. Uppdatera värdet för en roll genom att uppdatera rollbeskrivningen, rollvärdet eller rollvisningsnamnet efter behov.

    d. När du har uppdaterat alla nödvändiga roller väljer du **Kör fråga**.

## <a name="delete-an-existing-role"></a>Ta bort en befintlig roll

Så här tar du bort en befintlig roll:

1. Öppna [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

2. Logga in på Graph Explorer-webbplatsen med hjälp av de globala administratörs- eller coadmin-autentiseringsuppgifterna för din klientorganisation.

3. Ändra versionen till **beta**och hämta listan över tjänsthuvudnamn från din klient med hjälp av följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger följer du det här mönstret:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogrutan Graph Explorer, med frågan för att hämta listan över tjänstens huvudnamn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Hämta de senaste tjänsthuvudnamnen i listan över hämtade tjänsthuvudnamn. Du kan också använda Ctrl+F för att söka i programmet från alla angivna tjänsthuvudnamn. Sök efter det objekt-ID som du kopierade från sidan **Egenskaper** och använd följande fråga för att komma åt tjänstens huvudnamn:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Fråga för att hämta tjänstens huvudnamn som du behöver ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrahera egenskapen **appRoles** från tjänstens huvudobjekt.

    ![Information om egenskapen appRoles från tjänstens huvudobjekt](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Om du vill ta bort den befintliga rollen följer du stegen nedan.

    ![Begär brödtext för "PATCH", med IsEnabled inställd på false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Ändra metoden från **GET** till **PATCH**.

    b. Kopiera befintliga roller från programmet och klistra in dem under **Begärandetext**.

    c. Ange värdet **IsEnabled** till **false** för den roll som du vill ta bort.

    d. Välj **Kör fråga**.

    > [!NOTE]
    > Kontrollera att du har den msiam_access rollen och att ID:et matchar i den genererade rollen.

7. När rollen har inaktiverats tar du bort det rollblocket från avsnittet **appRoles.** Behåll metoden som **PATCH**och välj **Kör fråga**.

8. När du har kört frågan tas rollen bort.

    > [!NOTE]
    > Rollen måste inaktiveras innan den kan tas bort.

## <a name="next-steps"></a>Nästa steg

Ytterligare steg finns i [appdokumentationen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
