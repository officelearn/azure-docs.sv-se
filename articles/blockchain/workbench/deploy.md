---
title: Förhandsversion av Azure Blockchain Workbench
description: Distribuera Azure Blockchain Workbench Preview
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 141bb8825e47eb2309f9f551990a2976e8f4e209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943205"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Förhandsversion av Azure Blockchain Workbench

Azure Blockchain Workbench Preview distribueras med hjälp av en lösningsmall på Azure Marketplace. Mallen förenklar distributionen av komponenter som behövs för att skapa blockchain-program. När Blockchain Workbench har distribuerats får du tillgång till klientappar för att skapa och hantera användare och blockkedjeprogram.

Mer information om komponenterna i Blockchain Workbench finns i [Azure Blockchain Workbench-arkitekturen](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Med Blockchain Workbench kan du distribuera en blockchain-huvudbok tillsammans med en uppsättning relevanta Azure-tjänster som oftast används för att skapa ett blockchain-baserat program. Distribution av Blockchain Workbench resulterar i att följande Azure-tjänster etableras inom en resursgrupp i din Azure-prenumeration.

* App serviceplan (standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL-databas (Standard S0) + SQL Logisk server
* Azure Storage-konto (Standard LRS)
* Skalan för virtuell dator med kapacitet 1
* Resursgrupp för virtuellt nätverk (med belastningsutjämnare, nätverkssäkerhetsgrupp, offentlig IP-adress, virtuellt nätverk)
* Azure Blockchain-tjänst. Om du använder en tidigare Blockchain Workbench-distribution kan du överväga att distribuera om Azure Blockchain Workbench så att de använder Azure Blockchain Service.

Följande är en exempeldistribution som skapats i **minblockchain** resursgrupp.

![Exempel på distribution](media/deploy/example-deployment.png)

Kostnaden för Blockchain Workbench är en aggregat av kostnaden för de underliggande Azure-tjänsterna. Prisinformation för Azure-tjänster kan beräknas med hjälp av [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Krav

Azure Blockchain Workbench kräver Azure AD-konfiguration och programregistreringar. Du kan välja att göra Azure [AD-konfigurationerna manuellt](#azure-ad-configuration) innan du distribuerar eller kör en skriptinläggsdistribution. Om du distribuerar om Blockchain Workbench läser du [Azure AD-konfiguration](#azure-ad-configuration) för att verifiera din Azure AD-konfiguration.

> [!IMPORTANT]
> Workbench behöver inte distribueras i samma klient som den du använder för att registrera ett Azure AD-program. Workbench måste distribueras i en klient där du har tillräcklig behörighet för att distribuera resurser. Mer information om Azure AD-klienter finns i [Så här hämtar du en Active Directory-klientorganisation](../../active-directory/develop/quickstart-create-new-tenant.md) och [integrerar program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Distribuera Blockchain Workbench

När de nödvändiga stegen har slutförts är du redo att distribuera Blockchain Workbench. I följande avsnitt beskrivs hur du distribuerar ramverket.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ditt konto i det övre högra hörnet och växla till önskad Azure AD-klient där du vill distribuera Azure Blockchain Workbench.
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **Blockchain** > **Azure Blockchain Workbench (förhandsversion)**.

    ![Skapa Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Resursprefix | Kort unik identifierare för distributionen. Det här värdet används som bas för att namnge resurser. |
    | Vm-användarnamn | Användarnamnet används som administratör för alla virtuella datorer (VM). |
    | Autentiseringstyp | Välj om du vill använda ett lösenord eller en nyckel för att ansluta till virtuella datorer. |
    | lösenord | Lösenordet används för att ansluta till virtuella datorer. |
    | SSH | Använd en offentlig RSA-nyckel i ett enda radformat som börjar med **ssh-rsa** eller använd PEM-formatet med flera rader. Du kan generera SSH-nycklar med linux `ssh-keygen` och OS X, eller genom att använda PuTTYGen på Windows. Mer information om SSH-nycklar finns i [Så här använder du SSH-nycklar med Windows på Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Lösenord för databas och blockchain | Ange det lösenord som ska användas för åtkomst till databasen som skapats som en del av distributionen. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemener, 1 versaltecken, 1 tal och 1 specialtecken som inte är\`taltecken(#), procent(%), komma(,), stjärntecken(*), dubbel citattecken("), enkel offert('), dash(-) och semikolon(;) |
    | Distributionsregion | Ange var blockchain Workbench-resurser ska distribueras. För bästa tillgänglighet bör detta matcha inställningen **Plats.** |
    | Prenumeration | Ange den Azure-prenumeration som du vill använda för distributionen. |
    | Resursgrupper | Skapa en ny resursgrupp genom att välja **Skapa nytt** och ange ett unikt resursgruppnamn. |
    | Location | Ange den region som du vill distribuera ramverket. |

1. Välj **OK** för att slutföra konfigurationsavsnittet för grundläggande inställning.

1. I **Avancerade inställningar**väljer du om du vill skapa ett nytt blockchain-nätverk eller använda ett befintligt informationsnätverk för att få ordning på myndigheter.

    För **att skapa nya:**

    Alternativet *Skapa nytt* distribuerar en Azure Blockchain Service Quorum-redovisning med standardprincipen för grundläggande sku.

    ![Avancerade inställningar för nytt blockchain-nätverk](media/deploy/advanced-blockchain-settings-new.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Prisnivå för Azure Blockchain-tjänst | Välj **Grundläggande** eller **Standard** Azure Blockchain Service-nivå som används för Blockchain Workbench |
    | Azure Active Directory-inställningar | Välj **Lägg till senare**.</br>Om du väljer att [förkonfigurera Azure AD](#azure-ad-configuration) eller distribuerar om väljer du att *lägga till nu*. |
    | Val av virtuell dator | Välj önskad lagringsprestanda och VM-storlek för ditt blockchain-nätverk. Välj en mindre VM-storlek, till exempel *Standard DS1 v2* om du har en prenumeration med låga tjänstgränser som Azure-kostnadsfri nivå. |

    För **användning av befintliga:**

    Med *alternativet använd befintlig* kan du ange ett PoA-blockchain-nätverk (Ethereum Proof-of-Authority). Slutpunkter har följande krav.

   * Slutpunkten måste vara ett PoA-blockkedjenätverk (Ethereum Proof-of-Authority).
   * Slutpunkten måste vara allmänt tillgänglig via nätverket.
   * PoA blockchain-nätverket bör konfigureras så att gaspriset är noll.

     > [!NOTE]
     > Blockchain Workbench-konton finansieras inte. Om medel krävs misslyckas transaktionerna.

     ![Avancerade inställningar för befintligt blockchain-nätverk](media/deploy/advanced-blockchain-settings-existing.png)

     | Inställning | Beskrivning  |
     |---------|--------------|
     | Slutpunkt för Ethereum RPC | Ange RPC-slutpunkten för ett befintligt PoA-blockchain-nätverk. Slutpunkten börjar med https:// eller http:// och slutar med ett portnummer. Till exempel, `http<s>://<network-url>:<port>` |
     | Azure Active Directory-inställningar | Välj **Lägg till senare**.</br>Om du väljer att [förkonfigurera Azure AD](#azure-ad-configuration) eller distribuerar om väljer du att *lägga till nu*. |
     | Val av virtuell dator | Välj önskad lagringsprestanda och VM-storlek för ditt blockchain-nätverk. Välj en mindre VM-storlek, till exempel *Standard DS1 v2* om du har en prenumeration med låga tjänstgränser som Azure-kostnadsfri nivå. |

1. Välj **OK** för att slutföra avancerade inställningar.

1. Granska sammanfattningen för att kontrollera att parametrarna är korrekta.

    ![Sammanfattning](media/deploy/blockchain-workbench-summary.png)

1. Välj **Skapa** om du vill godkänna villkoren och distribuera din Azure Blockchain Workbench.

Distributionen kan ta upp till 90 minuter. Du kan använda Azure-portalen för att övervaka förloppet. I den nyligen skapade resursgruppen väljer du **Distributioner > Översikt för** att se status för de distribuerade artefakterna.

> [!IMPORTANT]
> Efter distributionen måste du slutföra Active Directory-inställningarna. Om du väljer **Lägg till senare**måste du köra Azure [AD-konfigurationsskriptet](#azure-ad-configuration-script).  Om du väljer **Lägg till nu**måste du konfigurera [svars-URL:en](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Url till blockchain Workbench-webb

När distributionen av Blockchain Workbench har slutförts innehåller en ny resursgrupp dina Blockchain Workbench-resurser. Blockchain Workbench-tjänster nås via en webbadress. Följande steg visar hur du hämtar webb-URL:en för det distribuerade ramverket.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**i det vänstra navigeringsfönstret .
1. Välj det resursgruppsnamn som du angav när du distribuerade Blockchain Workbench.
1. Markera kolumnrubriken **TYP** om du vill sortera listan i alfabetisk ordning efter typ.
1. Det finns två resurser med typen **App Service**. Välj resurs av typen **App Service** *utan* suffixet "-api".

    ![Lista över apptjänster](media/deploy/resource-group-list.png)

1. Kopiera **URL-värdet** i **apptjänstöversikten,** som representerar webb-URL:en till din distribuerade Blockchain Workbench.

    ![Viktigt med apptjänsten](media/deploy/app-service.png)

Information om hur du associerar ett anpassat domännamn med Blockchain Workbench finns i [konfigurera ett anpassat domännamn för en webbapp i Azure App Service med Traffic Manager](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Konfigurationsskript för Azure AD

Azure AD måste konfigureras för att slutföra distributionen av Blockchain Workbench. Du ska använda ett PowerShell-skript för att utföra konfigurationen.

1. I en webbläsare navigerar du till [webbplatsen Blockchain Workbench.](#blockchain-workbench-web-url)
1. Instruktioner för hur du konfigurerar Azure AD med Cloud Shell visas. Kopiera kommandot och starta Cloud Shell.

    ![Starta AAD-skript](media/deploy/launch-aad-script.png)

1. Välj Azure AD-klienten där du distribuerade Blockchain Workbench.
1. Klistra in och kör kommandot i PowerShell-miljön i Cloud Shell PowerShell.Ink.
1. Ange Azure AD-klienten som du vill använda för Blockchain Workbench när du uppmanas att göra det. Detta kommer att vara den klient som innehåller användarna för Blockchain Workbench.

    > [!IMPORTANT]
    > Den autentiserade användaren kräver behörigheter för att skapa Azure AD-programregistreringar och bevilja delegerade programbehörigheter i klienten. Du kan behöva be en administratör för klienten att köra Azure AD-konfigurationsskriptet eller skapa en ny klient.

    ![Ange Azure AD-klient](media/deploy/choose-tenant.png)

1. Du uppmanas att autentisera till Azure AD-klienten med hjälp av en webbläsare. Öppna webbadressen i en webbläsare, ange koden och autentisera.

    ![Autentisera med kod](media/deploy/authenticate.png)

1. Skriptet matar ut flera statusmeddelanden. Du får ett **STATUSMEDDELANDE FÖR LYCKA** OM klienten har etablerats.
1. Navigera till url:en för Blockchain Workbench. Du uppmanas att godkänna att bevilja läsbehörighet till katalogen. Detta gör att Blockchain Workbench webbapp tillgång till användare i klienten. Om du är klientadministratör kan du välja att godkänna för hela organisationen. Det här alternativet accepterar medgivande för alla användare i klienten. Annars uppmanas varje användare att godkännas vid första användningen av blockchain Workbenchs webbprogram.
1. Välj **Acceptera** om du vill medgivande.

     ![Samtycke till att läsa användarprofiler](media/deploy/graph-permission-consent.png)

1. Efter medgivande kan blockchain Workbench-webbappen användas.

Du har slutfört distributionen av Azure Blockchain Workbench. Se [Nästa steg](#next-steps) för förslag för att komma igång med distributionen.

## <a name="azure-ad-configuration"></a>Azure AD-konfiguration

Om du väljer att konfigurera eller verifiera Azure AD-inställningar manuellt före distributionen slutför du alla steg i det här avsnittet. Om du föredrar att automatiskt konfigurera Azure AD-inställningar använder du [Azure AD-konfigurationsskriptet](#azure-ad-configuration-script) när du har distribuerat Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registrering av Blockchain Workbench API-appen

Blockchain Workbench-distribution kräver registrering av ett Azure AD-program. Du behöver en Azure Active Directory-klient (Azure AD) för att registrera appen. Du kan använda en befintlig klient eller skapa en ny klient. Om du använder en befintlig Azure AD-klient behöver du tillräcklig behörighet för att registrera program, bevilja Graph API-behörigheter och tillåta gäståtkomst i en Azure AD-klientorganisation. Om du inte har tillräcklig behörighet i en befintlig Azure AD-klient skapar du en ny klientorganisation.


1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ditt konto i det övre högra hörnet och växla till önskad Azure AD-klientorganisation. Klienten ska vara prenumerationsadministratörens klientorganisation för prenumerationen där Azure Blockchain Workbench distribueras och du har tillräcklig behörighet för att registrera program.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Appregistreringar** > **Ny registrering**.

    ![Appregistrering](media/deploy/app-registration.png)

1. Ange ett **visningsnamn** och välj **Konton i den här organisationskatalogen**.

    ![Skapa en appregistrering](media/deploy/app-registration-create.png)

1. Välj **Registrera för** att registrera Azure AD-programmet.

### <a name="modify-manifest"></a>Ändra manifest

Därefter måste du ändra manifestet för att använda programroller i Azure AD för att ange Blockchain Workbench-administratörer.  Mer information om programmanifest finns i [Azure Active Directory-programmanifest](../../active-directory/develop/reference-app-manifest.md).


1. Ett GUID krävs för manifestet. Du kan generera ett GUID `[guid]::NewGuid()` med `New-GUID` kommandot PowerShell eller cmdlet. Ett annat alternativ är att använda en GUID generator webbplats.
1. För det program du registrerade väljer du **Manifest** i avsnittet **Hantera.**
1. Uppdatera sedan **avsnittet appRoles** i manifestet. Ersätt `"appRoles": []` med den medföljande JSON. Var noga med att ersätta värdet för **id-fältet** med det GUID som du har genererat. 

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
    > **Värdeadministratören** behövs för att identifiera Blockchain Workbench-administratörer.

1. I manifestet ändrar du också värdet **Oauth2AllowImplicitFlow** till **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Välj **Spara** om du vill spara manifeständringarna.

### <a name="add-graph-api-required-permissions"></a>Lägg till behörigheter för graph-API

API-programmet måste begära behörighet från användaren för att komma åt katalogen. Ange följande behörighet som krävs för API-programmet:

1. I *registreringen av Blockchain API-appen* väljer du **API-behörigheter**. Som standard läggs behörigheten Graph API **User.Read** till.
1. Workbench-programmet kräver läsåtkomst till användarnas grundläggande profilinformation. I *Konfigurerade behörigheter*väljer du **Lägg till en behörighet**. I **Microsoft API:er**väljer du **Microsoft Graph**.
1. Eftersom workbench-programmet använder de autentiserade användarautentiseringsuppgifterna väljer du **Delegerad behörighet**.
1. Välj **User.ReadBasic.All-behörighet** i kategorin *Användare.*

    ![Azure AD-appregistreringskonfiguration som visar att du lägger till microsoft graph user.readBasic.All delegerad behörighet](media/deploy/add-graph-user-permission.png)

    Välj **Lägg till behörigheter**.

1. I *Konfigurerade behörigheter*väljer du **Bevilja administratörsmedgivande** för domänen och välj sedan **Ja** för verifieringsprompten.

   ![Bevilja behörigheter](media/deploy/client-app-grant-permissions.png)

   Genom att bevilja behörighet kan Blockchain Workbench komma åt användare i katalogen. Läsbehörigheten krävs för att söka och lägga till medlemmar i Blockchain Workbench.

### <a name="get-application-id"></a>Hämta program-ID

Program-ID- och klientinformation krävs för distribution. Samla in och lagra informationen för användning under distributionen.

1. För det program du registrerade väljer du **Översikt**.
1. Kopiera och lagra **program-ID-värdet** för senare användning under distributionen.

    ![Egenskaper för API-appar](media/deploy/app-properties.png)

    | Ange att lagra  | Användning i distributionen |
    |------------------|-------------------|
    | Program-ID (klient) | Azure Active Directory-> program-ID |

### <a name="get-tenant-domain-name"></a>Hämta klientdomännamn

Samla in och lagra Active Directory-klientnamnet där programmen är registrerade. 

I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Egna domännamn**. Kopiera och lagra domännamnet.

![Domännamn](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Inställningar för gästanvändare

Om du har gästanvändare i din Azure AD-klient följer du de ytterligare stegen för att säkerställa att blockchain Workbench-användartilldelning och hantering fungerar korrekt.

1. Växla din Azure AD-klient och välj **Azure Active Directory > Användarinställningar > Hantera inställningar för externt samarbete**.
1. Ange **gästanvändarbehörigheter är begränsade** till **Nr**.
    ![Inställningar för externt samarbete](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurera svars-URL:en

När Azure Blockchain Workbench har distribuerats måste du konfigurera Azure Active Directory (Azure AD) klientprogram **Svara URL för** distribuerade Blockchain Workbench webb-URL.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Verifiera att du finns i klienten där du registrerade Azure AD-klientprogrammet.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Appregistreringar**.
1. Välj azure AD-klientprogrammet som du registrerade i avsnittet förutsättning.
1. Välj **Autentisering**.
1. Ange huvudwebb-URL:en för Azure Blockchain Workbench-distributionen som du hämtade i avsnittet [Blockchain Workbench-webb-URL:en.](#blockchain-workbench-web-url) Svars-URL:en föregås av `https://`. Till exempel, `https://myblockchain2-7v75.azurewebsites.net`

    ![Url:er för autentiseringssvar](media/deploy/configure-reply-url.png)

1. I avsnittet **Avancerad inställning** kontrollerar du **Access-token** och **ID-token**.

    ![Avancerade inställningar för autentisering](media/deploy/authentication-advanced-settings.png)

1. Välj **Spara** om du vill uppdatera klientregistreringen.

## <a name="remove-a-deployment"></a>Ta bort en distribution

När en distribution inte längre behövs kan du ta bort en distribution genom att ta bort resursgruppen Blockchain Workbench.

1. I Azure-portalen navigerar du till **resursgruppen** i det vänstra navigeringsfönstret och väljer den resursgrupp som du vill ta bort. 
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resursgruppsnamnet och välj **Ta bort**.

    ![Ta bort resursgrupp](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln distribuerade du Azure Blockchain Workbench. Om du vill veta hur du skapar ett blockchain-program fortsätter du till nästa how-to-artikel.

> [!div class="nextstepaction"]
> [Skapa ett blockkedjeprogram i Azure Blockchain Workbench](create-app.md)
