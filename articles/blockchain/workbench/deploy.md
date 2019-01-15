---
title: Distribuera Azure Blockchain Workbench
description: Så här distribuerar du Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: fcba3aef29e1566f9dfb2b151c15fe683be94fdb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266595"
---
# <a name="deploy-azure-blockchain-workbench"></a>Distribuera Azure Blockchain Workbench

Azure Blockchain Workbench med hjälp av en mall i Azure Marketplace. Mallen gör det enklare för distribueringen av komponenter som behövs för att skapa blockchain-program. När distribuerats ger Blockchain Workbench åtkomst till klientprogram för att skapa och hantera användare och blockchain-program.

Läs mer om komponenterna i Blockchain Workbench, [Azure Blockchain Workbench arkitektur](architecture.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Blockchain Workbench kan du distribuera en blockchain-redovisning tillsammans med en uppsättning relevanta Azure-tjänster som oftast används för att skapa en blockchain-baserade program. Distribuera Blockchain Workbench resulterar i följande Azure-tjänster håller på att etableras i en resursgrupp i Azure-prenumerationen.

* 1 event Grid-ämne
* 1 Service Bus Namespace
* 1 Application Insights
* 1 SQL-databas (Standard S0)
* 2 app Services (Standard)
* 2 Azure Key Vaults
* 2 azure Storage-konton (Standard LRS)
* 2 VM-skalningsuppsättningar (för systemhälsoverifierarens- och arbetsroller noder)
* 2 virtuella nätverk (inklusive belastningsutjämnaren nätverkssäkerhetsgrupp och offentlig IP-adress för varje virtuellt nätverk)
* Valfritt: Azure Monitor

Följande är ett exempel på distribution skapats i **myblockchain** resursgrupp.

![Exempel på distribution](media/deploy/example-deployment.png)

Kostnaden för Blockchain Workbench är en aggregering av kostnaden för de underliggande Azure-tjänsterna. Information om priser för Azure-tjänster kan beräknas med hjälp av den [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/).

> [!IMPORTANT]
> Om du använder en prenumeration med låg tjänstbegränsningar, till exempel en kostnadsfria nivån av Azure-prenumeration kan distributionen misslyckas på grund av otillräcklig kvot för VM-kärnor. Innan du distribuerar, kontrollera din kvot med hjälp av vägledning från den [VM vCPU-kvoter](../../virtual-machines/windows/quotas.md) artikeln. Standardvalet för virtuell dator kräver 6 VM-kärnor. Ändra till en mindre storlek på virtuell dator som *Standard DS1 v2* minskar antalet kärnor till 4.

## <a name="prerequisites"></a>Förutsättningar

Azure Blockchain Workbench kräver Azure AD-konfiguration och programmet registreringar. Du kan välja att göra Azure AD [konfigurationer manuellt](#azure-ad-configuration) innan distribution eller kör ett skript efter distributionen. Om du omdistribuerar Blockchain Workbench kan se [Azure AD-konfiguration](#azure-ad-configuration) för att verifiera din Azure AD-konfigurationen.

> [!IMPORTANT]
> Workbench behöver inte distribueras i samma klient som du använder för att registrera ett Azure AD-program. Workbench måste distribueras i en klient där du har tillräcklig behörighet för att distribuera resurser. Mer information om Azure AD-klienter finns i [så här hämtar du en Active Directory-klient](../../active-directory/develop/quickstart-create-new-tenant.md) och [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).



## <a name="deploy-blockchain-workbench"></a>Distribuera Blockchain Workbench

När nödvändiga steg har slutförts, är du redo att distribuera Blockchain Workbench. I följande avsnitt beskrivs hur du distribuerar ramen.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ditt konto i det övre högra hörnet och växla till önskad Azure AD-klient där du vill distribuera Azure Blockchain Workbench.
3. Välj **Skapa en resurs** i fönstret till vänster. Sök efter `Azure Blockchain Workbench` i den **Sök på Marketplace** sökfältet. 

    ![Marketplace-sökfältet](media/deploy/marketplace-search-bar.png)

4. Välj **Azure Blockchain Workbench**.

    ![Marketplace-sökresultat](media/deploy/marketplace-search-results.png)

5. Välj **Skapa**.
6. Slutför de grundläggande inställningarna.

    ![Skapa Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Resurs-prefix | Kort Unik identifierare för din distribution. Det här värdet används som bas för namngivning av resurser. |
    | VM-användarnamn | Användarnamnet används som administratör för alla virtuella datorer (VM). |
    | Autentiseringstyp | Välj om du vill använda ett lösenord eller nyckel för att ansluta till virtuella datorer. |
    | Lösenord | Lösenordet används för att ansluta till virtuella datorer. |
    | SSH | Använda en offentlig RSA-nyckel i den enradigt format som början med **ssh-rsa** eller Använd flerradigt PEM-format. Du kan generera SSH-nycklar med `ssh-keygen` på Linux och OS X eller genom att använda PuTTYGen i Windows. Mer information om SSH-nycklar finns i [hur du använder SSH-nycklar med Windows på Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Lösenord för databas / Bekräfta lösenord | Ange lösenord ska användas för åtkomst till databasen skapas som en del av distributionen. |
    | Distributionsregionen | Ange var du vill distribuera Blockchain Workbench resurser. För bästa tillgänglighet ska matcha den **plats** inställningen. |
    | Prenumeration | Ange Azure-prenumeration som du vill använda för din distribution. |
    | Resursgrupper | Skapa en ny resursgrupp genom att välja **Skapa nytt** och ange ett unikt Resursgruppsnamn. |
    | Plats | Ange den region som du vill distribuera ramen. |

7. Välj **OK** ska slutföras i inställningen grundläggande konfigurationsavsnittet.

8. I **avancerade inställningar**, Välj om du vill skapa ett nytt blockchain-nätverk eller Använd ett befintligt proof-of-authority blockchain-nätverk.

    För **Skapa nytt**:

    Den *skapa en ny* alternativet skapas en uppsättning Ethereum Proof-of Authority (PoA)-noder i en enda medlem prenumeration. 

    ![Avancerade inställningar för nya blockchain-nätverk](media/deploy/advanced-blockchain-settings-new.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Övervakning | Välj om du vill aktivera Azure Monitor för att övervaka nätverket blockchain |
    | Azure Active Directory-inställningar | Välj **lägga till senare**.</br>Obs! Om du har valt att [förkonfigurera Azure AD](#azure-ad-configuration) eller omdistribuera, välja att *Lägg till nu*. |
    | Val av virtuell dator | Välj önskad storlek för nätverket blockchain. Välj en mindre VM-storlek som *Standard DS1 v2* om du har en prenumeration med låg tjänstbegränsningar som kostnadsfria nivån av Azure. |

    För **Använd befintlig**:

    Den *Använd befintlig* gör det möjligt att ange en Ethereum Proof-of-Authority (PoA) blockchain-nätverk. Slutpunkter har följande krav.

    * Slutpunkten måste vara ett Ethereum Proof-of-Authority (PoA) blockchain-nätverk.
    * Slutpunkten måste vara offentligt tillgänglig över nätverket.
    * PoA blockchain nätverket ska konfigureras så att den har gas pris som har angetts till noll.

    > [!NOTE]
    > Blockchain Workbench konton är inte finansieras. Om pengar krävs inte transaktioner.

    ![Avancerade inställningar för befintliga blockchain-nätverk](media/deploy/advanced-blockchain-settings-existing.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Ethereum RPC-slutpunkt | Ange RPC-slutpunkten för ett befintligt PoA blockchain nätverk. Slutpunkten som börjar med https:// eller http:// och slutar med ett portnummer. Till exempel, `http<s>://<network-url>:<port>` |
    | Azure Active Directory-inställningar | Välj **lägga till senare**.</br>Obs! Om du har valt att [förkonfigurera Azure AD](#azure-ad-configuration) eller omdistribuera, välja att *Lägg till nu*. |
    | Val av virtuell dator | Välj önskad storlek för nätverket blockchain. |

9. Välj **OK** Slutför avancerade inställningar.

10. Granska sammanfattningen för att kontrollera parametrarna är korrekta.

    ![Sammanfattning](media/deploy/blockchain-workbench-summary.png)

11. Välj **skapa** Godkänn villkoren och distribuera Azure Blockchain Workbench.

Distributionen kan ta upp till 90 minuter. Du kan använda Azure-portalen för att övervaka förloppet. I den nyligen skapade resursgruppen, Välj **distributioner > Översikt** att se status för de distribuerade artefakterna.

> [!IMPORTANT]
> Efter distributionen, måste du slutföra inställningarna för Active Directory. Om du har valt **lägga till senare**, måste du köra den [konfigurationsskript för Azure AD](#azure-ad-configuration-script).  Om du har valt **Lägg till nu**, måste du [konfigurera svars-URL](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Webbadress till Blockchain Workbench

När distributionen av Blockchain Workbench är klar innehåller din Blockchain Workbench resurser i en ny resursgrupp. Blockchain Workbench tjänster kan nås via en URL. Följande steg visar hur du hämtar Webb-URL för den distribuerade framework.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det vänstra navigeringsfönstret väljer **resursgrupper**
3. Välj resursgruppens namn du angav när du distribuerar Blockchain Workbench.
4. Välj den **typ** kolumnrubrik för att sortera listan i alfabetisk ordning efter typ.
5. Det finns två resurser av typen **Apptjänst**. Välj resurs av typen **Apptjänst** *utan* den ”-api” suffix.

    ![App service-lista](media/deploy/resource-group-list.png)

6. I App Service **Essentials** avsnittet, kopiera den **URL** värde, som representerar webbtjänstens URL till dina distribuerade Blockchain Workbench.

    ![App service essentials](media/deploy/app-service.png)

Om du vill associera ett anpassat domännamn med Blockchain Workbench, se [konfigurera ett anpassat domännamn för en webbapp i Azure App Service med Traffic Manager](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Skriptexempel för Azure AD-konfiguration

Azure AD måste konfigureras för att slutföra distributionen Blockchain Workbench. Du måste använda ett PowerShell-skript för att göra konfigurationen.

1. I en webbläsare, navigerar du till den [Blockchain Workbench Webbadress](#blockchain-workbench-web-url).
2. Du ser instruktionerna för konfiguration av Azure AD med Cloud Shell. Kopiera kommandot och starta Cloud Shell.

    ![Starta AAD-skript](media/deploy/launch-aad-script.png)

3. Välj den Azure AD-klient där du har distribuerat Blockchain Workbench.
4. Klistra in i Cloud Shell och kör kommandot.
5. När du uppmanas, anger du den Azure AD-klient som du vill använda för Blockchain Workbench. Det här är den klient som innehåller användarna för Blockchain Workbench.

    > [!IMPORTANT]
    > Den autentiserade användaren kräver behörighet att skapa programregistreringar för Azure AD och beviljar delegerade programbehörigheter i klienten. Du kan behöva be en administratör för klienten att köra skript för Azure AD-konfiguration eller skapa en ny klient.

    ![Ange Azure AD-klient](media/deploy/choose-tenant.png)

6. Du uppmanas att autentisera till Azure AD-klient med en webbläsare. Öppna webbtjänstens URL i en webbläsare, ange koden och autentisera.

    ![Autentisera med kod](media/deploy/authenticate.png)

7. Skriptet matar ut flera statusmeddelanden. Du får en **lyckades** statusmeddelande om klienten har tillhandahållits.
8. Gå till URL: en Blockchain Workbench. Du uppmanas att godkänna bevilja läsbehörighet till katalogen. Detta kan Blockchain Workbench webbprogram åtkomst till användare i klienten. Om du är klientadministratören kan välja du att ge samtycke för hela organisationen. Det här alternativet accepterar medgivande för alla användare i klienten. Annars kan varje användare uppmanas att ange medgivande vid första användning av programmet Blockchain Workbench.
9. Välj **acceptera** samtycker till.

     ![Medgivande till att läsa användarprofiler](media/deploy/graph-permission-consent.png)

10. Efter medgivande kan appen Blockchain Workbench användas.

## <a name="azure-ad-configuration"></a>Azure AD-konfiguration

Om du väljer att manuellt konfigurera eller verifiera Azure AD-inställningar innan du distribuerar, kan du utföra alla steg i det här avsnittet. Om du föredrar att automatiskt konfigurera inställningar för Azure AD kan använda [konfigurationsskript för Azure AD](#azure-ad-configuration-script) när du har distribuerat Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Appregistrering för Blockchain Workbench API

Blockchain Workbench distributionen kräver registrering av en Azure AD-program. Du behöver en Azure Active Directory (Azure AD)-klient för att registrera appen. Du kan använda en befintlig klient eller skapa en ny klient. Om du använder en befintlig Azure AD-klient måste tillräcklig behörighet för att registrera program, bevilja Graph API och tillåter åtkomst inom en Azure AD-klient. Om du inte har tillräcklig behörighet i en befintlig Azure AD-klient kan du skapa en ny klient.


1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ditt konto i det övre högra hörnet och växla till den önskade Azure AD-klient. Klienten bör vara den prenumerationsadministratör klient för prenumerationen där Workbench har distribuerats och du har tillräcklig behörighet för att registrera program.
3. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **appregistreringar** > **ny programregistrering**.

    ![Appregistrering](media/deploy/app-registration.png)

4. Ange en **namn** och **inloggnings-URL** för programmet. Du kan använda platshållarvärdena eftersom värdena ändras under distributionen. 

    ![Skapa appregistrering](media/deploy/app-registration-create.png)

    |Inställning  | Värde  |
    |---------|---------|
    |Namn | `Blockchain API` |
    |Programtyp |Webbapp/API|
    |Inloggnings-URL | `https://blockchainapi` |

5. Välj **skapa** att registrera Azure AD-programmet.

### <a name="modify-manifest"></a>Ändra manifest

Därefter måste du ändra manifest för att använda programroller i Azure AD för att ange Blockchain Workbench administratörer.  Läs mer om programmanifesten [Azure Active Directorys programmanifest](../../active-directory/develop/reference-app-manifest.md).

1. Programmet som du har registrerat, Välj **Manifest** i informationsfönstret registrerade programmet.
2. Generera ett GUID. Du kan generera ett GUID med hjälp av PowerShell-kommandot [guid]:: NewGuid () eller cmdlet New-GUID. Ett annat alternativ är att använda en GUID generator-webbplats.
3. Du kommer att uppdatera den **appRoles** avsnittet i manifestet. I fönstret Redigera manifest väljer **redigera** och Ersätt `"appRoles": []` med den angivna JSON. Se till att ersätta värdet för den **id** fältet med det GUID som du skapade. 

    ![Redigera manifest](media/deploy/edit-manifest.png)

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

    > [!IMPORTANT]
    > Värdet **administratör** krävs för att identifiera Blockchain Workbench administratörer.

4. I manifestet, också ändra den **Oauth2AllowImplicitFlow** värde att **SANT**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Välj **spara** spara manifest ändringarna.

### <a name="add-graph-api-required-permissions"></a>Lägg till behörigheter för Graph API krävs

API-programmet måste begära behörighet från användaren att gå till katalogen. Ange följande behörighet för API-program:

1. Välj i appregistreringen Blockchain API **Inställningar > nödvändiga behörigheter > Välj en API > Microsoft Graph**.

    ![Välj ett API](media/deploy/client-app-select-api.png)

    Klicka på **Välj**.

2. I **Aktivera åtkomst** under **delegerade behörigheter**, Välj **läsa grundprofiler för alla användare**.

    ![Aktivera åtkomst](media/deploy/client-app-read-perms.png)

    Välj **spara** därefter **klar**.

3. I **nödvändiga behörigheter**väljer **bevilja behörigheter** därefter **Ja** för verifiering prompten.

   ![Bevilja behörigheter](media/deploy/client-app-grant-permissions.png)

   Bevilja behörighet kan Blockchain Workbench att få åtkomst till användare i katalogen. Skrivskyddad behörighet krävs för att söka efter och lägga till medlemmar i Blockchain Workbench.

### <a name="get-application-id"></a>Hämta program-ID

Program-ID och klient information krävs för distributionen. Samla in och lagra information för användning under distributionen.

1. Programmet som du har registrerat, Välj **inställningar** > **egenskaper**.
2. I den **egenskaper** fönstret, kopiera och store följande värden för senare användning under distributionen.

    ![Egenskaper för API-app](media/deploy/app-properties.png)

    | Inställningen för att lagra  | Använda i distributionen |
    |------------------|-------------------|
    | Program-ID:t | Konfigurationen av Azure Active Directory > program-ID |

### <a name="get-tenant-domain-name"></a>Hämta klient domännamnet

Samla in och lagra domännamnet för Active Directory-klient där programmen registreras. 

I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Egna domännamn**. Kopiera och lagra domännamnet.

![Domännamn](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Gäst-användarinställningar

Om du har gästanvändare i Azure AD-klienten, följer du de ytterligare stegen för att säkerställa Blockchain Workbench Användartilldelning och hantering fungerar korrekt.

1. Växla du Azure AD-klienten och välj **Azure Active Directory > Inställningar > Hantera inställningar för externt samarbete**.
2. Ange **användarbehörigheter för gäster är begränsade** till **nr**.
    ![Inställningar för externt samarbete](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurera svars-URL

När Azure Blockchain Workbench har distribuerats kan du behöva konfigurera Azure Active Directory (Azure AD)-klientprogram **svars-URL** av distribuerade Blockchain Workbench webbadress.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du är i klient där du registrerade Azure AD-klientprogram.
3. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Appregistreringar**.
4. Välj Azure AD-klientprogram som du registrerade i avsnittet förutsättningar.
5. Välj **Inställningar > svars-URL: er**.
6. Ange huvudsakliga Webb-URL för Azure Blockchain Workbench-distribution som du hämtade i den **hämta Webbadress för Azure Blockchain Workbench** avsnittet. Svars-URL har prefixet `https://`. Till exempel, `https://myblockchain2-7v75.azurewebsites.net`

    ![Svars-URL:er](media/deploy/configure-reply-url.png)

7. Välj **spara** att uppdatera klientregistrering.

## <a name="remove-a-deployment"></a>Ta bort en distribution

När en distribution inte längre behövs kan du ta bort en distribution genom att ta bort resursgruppen Blockchain Workbench.

1. I Azure-portalen går du till **resursgrupp** i det vänstra navigeringsfönstret och välj den resursgrupp som du vill ta bort. 
2. Välj **Ta bort resursgrupp**. Kontrollera borttagningen genom att ange resursgruppens namn och välj **ta bort**.

    ![Ta bort resursgrupp](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln med Azure Blockchain Workbench. Fortsätt till nästa artikel om anvisningar om du vill veta hur du skapar en blockchain-program.

> [!div class="nextstepaction"]
> [Skapa ett blockchain-program i Azure Blockchain Workbench](create-app.md)
