---
title: Installera Azure FarmBeats
description: I den här artikeln beskrivs hur du installerar Azure FarmBeats i din Azure-prenumeration
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479567"
---
# <a name="install-azure-farmbeats"></a>Installera Azure FarmBeats

I den här artikeln beskrivs hur du installerar Azure FarmBeats i din Azure-prenumeration.

Azure FarmBeats är ett business-to-business-erbjudande som är tillgängligt på Azure Marketplace. Det möjliggör aggregering av jordbruksdatauppsättningar mellan leverantörer och generering av användbara insikter. Azure FarmBeats gör detta genom att du kan skapa modeller för artificiell intelligens (AI) eller maskininlärning (ML) baserat på sammansmälta datauppsättningar. De två huvudkomponenterna i Azure FarmBeats är:

- **Datahub**: Ett API-lager som möjliggör aggregering, normalisering och kontextualisering av olika jordbruksdatauppsättningar mellan olika leverantörer.

- **Accelerator:** Webbprogram som är byggt ovanpå Datahub. Det rivstartar din modellutveckling och visualisering. Acceleratorn använder Azure FarmBeats API:er för att demonstrera visualisering av intjänade sensordata som diagram och visualisering av modellutdata som kartor.

## <a name="general-information"></a>Allmän information

### <a name="components-installed"></a>Installerade komponenter

När du installerar Azure FarmBeats etableras följande resurser i din Azure-prenumeration:

| Installerade Azure-resurser  | Azure FarmBeats-komponent  |
|---------|---------|
| Application Insights   |      Accelerator för Datahub-&      |
| App Service     |     Accelerator för Datahub-&     |
| App Service-plan   | Accelerator för Datahub-&  |
| API-anslutning    |  Datahub (datahub)       |
| Azure Cache for Redis       | Datahub (datahub)      |
| Azure Cosmos DB   |  Datahub (datahub)       |
| Azure Data Factory V2       |     Accelerator för Datahub-&      |
| Azure Batch-konto    | Datahub (datahub)   |
| Azure Key Vault |  Accelerator för Datahub-&        |
| Azure Maps-konto       |     Accelerator    |
| Namnområde för händelsehubben    |     Datahub (datahub)      |
| Logikapp      |  Datahub (datahub)       |
| Lagringskonto      |     Accelerator för Datahub-&      |
| Time Series Insights     |    Datahub (datahub)    |

### <a name="costs-incurred"></a>Kostnader

Kostnaden för Azure FarmBeats är en aggregat av kostnaden för de underliggande Azure-tjänsterna. Prisinformation för Azure-tjänster kan beräknas med hjälp av [priskalkylatorn](https://azure.microsoft.com/pricing/calculator). Den faktiska kostnaden för den totala installationen varierar beroende på användningen. Kostnaden för steady state för de två komponenterna är:

- Datahub - mindre än $10 per dag
- Accelerator - mindre än $2 per dag

### <a name="regions-supported"></a>Regioner som stöds

För närvarande stöds Azure FarmBeats i offentliga molnmiljöer i följande regioner:

- Australien, östra
- USA, centrala
- USA, östra
- USA, östra 2
- USA, västra
- USA, västra 2
- Europa, norra
- Europa, västra
- Asien, östra
- Sydostasien

### <a name="time-taken"></a>Tid det tar

Hela installationen av Azure FarmBeats, inklusive förberedelse och installation tar mindre än en timme.

## <a name="prerequisites"></a>Krav

Du måste slutföra följande steg innan du påbörjar den faktiska installationen av Azure FarmBeats:

### <a name="verify-permissions"></a>Verifiera behörigheter

Du behöver följande behörigheter i Azure-klienten för att installera Azure FarmBeats:

- Hyresgäst - Skaparskapare för AAD-appen
- Prenumeration - Ägare
- Resursgrupp där FarmBeats installeras - Ägare

De två första behörigheterna behövs för [att skapa AAD-programsteget.](#create-an-aad-application) Om det behövs kan du få någon med rätt behörighet att skapa AAD-programmet.

Den person som kör FarmBeats installera från marketplace måste vara en ägare av resursgruppen där FarmBeats installeras. För prenumerationsägare sker detta automatiskt när Resursgrupp skapas. För andra kan du skapa resursgruppen i föraningen och be prenumerationsägaren att göra dig till ägare av resursgruppen.

Du kan verifiera dina åtkomstbehörigheter i Azure-portalen genom att följa instruktionerna om [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Bestäm prenumeration och region

Du behöver Azure-prenumerations-ID och den region där du vill installera Azure FarmBeats. Välj en av de regioner som visas i avsnittet Regioner som [stöds.](#regions-supported)

Anteckna **Azure-prenumerations-ID** och **Azure-regionen**.

### <a name="create-an-aad-application"></a>Skapa ett AAD-program

Azure FarmBeats kräver att Azure Active Directory-program skapas och registreras. För att kunna köra skriptet för AAD-skapande krävs följande behörigheter:

- Hyresgäst - Skaparskapare för AAD-appen
- Prenumeration - Ägare

Kör följande steg i en Cloud Shell-instans med PowerShell-miljön. Förstagångsanvändare uppmanas att välja en prenumeration och skapa ett lagringskonto. Slutför installationen enligt instruktionerna.

1. Ladda ner [AAD app generator skript](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Som standard hämtas filen till din arbetskatalog. Navigera till katalogen.

    ```azurepowershell-interactive
        cd
    ```

3. Kör AAD-skriptet

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Skriptet frågar efter följande tre ingångar:

    - **FarmBeats webbplatsnamn:** Detta är den unika URL prefix för din FarmBeats webbapplikation. Om prefixet redan har tagits kommer skriptet att felas. När du har installerat kommer din FarmBeats-distribution att vara tillgänglig från https://\<FarmBeats-webbplatsnamn>.azurewebsites.net och swagger-API:erna kommer att finnas på https://\<FarmBeats-webbplatsnamn>-api.azurewebsites.net

    - **Azure-inloggnings-ID:** Ange Azure-inloggnings-ID för den användare som du vill ska läggas till som administratör för FarmBeats. Den här användaren kan sedan bevilja åtkomst till FarmBeats webbprogram till andra användare. Inloggnings-ID:et john.doe@domain.comär i allmänhet av formuläret . Azure UPN stöds också.

    - **Prenumerations-ID:** Det här är ID:t för den prenumeration där du vill installera Azure FarmBeats

5. AAD-skriptet tar cirka 2 minuter att köra och matar ut värden på skärmen samt till en json-fil i samma katalog. Om du lät någon annan köra skriptet ber du dem att dela utdata med dig.

### <a name="create-sentinel-account"></a>Skapa Sentinel-konto

Med installationen av Azure FarmBeats kan du få satellitbilder från European Space Agencys [satellituppdrag Sentinel-2](https://scihub.copernicus.eu/) för din gård. För att konfigurera den här konfigurationen behöver du ett Sentinel-konto.

Följ stegen för att skapa ett gratis konto med Sentinel:

1. Gå till den officiella [registreringssidan.](https://aka.ms/SentinelRegistration)
2. Ange de uppgifter som krävs (förnamn, efternamn, användarnamn, lösenord och e-post-ID) och fyll i formuläret.
3. En verifieringslänk skickas till det registrerade e-post-ID:t. Välj länken i e-postmeddelandet och slutför verifieringen.

Registreringsprocessen är klar. Anteckna ditt **Användarnamn och** **Sentinel-lösenord**för Sentinel när verifieringen också är klar.

## <a name="install"></a>Installera

Du är nu redo att installera FarmBeats. Följ stegen nedan för att starta installationen:

1. Logga in på Azure Portal. Välj ditt konto i det övre högra hörnet och växla till Azure AD-klienten där du vill installera Azure FarmBeats.

2. Gå till Azure Marketplace i portalen och sök efter **Azure FarmBeats** på Marketplace.

3. Ett nytt fönster med en översikt över Azure FarmBeats visas. Välj **Skapa**.

4. Ett nytt fönster visas. Slutför registreringsprocessen genom att välja rätt prenumeration, resursgrupp och plats som du vill installera Azure FarmBeats till.

5. Ange den e-postadress som ska ta emot alla tjänstaviseringar som är relaterade till Azure FarmBeats i avsnittet **FarmBeats-tjänstaviseringar.** Välj **Nästa** längst ned på sidan om du vill gå till fliken **Beroenden.**

    ![Fliken Grunderna](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Kopiera de enskilda posterna från utdata från [AAD-skriptet](#create-an-aad-application) till ingångarna i avsnittet AAD-program.

7. Ange användarnamn och lösenord för [Sentinel-kontot](#create-sentinel-account) i avsnittet Sentinel-konto. Välj **Nästa** om du vill gå till fliken **Granska + Skapa.**

    ![Fliken Beroenden](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. När de angivna detaljerna har validerats väljer du **OK**. Sidan Användarvillkor visas. Granska villkoren och välj **Skapa** för att starta installationen. Du kommer att omdirigeras till den sida där du kan följa installationsförloppet.

När installationen är klar kan du verifiera installationen och börja använda FarmBeats-portalen genom att navigera\<till det webbplatsnamn du angav under installationen: https:// FarmBeats-webbplatsnamn>.azurewebsites.net. Du bör se FarmBeats användargränssnitt med ett alternativ för att skapa gårdar.

**Datahub** finns på https://\<FarmBeats-webbplats-namn>-api.azurewebsites.net/swagger. Här ser du de olika FarmBeats API-objekten och utför REST-åtgärder på API:erna.

## <a name="upgrade"></a>Uppgradera

Om du vill uppgradera FarmBeats till den senaste versionen kör du följande steg i en Cloud Shell-instans med PowerShell-miljön. Användaren måste vara ägare till prenumerationen där FarmBeats är installerat.

Förstagångsanvändare uppmanas att välja en prenumeration och skapa ett lagringskonto. Slutför installationen enligt instruktionerna.

1. Ladda ner [uppgraderingsskriptet](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Som standard hämtas filen till din arbetskatalog. Navigera till katalogen.

    ```azurepowershell-interactive
        cd
    ```

3. Kör uppgraderingsskriptet

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Sökvägen till filen input.json är valfri. Om inget anges kommer skriptet att be om alla nödvändiga indata. Uppgraderingen ska avslutas om cirka 30 minuter.

## <a name="uninstall"></a>Avinstallera

Så här avinstallerar du Azure FarmBeats Datahub eller Accelerator:

1. Logga in på Azure-portalen och **ta bort resursgrupperna** där dessa komponenter är installerade.

2. Gå till Azure Active Directory & **ta bort Azure AD-programmet** som är länkat till Azure FarmBeats-installationen.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du installerar Azure FarmBeats i din Azure-prenumeration. Lär dig nu hur du [lägger](manage-users-in-azure-farmbeats.md#manage-users) till användare i din Azure FarmBeats-instans.
