---
title: Felsöka
description: Så här felsöker du Azure-FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 88a1280fb4a91d7ba45f2d0cfe92e604cd66ff0b
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672589"
---
# <a name="troubleshooting"></a>Felsöka

I följande avsnitt beskrivs vanliga problem med Azure-FarmBeats och hur du kan åtgärda dem.

Om du behöver ytterligare hjälp skriver du till oss på farmbeatssupport@microsoft.com, innehåller distributions. log-filen på det här e-postmeddelandet.

 Använd de här stegen för att hämta filen Deployer. log:

1. Den markerade ikonen och välj **nedladdnings** alternativ i list rutan.

    ![Taktslag i projekt grupp](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. På nästa skärm anger du sökvägen till filen Deployer. log. Till exempel farmbeats-Deployer. log.

## <a name="sensor-telemetry"></a>Sensor telemetri

### <a name="telemetry-not-seen"></a>Telemetri visas inte

**Symptom**: enheter/sensorer distribueras och du har länkat FarmBeats med din enhets partner. men du kan inte hämta/Visa telemetridata på FarmBeats.

**Korrigerande åtgärd**: gå till Azure Portal och följ de här stegen:

1. Gå till resurs gruppen FarmBeats data Hub.   
2. Välj **Event Hub** (DatafeedEventHubNamespace...)  och kontrol lera antalet inkommande meddelanden.   
3. Kontakta din enhets partner om det inte finns **några inkommande meddelanden**.  
4. Om det finns **inkommande meddelanden**kan du kontakta farmbeatssupport@microsoft.com med data hubb och Accelerator loggar och fånga telemetri.

Mer information om hur du hämtar loggar finns i [avsnittet om loggar](#collect-logs-manually) i dokumentet.  

### <a name="dont-have-the-eventhub-connection-string"></a>Har inte Eventhub-anslutningssträngen

**Korrigerande åtgärd**: besök Datahub-Swagger och följ de här stegen:
1. Gå till partner-API
2. Klicka på GET-> testa det – > köra
3. Anteckna partner-ID för den sensor partner som du är intresse rad av
4. Gå tillbaka till partner-API: et och klicka på GET/{ID}
5. Ange ID: t från steg 3 och klicka på Kör
6. API-svaret ska ha EventHub-anslutningssträngen

### <a name="device-appears-offline"></a>Enheten visas offline

**Symptom**: enheterna är installerade och du har länkat FarmBeats med din enhets partner. Enheterna är online och skickar telemetridata, men de visas offline.

**Korrigerande åtgärd**: rapporterings intervallet har inte kon figurer ATS för den här enheten. Kontakta enhets tillverkaren för att ange rapporterings intervallet. 

### <a name="error-deleting-a-resource"></a>Fel vid borttagning av resurs

Följande är vanliga fel scenarier när du tar bort en enhet:  

**Meddelande**: enheten refereras till sensorer: det finns en eller flera sensorer som är kopplade till enheten. Ta bort sensorer och ta sedan bort enheten.  

**Betydelse**: enheten är associerad med flera sensorer som distribueras i Server gruppen.   

**Korrigerande åtgärd**:  

1. Ta bort sensorer som är kopplade till enheten via acceleratorn.  
2. Om du vill koppla sensorer till en annan enhet ber du din enhets partner att göra det.  
3. Ta bort enheten med en BORTTAGNINGs-API-anrops inställning. parametern Force är true.  

**Meddelande**: enheten refereras till i enheter som ParentDeviceId: det finns en eller flera enheter som är associerade med den här enheten som underordnade enheter. Ta bort dem och ta sedan bort enheten.  

**Innebörd**: enheten har andra associerade enheter  

**Korrigerande åtgärd**

1. Ta bort de enheter som är associerade med den aktuella enheten  
2. Ta bort den angivna enheten  

    > [!NOTE]
    > Du kan inte ta bort en enhet om sensorer är kopplade till den. Mer information om hur du tar bort tillhör ande sensorer finns i [ta bort sensorer](get-sensor-data-from-sensor-partner.md) i Hämta sensor data kapitel.


## <a name="issues-with-jobs"></a>Problem med jobb

### <a name="farmbeats-internal-error"></a>Internt FarmBeats-fel

**Meddelande**: FarmBeats internt fel finns i fel söknings guiden för mer information.

**Korrigerande åtgärd**: det kan bero på ett tillfälligt fel i data pipelinen. Skapa jobbet en gång till. Om felet kvarstår kan du lägga till felet i ett inlägg i FarmBeats-forumet eller kontakta FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Accelerator-felsökning

### <a name="access-control"></a>Access Control

**Fel vid tillägg av roll tilldelning**

**Meddelande**: Inga matchande användare hittades

**Korrigerande åtgärd**: kontrol lera e-postadressen som du försöker utföra en roll tilldelning för. E-post-ID: t måste vara en exakt matchning av det som registrerats för den användaren i Active Directory. Om felet kvarstår kan du lägga till felet i ett inlägg i FarmBeats-forumet eller kontakta FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>Det gick inte att logga in på Accelerator

**Meddelande**: fel: du har inte behörighet att anropa tjänsten. Kontakta administratören om du vill ha behörighet.

**Korrigerande åtgärd**: be administratören att ge dig åtkomst till FarmBeats-distributionen. Detta kan göras genom att göra ett inlägg i RoleAssignment-API: erna eller via Access Control i fönstret inställningar i-acceleratorn.  

Om du redan har lagt till och fått det här felet kan du försöka igen genom att uppdatera sidan.  Om felet kvarstår kan du lägga till felet i ett inlägg i FarmBeats-forumet eller kontakta FarmBeatsSupport@microsoft.com.

![Taktslag i projekt grupp](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Accelerator-ett okänt fel uppstod  

**Meddelande**: fel: ett okänt fel uppstod

**Korrigerande åtgärd**: detta inträffar om du lämnar sidan inaktiv i för lång tid. Uppdatera sidan.  

Om felet kvarstår kan du lägga till felet i ett inlägg i FarmBeats-forumet eller kontakta FarmBeatsSupport@microsoft.com

**FarmBeats Accelerator visar inte den senaste versionen även efter att du har uppgraderat FarmBeatsDeployment**

**Korrigerande åtgärd**: detta inträffar på grund av beständiga service Worker i webbläsaren.
Stäng alla webb läsar flikar som har acceleratorn öppen och Stäng webbläsarfönstret. Starta en ny instans av webbläsaren och Läs in Accelerator-URI igen. Den nya versionen av acceleratorn kommer att läsas in.

## <a name="sentinel-imagery-related-issues"></a>Kontroll av bilder-relaterade problem

### <a name="sentinel-wrong-username-or-password"></a>Kontroll fel användar namn eller lösen ord

**Fel meddelande för jobb**; Fullständig autentisering krävs för att få åtkomst till den här resursen.

**Korrigerings åtgärd**: kör installations programmet igen för att uppgradera data hubben med rätt användar namn och lösen ord.

**Korrigerande åtgärd**: kör om misslyckat jobb eller kör ett satellit index jobb för datum intervallet 5-7 dagar och kontrol lera om jobbet har slutförts.

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>Fel webb adress för Sentinel Hub eller inte tillgänglig 

**Jobb fel meddelande**: Hoppsan, något gick fel. Sidan som du försökte komma åt är (tillfälligt) inte tillgänglig. 

**Korrigerande åtgärd**:
1.  Öppna kontroll webb adress (https://scihub.copernicus.eu/dhus/) i webbläsare och kontrol lera om webbplatsen är tillgänglig. 
2.  Om webbplatsen inte är tillgänglig kontrollerar du om några brand Väggs-eller företags nätverk osv. blockerar webbplatsen och vidtar nödvändiga steg för att tillåta ovanstående URL. 
3.  Kör om misslyckade jobb eller kör ett satellit index jobb för ett datum intervall på 5-7 dagar och kontrol lera om jobbet har slutförts.  

### <a name="sentinel-server-down-for-maintenance"></a>Kontroll Server för underhåll

**Jobb fel meddelande**: Copernicus öppna Access Hub kommer snart att gå tillbaka! Vi beklagar besväret, men vi utför vissa underhåll för tillfället. Vi kommer snart att bli online igen. 

**Korrigerande åtgärd**:

1.  Det här problemet kan inträffa om några underhålls aktiviteter utförs på Sentinel-servern. 
2.  Om något jobb/pipeline Miss lyckas med ovanstående orsak, skicka jobbet igen om en stund. 
3.  Användaren kan besöka https://scihub.copernicus.eu/news/ för att kontrol lera information om planerade/oplanerade kontroll underhålls aktiviteter.  
4.  Kör om misslyckade jobb eller kör ett satellit index jobb för ett datum intervall på 5-7 dagar och kontrol lera om jobbet har slutförts.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Högsta antalet anslutningar har uppnåtts för Sentinel

**Jobb fel meddelande**: maximalt antal två samtidiga flöden som uppnås av användaren<username> 

**Korrigerande åtgärd**
1.  Om ett jobb Miss lyckas med ovanstående orsak används samma Sentinel-konto i en annan distribution/program vara. 
2.  Användaren kan skapa ett nytt Sentinel-konto och köra installations programmet igen för att uppgradera datahubben med nytt kontroll användar namn och lösen ord.  
3.  Kör om misslyckade jobb eller kör ett satellit index jobb för datum intervallet 5-7 dagar och kontrol lera om jobbet har slutförts.

### <a name="sentinel-server-refused-connection"></a>Indikator servern nekade anslutning 

**Jobb fel meddelande**:

Servern nekade anslutning på: http://172.30.175.69:8983/solr/dhus 

**Korrigerande åtgärd**: det här problemet kan inträffa om några underhålls aktiviteter utförs på Sentinel-servern. 
1.  Om något jobb/pipeline Miss lyckas med ovanstående orsak, skicka jobbet igen om en stund. 
2.  Användaren kan besöka https://scihub.copernicus.eu/news/ för att kontrol lera information om planerade/oplanerade kontroll underhålls aktiviteter.  
3.  Kör om misslyckade jobb eller kör ett satellit index jobb för datum intervallet 5-7 dagar och kontrol lera om jobbet har slutförts.


## <a name="collect-logs-manually"></a>Samla in loggar manuellt

[Installera och distribuera]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) till Azure Storage Explorer.

### <a name="how-to-collect-data-hub-adf-job-logs"></a>Samla in data Hubbs ADF-jobb loggar
1. Logga in på https://portal.azure.com
2. I sökrutan **söker du efter** FarmBeats data hubb resurs grupp.

    > [!NOTE]
    > Välj den resurs grupp för data hubb som angavs vid tidpunkten för FarmBeats-distributionen.

I instrument panelen för resurs gruppen söker du efter lagrings kontot (datahublogs....). Till exempel datahublogsmvxmq  

1.  Välj lagrings kontot i kolumnen **namn** om du vill visa instrument panelen för **lagrings kontot** .
2.  På sidan (datahubblogs....) väljer du **Öppna i Utforskaren** för att visa **öppna Azure Storage Explorer** program.
3.  I den vänstra panelen (datahubblogs...), **BLOB-behållare**väljer du **jobb-loggar**.
4.  På fliken **jobb-loggar** väljer du **Hämta**.
5.  Välj den plats där du vill hämta loggarna till en lokal mapp på datorn.
6.  E-posta den hämtade ZIP-filen till farmbeatssupport@microsoft.com

    ![Taktslag i projekt grupp](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Samla in Accelerator-jobb loggar i ADF

1.  Logga in på https://portal.azure.com
2.  Sök efter resurs grupp för FarmBeats Accelerator i **Sök** text rutan.

    > [!NOTE]
    > Välj den Accelerator-resurs grupp som angavs vid tidpunkten för FarmBeats-distributionen.

3.  I instrument panelen för resurs gruppen söker du efter lagring.... lagrings konto. Till exempel storagedop4k
4.  Välj lagrings kontot i kolumnen **namn** om du vill visa instrument panelen för lagrings kontot.
5.  På sidan (lagring...) väljer du **Öppna i Utforskaren** för att visa öppna Azure Storage Explorer program.
6.  I den vänstra panelen < lagring...), BLOB- **behållare**, Välj **jobb-loggar**.
7.  På fliken **jobb-loggar** väljer du **Hämta**.
8.  Välj den plats där du vill hämta loggarna till en lokal mapp på datorn.
9.  E-posta den hämtade ZIP-filen till farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>Samla in data hubb App Service-loggar

1.  Logga in på https://portal.azure.com
2.  I sökrutan **söker du efter** FarmBeats data hubb resurs grupp.

    > [!NOTE]
    > Välj den resurs grupp för data hubb som angavs vid tidpunkten för FarmBeats-distributionen.

3.  I resurs gruppen söker du efter (datahublogs....) lagrings konto. Till exempel datahublogsmvxmq
4.  Välj lagrings kontot i kolumnen **namn** om du vill visa instrument panelen för **lagrings kontot** .
5.  På sidan (datahubblogs....) väljer du **Öppna i Utforskaren** för att visa **öppna Azure Storage Explorer** program.
6.  I den vänstra panelen (datahubblogs...), BLOB- **behållare**väljer du appinsights-loggar.
7.  På fliken appinsights-logs väljer du **Hämta**.
8.  Välj sökvägen för att ladda ned loggarna till en lokal mapp på datorn.
9.  E-posta den hämtade mappen till farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>Samla in Accelerator service-loggar

1.  Logga in på https://portal.azure.com
2.  Sök efter resurs gruppen FarmBeats Accelerator i **sökningen**.

    > [!NOTE]
    > Välj den Farmbeats Accelerator-resurs grupp som anges vid tidpunkten för FarmBeats-distributionen.

3.  I resurs gruppen söker du efter lagrings konto för (lagring....). Till exempel storagedop4k
4.  Välj lagrings kontot i kolumnen **namn** om du vill visa instrument panelen för **lagrings kontot** .
5.  På sidan (lagring...) väljer du **Öppna i Utforskaren** för att visa **öppna Azure Storage Explorer** program.
6.  I den vänstra panelen (lagring...), BLOB- **behållare**väljer du appinsights-logs.
7.  På fliken appinsights-logs väljer du **Hämta**.
8.  Välj den plats där du vill hämta loggarna till en lokal mapp på datorn.
9.  E-posta den hämtade mappen till farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Kända problem

## <a name="batch-related-issues"></a>Batch-relaterade problem

**Fel**: den regionala konto kvoten för batch-konton för den angivna prenumerationen har nåtts.

**Korrigerande åtgärd**: öka kvoten eller ta bort oanvända batch-konton och kör distributionen igen.

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory relaterade problem

**Fel**: det gick inte att uppdatera de nödvändiga inställningarna till Azure AD App d41axx40-xx21-4fbd-8xxf-97Xxx9e2xxc0: otillräckliga behörigheter för att slutföra åtgärden. Se till att inställningarna ovan är korrekt konfigurerade för Azure AD App.

**Betydelse**: Azure AD App Registration-konfigurationen har inte skett korrekt.  

**Korrigerande åtgärd**: be IT-administratören (som har Läs behörighet för klient organisationen) att använda vårt [skript](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) för att skapa Azure AD-appens registrering. Det här skriptet tar automatiskt hand om konfigurations stegen.

**Fel**: det gick inte att skapa det nya Active Directory programmet "dummyname" i den här klienten: det finns redan ett annat objekt med samma värde för URI: er för egenskaps identifierare

**Betydelse**: Azure AD App-registrering med samma namn finns redan.

**Korrigerande åtgärd**: ta bort den befintliga Azure AD-appens registrering eller Återanvänd den för installation. Om du återanvänder den befintliga Azure AD skickar du program-ID: t och klient hemligheten till installations programmet och distribuerar om.

## <a name="inputjson-related-issues"></a>Inmatade JSON-relaterade problem

**Fel vid** läsning av indata från indata-JSON-fil

**Korrigerande åtgärd**: det här problemet uppstår främst på grund av brist på att ange rätt sökväg eller namn för indata-JSON till installations programmet. Gör lämpliga korrigeringar och försök att distribuera igen.

**Fel vid parsning av JSON-inmatare**

**Korrigerande åtgärd**: det här problemet uppstår främst på grund av felaktiga värden i indata-JSON-filen. Gör lämpliga korrigeringar och försök att distribuera igen.

## <a name="high-cpu-usage"></a>Hög processoranvändning

**Fel**: du får en e-postavisering som refererar till aviseringen om hög CPU-användning. 

**Korrigerande åtgärd**: 
1.  Gå till resurs gruppen FarmBeats data Hub.
2.  Välj app service.  
3.  Gå till skala upp (App Service plan) och välj en lämplig [pris nivå](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="next-steps"></a>Nästa steg

Om du fortfarande drabbas av problem kan du kontakta oss i vårt [support forum](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
