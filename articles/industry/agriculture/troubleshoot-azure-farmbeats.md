---
title: Felsöka Azure FarmBeats
description: I den här artikeln beskrivs felsÃ¶kning av Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a40c64910260a2d63a529d25e1089fb618fcec1b
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113498"
---
# <a name="troubleshoot"></a>Felsöka

Den här artikeln innehåller lösningar på vanliga Azure FarmBeats-problem. För ytterligare hjälp, kontakta vårt [supportforum](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) eller maila oss på farmbeatssupport@microsoft.com.

> [!NOTE]
  > Om du har installerat FarmBeats under april och dina jobb misslyckas med ett tomt felmeddelande kanske installationen inte har tilldelats någon batchkvot för att prioritera stöd för kritiska arbetsmiljöorganisationer. Se [här](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) för mer information. Du måste begära virtuella datorer som ska allokeras till batch-kontot för att köra jobb.

## <a name="install-issues"></a>Installera problem

  > [!NOTE]
  > Om du startar om installationen på grund av ett fel måste du ta bort **resursgruppen** eller ta bort alla resurser från resursgruppen innan du återutlöster installationen.

### <a name="invalid-sentinel-credentials"></a>Ogiltiga Sentinel-autentiseringsuppgifter

Sentinel-autentiseringsuppgifterna som anges under installationen är felaktiga. Starta om installationen med rätt autentiseringsuppgifter.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Den regionala kontokvoten för batchkonton för den angivna prenumerationen har uppnåtts

Öka kvoten eller ta bort oanvända batchkonton och starta om installationen.

### <a name="invalid-resource-group-location"></a>Ogiltig resursgruppsplats

Kontrollera att **resursgruppen** är på samma plats som den **region som** angavs under installationen.

### <a name="other-install-issues"></a>Andra installationsproblem

Kontakta oss med följande information:

- Ditt prenumerations-ID
- Namn på resursgrupp
- Följ stegen nedan för att bifoga loggfilen för distributionsfel:

    1. Navigera till **resursgruppen** i Azure-portalen.

    2. Välj **Distributioner** under **avsnittet Inställningar** till vänster.

    3. För varje distribution som visar **Misslyckades**väljer du informationen och hämtar distributionsinformationen. Bifoga den här filen till e-postmeddelandet.

## <a name="sensor-telemetry"></a>Sensor telemetri

### <a name="cant-view-telemetry-data"></a>Det går inte att visa telemetridata

**Symptom:** Enheter eller sensorer distribueras och du har länkat FarmBeats med din enhetspartner, men du kan inte hämta eller visa telemetridata på FarmBeats.

**Korrigeringsåtgärder**

1. Gå till din FarmBeats Datahub-resursgrupp.
2. Välj **eventhubben** (DatafeedEventHubNamespace) och kontrollera sedan antalet inkommande meddelanden.
3. Gör något av följande:

   - Om det inte finns *några inkommande meddelanden*kontaktar du enhetspartnern.  
   - Om det finns *inkommande meddelanden*kontaktar du oss med dina Datahub- och Acceleratorloggar och har infångat telemetri.

Om du vill förstå hur du hämtar loggar går du till avsnittet ["Samla in loggar manuellt".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Det går inte att visa telemetridata efter intag av historiska/strömmande data från sensorerna

**Symptom:** Enheter eller sensorer distribueras och du har skapat enheter/sensorer på FarmBeats och inmatad telemetri till EventHub, men du kan inte hämta eller visa telemetridata på FarmBeats.

**Korrigeringsåtgärder**

1. Se till att du har gjort partnerregistreringen korrekt - du kan kontrollera detta genom att gå till din datahub swagger, navigera till / Partner API, Gör en Get och kontrollera om partnern är registrerad. Om inte, följ dessa [steg](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) för att lägga till partner.

2. Kontrollera att du har använt rätt telemetrimeddelandeformat:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Har inte azure event hubs-anslutningssträngen

**Korrigeringsåtgärder**

1. Gå till partner-API:et i Datahub Swagger.
2. Välj **Hämta** > **Prova det** > **Kör**.

> [!NOTE]
> Partner-ID:et för den sensorpartner du är intresserad av.

3. Gå tillbaka till partner-API:et och välj **Get/\<ID>**.
4. Ange partner-ID från steg 3 och välj sedan **Kör**.

   API-svaret bör ha anslutningssträngen För händelsehubbar.

### <a name="device-appears-offline"></a>Enheten visas offline

**Symptom:** Enheter är installerade och du har länkat FarmBeats med din enhetspartner. Enheterna är online och skickar telemetridata, men de visas offline.

**Korrigerande åtgärder** Rapporteringsintervallet är inte konfigurerat för den här enheten. Om du vill ställa in rapporteringsintervallet kontaktar du enhetstillverkaren. 

### <a name="error-deleting-a-device"></a>Det gick inte att ta bort en enhet

När du tar bort en enhet kan du stöta på något av följande vanliga felscenarier:  

**Meddelande**: "Enheten refereras i sensorer: Det finns en eller flera sensorer som är associerade med enheten. Ta bort sensorerna och ta sedan bort enheten."  

**Betydelse:** Enheten är associerad med flera sensorer som distribueras i servergruppen.

**Korrigeringsåtgärder**  

1. Ta bort sensorerna som är associerade med enheten via Accelerator.  
2. Om du vill associera sensorerna med en annan enhet ber du enhetspartnern att göra detsamma.  
3. Ta bort enheten `DELETE API` med hjälp av ett anrop och ange kraftparametern som *true*.  

**Meddelande**: "Enheten refereras i enheter som ParentDeviceId: Det finns en eller flera enheter som är associerade med den här enheten som underordnade enheter. Ta bort dem och ta sedan bort den här enheten."  

**Betydelse:** Enheten har andra enheter som är associerade med den.  

**Korrigeringsåtgärder**

1. Ta bort de enheter som är associerade med den här specifika enheten.  
2. Ta bort den specifika enheten.  

    > [!NOTE]
    > Du kan inte ta bort en enhet om sensorer är associerade med den. Mer information om hur du tar bort associerade sensorer finns i avsnittet **Ta bort sensor** i Hämta [sensordata från sensorpartner](get-sensor-data-from-sensor-partner.md).
    > Partner har inte behörighet att ta bort en enhet eller sensor. Endast administratörer har behörighet att ta bort.

## <a name="issues-with-jobs"></a>Problem med jobb

### <a name="farmbeats-internal-error"></a>Internt fel i FarmBeats

**Meddelande**: "FarmBeats internt fel, se felsökningsguide för mer information."

**Korrigerande åtgärder** Det här problemet kan bero på ett tillfälligt fel i datapipelinen. Skapa jobbet igen. Om felet kvarstår kontaktar du oss med felmeddelandet/loggarna.

## <a name="accelerator-troubleshooting"></a>Felsökning av acceleratorer

### <a name="access-control"></a>Åtkomstkontroll

**Problem**: Du får ett felmeddelande när du lägger till en rolltilldelning.

**Meddelande**: "Inga matchande användare hittades."

**Korrigerande åtgärder** Kontrollera det e-post-ID som du försöker lägga till en rolltilldelning för. E-post-ID:t måste vara en exakt matchning av ID:t, som är registrerad för den användaren i Active Directory. Om felet kvarstår kontaktar du oss med felmeddelandet/loggarna.

### <a name="unable-to-log-in-to-accelerator"></a>Det går inte att logga in på Accelerator

**Meddelande**: "Fel: Du har inte behörighet att ringa tjänsten. Kontakta administratören för auktorisering."

**Korrigerande åtgärder** Be administratören att ge dig åtkomst till FarmBeats-distributionen. Detta kan göras genom att göra ett POST av RollAssignment API:er eller via åtkomstkontrollen i **fönstret Inställningar** i Accelerator.  

Om du redan har beviljats åtkomst och ställts inför det här felet kan du försöka igen genom att uppdatera sidan. Om felet kvarstår kontaktar du oss med felmeddelandet/loggarna.

![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator problem  

**Problem:** Du har fått ett acceleratorfel av obestämd orsak.

**Meddelande**: "Fel: Ett okänt fel uppstod."

**Korrigerande åtgärder** Det här felet uppstår om du lämnar sidan inaktiv för länge. Uppdatera sidan. Om felet kvarstår kontaktar du oss med felmeddelandet/loggarna.

**Problem:** FarmBeats Accelerator visar inte den senaste versionen, även efter att du har uppgraderat FarmBeatsDeployment.

**Korrigerande åtgärder** Det här felet uppstår på grund av servicearbetare persistens i webbläsaren. Gör följande:

1. Stäng alla webbläsarflikar som har Accelerator öppen och stäng webbläsarfönstret.
2. Starta en ny instans av webbläsaren och ladda om accelerator-URI:n. Den här åtgärden läser in den nya versionen av Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Bild-relaterade frågor

### <a name="wrong-username-or-password"></a>Fel användarnamn eller lösenord

**Meddelande om jobbfel**: "Fullständig autentisering krävs för att komma åt den här resursen."

**Korrigerande åtgärder**: Gör något av följande:

- Uppdatera FarmBeats med rätt användarnamn/lösenord med hjälp av stegen nedan och försök igen.

  **Uppdatera Användarnamn på Sentinel**

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Sök **Search** efter resursgruppen FarmBeats Datahub i sökrutan.
    3. Välj Lagringskontolagring***** > **Behållare** > **batch-prep-filer** > **to_vm** > **config.ini**
    4. Välj **redigera**
    5. Uppdatera användarnamnet i avsnittet sentinel_account

  **Uppdatera Sentinel-lösenord**

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Sök **Search** efter resursgruppen FarmBeats Datahub i sökrutan.
    3. Välj keyvault-*****
    4. Välj åtkomstprinciper under Inställningar
    5. Välj **Lägg till åtkomstprincip**
    6. Använd **hemlig hantering** för Konfigurera från mall och lägg till dig själv i Huvudman
    7. Välj **Lägg till**och välj sedan **Spara** på sidan **Åtkomstprinciper**
    8. Välj **Hemligheter** under **Inställningar**
    9. Välj **Sentinel-lösenord**
    10. Skapa en ny version av värdet och aktivera den.

- Kör det misslyckade jobbet igen eller kör ett satellitindexjobb för ett datumintervall på 5 till 7 dagar och kontrollera sedan om jobbet lyckas.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel-hubben: Fel WEBBADRESS eller webbplats är inte tillgänglig

**Jobbfel meddelande:**"Oj, något gick fel. Sidan du försökte komma åt är (tillfälligt) inte tillgänglig."

**Korrigerande åtgärder:**

1. Öppna [Sentinel](https://scihub.copernicus.eu/dhus/) i din webbläsare för att se om webbplatsen är tillgänglig.
2. Om webbplatsen inte är tillgänglig kontrollerar du om någon brandvägg, företagsnätverk eller annan blockerande programvara hindrar åtkomst till webbplatsen och vidtar sedan nödvändiga åtgärder för att tillåta Sentinel-URL:en. 
3. Kör det misslyckade jobbet igen eller kör ett satellitindexjobb för ett datumintervall på 5 till 7 dagar och kontrollera sedan om jobbet lyckas.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel server: Ner för underhåll

**Jobbfel meddelande:**"Copernicus Open Access Hub kommer tillbaka snart! Ledsen för besväret, vi utför en del underhåll för tillfället. Vi kommer snart tillbaka!" 

**Korrigerande åtgärder:**

Det här problemet kan uppstå om några underhållsaktiviteter utförs på Sentinel-servern.

1. Om något jobb eller pipeline misslyckas på grund av att underhåll utförs skickar du jobbet igen efter en viss tid. 

   Mer information om planerade eller oplanerade Sentinel-underhållsaktiviteter finns på [copernicus open access hub-nyhetswebbplats.](https://scihub.copernicus.eu/news/)  

2. Kör det misslyckade jobbet igen eller kör ett satellitindexjobb för ett datumintervall på 5 till 7 dagar och kontrollera sedan om jobbet lyckas.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: Maximalt antal anslutningar som nåtts

**Meddelande om jobbfel**: "Maximalt antal två samtidiga\<flöden som uppnåtts av användarens användarnamn>"."

**Betydelse:** Om ett jobb misslyckas eftersom det maximala antalet anslutningar har nåtts används samma Sentinel-konto i flera jobb.

**Korrigerande åtgärder**: Prova något av följande:

* Vänta tills de andra jobben är klara innan du kör det misslyckade jobbet igen.
* Skapa ett nytt Sentinel-konto och uppdatera sedan Sentinels användarnamn och lösenord i FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Sentinel-server: Nekad anslutning

**Meddelande om fel i jobbet** http://172.30.175.69:8983/solr/dhus: "Server nekade anslutning vid: ."

**Korrigerande åtgärder**: Det här problemet kan uppstå om några underhållsaktiviteter utförs på Sentinel-servern.

1. Om något jobb eller pipeline misslyckas på grund av att underhåll utförs skickar du jobbet igen efter en viss tid.

   Mer information om planerade eller oplanerade Sentinel-underhållsaktiviteter finns på [copernicus open access hub-nyhetswebbplats.](https://scihub.copernicus.eu/news/)  

2. Kör det misslyckade jobbet igen eller kör ett satellitindexjobb för ett datumintervall på 5 till 7 dagar och kontrollera sedan om jobbet lyckas.

### <a name="soil-moisture-map-has-white-areas"></a>Soil Moisture karta har vita områden

**Problem:** **Jordfuktningskartan** skapades, men kartan har mestadels vita områden.

**Korrigerande åtgärder**: Det här problemet kan uppstå om de satellitindex som genereras för den tid för vilken kartan begärdes har NDVI-värden som är mindre än 0,3. För mer information, besök [Teknisk guide från Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Kör jobbet för ett annat datumintervall och kontrollera om NDVI-värdena i satellitindexen är mer än 0,3.

## <a name="collect-logs-manually"></a>Samla in loggar manuellt

[Installera och distribuera Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Samla in Azure Data Factory-jobbloggar eller App Service-loggar i Datahub

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök **Search** efter resursgruppen FarmBeats Datahub i sökrutan.
3. Sök efter lagringskontot för *datahublogs\* * på instrumentpanelen för **resursgrupp.** *Datahublogsmvxmq*.  
4. I kolumnen **Namn** väljer du lagringskontot för att visa instrumentpanelen **För lagringskonto.**
5. I fönstret **datahubblogs\* ** väljer du **Öppna i Utforskaren** för att visa Programmet Öppna Azure Storage **Explorer.**
6. I den vänstra rutan väljer du **Blob Containers**och väljer sedan **jobbloggar** för Azure Data **Factory-loggar eller appinsights-loggar** för App Service-loggar.
7. Välj **Hämta** och hämta loggarna till en lokal mapp på datorn.

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Samla in Azure Data Factory-jobbloggar eller App Service-loggar för accelerator

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök **Search** efter resursgruppen FarmBeats Accelerator i sökrutan.
3. Sök efter lagringslagringskontot på instrumentpanelen för **resursgrupp.** *\* * Till exempel *lagringdop4k\**.
4. Välj lagringskontot i kolumnen **Namn** om du vill visa instrumentpanelen **För lagringskonto.**
5. I **lagringsfönstret\* ** väljer du **Öppna i Utforskaren** för att öppna Azure Storage Explorer-programmet.
6. I den vänstra rutan väljer du **Blob Containers**och väljer sedan **jobbloggar** för Azure Data **Factory-loggar eller appinsights-loggar** för App Service-loggar.
7. Välj **Hämta** och hämta loggarna till en lokal mapp på datorn.

## <a name="high-cpu-usage"></a>Hög processoranvändning

**Fel:** Du får en e-postavisering som refererar till en **varning för hög CPU-användning**.

**Korrigerande åtgärder:**

1. Gå till din FarmBeats Datahub-resursgrupp.
2. Välj **apptjänsten**.  
3. Gå till [prissidan](https://azure.microsoft.com/pricing/details/app-service/windows/)för App Service och välj sedan en lämplig prisnivå.
