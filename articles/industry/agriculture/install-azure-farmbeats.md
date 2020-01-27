---
title: Installera Azure FarmBeats
description: Den här artikeln beskriver hur du installerar Azure-FarmBeats i din Azure-prenumeration
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482457"
---
# <a name="install-azure-farmbeats"></a>Installera Azure FarmBeats

Den här artikeln beskriver hur du installerar Azure-FarmBeats i din Azure-prenumeration.

Azure FarmBeats är ett affärs-till-affärs erbjudande som är tillgängligt på Azure Marketplace. Det möjliggör agg regering av data uppsättningar i jordbruket mellan leverantörer och generering av åtgärds bara insikter. Azure FarmBeats gör detta genom att göra det möjligt för dig att bygga artificiell intelligens (AI) eller Machine Learning-modeller (ML) baserat på säkrade data uppsättningar. De två huvud komponenterna i Azure FarmBeats är:

- **Datahub**: ett API-lager som möjliggör agg regerings-, normaliserings-och contextualization för olika jordbruks data uppsättningar mellan olika leverantörer.

- **Accelerator**: ett exempel på ett webb program som byggts ovanpå Datahub. Den hoppar – startar din modell utveckling och visualisering. Acceleratorn använder Azure FarmBeats-API: er för att demonstrera visualisering av inmatade sensor data som diagram och visualisering av modellens utdata som Maps.

## <a name="before-you-start"></a>Innan du börjar
### <a name="components-installed"></a>Installerade komponenter

När du installerar Azure-FarmBeats är följande resurser etablerade i din Azure-prenumeration:

| Azure-resurser har installerats  | Azure FarmBeats-komponent  |
|---------|---------|
| Application Insights   |      Datahub & Accelerator      |
| App Service     |     Datahub & Accelerator     |
| App Service-plan   | Datahub & Accelerator  |
| API-anslutning    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & Accelerator      |
| Azure Batch-konto    | Datahub   |
| Azure Key Vault |  Datahub & Accelerator        |
| Azure Maps konto       |     Gas    |
| Event Hub-namnområde    |     Datahub      |
| Logisk app      |  Datahub       |
| Lagringskonto      |     Datahub & Accelerator      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Kostnader som uppstår

Kostnaden för Azure FarmBeats är en mängd kostnader för de underliggande Azure-tjänsterna. Pris information för Azure-tjänster kan beräknas med hjälp av [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator). Den faktiska kostnaden för den totala installationen varierar beroende på användningen. Den stabila status kostnaden för de två komponenterna är:

* Datahub – mindre än $10 per dag
* Accelerator-mindre än $2 per dag

### <a name="regions-supported"></a>Regioner som stöds

Azure-FarmBeats stöds för närvarande i offentliga moln miljöer i följande regioner:
* Australien, östra
* USA, centrala
* USA, östra
* USA, östra 2
* USA, västra
* USA, västra 2
* Europa, norra
* Europa, västra
* Sydostasien

### <a name="time-taken"></a>Åtgången tid

Hela installationen av Azure FarmBeats, inklusive förberedelser och installation tar mindre än en timme.

## <a name="prerequisites"></a>Krav    

Innan du påbörjar den faktiska installationen av Azure-FarmBeats måste du utföra följande steg:

### <a name="create-sentinel-account"></a>Skapa Sentinel-konto
Med din Azure FarmBeats-installation kan du få gratis satellit-bilder från Europeiska rums Byråens [kontroll 2](https://scihub.copernicus.eu/) satellit uppdrag för din server grupp. Om du vill konfigurera den här installationen behöver du ett Sentinel-konto.

Följ stegen för att skapa ett kostnads fritt konto med Sentinel:

1. Gå till den officiella [registrerings](https://scihub.copernicus.eu/dhus/#/self-registration) sidan.
2. Ange nödvändig information (förnamn, efter namn, användar namn, lösen ord och e-post-ID) och fyll i formuläret.
3. En verifierings länk skickas till det registrerade e-post-ID: t. Välj länken som finns i e-postmeddelandet och slutför verifieringen.

Registreringen slutförs när du har slutfört verifieringen. Anteckna ditt **kontroll användar namn** och **lösen ord**för kontroll.

### <a name="decide-subscription-and-region"></a>Bestäm prenumeration och region

Du behöver ID för Azure-prenumeration och den region där du vill installera Azure-FarmBeats. Välj en av de regioner som anges i avsnittet [regioner som stöds](#regions-supported) .

Anteckna **ID för Azure-prenumerationen** och Azure- **regionen**.

### <a name="verify-permissions"></a>Verifiera behörigheter

Du måste kontrol lera om du har behörighet och behörighet i Azure-klienten som du vill installera Azure FarmBeats.

Du kan kontrol lera åtkomst behörigheterna i Azure Portal genom att följa anvisningarna i [rollbaserad åtkomst kontroll](https://docs.microsoft.com/azure/role-based-access-control/check-access)

För att installera Azure-FarmBeats behöver du följande behörigheter:
- Klient – Läs behörighet
- Prenumeration – deltagare eller ägare
- Resurs grupp-ägare.

Dessutom kräver Azure-FarmBeats Azure Active Directory program registreringar. Det finns två sätt att slutföra den nödvändiga installationen av Azure AD:

**Fall 1**: du har **Skriv** behörighet i den Azure-klient som du installerar på. Det här fallet innebär att du har de behörigheter som krävs för att skapa AAD-appens registrering dynamiskt under installationen.

Du kan fortsätta direkt till det [fullständiga registrerings avsnittet för Marketplace](#complete-azure-marketplace-sign-up) .


**Fall 2**: du har inte **Skriv** behörighet i Azure-klienten. Det här fallet är vanligt när du försöker installera Azure-FarmBeats i ditt företags Azure-prenumeration och **Skriv** åtkomsten är begränsad till endast den resurs grupp du äger.
I det här fallet ber du IT-administratören att följa stegen nedan för att automatiskt generera och slutföra registreringen av Azure AD-appen i Azure Portal.

1. Ladda ned och extrahera [AAD app Generator-skriptet](https://aka.ms/FarmBeatsAADScript) till din lokala dator.
2. Logga in på Azure Portal och välj din prenumeration och Azure AD-klient.
3. Starta Cloud Shell från verktygsfältet överst i Azure Portal.

    ![Project-FarmBeats](./media/install-azure-farmbeats/navigation-bar-1.png)

4. Välj PowerShell som önskad Shell-upplevelse. Första gången användaren uppmanas att välja en prenumeration och skapa ett lagrings konto. Slutför installationen enligt anvisningarna.
5. Ladda upp skriptet (från steg 1) till Cloud Shell och Anteckna platsen för den överförda filen.

    > [!NOTE]
    > Som standard överförs filen till din Hem Katalog.

6. Gå till arbets katalogen med hjälp av kommandot "CD" och kör följande skript:

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. Ange namnet på den **Datahub webbplatsen** och **acceleratorns webbplats** namn. Anteckna utdata för skriptet och dela det med den person som installerar Azure-FarmBeats.

När IT-administratören ger dig den information du behöver, noterar du **AAD-klient-ID, AAD-klient hemlighet, Datahub webbplats namn & Accelerator-webbplats namn**.

   > [!NOTE]
   > Om du följer anvisningarna från fall 2, glöm inte att lägga till AAD-klient-ID & AAD-klient hemlighet som separata parametrar i [parameter filen](#prepare-parameters-file)

Nu har du all information som krävs för att fortsätta till nästa avsnitt.

### <a name="complete-azure-marketplace-sign-up"></a>Slutför registrering av Azure Marketplace

Du måste slutföra registreringen till Azure FarmBeats-erbjudandet på Azure Marketplace innan du kan starta processen för molnbaserad installation av molnet. Följ stegen nedan för att slutföra registreringen:

1.  Logga in på Azure Portal. Välj ditt konto i det övre högra hörnet och växla till den Azure AD-klient där du vill installera Azure-FarmBeats.

2.  Gå till Azure Marketplace på portalen och Sök efter **Azure-FarmBeats** på Marketplace

3.  Ett nytt fönster med en översikt över Azure-FarmBeats visas. Välj **create**.

4.  Ett nytt fönster visas. Slutför registrerings processen genom att välja rätt prenumeration, resurs grupp och plats där du vill installera Azure-FarmBeats.

5.  När den angivna informationen har verifierats väljer du **OK**. Sidan Användningsvillkor visas. Granska villkoren och välj **skapa** för att slutföra registrerings processen.

I det här steget slutförs registrerings processen på Azure Marketplace. Du är nu redo att starta förberedelse av parameter filen.

### <a name="prepare-parameters-file"></a>Förbered parameter filen
Det sista steget i krav fasen är att skapa en JSON-fil som fungerar som indata under installationen av Cloud Shell. Parametrarna i JSON-filen måste ersättas med lämpliga värden.

Ett exempel på en JSON-fil anges nedan. Hämta exemplet och uppdatera den information som krävs.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

Du kan läsa nedanstående parameter tabell om du vill veta mer om var och en av parametrarna.

| Parameter | Värde|
|--- | ---|
|sku  | Ger användaren möjlighet att installera både Datahub och Accelerator eller bara Datahub. Om du bara vill installera Datahub använder du "Datahub". Om du vill installera Datahub och Accelerator använder du "båda"|
|subscriptionId | Anger Azure-prenumerationen för att installera Azure-FarmBeats|
|datahubResourceGroup| Anger resurs grupps namnet för dina Datahub-resurser. Ange namnet på den resurs grupp som du skapade i Azure Marketplace här|
|location |Den plats/Azure-region där du vill installera Azure-FarmBeats|
|datahubWebsiteName  | Det unika URL-prefixet för ditt Datahub-webbprogram |
|acceleratorResourceGroup  | Anger resurs grupps namnet för dina Accelerator-resurser|
|acceleratorWebsiteName |Det unika URL-prefixet för ditt Accelerator-webbprogram|
|sentinelUsername | Användar namnet för att hämta kontroll satellit bilder|
|notificationEmailAddress  | E-postadressen för att ta emot meddelanden för aviseringar som du konfigurerar i din Datahub|
|updateIfExists| Valfritt En parameter som ska tas med i parameter filen bara om du vill uppgradera en befintlig Azure FarmBeats-instans. För en uppgradering måste annan information, till exempel resurs gruppens namn och platser, vara samma|
|aadAppClientId | Valfritt En parameter som ska tas med i parameter filen endast om du använder en redan skapade AAD-app. Mer information finns i fall 2 i avsnittet [kontrol lera behörigheter](#verify-permissions) |
|aadAppClientSecret  | Valfritt Valfritt En parameter som ska tas med i parameter filen endast om du använder en redan skapade AAD-app. Mer information finns i fall 2 i avsnittet [kontrol lera behörigheter](#verify-permissions)|

Baserat på tabellen ovan och exempel-JSON-filen skapar du parametrarna JSON-filen och sparar den på den lokala datorn.

## <a name="install"></a>Installera

Den faktiska installationen av Azure FarmBeats-resurser sker i Cloud Shell webbläsarbaserat kommando rads gränssnitt med hjälp av bash-miljön. Följ anvisningarna nedan för att installera Azure FarmBeats:

1. Logga in på Azure Portal. Välj den Azure-prenumeration och klient organisation som du vill installera Azure-FarmBeats.
2. Starta **Cloud Shell** från verktygsfältet i det övre högra hörnet av Azure Portal.
3. Välj bash som önskad Shell-upplevelse. Välj knappen **överför** (markerad i bilden nedan) och överför den för beredda parametrarna JSON-filen.

      ![Project-FarmBeats](./media/install-azure-farmbeats/bash-2-1.png)

4. **Kopiera** kommandot nedan och **Ersätt \<användar namn >** med rätt värde så att kommandot pekar på rätt sökväg för den överförda filen.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. Kör det ändrade kommandot för att starta installations processen. Du uppmanas att:
 - Godkänn licens villkoren för **Azure FarmBeats** . Ange "Y" för att fortsätta till nästa steg om du samtycker till Användningsvillkor. Ange "N" om du vill avsluta installationen, om du inte godkänner användnings villkoren.

 - Sedan uppmanas du att ange en åtkomsttoken för installationen. Kopiera den genererade koden och logga in på [sidan för enhets inloggning](https://microsoft.com/devicelogin) med dina **Azure-autentiseringsuppgifter**.

 - När inloggningen har slutförts uppmanas du att ange lösen ordet för ditt Sentinel-konto. Ange **lösen ordet**för ditt Sentinel-konto.

6. Parameter filen verifieras och installationen av Azure-resurserna startar. Det tar ungefär **25 minuter** att slutföra installationen.    
> [!NOTE]
> Inaktiva Cloud Shell sessioner upphör att gälla efter **20 minuter**. Se till att Cloud Shell-sessionen är aktiv medan installations programmet distribuerar Azure-resurserna. Om tids gränsen för sessionen har överskridits måste du starta om installations processen.

När installationen är klar får du följande utgående länkar:
* **Datahub-URL**: Swagger-länk för åtkomst till Datahub-API: er.
* **Accelerator-URL**: webb programmet för att utforska Azure FarmBeats-acceleratorn.
* **Installations logg filen**: logg filen som innehåller information om installationen. Logg filen kan användas för att felsöka installationen, om det behövs.

Du kan kontrol lera att installationen av Azure-FarmBeats är klar genom att göra följande:

**Datahub**
1. Logga in på den angivna Accelerator-URL: en (i formatet **https://\<yourdatahub-site-name >. azurewebsites. net/Swagger**) med dina Azure-autentiseringsuppgifter.
2. Du bör kunna se de olika FarmBeats API-objekten och utföra REST-åtgärder på API: erna.

**Gas**
1. Logga in på den angivna Accelerator-URL: en (i formatet **https://\<youraccelerator-site-name >. azurewebsites. net/Swagger**) med dina Azure-autentiseringsuppgifter.
2. Du bör kunna se användar gränssnittet för Accelerator med ett alternativ för att skapa Server grupper i din webbläsare.

Din möjlighet att utföra ovanstående åtgärder innebär en lyckad installation av Azure-FarmBeats.

## <a name="upgrade"></a>Uppgradera
I den offentliga för hands versionen, för att uppgradera en befintlig installation av Azure FarmBeats, måste du köra installations kommandot i Cloud Shell igen, med ytterligare en "**updateIfExists**"-parameter i parameter filen inställd på "**True**". Se den sista raden i JSON-exemplet nedan för uppdaterings parametern.

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
Kommandot uppdaterar den befintliga Azure FarmBeats-installationen till den senaste versionen och ger dig de utgående länkarna.

## <a name="uninstall"></a>Avinstallera

Slutför följande steg för att avinstallera Azure FarmBeats Datahub eller Accelerator:

1.  Logga in på Azure Portal och **ta bort resurs grupperna** där dessa komponenter är installerade.

2.  Gå till Azure Active Directory & **ta bort Azure AD-programmet** som är länkat till Azure FarmBeats-installationen. Detta tar bort installationen av Azure-FarmBeats från din Azure-prenumeration.

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du installerar Azure-FarmBeats i din Azure-prenumeration. Nu kan du lära dig hur du [lägger till användare](manage-users-in-azure-farmbeats.md#manage-users) till din Azure FarmBeats-instans.
