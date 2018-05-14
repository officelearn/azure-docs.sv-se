---
title: Distribuera Azure Blockchain arbetsstationen
description: Hur du distribuerar Azure Blockchain arbetsstationen
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/22/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 6ee60d05897de7bb5408a226202623fd5955a88a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="deploy-azure-blockchain-workbench"></a>Distribuera Azure Blockchain arbetsstationen

Azure Blockchain arbetsstationen distribueras med hjälp av en lösningsmall i Azure Marketplace. Mallen förenklar distributionen av komponenter som behövs för att skapa blockchain program. När har distribuerats, ger Blockchain arbetsstationen åtkomst till klientprogram att skapa och hantera användare och blockchain program.

Läs mer om komponenterna Blockchain arbetsstationen, [Azure Blockchain arbetsstationen arkitektur](blockchain-workbench-architecture.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Azure Blockchain arbetsstationen kräver flera förutsättningar före distributionen. Kraven inkluderar Azure AD-konfigurationen och tillämpningen registreringar.

### <a name="blockchain-workbench-api-app-registration"></a>Registrering för Blockchain arbetsstationen API-app

Distribution av Blockchain Workbench kräver registrering av Azure AD-program. Du behöver en Azure Active Directory (Azure AD)-klient för att registrera appen. Du kan använda en befintlig klient eller skapa en ny klient. Om du använder en befintlig Azure AD-klient behöver behörighet att registrera program inom en Azure AD-klient. Programmet registreringar måste vara i prenumerationen administratörens innehavare för prenumerationen där arbetsstationen har distribuerats. Mer information om Azure AD-klienter finns [skaffa en Active Directory-klient](../active-directory/develop/active-directory-howto-tenant.md) och [integrera program med Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ditt konto i övre högra hörn och växla till önskad Azure AD-klient. Klienten måste vara den prenumerationsadministratör innehavare för prenumerationen där arbetsstationen distribueras och du har behörighet att registrera program.
3. I det vänstra navigeringsfönstret väljer du den **Azure Active Directory** service. Välj **App registreringar** > **nya appregistrering**.

    ![Appregistrering](media/blockchain-workbench-deploy/app-registration.png)

4. Ange en **namn** och **inloggnings-URL** för programmet. Du kan använda platshållarvärdena eftersom värdena ändras under distributionen. 

    ![Skapa appregistrering](media/blockchain-workbench-deploy/app-registration-create.png)

    |Inställning  | Värde  |
    |---------|---------|
    |Namn | `Blockchain API` |
    |Programtyp |Webbapp/API|
    |Inloggnings-URL | `https://blockchainapi` |

5. Välj **skapa** registrera Azure AD-programmet.

### <a name="modify-application-manifest"></a>Ändra programmanifestet

Därefter måste du ändra programmanifestet att använda programmet roller i Azure AD för att ange Blockchain arbetsstationen administratörer.  Läs mer om applikationsmanifest [Azure Active Directory-programmanifestet](../active-directory/develop/active-directory-application-manifest.md).

1. Programmet som du har registrerat, Välj **Manifest** i informationsfönstret registrerade programmet.
2. Generera ett GUID. Du kan använda PowerShell-kommandot `[guid]::NewGuid()` eller online verktyg för att generera ett GUID. 
3. Du kommer att uppdatera den **appRoles** avsnitt i manifestet. Välj i rutan Redigera manifestet **redigera** och Ersätt `"appRoles": []` med den angivna JSON. Se till att ersätta värdet för den **id** med det GUID som du genererade. 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![Redigera manifest](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > Värdet **administratör** krävs för att identifiera Blockchain arbetsstationen administratörer.

4.  Klicka på **spara** spara application manifest ändringarna.

### <a name="add-graph-api-required-permissions"></a>Lägg till behörigheter för Graph API krävs

API-programmet måste begära behörighet för användaren att få åtkomst till katalogen. Ange följande behörighet för API-program:

1. Välj i appregistrering Blockchain API **Inställningar > nödvändiga behörigheter > Välj en API > Microsoft Graph**.

    ![Välj ett API](media/blockchain-workbench-deploy/client-app-select-api.png)

    Klicka på **Välj**.

2. I **Aktivera åtkomst** under **programbehörigheter**, Välj **fullständiga profiler för alla användare att läsa**.

    ![Aktivera åtkomst](media/blockchain-workbench-deploy/client-app-read-perms.png)

    Klicka på **Välj** Klicka **klar**.

3. I **nödvändiga behörigheter**väljer **bevilja med** Välj **Ja** för verifiering prompten.

   ![Bevilja behörigheter](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   Bevilja behörighet tillåter Blockchain arbetsstationen för att få åtkomst till användare i katalogen. Skrivskyddad behörighet krävs för att söka efter och lägga till medlemmar i Blockchain arbetsstationen.

### <a name="add-graph-api-key-to-application"></a>Lägga till Graph API-nyckel i programmet

Blockchain arbetsstationen använder Azure AD som hanteringssystemet huvudsakliga identiteten för användare som interagerar med blockchain program. Du måste lägga till en snabbtangent för Blockchain arbetsstationen för att få åtkomst till Azure AD och hämta användarinformation, till exempel namn och e-postmeddelanden. Blockchain arbetsstationen använder nyckeln för att autentisera med Azure AD.

1. Programmet som du har registrerat, Välj **inställningar** i informationsfönstret registrerade programmet.
2. Välj **Nycklar**.
3. Lägg till en ny nyckel genom att ange en nyckel **beskrivning** och välja **upphör att gälla** duration-värde. 

    ![Skapa en nyckel](media/blockchain-workbench-deploy/app-key-create.png)

    |Inställning  | Värde  |
    |---------|---------|
    | Beskrivning | `Service` |
    | Förfaller | Välj en giltighetstiden |

4. Välj **Spara**. 
5. Kopiera värdet för nyckeln och spara den för senare. Du behöver den för distribution.

    > [!IMPORTANT]
    >  Om du inte sparar nyckeln för distributionen av behöver du skapa en ny nyckel. Du kan inte hämta värdet för nyckeln från portalen senare.

### <a name="get-application-id"></a>Hämta program-ID

Program-ID och klient information krävs för distributionen. Samla in och lagra information för användning under distributionen.

1. Programmet som du har registrerat, Välj **inställningar** > **egenskaper**.
2.  I den **egenskaper** rutan, kopiera och store följande värden för senare användning under distributionen.

    ![Egenskaper för API-app](media/blockchain-workbench-deploy/app-properties.png)

    | Inställningen för att lagra  | Använda i distributionen |
    |------------------|-------------------|
    | Program-ID:t | Installationsprogram för Azure Active Directory > program-ID |

### <a name="get-tenant-domain-name"></a>Hämta klient domännamnet

Samla in och lagra domännamnet för Active Directory-klient där programmen har registrerats. 

I det vänstra navigeringsfönstret väljer du den **Azure Active Directory** service. Välj **anpassade domännamn**. Kopiera och spara namnet på en domän.

![Domännamn](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Distribuera Blockchain arbetsstationen

När de nödvändiga steg har utförts, är du redo att distribuera Blockchain arbetsstationen. Följande avsnitt beskriver hur du distribuerar ramen.

1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  Välj ditt konto i övre högra hörnet och växla till önskad Azure AD-klient där du vill distribuera Azure Blockchain arbetsstationen.
3.  I den vänstra rutan, Välj **skapar du en resurs**. Sök efter `Azure Blockchain Workbench` i den **söka Marketplace** sökfältet. 

    ![Marketplace-sökfältet](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  Välj **Azure Blockchain arbetsstationen**.

    ![Marketplace-sökresultat](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  Välj **Skapa**.
5.  Slutför de grundläggande inställningarna.

    ![Skapa Azure Blockchain arbetsstationen](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Resurs-prefix | Kort Unik identifierare för din distribution. Det här värdet används som bas för namngivning av resurser. |
    | Användarnamn för VM | Användarnamnet används som administratör för alla virtuella datorer (VM). |
    | Autentiseringstyp | Välj om du vill använda ett lösenord eller nyckeln för att ansluta till virtuella datorer. |
    | Lösenord | Lösenordet som används för att ansluta till virtuella datorer. |
    | SSH | Använd en offentlig RSA-nyckel i den format för en rad som början med **ssh-rsa** eller använda PEM-format med flera rader. Du kan skapa SSH-nycklar med hjälp av `ssh-keygen` på Linux och OS X eller genom att använda PuTTYGen i Windows. Mer information om SSH-nycklar finns [hur du använder SSH-nycklar med Windows på Azure](../virtual-machines/linux/ssh-from-windows.md). |
    | Lösenord för databas / Bekräfta lösenord | Ange lösenordet som ska användas för åtkomst till databasen som skapats som en del av distributionen. |
    | Region för distribution | Ange var du vill distribuera Blockchain arbetsstationen resurser. För bästa initieras ska matcha den **plats** inställningen. |
    | Prenumeration | Ange Azure-prenumeration som du vill använda för din distribution. |
    | Resursgrupper | Skapa en ny resursgrupp genom att välja **Skapa nytt** och ange en unik resursgruppens namn. |
    | Plats | Ange den region som du vill distribuera ramen. |

6.  Välj **OK** Slutför konfigurationsavsnittet grundläggande inställningen.

7.  Slutför den **installationsprogram för Azure Active Directory**.

    ![Installationsprogram för Azure AD](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Domännamn | Använda Azure AD-klient som samlas in i den [Get klientdomännamn](#get-tenant-domain-name) nödvändiga avsnittet. |
    | Program-ID:t | Använd det program-ID från app klientregistrering Blockchain som samlas in i den [hämta program-ID](#get-application-id) nödvändiga avsnittet. |
    | Programnyckel | Använd nyckeln för programmet från Blockchain app klientregistrering som samlas in i den [lägga till Graph API-nyckeln som programmet](#add-graph-api-key-to-application) nödvändiga avsnittet. |


8.  Klicka på **OK** Slutför konfigurationsavsnittet Azure AD-parametrar.

9.  Slutför den **storlek och prestanda** inställningar.

    ![Inställningarna för nätverk och prestanda](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Antalet blockchain noder | Välj antal Ethereum PoA verifieraren noder som ska distribueras i nätverket. |
    | Lagringsprestanda | Välj den önskade Virtuella lagringsprestanda för nätverket blockchain. |
    | Virtuell datorstorlek | Välj den önskade VM-storleken för nätverket blockchain. |

10. Välj **OK** Slutför avsnittet network storlek och prestanda.

11. Slutför den **Azure-Monitor** inställningar.

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Övervakning | Välj om du vill aktivera Azure-Monitor att övervaka nätverket blockchain |
    | Ansluta till befintliga logganalys-instans | Välj om du vill använda en befintlig logganalys-instans eller skapa en ny. Om du använder en befintlig instans, ange ditt arbetsyte-ID och primärnyckel. |

12. Klicka på **OK** Slutför Azure-Monitor-avsnittet.

13. Granska sammanfattningen för att kontrollera att alla parametrar är korrekta.

    ![Sammanfattning](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. Välj **skapa** att acceptera villkoren och distribuera din Azure Blockchain arbetsstationen.

Distributionen kan ta upp till 90 minuter. Du kan använda Azure-portalen för att övervaka förloppet. I den nya resursgruppen väljer **distributioner > Översikt** att se status för distribuerade artefakter.

## <a name="blockchain-workbench-web-url"></a>Webbadress för Blockchain arbetsstationen

När distributionen av Blockchain arbetsstationen är klar innehåller en ny resursgrupp resurserna Blockchain arbetsstationen. Blockchain arbetsstationen tjänster kan nås via en URL. Följande steg visar hur du hämtar Webbadressen av distribuerade framework.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det vänstra navigeringsfönstret väljer **resursgrupper**
3. Välj resursgruppens namn du angav när du distribuerar Blockchain arbetsstationen.
4. Klicka på den **typen** kolumnrubrik för att sortera listan efter typ i alfabetisk ordning.
5. Det finns två resurser med typen **Apptjänst**. Välj en resurs av typen **Apptjänst** *utan* den ”-api” suffix.

    ![App service-lista](media/blockchain-workbench-deploy/resource-group-list.png)

6.  I App Service **Essentials** avsnittet, kopiera den **URL** -värde som representerar Webbadressen till din distribuerade Blockchain arbetsstationen.

    ![App service essentials](media/blockchain-workbench-deploy/app-service.png)

## <a name="configuring-the-reply-url"></a>Konfigurera Reply-URL

När Azure Blockchain arbetsstationen har distribuerats, nästa steg är att kontrollera att klientprogrammet Azure Active Directory (AD Azure) har registrerats på rätt **Reply URL** i distribuerade Blockchain arbetsstationen Webb-URL.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du är i klienten där du har registrerat Azure AD-klientprogrammet.
3. I det vänstra navigeringsfönstret väljer du den **Azure Active Directory** service. Välj **Appregistreringar**.
4. Välj Azure AD-client-program som du har registrerat i avsnittet förutsättningar.
5. Välj **Inställningar > svara URL: er**.
6. Ange huvudsakliga Webbadressen för Azure Blockchain arbetsstationen distributionen som du hämtade i den **hämta Webbadress för Azure Blockchain arbetsstationen** avsnitt. Svars-URL med prefixet `https://`.  Till exempel, `https://myblockchain2-7v75.azurewebsites.net`

    ![Svars-URL:er](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Välj **spara** att uppdatera klientregistrering.

## <a name="next-steps"></a>Nästa steg

Du har distribuerat Azure Blockchain arbetsstationen i den här artikeln. Information om hur du skapar ett program för blockchain fortsätta i nästa artikel.

> [!div class="nextstepaction"]
> [Skapa en blockchain program i Azure Blockchain arbetsstationen](blockchain-workbench-create-app.md)