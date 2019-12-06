---
title: Felsöka Azure-FarmBeats
description: Den här artikeln beskriver hur du felsöker Azure-FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0a4fb337adfb2f4e6b8edb86ac620103e929c3a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842143"
---
# <a name="troubleshoot-azure-farmbeats"></a>Felsöka Azure-FarmBeats

Den här artikeln innehåller lösningar på vanliga problem med Azure FarmBeats.

Om du behöver ytterligare hjälp kan du kontakta oss på farmbeatssupport@microsoft.com. Se till att inkludera *Deployer. log* -filen i din e-postadress.

Så här hämtar du filen *Deployer. log* :

1. Välj den markerade ikonen och välj sedan **Hämta**i list rutan.

    ![Project-FarmBeats](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

1. I nästa fönster anger du sökvägen till filen *Deployer. log* . Skriv till exempel **farmbeats-Deployer. log**.

## <a name="sensor-telemetry"></a>Sensor telemetri

### <a name="cant-view-telemetry-data"></a>Det går inte att Visa telemetridata

**Symptom**: enheter eller sensorer distribueras och du har länkat FarmBeats med din enhets partner, men du kan inte hämta eller Visa telemetridata på FarmBeats.

**Korrigerande åtgärd**: 

1. Gå till resurs gruppen FarmBeats Datahub.   
1. Välj **Event Hub** (DatafeedEventHubNamespace) och kontrol lera sedan antalet inkommande meddelanden.
1. Gör något av följande:   
   * Kontakta din enhets partner om det inte finns *några inkommande meddelanden*.  
   * Kontakta farmbeatssupport@microsoft.comom det finns *inkommande meddelanden*. Koppla dina Datahub-och Accelerator-loggar och fånga in telemetri.

Information om hur du hämtar loggar finns i avsnittet ["samla in loggar manuellt"](#collect-logs-manually) .  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Du har inte Azure Event Hubs-anslutningssträng

**Korrigerande åtgärd**: 

1. I Datahub Swagger går du till partner-API: et.
1. Välj **hämta** > **testa** > **köra**.
1. Notera partner-ID för den sensor partner som du är intresse rad av.
1. Gå tillbaka till partner-API: et och välj **Get/\<ID >** .
1. Ange partner-ID: t från steg 3 och välj sedan **Kör**.
   
   API-svaret ska ha Event Hubs anslutnings strängen.

### <a name="device-appears-offline"></a>Enheten visas offline

**Symptom**: enheterna är installerade och du har länkat FarmBeats med din enhets partner. Enheterna är online och skickar telemetridata, men de visas offline.

**Korrigerande åtgärd**: rapporterings intervallet har inte kon figurer ATS för den här enheten. Kontakta enhets tillverkaren för att ange rapporterings intervallet. 

### <a name="error-deleting-a-device"></a>Fel vid borttagning av en enhet

När du tar bort en enhet kan du stöta på något av följande vanliga fel scenarier:  

**Meddelande**: "enheten refereras till sensorer: det finns en eller flera sensorer som är kopplade till enheten. Ta bort sensorer och ta sedan bort enheten. "  

**Betydelse**: enheten är associerad med flera sensorer som distribueras i Server gruppen.   

**Korrigerande åtgärd**:  

1. Ta bort sensorer som är kopplade till enheten via Accelerator.  
1. Om du vill koppla sensorer till en annan enhet ber du din enhets partner att göra det.  
1. Ta bort enheten med ett `DELETE API`-anrop och ange parametern Force som *True*.  

**Meddelande**: "enheten refereras till i enheter som ParentDeviceId: det finns en eller flera enheter som är associerade med den här enheten som underordnade enheter. Ta bort dem och ta sedan bort enheten. "  

**Innebär**: andra enheter är kopplade till enheten.  

**Korrigerande åtgärd**

1. Ta bort de enheter som är associerade med den här enheten.  
1. Ta bort den aktuella enheten.  

    > [!NOTE]
    > Du kan inte ta bort en enhet om sensorer är kopplade till den. Mer information om hur du tar bort tillhör ande sensorer finns i avsnittet "ta bort sensor" i [Hämta sensor data från sensor partner](get-sensor-data-from-sensor-partner.md).


## <a name="issues-with-jobs"></a>Problem med jobb

### <a name="farmbeats-internal-error"></a>Internt FarmBeats-fel

**Meddelande**: "FarmBeats internt fel, se fel söknings guiden för mer information."

**Korrigerande åtgärd**: det här problemet kan bero på ett tillfälligt fel i data pipelinen. Skapa jobbet igen. Om felet kvarstår kan du lägga till fel meddelandet i ett inlägg i FarmBeats-forumet eller kontakta FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Accelerator-felsökning

### <a name="access-control"></a>Åtkomstkontroll

**Problem**: du får ett fel meddelande när du lägger till en roll tilldelning.

**Meddelande**: "Det gick inte att hitta matchande användare".

**Korrigerande åtgärd**: kontrol lera e-postadressen som du försöker lägga till en roll tilldelning för. E-post-ID: t måste vara en exakt matchning av det ID som är registrerat för den användaren i Active Directory. Om felet kvarstår kan du lägga till fel meddelandet i ett inlägg i FarmBeats-forumet eller kontakta FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Det gick inte att logga in på Accelerator

**Meddelande**: "fel: du har inte behörighet att anropa tjänsten. Kontakta administratören för auktorisering. "

**Korrigerande åtgärd**: be administratören att ge dig åtkomst till FarmBeats-distributionen. Detta kan göras genom att göra ett inlägg i RoleAssignment-API: erna eller via Access Control i fönstret **Inställningar** i Accelerator.  

Om du redan har beviljats åtkomst och har det här felet kan du försöka igen genom att uppdatera sidan. Om felet kvarstår kan du lägga till fel meddelandet i ett inlägg i FarmBeats-forumet eller kontakta FarmBeatsSupport@microsoft.com.

![Project-FarmBeats](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator problem  

**Problem**: du har fått ett Accelerator-fel av den obestämda orsaken.

**Meddelande**: "fel: ett okänt fel har inträffat."

**Korrigerande åtgärd**: det här felet uppstår om du lämnar sidan inaktiv för lång. Uppdatera sidan.  

Om felet kvarstår kan du lägga till fel meddelandet i ett inlägg i FarmBeats-forumet eller kontakta FarmBeatsSupport@microsoft.com.

**Problem**: FarmBeats Accelerator visar inte den senaste versionen, även efter att du har uppgraderat FarmBeatsDeployment.

**Korrigerande åtgärd**: det här felet uppstår på grund av beständiga service Worker i webbläsaren. Gör följande:
1. Stäng alla flikar för webbläsare som har en Accelerator öppen och Stäng webbläsarfönstret. 
1. Starta en ny instans av webbläsaren och Läs in Accelerator-URI igen. Den här åtgärden läser in den nya versionen av Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: bilder problem

### <a name="wrong-username-or-password"></a>Felaktigt användar namn eller lösen ord

**Jobb fel meddelande**: "fullständig autentisering krävs för att få åtkomst till den här resursen".

**Korrigerande åtgärd**: 

Gör något av följande:
* Kör installations programmet för att uppgradera Datahub med rätt användar namn och lösen ord.
* Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan för att se om jobbet lyckades.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel Hub: felaktig URL eller plats är inte tillgänglig 

**Jobb fel meddelande**: "Hoppsan, något gick fel. Sidan som du försökte komma åt är (tillfälligt) inte tillgänglig. " 

**Korrigerande åtgärd**:
1. Öppna [Sentinel](https://scihub.copernicus.eu/dhus/) i webbläsaren för att se om webbplatsen är tillgänglig. 
1. Om webbplatsen inte är tillgänglig kontrollerar du om någon brand vägg, företags nätverk eller annan blockerande program vara förhindrar åtkomst till webbplatsen och vidtar sedan nödvändiga steg för att tillåta kontroll-URL: en. 
1. Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan för att se om jobbet lyckades.  

### <a name="sentinel-server-down-for-maintenance"></a>Kontroll Server: nere för underhåll

**Jobb fel meddelande**: "Copernicus öppna Access Hub kommer snart att vara tillbaka! Vi beklagar besväret, men vi utför vissa underhåll för tillfället. Vi kommer att bli online igen snart! " 

**Korrigerande åtgärd**:

Det här problemet kan inträffa om några underhålls aktiviteter utförs på Sentinel-servern.

1. Om ett jobb eller en pipeline Miss lyckas eftersom underhåll utförs, skicka om jobbet efter en stund. 

   Information om planerade eller oplanerade aktiviteter finns i [Copernicus öppna Access Hub nyhets](https://scihub.copernicus.eu/news/) webbplats.  
1. Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan för att se om jobbet lyckades.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: högsta antalet anslutningar har uppnåtts

**Jobb fel meddelande**: "maximalt antal samtidiga flöden som uppnås av användaren\<användar namn >". "

**Betydelse**: om ett jobb Miss lyckas eftersom det maximala antalet anslutningar har uppnåtts, används samma kontroll konto i en annan program distribution.

**Korrigerande åtgärd**: prova något av följande:
* Skapa ett nytt Sentinel-konto och kör sedan installations programmet igen för att uppgradera Datahub med ett nytt kontroll användar namn och lösen ord.  
* Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan för att se om jobbet lyckades.

### <a name="sentinel-server-refused-connection"></a>Sentinel-Server: nekad anslutning 

**Jobb fel meddelande**: "servern nekade anslutning på: http://172.30.175.69:8983/solr/dhus." 

**Korrigerande åtgärd**: det här problemet kan inträffa om några underhålls aktiviteter utförs på Sentinel-servern. 
1. Om ett jobb eller en pipeline Miss lyckas eftersom underhåll utförs, skicka om jobbet efter en stund. 

   Information om planerade eller oplanerade aktiviteter finns i [Copernicus öppna Access Hub nyhets](https://scihub.copernicus.eu/news/) webbplats.  
1. Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan för att se om jobbet lyckades.

## <a name="collect-logs-manually"></a>Samla in loggar manuellt

[Installera och distribuera Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Samla in Azure Data Factory jobb loggar i Datahub
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter resurs gruppen FarmBeats Datahub i **sökrutan.**

    > [!NOTE]
    > Välj den resurs grupp för Datahub som du angav vid FarmBeats-distributionen.

1. På instrument panelen för **resurs gruppen** söker du efter *datahublogs-\** lagrings konto. Sök till exempel efter **datahublogsmvxmq**.  

1. I kolumnen **namn** väljer du lagrings konto för att visa instrument panelen för **lagrings kontot** .
1. I fönstret **datahubblogs\*** väljer du **Öppna i Utforskaren** för att visa det **öppna Azure Storage Explorer** programmet.
1. Välj **BLOB-behållare**i den vänstra rutan och välj sedan **jobb loggar**.
1. I fönstret **jobb-loggar** väljer du **Hämta**.
1. Hämta loggarna till en lokal mapp på datorn.
1. Skicka den hämtade ZIP-filen med e-post till farmbeatssupport@microsoft.com.

    ![Project-FarmBeats](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Samla in Azure Data Factory jobb loggar i Accelerator 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter resurs gruppen FarmBeats Accelerator i **sökrutan.**

    > [!NOTE]
    > Välj den Accelerator-resurs grupp som du angav under FarmBeats-distributionen.

1. På instrument panelen för **resurs gruppen** söker du efter *lagrings\** lagrings kontot. Sök till exempel efter **storagedop4k\*** .
1. Välj lagrings kontot i kolumnen **namn** om du vill visa instrument panelen för **lagrings kontot** .
1. I fönstret **lagrings\*** väljer du **Öppna i Utforskaren** för att öppna Azure Storage Explorer programmet.
1. Välj **BLOB-behållare**i den vänstra rutan och välj sedan **jobb loggar**.
1. I fönstret **jobb-loggar** väljer du **Hämta**.
1. Hämta loggarna till en lokal mapp på datorn.
1. Skicka den hämtade ZIP-filen med e-post till farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Samla in Datahub App Service-loggar

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter resurs gruppen FarmBeats Datahub i **sökrutan.**

    > [!NOTE]
    > Välj den resurs grupp för Datahub som du angav vid FarmBeats-distributionen.

1. I resurs gruppen söker du efter datahublogs- *\** lagrings konto. Sök till exempel efter **fordatahublogsmvxmq\*** .
1. Välj lagrings kontot i kolumnen **namn** om du vill visa instrument panelen för **lagrings kontot** .
1. I fönstret **datahubblogs\*** väljer du **Öppna i Utforskaren** för att öppna Azure Storage Explorer programmet.
1. I det vänstra fönstret väljer du **BLOB-behållare**och väljer sedan **appinsights-logs**.
1. I fönstret **appinsights-logs** väljer du **Hämta**.
1. Hämta loggarna till en lokal mapp på datorn.
1. Skicka den hämtade ZIP-filen med e-post till farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>Samla in Accelerator service-loggar

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter resurs gruppen FarmBeats Accelerator i **sökrutan.**

    > [!NOTE]
    > Välj den FarmBeats Accelerator-resurs grupp som angavs vid FarmBeats-distributionen.

1. Sök efter *lagrings\** lagrings kontot i resurs gruppen. Sök till exempel efter **storagedop4k\*** .
1. Välj lagrings kontot i kolumnen **namn** om du vill visa instrument panelen för **lagrings kontot** .
1. I fönstret **lagrings\*** väljer du **Öppna i Utforskaren** för att öppna Azure Storage Explorer programmet.
1. I det vänstra fönstret väljer du **BLOB-behållare**och väljer sedan **appinsights-logs**.
1. I fönstret **appinsights-logs** väljer du **Hämta**.
1. Hämta loggarna till en lokal mapp på datorn.
1. E-posta den hämtade mappen till farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Kända problem

## <a name="batch-related-issues"></a>Batch-relaterade problem

**Fel meddelande**: "den regionala konto kvoten för batch-konton för den angivna prenumerationen har nåtts."

**Korrigerande åtgärd**: öka kvoten eller ta bort de oanvända batch-kontona och kör distributionen igen.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory (Azure AD)-relaterade problem

**Fel meddelande**: "Det gick inte att uppdatera de nödvändiga inställningarna till Azure AD App d41axx40-xx21-4fbd-8xxf-97Xxx9e2xxc0: otillräckliga behörigheter för att slutföra åtgärden. Se till att inställningarna ovan är korrekt konfigurerade för Azure AD App. "

**Betydelse**: Azure AD App Registration-konfigurationen slutfördes inte korrekt.  

**Korrigerande åtgärd**: be IT-administratören (personen med Läs behörighet för klient organisationen) att använda vårt [skript](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) för att skapa Azure AD-appens registrering. Det här skriptet tar automatiskt hand om konfigurations stegen.

**Fel meddelande**: "Det gick inte att skapa det nya Active Directory programmet\<program namnet\>" i den här klienten: ett annat objekt med samma värde för URI: er för egenskaps identifierare finns redan. "

**Betydelse**: det finns redan en Azure AD App-registrering med samma namn.

**Korrigerande åtgärd**: ta bort den befintliga Azure AD-appens registrering eller Återanvänd den för installation. Om du återanvänder den befintliga Azure AD-appen skickar du program-ID: t och klient hemligheten till installations programmet och distribuerar om.

## <a name="issues-with-the-inputjson-file"></a>Problem med filen indata. JSON

**Fel**: det uppstod ett fel när indata lästes från *indata-JSON* -filen.

**Korrigerande åtgärd**: det här problemet uppstår vanligt vis på grund av ett fel med att ange rätt sökväg eller namn för *indata-JSON* -fil till installations programmet. Gör lämpliga ändringar och försök att distribuera igen.

**Fel**: det uppstod ett fel vid parsning av värden i *indata. JSON* -filen.

**Korrigerande åtgärd**: det här problemet uppstår främst på grund av felaktiga värden i *indata-JSON* -filen. Gör lämpliga ändringar och försök att distribuera igen.

## <a name="high-cpu-usage"></a>Hög processoranvändning

**Fel**: du får en e-postavisering som refererar till en *hög CPU-användnings avisering*. 

**Korrigerande åtgärd**: 
1. Gå till resurs gruppen FarmBeats Datahub.
1. Välj app service.  
1. Gå till sidan för att skala upp [App Service priser](https://azure.microsoft.com/pricing/details/app-service/windows/)och välj sedan en lämplig pris nivå.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem med FarmBeats kan du kontakta vårt [support forum](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
