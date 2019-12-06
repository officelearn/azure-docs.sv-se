---
title: Distribuera Azure-FarmBeats
description: Den här artikeln beskriver hur du distribuerar Azure-FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c29bba81132df15fcea9ff0df7be6a8cea94c9a0
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851136"
---
# <a name="deploy-azure-farmbeats"></a>Distribuera Azure-FarmBeats

Den här artikeln beskriver hur du konfigurerar Azure-FarmBeats.

Azure FarmBeats är en bransch bestämd, utöknings bar lösning för data drivna arbets odlingar som möjliggör sömlös etablering och sensor anslutning till Azure-molnet, telemetri-datainsamling och agg regering. Azure FarmBeats innehåller flera olika sensorer, till exempel kameror, drönare och jord sensorer. Med Azure FarmBeats kan du hantera enheter från molnet, bland annat hantering av infrastruktur och tjänster i Azure för IoT-klara enheter och med en utöknings bar webb-och mobilapp som ger visualisering, aviseringar och insikter.

> [!NOTE]
> Azure FarmBeats stöds endast i offentliga moln miljöer. Mer information finns i [Vad är ett offentligt moln?](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Azure FarmBeats har följande två komponenter:

- **Datahub**: plattforms skiktet i Azure FarmBeats, som gör att du kan skapa, lagra, bearbeta data och rita insikter från befintliga eller nya datapipelines. Detta plattforms lager är användbart för att köra och bygga data pipeliner och-modeller i jordbruket.

- **Accelerator**: lösnings skiktet i Azure FarmBeats, som har ett inbyggt program för att illustrera funktionerna i Azure FarmBeats med hjälp av de i förväg skapade jordbruks modellerna. Med det här lösnings skiktet kan du skapa server grupps gränser och rita insikter från jordbrukets data inom ramen för Server gruppens gräns.

En snabb distribution av Azure FarmBeats bör ta mindre än en timme. Kostnaderna för Datahub och Accelerator varierar beroende på användning.

## <a name="deployed-resources"></a>Distribuerade resurser

Azure FarmBeats-distributionen skapar följande resurser i din prenumeration:

| S. nr  | Resursnamn  | FarmBeats-komponent  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     Datahub/Accelerator     |
|3  |Azure Cache for Redis   |Datahub   |
|4  |       Azure Key Vault    |  Datahub/Accelerator        |
|5  |    Azure Time Series Insights       |     Datahub      |
|6 |      Azure Event Hub-namnrymd    |  Datahub       |
|7  |    Azure Data Factory V2       |     Datahub/Accelerator      |
|8  |Batch-konto    |Datahub   |
|9  |       Lagringskonto     |  Datahub/Accelerator        |
|10  |    Logikapp        |     Datahub      |
|11  |    API-anslutning        |     Datahub      |
|12|      Azure App Service      |  Datahub/Accelerator       |
|13 |    App Service-plan        |     Datahub/Accelerator      |
|14 |Azure Maps konto     |Gas    |
|15 |       Azure Time Series Insights      |  Datahub     |

Azure FarmBeats är tillgängligt för hämtning på Azure Marketplace, som du kan komma åt direkt från Azure Portal.  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>Skapa ett Azure FarmBeats-erbjudande på Azure Marketplace

Gör så här för att skapa ett Azure FarmBeats-erbjudande på Azure Marketplace:

1. Logga in på Azure Portal, Välj ditt konto längst upp till höger och växla sedan till den Azure Active Directory (Azure AD)-klient där du vill distribuera Azure-FarmBeats.
1. Azure-FarmBeats finns på Azure Marketplace. På sidan **Azure Marketplace** väljer du **Hämta nu**.
1. Välj **Skapa**.

> [!NOTE]
> Att slutföra erbjudandet i Azure Marketplace är bara en del av installationen. Följ anvisningarna i nästa avsnitt om du vill slutföra distributionen av Azure-FarmBeats i din Azure-prenumeration.

## <a name="prepare"></a>Förbered dig

Innan du kan distribuera Azure-FarmBeats behöver du följande behörigheter:

- **Klient**: Läs behörighet
- **Prenumeration**: deltagare eller ägare
- **Resurs grupp**: ägare

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar distributionen måste du kontrol lera att följande krav är uppfyllda:

- Ett Sentinel-konto
- En Azure AD-App-registrering

## <a name="create-a-sentinel-account"></a>Skapa ett Sentinel-konto    

Ett konto med Sentinel hjälper dig att ladda ned kontroll satellit bilder från sin officiella webbplats till din enhet. Om du vill skapa ett kostnads fritt konto gör du följande:

1. Gå till [registrerings sidan för Sentinel-kontot](https://scihub.copernicus.eu/dhus/#/self-registration). 
1. I registrerings formuläret anger du ditt förnamn, efter namn, användar namn, lösen ord och e-postadress.

En verifierings-e-postadress skickas till den registrerade e-postadressen för bekräftelse. Välj länken för att bekräfta din e-postadress. Registrerings processen har slutförts.

## <a name="create-an-azure-ad-app-registration"></a>Skapa en Azure AD-App-registrering

För autentisering och auktorisering på Azure-FarmBeats måste du ha en Azure AD-App-registrering. Du kan skapa den på något av två sätt:

* **Alternativ 1**: installations programmet kan skapa registreringen automatiskt, förutsatt att du har behörighet för klient organisation, prenumeration och resurs grupp som krävs. Om dessa behörigheter finns på plats fortsätter du till avsnittet ["förbereda indatafilen. JSON-filen"](#prepare-the-inputjson-file) .

* **Alternativ 2**: du kan skapa och konfigurera registreringen manuellt innan du distribuerar Azure-FarmBeats. Vi rekommenderar den här metoden när du inte har de behörigheter som krävs för att skapa och konfigurera en Azure AD-App-registrering i din prenumeration. Be administratören att använda det [anpassade skriptet](https://aka.ms/FarmBeatsAADScript), som hjälper IT-administratören att automatiskt generera och konfigurera Azure AD-appens registrering på Azure Portal. Som utdata för att köra det här anpassade skriptet med PowerShell-miljön måste IT-administratören dela ett Azure AD-programs klient-ID och lösen ords hemlighet med dig. Anteckna dessa värden.

    Om du vill köra registrerings skriptet för Azure AD-appen gör du följande:

    1. Hämta [skriptet](https://aka.ms/FarmBeatsAADScript).
    1. Logga in på Azure Portal och välj din prenumeration och Azure AD-klient.
    1. Starta Cloud Shell från verktygsfältet överst i Azure Portal.

        ![Project-FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

    1. Om du är en användare med slutdatum uppmanas du att välja en prenumeration för att skapa ett lagrings konto och Microsoft Azure fil resurs. Välj **Skapa lagring**.
    1. Användare med första gången erbjuds också ett val av Shell-upplevelser, bash eller PowerShell. Välj PowerShell.
    1. Ladda upp skriptet (från steg 1) till Cloud Shell och Anteckna platsen för den överförda filen.

        > [!NOTE]
        > Som standard överförs filen till din Hem Katalog.

        Använd följande skript:

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. Anteckna Azure AD-programmets ID och klient hemlighet för att dela med den person som distribuerar Azure-FarmBeats.

### <a name="prepare-the-inputjson-file"></a>Förbered indata. JSON-filen

Som en del av installationen skapar du en *indata. JSON* -fil som visas här:

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

Den här filen är indatafilen som ska Azure Cloud Shells. Den innehåller de parametrar vars värden används under installationen. Alla parametrar i JSON-filen måste ersättas med lämpliga värden eller tas bort. Den tas bort, men installations programmet kommer att uppmana dig under installationen.

Innan du förbereder filen granskar du parametrarna i följande tabell:

|Kommando | Beskrivning|
|--- | ---|
|sku  | Är ett alternativ för att ladda ned antingen eller båda komponenterna i Azure FarmBeats. Den anger vilka komponenter som ska laddas ned. Om du bara vill installera Datahub använder du "onlydatabhub". Om du vill installera Datahub och Accelerator använder du "båda".|
|subscriptionId | Anger prenumerationen för att installera Azure-FarmBeats.|
|datahubResourceGroup| Resurs grupps namnet för dina Datahub-resurser.|
|location |Den plats där du vill skapa resurserna.|
|acceleratorWebsiteName |Unikt URL-prefix som namnger din Datahub-instans.|
|acceleratorResourceGroup  | Unikt URL-prefix som namnger Accelerator-webbplatsen.|
|datahubWebsiteName  | Unikt URL-prefix som namnger din Datahub-webbplats. |
|sentinelUsername | Användar namnet som används för att logga in med (till exempel `https://scihub.copernicus.eu/dhus/#/self-registration`).|
|notificationEmailAddress  | E-postadressen för att ta emot meddelanden för aviseringar som du konfigurerar i din Datahub-instans.|
|updateIfExists| Valfritt En parameter som ska tas med i filen *indata. JSON* om du vill uppgradera en befintlig Azure FarmBeats-instans. För en uppgradering måste annan information, till exempel resurs gruppens namn och platser, vara samma.|
|aadAppClientId | Valfritt En parameter som ska ingå i *indata. JSON* -filen endast om Azure AD-appen redan finns.  |
|aadAppClientSecret  | Valfritt En parameter som ska ingå i *indata. JSON* -filen endast om Azure AD-appen redan finns.|

## <a name="deploy-the-app-within-cloud-shell"></a>Distribuera appen i Cloud Shell

Som en del av det tidigare diskuterade Marketplace-arbetsflödet måste du ha skapat en resurs grupp och undertecknat avtalet om licens villkor, som du kan granska igen som en del av den faktiska distributionen. Du kan distribuera appen via det Cloud Shell webbläsarbaserat kommando rads gränssnittet med hjälp av bash-miljön. Fortsätt till nästa avsnitt om du vill distribuera via Cloud Shell.

> [!NOTE]
> Inaktiva Cloud Shell sessioner upphör att gälla efter 20 minuter. Försök att slutföra distributionen inom den här tiden.

1. Logga in på Azure Portal och välj den prenumeration och Azure-klient som du vill använda.
1. Starta Cloud Shell från verktygsfältet överst i Azure Portal.
1. Om du använder Cloud Shell för första gången uppmanas du att välja en prenumeration för att skapa ett lagrings konto och en Azure Files-resurs.
1. Välj **skapa lagring**.  
1. För miljön väljer du **bash** (inte PowerShell).

## <a name="deployment-scenario-1"></a>Distributions scenario 1

I det här scenariot, som beskrivs tidigare i "alternativ 1", skapar installations programmet Azure AD App-registreringen automatiskt. Du distribuerar FarmBeats genom att göra följande:

1. Kopiera följande exempel-JSON-mall och spara den som *indata. JSON*. Se till att anteckna sökvägen till filen på den lokala datorn.

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

1. Öppna Azure Cloud Shell. När autentiseringen är klar väljer du knappen **överför** (markerad i följande bild) och laddar sedan upp filen *indata. json* för att Cloud Shell lagring.  

    ![Project-FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. Gå till din arbets katalog i Cloud Shell. Som standard är katalog namnet */home/\<användar namn >* .
1. I Cloud Shell anger du följande kommando. Se till att ändra sökvägen till *indata. JSON* -filen och välj sedan retur nyckeln.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Installations programmet hämtar automatiskt alla beroenden och skapar distributören. Du uppmanas att godkänna licens villkoren för Azure FarmBeats.

     - Om du godkänner, ange **Y** för att fortsätta till nästa steg.
     - Om du inte accepterar, anger du **N**och distributionen avbryts.

1. Ange en åtkomsttoken för distributionen i prompten. Kopiera den genererade koden och logga in på [sidan för enhets inloggning](https://microsoft.com/devicelogin) med dina Azure-autentiseringsuppgifter.

    > [!NOTE]
    > Token upphör att gälla efter 60 minuter. Om den går ut kan du starta om genom att skriva om distributions kommandot.

1. Ange lösen ordet för Sentinel-kontot i prompten.
   
   Installations programmet validerar och startar distributionen, vilket kan ta ungefär 20 minuter.

   När distributionen har slutförts får du följande utgående länkar:

    - **Datahub-URL**: Swagger-länken för att testa Azure FarmBeats-API: er.
    - **Accelerator-URL**: användar gränssnittet för att utforska Azure FarmBeats Smart Farm-Accelerator.
    - **Distributions logg fil**: logg filen som skapas under distributionen. Du kan använda den för fel sökning, om det behövs.

## <a name="deployment-scenario-2"></a>Distributions scenario 2

I det här scenariot, som beskrivs tidigare i "alternativ 2", använder du din befintliga Azure Active Directory app-registrering för att distribuera FarmBeats genom att göra följande:

1. Kopiera följande JSON-fil, som innehåller Azure Application klient-ID och lösen ord i *indata* -JSON-filen och spara den. Se till att anteckna sökvägen till filen på den lokala datorn.

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

1. Gå till Azure Cloud Shell igen och du har autentiserats.
1. Välj knappen **överför** (markerad i följande bild) och ladda upp filen *indata. json* för att Cloud Shell lagring.

    ![Project-FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. Gå till din arbets katalog i Cloud Shell. Som standard är katalog namnet */home/\<användar namn >* .
1. I Cloud Shell anger du följande kommando. Se till att ändra sökvägen till *indata. JSON* -filen och välj sedan retur nyckeln.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     Installations programmet hämtar automatiskt alla beroenden och skapar distributören. Du uppmanas att godkänna licens villkoren för Azure FarmBeats.

     - Om du godkänner, ange **Y** för att fortsätta till nästa steg.
     - Om du inte accepterar, anger du **N**och distributionen avbryts.

1. Ange en åtkomsttoken för distributionen i prompten. Kopiera den genererade koden och logga in på [sidan för enhets inloggning](https://microsoft.com/devicelogin) med dina Azure-autentiseringsuppgifter.

   Installations programmet validerar och startar distributionen, vilket kan ta ungefär 20 minuter.

   När distributionen har slutförts får du följande utgående länkar:

    - **Datahub-URL**: Swagger-länken för att testa Azure FarmBeats-API: er.
    - **Accelerator-URL**: användar gränssnittet för att utforska Azure FarmBeats Smart Farm-Accelerator.
    - **Distributions logg fil**: logg filen som skapas under distributionen. Du kan använda den för fel sökning, om det behövs.

Om du stöter på problem kan du läsa [fel sökning](troubleshoot-project-farmbeats.md).


## <a name="validate-the-deployment"></a>Verifiera distributionen

### <a name="datahub"></a>Datahub

När Datahub-installationen är klar får du en URL för att få åtkomst till Azure FarmBeats-API: er via Swagger-gränssnittet i formatet https://\<yourdatahub-webbplats-Name >. azurewebsites. net/Swagger.

1. Om du vill logga in via Swagger kopierar du och klistrar in webb adressen i webbläsaren.
1. Logga in med dina Azure Portal autentiseringsuppgifter.
1. Valfritt Prova detta "Sanity-test": 

     - Logga in på Swagger-portalen med hjälp av Datahub-länken, som du fick som utdata till en lyckad distribution.
     - Utökade typer Hämta API – Välj "testa utsättaren"
     - Du bör få Server svars koden 200 och inte ett undantag, t. ex. "403 obehörig användare".

### <a name="accelerator"></a>Gas

När Accelerator-installationen är klar får du URL: en för att komma åt användar gränssnittet för Azure-FarmBeats i formatet https://\<Accelerator-webbplats-Name >. azurewebsites. net.

1. Om du vill logga in från Accelerator kopierar du och klistrar in webb adressen i webbläsaren.
1. Logga in med dina Azure Portal autentiseringsuppgifter.

## <a name="upgrade"></a>Uppgradera

Uppgraderings anvisningarna liknar dem för installation för första gången. Gör följande:

1. Logga in på Azure Portal och välj sedan önskad prenumeration och Azure-klient.
1. Öppna Cloud Shell från det övre navigerings fältet i Azure Portal.

   ![Project-FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

1. Välj **bash**i list rutan **miljö** till vänster.
1. Om det behövs gör du ändringar i *indata. JSON* -filen och laddar sedan upp den till Cloud Shell. Du kan till exempel uppdatera din e-postadress för den avisering som du vill ta emot.
1. Skriv eller klistra in följande två kommandon i Cloud Shell. Se till att ändra sökvägen till filen *indata. JSON* och välj sedan Retur.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   Installations programmet efterfrågar automatiskt nödvändiga indata vid körning.

1. Ange en åtkomsttoken för distribution. Kopiera den genererade koden och logga in på [sidan för enhets inloggning](https://microsoft.com/devicelogin) med dina Azure-autentiseringsuppgifter.
1. Ange ditt Sentinel-lösenord.

   Installations programmet validerar nu och börjar skapa resurserna, vilket kan ta ungefär 20 minuter.

   När distributionen har slutförts får du följande utgående länkar:
    - **Datahub-URL**: Swagger-länken för att testa Azure FarmBeats-API: er.
    - **Accelerator-URL**: användar gränssnittet för att utforska Azure FarmBeats Smart Farm-Accelerator.
    - **Distributions logg fil**: logg filen som skapas under distributionen. Du kan använda den för fel sökning, om det behövs.

> [!NOTE]
> Anteckna föregående värden för framtida bruk.


## <a name="uninstall"></a>Avinstallera

För närvarande stöder vi inte automatisk avinstallation av Azure-FarmBeats med hjälp av installations programmet. Ta bort Datahub eller Accelerator genom att i Azure Portal ta bort resurs gruppen där dessa komponenter är installerade, eller ta bort resurserna manuellt.

Om du till exempel har distribuerat Datahub och Accelerator i två olika resurs grupper kan du ta bort dessa resurs grupper på följande sätt:

1. Logga in på Azure Portal.
1. Välj ditt konto längst upp till höger och växla till den Azure AD-klient där du vill distribuera Azure-FarmBeats.

   > [!NOTE]
   > Datahub krävs för att Accelerator ska fungera korrekt. Vi rekommenderar inte att du avinstallerar Datahub utan att avinstallera Accelerator.

1. Välj **resurs grupper**och ange sedan namnet på den Datahub eller den Accelerator-resurs grupp som du vill ta bort.
1. Välj resursgruppens namn. Skriv namnet på nytt för att bekräfta det och välj sedan **ta bort** för att ta bort resurs gruppen och alla dess underliggande resurser.
   Du kan också ta bort varje resurs manuellt, en metod som vi inte rekommenderar.
1. Om du vill ta bort eller avinstallera Datahub går du till resurs gruppen direkt på Azure och tar sedan bort resurs gruppen därifrån.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du distribuerar Azure-FarmBeats. Nu ska du lära dig hur du [skapar FarmBeats-servergrupper](manage-farms.md#create-farms).
