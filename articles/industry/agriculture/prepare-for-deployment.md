---
title: Distribuera Azure-FarmBeats
description: Beskriver hur du distribuerar FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0fc7eb6c3b582c4fc95d78397c4cb2820ebb4ea8
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534248"
---
# <a name="deploy-farmbeats"></a>Distribuera FarmBeats

Den här artikeln beskriver hur du konfigurerar Azure-FarmBeats.

Azure FarmBeats är en branschspecifika, utöknings bar lösning för data drivna lantbruk som möjliggör sömlös etablering och sensor anslutningar till Azure-molnet, telemetri data insamling och agg regering. Azure FarmBeats har olika sensorer, till exempel kameror, drönare, jord sensorer och hantering av enheter från molnet, som innehåller infrastruktur och tjänster i Azure för IoT Ready-enheter (Sakernas Internet) till en Extendible webb-och mobilapp för att tillhandahålla visualisering, aviseringar och insikter.

> [!NOTE]
> Azure FarmBeats stöds endast i offentliga moln miljöer. Mer information om moln miljö finns i [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Azure FarmBeats har följande två komponenter:

- **Data** Hub – data Hub är plattforms skiktet i Azure-FarmBeats som gör att du kan bygga, lagra, bearbeta data och rita insikter från befintliga eller nya datapipelines. Detta plattforms lager är användbart för att köra och bygga data pipeliner och-modeller i jordbruket.

- **Accelerator** -Accelerator är lösnings skiktet i Azure-FarmBeats som har ett inbyggt program för att illustrera funktionerna i Azure FarmBeats med hjälp av de i förväg skapade jordbruks modellerna. Med det här lösnings skiktet kan du skapa server grupps gränser och rita insikter från jordbrukets data inom ramen för Server gruppens gräns.

En snabb distribution av Azure FarmBeats bör ta mindre än en timme. Kostnaderna för data hubben och acceleratorn varierar beroende på användning.

## <a name="deployed-resources"></a>Distribuerade resurser

Azure FarmBeats-distributionen skapar nedanstående listade resurser i din prenumeration:

|Nr  |Resursnamn  |Azure FarmBeats-komponent  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Data hubb       |
|2  |    Application Insights      |     Data hubb/Accelerator     |
|3  |Azure Cache for Redis   |Data hubb   |
|4  |       Azure KeyVault    |  Data hubb/Accelerator        |
|5  |    Time Series Insights       |     Data hubb      |
|6 |      EventHub-namnområde    |  Data hubb       |
|7  |    Azure Data Factory V2       |     Data hubb/Accelerator      |
|8  |Batch-konto    |Data hubb   |
|9  |       Lagringskonto     |  Data hubb/Accelerator        |
|10  |    Logikapp        |     Data hubb      |
|11  |    API-anslutning        |     Data hubb      |
|12|      Apptjänst      |  Data hubb/Accelerator       |
|13 |    App Service-plan        |     Data hubb/Accelerator      |
|14 |Azure Maps konto     |Gas    |
|15 |       Time Series Insights      |  Data hubb     |

Azure FarmBeats är tillgängligt för att ladda ned från Azure Marketplace. Du kan komma åt den direkt från Azure Portal.  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Skapa ett Azure FarmBeats-erbjudande på Marketplace

Använd de här stegen för att skapa ett Azure FarmBeats-erbjudande på Marketplace:

1. Logga in på Azure Portal och välj ditt konto i det övre högra hörnet och växla till den Azure AD-klient där du vill distribuera Microsoft Azure FarmBeats.
2. Azure-FarmBeats finns på Azure Marketplace. På Marketplace-sidan väljer du på "Hämta nu".
3. Välj Skapa och ange följande information:
    - prenumerations namn.
    - ett befintligt resurs grupp namn (endast tom resurs grupp) eller skapa en ny resurs grupp för att distribuera Azure-FarmBeats. Anteckna den här resurs gruppen i efterföljande avsnitt.
4. Den region där du vill installera Azure-FarmBeats. Azure-FarmBeats stöds för närvarande i följande regioner: centrala USA, Västeuropa, USA, östra 2, norra Europa, västra USA, Sydostasien, östra USA, östra Australien, västra USA 2.
5. Välj **OK**.
Sidan Användningsvillkor visas. Granska standard villkoren för Marketplace eller Välj hyperlänken för att granska användnings villkoren.
6. Välj **Stäng**, sedan kryss rutan "Jag accepterar" och välj sedan **skapa**.
7. Nu har du signerat licens avtalet (EULA) för Azure FarmBeats på Marketplace.  
7. Följ nästa steg i den här guiden om du vill fortsätta med distributionen.

> [!NOTE]
> Om erbjudandet i Azure Marketplace slutförs, slutförs bara en del av installationen. Följ instruktionerna nedan för att slutföra distributionen av Azure-FarmBeats i din Azure-prenumeration.

## <a name="prepare"></a>Förbered dig

Du behöver följande behörigheter för att distribuera Azure-FarmBeats:

- Klient: Läs behörighet
- Prenumeration: deltagare eller ägare
- Resurs grupp: ägare

## <a name="before-you-begin"></a>Innan du börjar

Innan du påbörjar distributionen måste du se till att du har följande:

- Kontroll konto
- Registrerings program för Azure Active Directory (AD)

## <a name="create-a-sentinel-account"></a>Skapa ett Sentinel-konto    

Ett konto med Sentinel hjälper dig att ladda ned kontroll satellit bilder från sin officiella webbplats till din enhet. Följ de här stegen för att skapa ett kostnads fritt konto:

Gå till https://scihub.copernicus.eu/dhus/#/self-registration. På sidan registrering anger du ett förnamn, efter namn, användar namn, lösen ord och e-postadress.
En verifierings-e-postadress skickas till den registrerade e-postadressen för bekräftelse. Välj länken och bekräfta. Registrerings processen har slutförts.

## <a name="create-azure-ad-app-registration"></a>Skapa Azure AD-App-registrering

För autentisering och auktorisering på Azure-FarmBeats måste du ha en Azure Active Directory-programregistrering som:

- Fall 1: installations programmet kan skapa automatiskt (förutsatt att du har den behörighet som krävs för klient organisation, prenumeration och resurs grupp).
- Fall 2: du kan skapa och konfigurera innan du distribuerar Azure-FarmBeats (manuella steg krävs).

**Fall 1**: om du har åtkomst för att skapa en AAD-app-registrering kan du hoppa över det här steget och låta installations programmet skapa appens registrering. Fortsätt till nästa avsnitt: [Förbered indata. JSON-filen](#prepare-input-json-file)

Om du redan har en prenumeration kan du flytta den direkt till nästa procedur.

**Fall 2**: den här metoden är det bästa steget när du inte har tillräcklig behörighet för att skapa och konfigurera en Azure AD-App-registrering i din prenumeration. Be administratören att använda det [anpassade skriptet](https://aka.ms/FarmBeatsAADScript), vilket hjälper IT-administratören att automatiskt generera och konfigurera Azure AD-appens registrering på Azure Portal. Som utdata för att köra det här anpassade skriptet med hjälp av PowerShell-miljön måste IT-administratören dela ett Azure Active Directory-klient-ID och lösen ords hemlighet med dig. Anteckna dessa värden.

Använd följande steg för att köra skriptet för Azure AD-program registrering:

1. Hämta [skript](https://aka.ms/FarmBeatsAADScript).
2. Logga in på Azure Portal och välj din prenumeration och AD-klient.
3. Starta Cloud Shell från det övre navigeringsfältet i Azure Portal.

    ![Taktslag i projekt grupp](./media/prepare-for-deployment/navigation-bar-1.png)


4. Användare i första gången uppmanas att välja en prenumeration för att skapa ett lagrings konto och Microsoft Azure fil resurs. Välj **Skapa lagring**.
5. Första gången användaren uppmanas att välja önskad Shell-upplevelse – bash eller PowerShell. Välj PowerShell.
6. Ladda upp skriptet (från steg 1) till Cloud Shell och Anteckna platsen för den överförda filen.

    > [!NOTE]
    > Som standard överförs den till din Hem Katalog.

    Använd följande skript:

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Anteckna Azure AD-programmets ID och klient hemlighet för att dela med person som distribuerar Azure-FarmBeats.

### <a name="prepare-input-json-file"></a>Förbered indata-JSON-fil

Som en del av installationen skapar du en indata. JSON-fil på följande sätt:

```json
    {  
       "sku":"both",
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
       "datahubResourceGroup":"dummy-test-dh1",
       "location":"westus2",
       "datahubWebsiteName":"dummy-test-dh1",
       "acceleratorResourceGroup":" dummy-test-acc1",
       "acceleratorWebsiteName":" dummy-test-acc1",
       "sentinelUsername":"dummy-dev",
       "notificationEmailAddress":"dummy@yourorg.com",
       "updateIfExists":true
    }
```

Den här filen är indatafilen till Azure Cloud Shell och parametrar vars värden används under installationen. Alla parametrar i JSON måste ersättas med lämpliga värden eller tas bort. om det tas bort uppmanas du att installera installations programmet


Granska parametrarna innan du förbereder filen.

|Kommando | Beskrivning|
|--- | ---|
|sku  | Är ett alternativ för att ladda ned antingen eller båda komponenterna i Azure FarmBeats. Anger vilka komponenter som ska laddas ned. Om du bara vill installera data hubb använder du "onlydatabhub". Om du vill installera data hubb och Accelerator använder du "båda"|
|subscriptionId | Anger prenumerationen för installation av Azure-FarmBeats|
|datahubResourceGroup| Resurs grupp namn för data Hubbs resurser|
|location |Plats där du vill skapa resurserna|
|acceleratorWebsiteName |Unikt URL-prefix för att namnge din data hubb|
|acceleratorResourceGroup  | Unikt URL-prefix som namnger Accelerator-webbplatsen.|
|datahubWebsiteName  | UUnique URL-prefix som namn på data nav webbplatsen. |
|sentinelUsername | användar namn för att logga in på: https://scihub.copernicus.eu/dhus/#/self-registration.|
|notificationEmailAddress  | E-postadress för att ta emot meddelanden för aviseringar som du konfigurerar i data hubben.|
|updateIfExists|Valfritt Parameter som ska ingå i indatamängden. JSON endast om du vill uppgradera en befintlig Azure FarmBeats-instans. För uppgradering, annan information t. ex. resurs grupp namn, platser osv. måste vara desamma.|
|aadAppClientId | [**Valfritt**] Parameter som ska ingå i indatamängden. JSON endast om Azure AD-appen redan finns.  |
|aadAppClientSecret  | [**Valfritt**] Parameter som ska ingå i indatamängden. JSON endast om Azure AD-appen redan finns.|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Distribuera i Cloud Shell webbläsarbaserad kommando rad

Som en del av marknads arbets flödet ovan måste du ha skapat en resurs grupp och signerat slut användar licens avtalet, som kan granskas en gång igen som en del av den faktiska distributionen. Distributionen kan göras via Azure Cloud Shell (webbläsarbaserad kommando rad) med hjälp av bash-miljön. Fortsätt till nästa avsnitt för att distribuera via Cloud Shell.

> [!NOTE]
> Inaktiva Cloud Shell sessioner upphör att gälla efter 20 minuter. Försök att slutföra distributionen inom den här tiden.

1. Logga in på Azure Portal och välj önskad prenumeration och AD-klient.
2. Starta Cloud Shell från det övre navigeringsfältet i Azure Portal.
3. Om du använder Cloud Shell för första gången uppmanas du att välja en prenumeration för att skapa ett lagrings konto och Microsoft Azure fil resurs.
4. Välj **skapa lagring**.  

Välj miljön som bash (och inte PowerShell).

## <a name="deployment-scenario-1"></a>Distributions scenario 1

Installations programmet skapar Azure AD App registreringen (fall 1 ovan)

1. Kopiera följande mall och ge den namnet inmatad. JSON.  
Exempel på JSON-ineffekt:

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

2. Spara filen och anteckna sökvägen (på den lokala datorn).
3. Gå till Azure Cloud Shell och när autentiseringen är klar väljer du överför (se ikonen markerad i bilden nedan) och laddar upp indata. JSON-filen för att Cloud Shell lagring.  

    ![Taktslag i projekt grupp](./media/prepare-for-deployment/bash-2-1.png)

4. Gå till din Hem Katalog i Cloud Shell. Som standard är det/Home/<username>
5. Skriv eller klistra in följande kommando i Cloud Shell. Se till att ändra sökvägen till indatamängden. JSON-fil och tryck på RETUR.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Installations programmet hämtar automatiskt alla beroenden och skapar distributören. Du uppmanas att godkänna licens avtalet för slutanvändare (EULA) för Azure FarmBeats.

     - Ange "Y" om du godkänner och du fortsätter till nästa steg.
     - Ange "N" om du inte samtycker till villkoren och distributionen kommer att avslutas.

6. Sedan uppmanas du att ange en åtkomsttoken för distributionen. Kopiera koden som genereras och logga in på https://microsoft.com/devicelogin med dina Azure-autentiseringsuppgifter.

    > [!NOTE]
    > Token upphör att gälla efter 60 minuter. När den går ut kan du starta om genom att skriva distributions kommandot igen.

7. När du uppmanas till det anger du ditt kontroll konto lösen ord.
8. Installations programmet validerar nu och börjar distribuera, vilket kan ta ungefär 20 minuter.
9. När distributionen har slutförts visas följande utgående länkar:

 - **Datahubbs-URL**: Swagger-länk för att testa Azure FarmBeats-API: er.
 - **Accelerator-URL**: användar gränssnitt för att utforska Azure FarmBeats Smart Farm-Accelerator.
 - Loggfil för **distributions logg filen**som skapades under distributionen. Den kan användas för fel sökning vid behov.

## <a name="deployment-scenario-2"></a>Distributions scenario 2

Befintlig Azure Active Directory app-registrering används för att distribuera (fall 2 ovan)

1. Kopiera JSON-filen nedan, som innehåller Azure Application klient-ID och lösen ord i indata. JSON, och spara den.

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

Följ resten av stegen:

2. Anteckna sökvägen till indata-JSON-filen (på den lokala datorn).
3. Gå till Azure Cloud Shell en gång igen och du har autentiserats, Välj knappen överför (se markerad ikon i bilden nedan) och ladda upp indata. JSON-filen till Cloud Shell lagring.

    ![Taktslag i projekt grupp](./media/prepare-for-deployment/bash-2-1.png)

4. Gå till din Hem Katalog i Cloud Shell. Som standard är det/Home/<username>
5. Skriv eller klistra in följande kommando i Cloud Shell. Se till att ändra sökvägen till indatamängden. JSON-fil och tryck på RETUR.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

Följ anvisningarna på skärmen.

6. Skriptet hämtar automatiskt alla beroenden och skapar distributören.
7. Du uppmanas att läsa och godkänna licens avtalet för slutanvändare (EULA) för Azure FarmBeats.

    - Ange "Y" om du godkänner och fortsätter till nästa steg.
    - Ange "N" om du inte accepterar villkoren och distributionen kommer att avslutas.

8. Du uppmanas att ange en åtkomsttoken för distributionen. Kopiera koden som genereras och logga in för att https://microsoft.com/devicelogin med dina Azure-autentiseringsuppgifter.
9. Nu kommer installations programmet att validera och börja skapa resurserna, vilket kan ta ungefär 20 minuter. Låt sessionen vara aktiv på Cloud Shell under den här tiden.
10. När distributionen har slutförts visas följande utgående länkar:

 - **Datahubbs-URL**: Swagger-länk för att testa Azure FarmBeats-API: er.
 - **Accelerator-URL**: användar gränssnitt för att utforska Azure FarmBeats Accelerator.
 - **Distributions logg filen**: logg filen skapades under distributionen. Den kan användas för fel sökning vid behov.

Om du stöter på problem kan du läsa [fel sökning](troubleshoot-project-farmbeats.md).


## <a name="validate-deployment"></a>Verifiera distribution

### <a name="data-hub"></a>Data hubb

När installationen av data hubben är klar får du URL: en för att få åtkomst till Azure FarmBeats-API: er via Swagger-gränssnittet i formatet: https://\<yourdatahub-webbplats-Name >. azurewebsites. net/Swagger

1. Om du vill logga in via Swagger kopierar du och klistrar in webb adressen i webbläsaren.
2. Logga in med Azure Portal autentiseringsuppgifter.
3. Sanity-test (valfritt)

     - Kan logga in på Swagger-portalen med hjälp av länken data hubb, som du fick som utdata till en lyckad distribution.
     - Utökade typer Hämta API – Välj "testa utsättaren"
     - Du bör få Server svars koden 200 och inte ett undantag, till exempel 403 "obehörig användare".

### <a name="accelerator"></a>Gas

När Accelerator-installationen är klar får du URL: en för att få åtkomst till Azure FarmBeats User-Interface i formatet: https://\<Accelerator-webbplats-Name >. azurewebsites. net

1. Om du vill logga in från Accelerator kopierar du och klistrar in webb adressen i webbläsaren.
2. Logga in med Azure Portal autentiseringsuppgifter.

## <a name="upgrade"></a>Uppgradera

Uppgraderings stegen liknar installationen vid första tiden. Följ de här stegen:

1. Logga in på Azure Portal och välj önskad prenumeration och AD-klient.
2. Starta Cloud Shell från det övre navigeringsfältet i Azure Portal.

   ![Taktslag i projekt grupp](./media/prepare-for-deployment/navigation-bar-1.png)

3. Välj miljön som "bash" från List rutan till vänster om-gränssnittet.
4. Gör ändringar i indata. JSON-filen om det behövs och överför till Azure Cloud Shell. Du kan till exempel uppdatera din e-postadress för den avisering som du vill ta emot.
5. Ladda upp indata. JSON-filen till Azure Cloud Shell.
6. Skriv eller klistra in följande två kommandon i Cloud Shell. Se till att ändra sökvägen till indata. JSON-filen och tryck på RETUR.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
Följ anvisningarna på skärmen:

7. Installations programmet efterfrågar automatiskt nödvändiga indata vid körning:
8. Ange en åtkomsttoken för distribution. Kopiera koden som genereras och logga in för att https://microsoft.com/devicelogin med dina Azure-autentiseringsuppgifter.
9. Kontroll lösen ord
10. Installations programmet validerar nu och börjar skapa resurserna, vilket kan ta ungefär 20 minuter.
11. När distributionen har slutförts visas följande utgående länkar:
 - **Datahubbs-URL**: Swagger-länk för att testa Azure FarmBeats-API: er.
 - **Accelerator-URL**: användar gränssnitt för att utforska Azure FarmBeats Accelerator.
 - **Distributions logg fil**: sparar loggar under distributionen. Den kan användas för fel sökning.

> [!NOTE]
> Anteckna ovanstående värden för framtida bruk.


## <a name="uninstall"></a>Avinstallera

För närvarande har vi inte stöd för automatisk avinstallation av Azure-FarmBeats med installations programmet. Ta bort data hubben eller-acceleratorn genom att i Azure Portal ta bort resurs gruppen där dessa komponenter är installerade, eller ta bort resurser manuellt.

Om du till exempel har distribuerat data hubb och Accelerator i två olika resurs grupper, tar du bort resurs grupperna enligt följande:

1. Logga in i Azure-portalen.
2. Välj ditt konto i det övre högra hörnet och växla till önskad Azure AD-klient där du vill distribuera Azure-FarmBeats.

   > [!NOTE]
   > Data Hub krävs för att Accelerator ska fungera korrekt. Vi rekommenderar inte att du avinstallerar data Hub utan att avinstallera Accelerator.

3. Välj resurs grupper och ange namnet på den data hubb eller Accelerator-resurs grupp som du vill ta bort.
4. Välj resursgruppens namn. Skriv namnet igen för att dubbelklicka och välj Ta bort för att ta bort resurs gruppen och alla dess underliggande resurser.
5. Du kan också ta bort varje resurs manuellt, vilket inte rekommenderas.
7. Om du vill ta bort/avinstallera data hubb går du till resurs gruppen direkt på Azure och tar bort resurs gruppen därifrån.

## <a name="next-steps"></a>Nästa steg

Du har distribuerat Azure-FarmBeats. Nu kan du lära dig hur du [skapar Server grupper](manage-farms.md#create-farms).
