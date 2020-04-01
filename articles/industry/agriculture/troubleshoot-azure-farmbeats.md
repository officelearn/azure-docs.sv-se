---
title: Felsöka Azure FarmBeats
description: I den här artikeln beskrivs felsÃ¶kning av Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a31eece6629558b14b614853addce59642e698b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422725"
---
# <a name="troubleshoot"></a>Felsöka

Den här artikeln innehåller lösningar på vanliga Azure FarmBeats-problem.

För ytterligare hjälp, farmbeatssupport@microsoft.comkontakta oss på . Se till att inkludera **filen deployer.log** i din e-post.

Så här hämtar du **filen deployer.log:**

1. Logga in på **Azure-portalen** och välj din prenumeration och Azure AD-klient.
2. Starta Cloud Shell från det övre navigeringsfältet i Azure Portal.
3. Välj **Bash** som den föredragna Cloud Shell-upplevelsen.
4. Markera den markerade ikonen och välj sedan **Hämta**i listrutan .

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. I nästa fönster anger du sökvägen till **filen deployer.log.** Ange till exempel **farmbeats-deployer.log**.

## <a name="sensor-telemetry"></a>Sensor telemetri

### <a name="cant-view-telemetry-data"></a>Det går inte att visa telemetridata

**Symptom:** Enheter eller sensorer distribueras och du har länkat FarmBeats med din enhetspartner, men du kan inte hämta eller visa telemetridata på FarmBeats.

**Korrigerande åtgärder:**

1. Gå till din FarmBeats Datahub-resursgrupp.   

2. Välj **eventhubben** (DatafeedEventHubNamespace) och kontrollera sedan antalet inkommande meddelanden.

3. Gör något av följande:   
   - Om det inte finns *några inkommande meddelanden*kontaktar du enhetspartnern.  
   - Om det finns *inkommande* farmbeatssupport@microsoft.commeddelanden kontaktar du . Bifoga dina Datahub- och Acceleratorloggar och hämtade telemetri.

Om du vill förstå hur du hämtar loggar går du till avsnittet ["Samla in loggar manuellt".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Det går inte att visa telemetridata efter intag av historiska/strömmande data från sensorerna

**Symptom:** Enheter eller sensorer distribueras och du har skapat enheter/sensorer på FarmBeats och inmatad telemetri till EventHub, men du kan inte hämta eller visa telemetridata på FarmBeats.

**Korrigerande åtgärder:**

1. Se till att du har gjort partnerregistreringen korrekt - du kan kontrollera detta genom att gå till din datahub swagger, navigera till / Partner API, Gör en Get och kontrollera om partnern är registrerad. Om inte, följ [stegen här](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) för att lägga till partner.

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

**Korrigerande åtgärder:**

1. Gå till partner-API:et i Datahub Swagger.
2. Välj **Hämta** > **Prova det** > **Kör**.

> [!NOTE]
> Partner-ID:et för den sensorpartner du är intresserad av.

3. Gå tillbaka till partner-API:et och välj **Get/\<ID>**.
4. Ange partner-ID från steg 3 och välj sedan **Kör**.

   API-svaret bör ha anslutningssträngen För händelsehubbar.

### <a name="device-appears-offline"></a>Enheten visas offline

**Symptom:** Enheter är installerade och du har länkat FarmBeats med din enhetspartner. Enheterna är online och skickar telemetridata, men de visas offline.

**Korrigerande åtgärd**: Rapporteringsintervallet är inte konfigurerat för den här enheten. Om du vill ställa in rapporteringsintervallet kontaktar du enhetstillverkaren. 

### <a name="error-deleting-a-device"></a>Det gick inte att ta bort en enhet

När du tar bort en enhet kan du stöta på något av följande vanliga felscenarier:  

**Meddelande**: "Enheten refereras i sensorer: Det finns en eller flera sensorer som är associerade med enheten. Ta bort sensorerna och ta sedan bort enheten."  

**Betydelse:** Enheten är associerad med flera sensorer som distribueras i servergruppen.   

**Korrigerande åtgärder:**  

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

    > Partner har inte tillgång till att ta bort en enhet eller sensor. Endast administratörer har tillgång till göra detsamma.


## <a name="issues-with-jobs"></a>Problem med jobb

### <a name="farmbeats-internal-error"></a>Internt fel i FarmBeats

**Meddelande**: "FarmBeats internt fel, se felsökningsguide för mer information".

**Korrigerande åtgärd**: Det här problemet kan bero på ett tillfälligt fel i datapipelinen. Skapa jobbet igen. Om felet kvarstår lägger du till felmeddelandet i ett inlägg FarmBeatsSupport@microsoft.compå FarmBeats-forumet eller kontaktar .

## <a name="accelerator-troubleshooting"></a>Felsökning av acceleratorer

### <a name="access-control"></a>Åtkomstkontroll

**Problem**: Du får ett felmeddelande när du lägger till en rolltilldelning.

**Meddelande**: "Inga matchande användare hittades."

**Korrigerande åtgärd**: Kontrollera det e-post-ID som du försöker lägga till en rolltilldelning för. E-post-ID:t måste vara en exakt matchning av ID:t, som är registrerad för den användaren i Active Directory. Om felet kvarstår lägger du till felmeddelandet i ett inlägg FarmBeatsSupport@microsoft.compå FarmBeats-forumet eller kontaktar .

### <a name="unable-to-log-in-to-accelerator"></a>Det går inte att logga in på Accelerator

**Meddelande**: "Fel: Du har inte behörighet att ringa tjänsten. Kontakta administratören för auktorisering."

**Korrigerande åtgärd**: Be administratören att ge dig åtkomst till FarmBeats-distributionen. Detta kan göras genom att göra ett POST av RollAssignment API:er eller via åtkomstkontrollen i **fönstret Inställningar** i Accelerator.  

Om du redan har beviljats åtkomst och står inför det här felet kan du försöka igen genom att uppdatera sidan. Om felet kvarstår lägger du till felmeddelandet i ett inlägg FarmBeatsSupport@microsoft.compå FarmBeats-forumet eller kontaktar .

![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator problem  

**Problem:** Du har fått ett acceleratorfel av obestämd orsak.

**Meddelande**: "Fel: Ett okänt fel uppstod."

**Korrigerande åtgärder**: Det här felet uppstår om du lämnar sidan inaktiv för länge. Uppdatera sidan.  

Om felet kvarstår lägger du till felmeddelandet i ett inlägg FarmBeatsSupport@microsoft.compå FarmBeats-forumet eller kontaktar .

**Problem:** FarmBeats Accelerator visar inte den senaste versionen, även efter att du har uppgraderat FarmBeatsDeployment.

**Korrigerande åtgärd**: Det här felet uppstår på grund av servicearbetare uthållighet i webbläsaren. Gör följande:

1. Stäng alla webbläsarflikar som har Accelerator öppen och stäng webbläsarfönstret.
2. Starta en ny instans av webbläsaren och ladda om accelerator-URI:n. Den här åtgärden läser in den nya versionen av Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Bild-relaterade frågor

### <a name="wrong-username-or-password"></a>Fel användarnamn eller lösenord

**Meddelande om jobbfel**: "Fullständig autentisering krävs för att komma åt den här resursen."

**Korrigerande åtgärder:**

Gör något av följande:

- Kör installationsprogrammet igen för att uppgradera Datahub med rätt användarnamn och lösenord.
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

**Betydelse:** Om ett jobb misslyckas på grund av att det maximala antalet anslutningar har nåtts används samma Sentinel-konto i en annan programdistribution.

**Korrigerande åtgärder**: Prova något av följande:

* Skapa ett nytt Sentinel-konto och kör sedan installationsprogrammet igen för att uppgradera Datahub med hjälp av ett nytt Sentinel-användarnamn och lösenord.  
* Kör det misslyckade jobbet igen eller kör ett satellitindexjobb för ett datumintervall på 5 till 7 dagar och kontrollera sedan om jobbet lyckas.

### <a name="sentinel-server-refused-connection"></a>Sentinel-server: Nekad anslutning 

**Meddelande om fel i jobbet** http://172.30.175.69:8983/solr/dhus: "Server nekade anslutning vid: ." 

**Korrigerande åtgärder**: Det här problemet kan uppstå om några underhållsaktiviteter utförs på Sentinel-servern. 
1. Om något jobb eller pipeline misslyckas på grund av att underhåll utförs skickar du jobbet igen efter en viss tid. 

   Mer information om planerade eller oplanerade Sentinel-underhållsaktiviteter finns på [copernicus open access hub-nyhetswebbplats.](https://scihub.copernicus.eu/news/)  

2. Kör det misslyckade jobbet igen eller kör ett satellitindexjobb för ett datumintervall på 5 till 7 dagar och kontrollera sedan om jobbet lyckas.

### <a name="soil-moisture-map-has-white-areas"></a>Soil Moisture karta har vita områden 

**Problem:** Jordfuktningskartan skapades, men kartan har mestadels vita områden.

**Korrigerande åtgärder**: Det här problemet kan uppstå om de satellitindex som genereras för den tid för vilken kartan begärdes har NDVI-värden som är mindre än 0,3. För mer information, besök [Teknisk guide från Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).
1. Kör jobbet för ett annat datumintervall och kontrollera om NDVI-värdena i satellitindexen är mer än 0,3

## <a name="collect-logs-manually"></a>Samla in loggar manuellt

[Installera och distribuera Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Samla in Azure Data Factory-jobbloggar i Datahub

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök **Search** efter resursgruppen FarmBeats Datahub i sökrutan.

    > [!NOTE]
    > Välj den Datahub-resursgrupp som du angav under installationen av Farmbeats.

3. Sök efter lagringskontot för *datahublogs\* * på instrumentpanelen för **resursgrupp.** Sök till exempel efter **datahublogsmvxmq**.  
4. I kolumnen **Namn** väljer du lagringskontot för att visa instrumentpanelen **För lagringskonto.**
5. I fönstret **datahubblogs\* ** väljer du **Öppna i Utforskaren** för att visa Programmet Öppna Azure Storage **Explorer.**
6. Välj **Blob Containers**i den vänstra rutan och välj sedan **jobbloggar**.
7. Välj **Hämta**i fönstret **Jobbloggar** .
8. Ladda ned loggarna till en lokal mapp på datorn.
9. Skicka den nedladdade farmbeatssupport@microsoft.comZIP-filen via e-post till .

    ![Projekt FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Samla in Azure Data Factory-jobbloggar i Accelerator

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök **Search** efter resursgruppen FarmBeats Accelerator i sökrutan.

    > [!NOTE]
    > Välj den acceleratorresursgrupp som du angav under installationen av FarmBeats.

3. Sök efter lagringslagringskontot på instrumentpanelen för **resursgrupp.** *\* * Sök till exempel efter **storagedop4k\***.
4. Välj lagringskontot i kolumnen **Namn** om du vill visa instrumentpanelen **För lagringskonto.**
5. I **lagringsfönstret\* ** väljer du **Öppna i Utforskaren** för att öppna Azure Storage Explorer-programmet.
6. Välj **Blob Containers**i den vänstra rutan och välj sedan **jobbloggar**.
7. Välj **Hämta**i fönstret **Jobbloggar** .
8. Ladda ned loggarna till en lokal mapp på datorn.
9. Skicka den nedladdade farmbeatssupport@microsoft.comZIP-filen via e-post till .


### <a name="collect-datahub-app-service-logs"></a>Samla in tjänstloggar för Datahub-appar

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök **Search** efter resursgruppen FarmBeats Datahub i sökrutan.

    > [!NOTE]
    > Välj den Datahub-resursgrupp som du angav under installationen av Farmbeats.

3. Sök efter lagringskontot för *datahublogs\* * i resursgruppen. Sök till exempel **efterdatahublogsmvxmq\***.
4. Välj lagringskontot i kolumnen **Namn** om du vill visa instrumentpanelen **För lagringskonto.**
5. I fönstret **datahubblogs\* ** väljer du **Öppna i Utforskaren** för att öppna Azure Storage Explorer-programmet.
6. I den vänstra rutan väljer du **Blob Containers**och väljer sedan **appinsights-logs**.
7. Välj **Hämta**i fönstret **appinsights-logs** .
8. Ladda ned loggarna till en lokal mapp på datorn.
9. Skicka den nedladdade farmbeatssupport@microsoft.comZIP-filen via e-post till .

### <a name="collect-accelerator-app-service-logs"></a>Samla in tjänstloggar för acceleratorapptjänsten

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök **Search** efter resursgruppen FarmBeats Accelerator i sökrutan.

    > [!NOTE]
    > Välj den FarmBeats Accelerator-resursgrupp som tillhandahölls under Installationen av FarmBeats.

3. Sök efter *lagringslagringskontot\* * i resursgruppen. Sök till exempel efter **storagedop4k\***.
4. Välj lagringskontot i kolumnen **Namn** om du vill visa instrumentpanelen **För lagringskonto.**
5. I **lagringsfönstret\* ** väljer du **Öppna i Utforskaren** för att öppna Azure Storage Explorer-programmet.
6. I den vänstra rutan väljer du **Blob Containers**och väljer sedan **appinsights-logs**.
7. Välj **Hämta**i fönstret **appinsights-logs** .
8. Ladda ned loggarna till en lokal mapp på datorn.
9. Skicka den nedladdade mappen via e-post till farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Kända problem

## <a name="batch-related-issues"></a>Batchrelaterade problem

**Felmeddelande:**"Den regionala kontokvoten för batchkonton för den angivna prenumerationen har uppnåtts."

**Korrigerande åtgärd**: Öka kvoten eller ta bort oanvända batchkonton och kör distributionen igen.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory(Azure AD)-relaterade problem

**Felmeddelande:**"Det gick inte att uppdatera nödvändiga inställningar till Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: Otillräckliga privilegier för att slutföra åtgärden. Kontrollera att ovanstående inställningar är korrekt konfigurerade för Azure AD-appen."

**Betydelse:** Azure AD-appregistreringskonfigurationen slutfördes inte korrekt.  

**Korrigerande åtgärd**: Be IT-administratören (personen med klientläsningsåtkomst) att använda vårt [skript](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) för att skapa Azure AD-appregistreringen. Det här skriptet tar också hand om konfigurationsstegen.

**Felmeddelande:**"Det gick inte att\<skapa\>ett nytt Active Directory-programnamn ' i den här klienten: Ett annat objekt med samma värde för uri:er för egenskapsidentifierare finns redan."

**Betydelse:** En Azure AD-appregistrering med samma namn finns redan.

**Korrigerande åtgärd**: Ta bort den befintliga Azure AD-appregistreringen eller återanvänd den för installation. Om du återanvänder den befintliga Azure AD-appregistreringen skickar du program-ID:et och klienthemligheten till installationsprogrammet och distribuerar om.

## <a name="issues-with-the-inputjson-file"></a>Problem med filen input.json

**Fel**: Det finns ett fel när du läser indata från *filen input.json.*

**Korrigerande åtgärd**: Det här problemet uppstår vanligtvis på grund av ett fel i att ange rätt *input.json* filsökväg eller namn till installationsprogrammet. Gör lämpliga korrigeringar och försök igen distributionen.

**Fel**: Det finns ett fel som tolkar värden i *filen input.json.*

**Korrigerande åtgärder**: Det här problemet uppstår oftast på grund av felaktiga värden i *filen input.json.* Gör lämpliga korrigeringar och försök igen distributionen.

## <a name="high-cpu-usage"></a>Hög processoranvändning

**Fel:** Du får en e-postavisering som refererar till en **varning för hög CPU-användning**. 

**Korrigerande åtgärder:** 
1. Gå till din FarmBeats Datahub-resursgrupp.
2. Välj **apptjänsten**.  
3. Gå till [prissidan](https://azure.microsoft.com/pricing/details/app-service/windows/)för App Service och välj sedan en lämplig prisnivå.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande står inför FarmBeats frågor, kontakta vårt [supportforum](https://aka.ms/farmbeatssupport).
