---
title: Distribuera Azure Blockchain Workbench
description: Så här distribuerar du Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 3ee169e4139f5fc9cd4208c53c4997d50521fed7
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48243496"
---
# <a name="deploy-azure-blockchain-workbench"></a>Distribuera Azure Blockchain Workbench

Azure Blockchain Workbench med hjälp av en mall i Azure Marketplace. Mallen gör det enklare för distribueringen av komponenter som behövs för att skapa blockchain-program. När distribuerats ger Blockchain Workbench åtkomst till klientprogram för att skapa och hantera användare och blockchain-program.

Läs mer om komponenterna i Blockchain Workbench, [Azure Blockchain Workbench arkitektur](architecture.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Blockchain Workbench kan du distribuera en blockchain-redovisning tillsammans med en uppsättning relevanta Azure-tjänster som oftast används för att skapa en blockchain-baserade program. Distribuera Blockchain Workbench resulterar i följande Azure-tjänster håller på att etableras i en resursgrupp i Azure-prenumerationen.

* 1 event Grid-ämne
* 1 Service Bus Namespace
* 1 application Insights
* 1 SQL-databas (Standard S0)
* 2 app Services (Standard)
* 2 azure-Nyckelvalv
* 2 azure Storage-konton (Standard LRS)
* 2 VM-skalningsuppsättningar (för systemhälsoverifierarens- och arbetsroller noder)
* 2 virtuella nätverk (inklusive belastningsutjämnaren nätverkssäkerhetsgrupp och offentlig IP-adress för varje virtuellt nätverk)
* Valfritt: Azure Monitor

Följande är ett exempel på distribution skapats i **myblockchain** resursgrupp.

![Exempel på distribution](media/deploy/example-deployment.png)

Kostnaden för Blockchain Workbench är en aggregering av kostnaden för de underliggande Azure-tjänsterna. Information om priser för Azure-tjänster kan beräknas med hjälp av den [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/).

Azure Blockchain Workbench kräver flera förutsättningar före distributionen. Kraven är Azure AD-konfiguration och programmet registreringar.

### <a name="blockchain-workbench-api-app-registration"></a>Appregistrering för Blockchain Workbench API

Blockchain Workbench distributionen kräver registrering av en Azure AD-program. Du behöver en Azure Active Directory (Azure AD)-klient för att registrera appen. Du kan använda en befintlig klient eller skapa en ny klient. Om du använder en befintlig Azure AD-klient måste tillräcklig behörighet för att registrera program och bevilja Graph API i en Azure AD-klient. Om du inte har tillräcklig behörighet i en befintlig Azure AD-klient kan du skapa en ny klient. 

> [!IMPORTANT]
> Workbench behöver inte distribueras i samma klient som du använder för att registrera ett Azure AD-program. Workbench måste distribueras i en klient där du har tillräcklig behörighet för att distribuera resurser. Mer information om Azure AD-klienter finns i [så här hämtar du en Active Directory-klient](../../active-directory/develop/quickstart-create-new-tenant.md) och [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ditt konto i övre högra hörnet och växla till den önskade Azure AD-klient. Klienten bör vara den prenumerationsadministratör klient för prenumerationen där Workbench har distribuerats och du har tillräcklig behörighet för att registrera program.
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

### <a name="modify-application-manifest"></a>Ändra programmanifest

Därefter måste du ändra applikationsmanifestet om du vill använda programroller i Azure AD för att ange Blockchain Workbench administratörer.  Läs mer om programmanifesten [Azure Active Directorys programmanifest](../../active-directory/develop/reference-app-manifest.md).

1. Programmet som du har registrerat, Välj **Manifest** i informationsfönstret registrerade programmet.
2. Generera ett GUID. Du kan generera ett GUID med hjälp av PowerShell-kommandot [guid]:: NewGuid () eller cmdlet New-GUID. Ett annat alternativ är att använda en GUID generator-webbplats.
3. Du kommer att uppdatera den **appRoles** avsnittet i manifestet. I fönstret Redigera manifest väljer **redigera** och Ersätt `"appRoles": []` med den angivna JSON. Se till att ersätta värdet för den **id** fältet med det GUID som du skapade. 

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

    ![Redigera manifest](media/deploy/edit-manifest.png)

    > [!IMPORTANT]
    > Värdet **administratör** krävs för att identifiera Blockchain Workbench administratörer.

4.  Klicka på **spara** att spara manifestet programändringar.

### <a name="add-graph-api-required-permissions"></a>Lägg till behörigheter för Graph API krävs

API-programmet måste begära behörighet från användaren att gå till katalogen. Ange följande behörighet för API-program:

1. Välj i appregistreringen Blockchain API **Inställningar > nödvändiga behörigheter > Välj en API > Microsoft Graph**.

    ![Välj ett API](media/deploy/client-app-select-api.png)

    Klicka på **Välj**.

2. I **Aktivera åtkomst** under **programbehörigheter**, Välj **läsa fullständiga profiler för alla användare**.

    ![Aktivera åtkomst](media/deploy/client-app-read-perms.png)

    Klicka på **Välj** klickar **klar**.

3. I **nödvändiga behörigheter**väljer **bevilja behörigheter** därefter **Ja** för verifiering prompten.

   ![Bevilja behörigheter](media/deploy/client-app-grant-permissions.png)

   Bevilja behörighet kan Blockchain Workbench att få åtkomst till användare i katalogen. Skrivskyddad behörighet krävs för att söka efter och lägga till medlemmar i Blockchain Workbench.

### <a name="add-graph-api-key-to-application"></a>Lägg till Graph API-nyckel till program

Blockchain Workbench använder Azure AD som hanteringssystemet huvudsakliga identitet för användare som interagerar med blockchain-program. Du måste lägga till en åtkomstnyckel för Blockchain Workbench åtkomst till Azure AD och hämta användarinformation, till exempel namn och e-postmeddelanden. Blockchain Workbench använder för att autentisera med Azure AD.

1. Programmet som du har registrerat, Välj **inställningar** i informationsfönstret registrerade programmet.
2. Välj **Nycklar**.
3. Lägg till en ny nyckel genom att ange en nyckel **beskrivning** och välja **upphör att gälla** duration-värde. 

    ![Skapa en nyckel](media/deploy/app-key-create.png)

    |Inställning  | Värde  |
    |---------|---------|
    | Beskrivning | `Service` |
    | Upphör att gälla | Välj en giltighetstid |

4. Välj **Spara**. 
5. Kopiera värdet för nyckeln och lagra den för senare. Du behöver den för distribution.

    > [!IMPORTANT]
    >  Om du inte har sparat nyckeln för distributionen, kommer du behöva skapa en ny nyckel. Du kan inte hämta nyckelvärdet från portalen senare.

### <a name="get-application-id"></a>Hämta program-ID

Program-ID och klient information krävs för distributionen. Samla in och lagra information för användning under distributionen.

1. Programmet som du har registrerat, Välj **inställningar** > **egenskaper**.
2.  I den **egenskaper** fönstret, kopiera och store följande värden för senare användning under distributionen.

    ![Egenskaper för API-app](media/deploy/app-properties.png)

    | Inställningen för att lagra  | Använda i distributionen |
    |------------------|-------------------|
    | Program-ID:t | Konfigurationen av Azure Active Directory > program-ID |

### <a name="get-tenant-domain-name"></a>Hämta klient domännamnet

Samla in och lagra domännamnet för Active Directory-klient där programmen registreras. 

I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Egna domännamn**. Kopiera och lagra domännamnet.

![Domännamn](media/deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Distribuera Blockchain Workbench

När nödvändiga steg har slutförts, är du redo att distribuera Blockchain Workbench. I följande avsnitt beskrivs hur du distribuerar ramen.

1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  Välj ditt konto i övre högra hörnet och växla till den önskade Azure AD-klient där du vill distribuera Azure Blockchain Workbench.
3.  I den vänstra rutan väljer **skapa en resurs**. Sök efter `Azure Blockchain Workbench` i den **Sök på Marketplace** sökfältet. 

    ![Marketplace-sökfältet](media/deploy/marketplace-search-bar.png)

4.  Välj **Azure Blockchain Workbench**.

    ![Marketplace-sökresultat](media/deploy/marketplace-search-results.png)

4.  Välj **Skapa**.
5.  Slutför de grundläggande inställningarna.

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

6.  Välj **OK** ska slutföras i inställningen grundläggande konfigurationsavsnittet.

7.  Slutför den **Azure Active Directory-installationen**.

    ![Konfigurationen av Azure AD](media/deploy/blockchain-workbench-settings-aad.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Domännamn | Använd Azure AD-klient som samlas in i den [Get klientdomännamn](#get-tenant-domain-name) nödvändiga avsnittet. |
    | Program-ID:t | Använd program-ID från app klientregistrering Blockchain som samlas in i den [hämta program-ID](#get-application-id) nödvändiga avsnittet. |
    | Programnyckel | Använda programnyckeln från Blockchain app klientregistrering som samlas in i den [lägga till Graph API-nyckel till programmet](#add-graph-api-key-to-application) nödvändiga avsnittet. |


8.  Klicka på **OK** att slutföra avsnittet för konfiguration av Azure AD-parametrar.

9.  I **nätverksinställningar och prestanda**, Välj om du vill skapa ett nytt blockchain-nätverk eller Använd ett befintligt proof-of-authority blockchain-nätverk.

    För **Skapa nytt**:

    Den *skapa en ny* alternativet skapas en uppsättning Ethereum Proof-of Authority (PoA)-noder i en enda medlem prenumeration. 

    ![Nätverksinställningar och prestanda](media/deploy/blockchain-workbench-settings-network-new.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Antalet blockchain-noder | Välj antalet Ethereum PoA verifieraren noder som ska distribueras i nätverket. |
    | Lagringsprestanda | Välj önskad VM-lagringsprestanda för nätverket blockchain. |
    | Virtuell datorstorlek | Välj önskad storlek för nätverket blockchain. |

    För **Använd befintlig**:

    Den *Använd befintlig* gör det möjligt att ange en Ethereum Proof-of-Authority (PoA) blockchain-nätverk. Slutpunkter har följande krav.

    * Slutpunkten måste vara ett Ethereum Proof-of-Authority (PoA) blockchain-nätverk.
    * Slutpunkten måste vara offentligt tillgänglig över nätverket.
    * PoA blockchain nätverket ska konfigureras för att ha gas priset noll (Obs: Blockchain Workbench konton är inte finansieras. Om pengar måste, misslyckas transaktioner).

    ![Nätverksinställningar och prestanda](media/deploy/blockchain-workbench-settings-network-existing.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Ethereum RPC-slutpunkt | Ange RPC-slutpunkten för ett befintligt PoA blockchain nätverk. Slutpunkten som börjar med http:// och slutar med ett portnummer. Till exempel, `http://contoso-chain.onmicrosoft.com:8545` |
    | Lagringsprestanda | Välj önskad VM-lagringsprestanda för nätverket blockchain. |
    | Virtuell datorstorlek | Välj önskad storlek för nätverket blockchain. |

10. Välj **OK** Slutför nätverksinställningar och prestanda.

11. Slutför den **Azure Monitor** inställningar.

    ![Azure Monitor](media/deploy/blockchain-workbench-settings-oms.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Övervakning | Välj om du vill aktivera Azure Monitor för att övervaka nätverket blockchain |
    | Anslut till befintliga Log Analytics-instans | Välj om du vill använda en befintlig Log Analytics-instans eller skapa en ny. Om du använder en befintlig instans anger du ditt arbetsyte-ID och den primära nyckeln. |

12. Klicka på **OK** att slutföra avsnittet Azure Monitor.

13. Granska sammanfattningen för att kontrollera parametrarna är korrekta.

    ![Sammanfattning](media/deploy/blockchain-workbench-summary.png)

14. Välj **skapa** Godkänn villkoren och distribuera Azure Blockchain Workbench.

Distributionen kan ta upp till 90 minuter. Du kan använda Azure-portalen för att övervaka förloppet. I den nyligen skapade resursgruppen, Välj **distributioner > Översikt** att se status för de distribuerade artefakterna.

## <a name="blockchain-workbench-web-url"></a>Webbadress till Blockchain Workbench

När distributionen av Blockchain Workbench är klar innehåller din Blockchain Workbench resurser i en ny resursgrupp. Blockchain Workbench tjänster kan nås via en URL. Följande steg visar hur du hämtar Webb-URL för den distribuerade framework.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det vänstra navigeringsfönstret väljer **resursgrupper**
3. Välj resursgruppens namn du angav när du distribuerar Blockchain Workbench.
4. Klicka på den **typ** kolumnrubrik för att sortera listan i alfabetisk ordning efter typ.
5. Det finns två resurser av typen **Apptjänst**. Välj resurs av typen **Apptjänst** *utan* den ”-api” suffix.

    ![App service-lista](media/deploy/resource-group-list.png)

6.  I App Service **Essentials** avsnittet, kopiera den **URL** värde, som representerar webbtjänstens URL till dina distribuerade Blockchain Workbench.

    ![App service essentials](media/deploy/app-service.png)

Om du vill associera ett anpassat domännamn med Blockchain Workbench, se [konfigurera ett anpassat domännamn för en webbapp i Azure App Service med Traffic Manager](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Konfigurera svars-URL

När Azure Blockchain Workbench har distribuerats, nästa steg är att kontrollera att klientprogrammet Azure Active Directory (AD Azure) registreras till rätt **svars-URL** av distribuerade Blockchain Workbench webbadress.

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
