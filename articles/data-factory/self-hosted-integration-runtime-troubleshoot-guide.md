---
title: Felsöka integration runtime med egen värd i Azure Data Factory
description: Lär dig hur du felsöker problem med integration runtime med egen värd i Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
ms.openlocfilehash: 93c35828444ec93a974769ed3a2f1981c0ec4368
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96013468"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Felsöka integration runtime med egen värd

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för integration runtime med egen värd i Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Samla in IR-loggar med egen värd från Azure Data Factory

Azure Data Factory stöder visning och överföring av fel loggar för misslyckade aktiviteter som körs på IR/delad IR/delad IR. Du kan följa stegen nedan för att hämta fel rapportens ID och sedan skriva in rapport-ID: t för att hitta relaterade kända problem.

1. Gå till sidan **aktivitets körningar** .

1. Under kolumnen **fel** klickar du på knappen nedan.

    ![Sidan aktivitets körningar](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Du kommer att se relaterade loggar för körningen av misslyckad aktivitet. Klicka på **skicka loggar** om du vill ha mer hjälp.

    ![Skicka loggar](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Du kan välja vilka loggar du vill skicka. För *IR med egen värd* kan du överföra loggar relaterade till misslyckad aktivitet eller alla loggar på IR-noden med egen värd. För *delad IR* kan du bara ladda upp loggar som är relaterade till misslyckad aktivitet.

    ![Välj loggar](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. När loggarna laddas upp ska du behålla en post med rapport-ID om du behöver hjälp med att lösa problemet.

    ![Ladda upp loggar](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Logg visning och uppladdning av begär Anden körs på alla online-IR-instanser online. Kontrol lera att alla egna IR-instanser är online om det finns saknade loggar som saknas. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Allmänt fel eller fel i lokalt installerad IR

### <a name="out-of-memory-issue"></a>Slut på minnes problem

#### <a name="symptoms"></a>Symtom

Problemet "OutOfMemoryException" inträffar när du försöker köra en söknings aktivitet med länkad IR eller lokal IR.

#### <a name="cause"></a>Orsak

Ny aktivitet kan mötas med OOM-problemet (OutOfMemory) om IR-datorn har en hög minnes användning för tillfället. Problemet kan orsakas av en stor skala för körning av samtidiga aktiviteter och felet är avsiktligt.

#### <a name="resolution"></a>Lösning

Kontrol lera resursanvändningen och körningen av samtidiga aktiviteter på IR-noden. Justera den interna och Utlös ande tiden för aktivitets körningar för att undvika för mycket körning på samma IR-nod på samma tid.


### <a name="tlsssl-certificate-issue"></a>TLS/SSL-certifikatproblem

#### <a name="symptoms"></a>Symtom

När du försöker aktivera TLS/SSL-certifikat (avancerat) från **Konfigurationshanterare för lokalt installerad IR** -> **Fjärråtkomst från intranät**, efter att ha valt TLS/SSL-certifikat, visas följande fel:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

I ovanstående fall använder användaren certifikat med ”microsoft.com” som sista objekt.

#### <a name="cause"></a>Orsak

Det här är ett känt problem i WCF: WCF TLS/SSL-valideringen kontrollerar endast senaste DNSName i SAN. 

#### <a name="resolution"></a>Lösning

Jokerteckencertifikat stöds i Azure Data Factory v2 – lokalt installerad IR. Det här problemet beror vanligtvis på att SSL-certifikatet inte är korrekt. Senaste DNSName i SAN ska vara giltigt. Följ stegen nedan för att kontrollera det. 
1.  Öppna hanterings konsolen, markera både *ämne* och *alternativt ämnes namn* i certifikat informationen. I ovanstående fall är till exempel det sista objektet i *Alternativt namn för certifikat mottagare*, som är "DNS-namn = Microsoft.com.com", inte giltigt.
2.  Kontakta företaget för certifikat utfärdare för att ta bort fel DNS-namn.

### <a name="concurrent-jobs-limit-issue"></a>Problem med gräns för samtidiga jobb

#### <a name="symptoms"></a>Symtom

När du försöker öka gränsen för samtidiga jobb från Azure Data Factory-gränssnittet, låser det sig som *uppdaterar* under lång tid.
Det maximala värdet för samtidiga jobb har angetts till 24 och du vill öka antalet så att jobben kan köras snabbare. Det minsta värde som du kan ange är 3 och det högsta värdet som du kan ange är 32. Du har ökat värdet från 24 till 32 och klickar på knappen *Uppdatera* i det användar gränssnitt som det har fastnat i *uppdateringen* som du ser nedan. Efter uppdateringen såg kunden fortfarande värdet 24 och det uppdaterades aldrig till 32.

![Uppdaterar status](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Orsak

Det finns en begränsning för inställningen eftersom värdet beror på datorns logicCore och minne, men du kan justera det till ett mindre värde, till exempel 24, och se resultatet.

> [!TIP] 
> - Mer information om vad Logic Core-antalet är och hur du hittar vår dators Logic Core-antal finns i [den här artikeln](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Mer information om hur du beräknar matematik. log finns i [den här artikeln](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problem med HA SSL-certifikat för lokalt installerad IR

#### <a name="symptoms"></a>Symtom

En arbetsnod för lokalt installerad IR rapporterade felet nedan:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Orsak

När vi hanterar fall som rör SSL/TLS-handskakning kan vi stöta på problem som rör verifiering av certifikatkedjan. 

#### <a name="resolution"></a>Lösning

- Här är ett snabbt och intuitivt sätt att felsöka X. 509-certifikat kedjans build-fel.
 
    1. Exportera certifikatet, som måste verifieras. Gå till Hantera datorcertifikat och leta upp det certifikat som du vill kontrollera och högerklicka på **Alla uppgifter** -> **Exportera**.
    
        ![Exportera uppgifter](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Kopiera det exporterade certifikatet till klient datorn. 
    3. Kör kommandot nedan i CMD på klientsidan. Kontrol lera att du har ersatt under *\<certificate path>* och *\<output txt file path>* plats hållare med relaterade sökvägar.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Exempel:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Kontrollera om det finns något fel i txt-utdatafilen. Du hittar felsammanfattningen i slutet av txt-filen.

        Exempel: 

        ![Fel Sammanfattning](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Om du inte ser något fel i slutet av logg filen som visas nedan, kan du tänka på att certifikat kedjan har skapats på klient datorn.
        
        ![Inget fel i logg filen](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Om det finns AIA, CDP och OCSP konfigurerat i certifikat filen. Vi kan kontrol lera det på ett mer intuitivt sätt.
 
    1. Du kan hämta den här informationen genom att kontrol lera information om ett certifikat.
    
        ![Certifikat information](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Kör kommandot nedan. Kontrol lera att du har ersatt *\<certificate path>* plats hållaren med en relaterad sökväg till certifikatet.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Därefter öppnas **URL-hämtningsverktyget**. Du kan verifiera certifikat från AIA, CDP och OCSP genom att klicka på knappen **Hämta**.

        ![Hämtnings knapp](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Certifikatkedjan kan skapas om certifikatet från AIA är ”verifierat” och certifikatet från CDP eller OCSP är ”verifierat”.

        Om du ser ett fel när du hämtar AIA eller CDP kan du samarbeta med nätverksteamet för att förbereda klientdatorn för att ansluta till mål-URL:en. Det är tillräckligt om antingen http-sökvägen eller LDAP-sökvägen kan verifieras.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Lokalt installerad IR kunde inte läsa in filen eller sammansättningen

#### <a name="symptoms"></a>Symtom

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Exempel: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Orsak

Om du tar Process Monitor kan du se följande resultat:

[![Process övervakare](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Du kan ange filter så som visas i skärm bilden nedan.
> Det meddelar oss att dll- **systemet. ValueTuple** inte finns i GAC-relaterade mappar, eller i *C:\Program Files\Microsoft integration Runtime\4.0\Gateway* eller i *c:\Program Files\Microsoft integration Runtime\4.0\Shared* Folder.
> I princip kommer den att läsa in dll-filen från *GAC*-mappen först och sedan från *Delas* och slutligen från mappen *Gateway*. Därför kan du placera dll-filen på valfri sökväg som kan vara till hjälp.

![Konfigurera filter](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Lösning

Du kan se att **System.ValueTuple.dll** finns i *C:\Program Files\Microsoft integration Runtime\4.0\Gateway\DataScan* -mappen. Kopiera **System.ValueTuple.dll** till mappen *c:\Program\Microsoft integration Runtime\4.0\Gateway* för att lösa problemet.

Du kan använda samma metod för att lösa andra problem med en saknad fil eller sammansättning.

#### <a name="more-information"></a>Mer information

Anledningen till att du ser System.ValueTuple.dll under *%windir%\Microsoft.NET\assembly* och *%windir%\assembly* är att det är ett .net-beteende. 

Från felet nedan kan du tydligt se sammansättnings *systemet ValueTuple.* Det här problemet uppstår när programmet försöker kontrol lera sammansättnings *System.ValueTuple.dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Mer information om GAC finns i [den här artikeln](/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Så här granskar du nyckel för lokalt installerad IR som saknas

#### <a name="symptoms"></a>Symtom

Den egna värdbaserade integreringskörningen kopplas plötsligt från utan nyckel, medan felmeddelandet nedan visas i händelseloggen: `Authentication Key is not assigned yet`

![Autentiseringsnyckel saknas](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Orsak

- Noden för lokalt installerad IR eller logisk lokalt installerad IR i portalen tas bort.
- En ren avinstallation görs.

#### <a name="resolution"></a>Lösning

Om inget av ovanstående orsaker gäller kan du gå till mappen: *%programdata%\Microsoft\Data Transfer\DataManagementGateway* och kontrol lera om filen med namnet **konfigurationer** har tagits bort. Om den är borttagen följer du anvisningarna [här](https://www.netwrix.com/how_to_detect_who_deleted_file.html) för att se efter vem som tagit bort filen.

![Kontrol lera konfigurations filen](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Det går inte att använda lokalt installerad IR för att överbrygga två lokala datalager

#### <a name="symptoms"></a>Symtom

När du har skapat lokalt installerad IR för både käll- och måldatalager, behöver du ansluta dina två IR för att slutföra en kopia. Om data butikerna har kon figurer ATS i olika virtuella nätverk, eller om de inte kan förstå Gateway-mekanismen, kommer du att trycka på fel som: *det går inte att hitta driv rutinen för källan i målet IR*. *källan kan inte nås av mål-IR*.
 
#### <a name="cause"></a>Orsak

Lokalt installerad IR är utformad som en central nod i en kopieringsaktivitet, inte en klientagent som behöver installeras för varje datalager.
 
I ovanstående fall bör den länkade tjänsten för varje datalager skapas med samma IR, och IR bör kunna komma åt båda datalagren via nätverk. Oavsett om IR installeras med källdatalager, måldatalager eller på en tredje dator, gäller att om två länkade tjänster har skapats med olika IR, men som används i samma kopieringsaktivitet, används mål-IR, och drivrutinerna för båda datalagren måste installeras på IR-datorn för målet.

#### <a name="resolution"></a>Lösning

Installera drivrutiner för både källa och mål på mål-IR och se till att den kan komma åt källdatalagret.
 
Om trafiken inte kan passera genom nätverket mellan två datalager (till exempel att de har konfigurerats i två virtuella nätverk) kan du kanske inte slutföra kopieringen i en aktivitet även med IR installerad. I så fall kan du skapa två kopieringsaktiviteter med två IR, var och en i ett virtuellt nätverk: 1 IR för att kopiera från datalager 1 till Azure Blob Storage, och en annan för att kopiera från Azure Blob Storage till datalager 2. Detta kan simulera kravet att använda IR för att skapa en brygga som ansluter två frånkopplade datalager.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Problem med synkronisering av autentiseringsuppgifter medför att autentiseringsuppgiften förloras från HA

#### <a name="symptoms"></a>Symtom

Autentiseringsuppgifterna för datakällan ”XXXXXXXXXX” tas bort från den aktuella Integration Runtime-noden med nyttolasten ”när du tar bort länktjänsten på Azure Portal, eller uppgifter har fel nyttolast, ska du på nytt skapa en ny länktjänst med dina autentiseringsuppgifter”.

#### <a name="cause"></a>Orsak

Din lokalt installerade IR har skapats i HA-läge med två noder, men de är inte i ett synkroniseringstillstånd för autentiseringsuppgifter, vilket innebär att autentiseringsuppgifterna som lagras i dispatcher-noden inte synkroniseras till andra arbetsnoder. Om redundansväxling sker från dispatcher-noden till arbetsnoden, men autentiseringsuppgifterna bara fanns i den tidigare dispatcher-noden, misslyckas uppgiften vid försök att få åtkomst till autentiseringsuppgifter och du kommer att få ovanstående fel.

#### <a name="resolution"></a>Lösning

Det enda sättet att undvika det här problemet är att se till att två noder är i synkroniseringsläge för autentiseringsuppgifter. Annars måste du ange autentiseringsuppgifter igen för en ny dispatcher.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Det går inte att välja certifikatet eftersom privat nyckel saknas

#### <a name="symptoms"></a>Symtom

1.  Importera en PFX-fil till certifikatarkivet.
2.  När du väljer certifikatet via IR Configuration Manager-gränssnittet visas följande fel:

    ![Privat nyckel saknas](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Orsak

- Användarkontot har låg behörighet och kan inte komma åt den privata nyckeln.
- Certifikatet genererades som signatur, men inte som nyckelutbyte.

#### <a name="resolution"></a>Lösning

1.  Använd ett konto med privilegier som kan komma åt den privata nyckeln för att hantera användargränssnittet.
2.  Kör följande kommando för att importera certifikatet:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>IR-installation med egen värd

### <a name="the-integration-runtime-registration-error"></a>Integration Runtime registrerings fel 

#### <a name="symptoms"></a>Symtom

Ibland vill vi köra IR med egen värd i ett annat konto av följande orsaker:
- Företags principen tillåter inte tjänst kontot.
- Viss autentisering krävs.

När du har ändrat tjänst kontot i panelen tjänst kanske du upptäcker att Integration Runtime slutar fungera.

![IR-registrerings fel](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Orsak

Det finns många resurser som endast beviljas till tjänst kontot. När du ändrar tjänst kontot till ett annat konto förblir behörigheten för alla beroende resurser samma.

#### <a name="resolution"></a>Lösning

Gå till händelse loggen för Integration Runtime för att kontrol lera felet.

![IR-händelseloggen](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Om felet visas som ovan *UnauthorizedAccessException* följer du anvisningarna nedan:


1. Kontrol lera inloggnings tjänst kontot för *dia Host service* på panelen Windows-tjänst.

    ![Konto för inloggnings tjänst](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Kontrol lera om kontot för inloggnings tjänsten har behörigheten R/W för mappen: *%programdata%\Microsoft\DataTransfer\DataManagementGateway*.

    - Om tjänst inloggnings kontot inte har ändrats bör som standard ha behörigheten R/W.

        ![Tjänst behörighet](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Om du har ändrat tjänst inloggnings kontot följer du stegen nedan för att åtgärda problemet:
        1. Rensa den aktuella IR-filen med egen värd.
        1. Installera IR-BITS med egen värd.
        1. Följ anvisningarna nedan om du vill ändra tjänst kontot: 
            1. Gå till installationsmappen för den egna värdbaserade IR-filen och växla till mappen: *Microsoft integration Runtime\4.0\Shared*.
            1. Starta en kommando rad med utökade privilegier. Ersätt *\<user>* och *\<password>* med ditt eget användar namn och lösen ord och kör sedan följande kommando:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Om du vill ändra till LocalSystem-konto måste du ange ett korrekt format för det här kontot. Nedan visas ett exempel på rätt format:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Använd **inte** det format som visas nedan:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. För alternativ, eftersom det lokala systemet har högre privilegier än administratör, kan du också ändra det direkt i "tjänster".
            1. Du kan använda en lokal/domän användare för inloggnings kontot för IR-tjänsten.            
        1. Registrera Integration Runtime.

Om felet visas som: *Det gick inte att starta tjänsten integration runtime tjänsten (dia Host service). Kontrol lera att du har behörighet att starta system tjänster*, följ instruktionerna nedan:

1. Kontrol lera inloggnings tjänst kontot för *dia Host service* på panelen Windows-tjänst.
   
    ![Konto för inloggnings tjänst](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Kontrol lera om inloggnings tjänst kontot har behörigheten **Logga in som tjänst** för att starta Windows-tjänsten:

    ![Logga in som tjänst](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Mer information

Om inget av ovanstående matchnings mönster gäller i ditt fall försöker du samla in under Windows-händelseloggar: 
- Program-och tjänst loggar – > Integration Runtime
- Windows-loggar – > program

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Det gick inte att hitta knappen Registrera för att registrera en IR med egen värd    

#### <a name="symptoms"></a>Symtom

Det gick inte att hitta knappen **Registrera** i Configuration Manager UI när du registrerade en lokal IR.

![Knappen ingen registrering](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Orsak

Eftersom lanseringen av *Integration Runtime 3,0* har du tagit bort **register** knappen på en befintlig integration runtime-nod för att aktivera en renare och säkrare miljö. Om en nod har registrerats på någon Integration Runtime (oavsett om den är online eller inte), för att omregistrera den till en annan Integration Runtime, måste du avinstallera den tidigare noden och sedan installera och registrera noden.

#### <a name="resolution"></a>Lösning

1. Gå till kontroll panelen för att avinstallera den befintliga Integration Runtime.

    > [!IMPORTANT] 
    > I processen nedan väljer du Ja. Behåll inte data under avinstallations processen.

    ![Ta bort data](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Om du inte har installations-MSI för integration runtime går du till [Download Center](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) för att hämta den senaste integration Runtime.
1. Installera MSI och registrera Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Det går inte att registrera en lokalt installerad IR på grund av localhost    

#### <a name="symptoms"></a>Symtom

Det går inte att registrera IR med egen värd på en ny dator när get_LoopbackIpOrName.

**Fel sökning:** Ett körnings fel har inträffat.
Typ initieraren för "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" utlöste ett undantag.
Ett oåterkalleligt fel inträffade under en databas sökning.
 
**Undantags information:** System. TypeInitializationException: typ initieraren för "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" utlöste ett undantag. ---> system .net. Sockets. SocketException: ett oåterkalleligt fel inträffade under en databas sökning på system .net. DNS. GetAddrInfo (sträng namn).

#### <a name="cause"></a>Orsak

Problemet uppstår vanligt vis när du löser in localhost.

#### <a name="resolution"></a>Lösning

Använd localhost 127.0.0.1 som värd för filen och Lös problemet.


### <a name="self-hosted-setup-failed"></a>Installation av egen värd misslyckades    

#### <a name="symptoms"></a>Symtom

Det går inte att avinstallera en befintlig IR eller installera en ny IR eller uppgradera en befintlig IR till en ny IR.

#### <a name="cause"></a>Orsak

Installationen beror på Windows Installer tjänsten. Det finns varianter av orsaker som kan orsaka installations problem:
- Det finns inte tillräckligt med disk utrymme
- Saknar behörighet
- NT-tjänsten är låst av någon anledning
- PROCESSOR användningen är för hög
- MSI-filen finns på en långsam nätverks plats
- Vissa systemfiler eller register har berörings av oavsiktligt


### <a name="ir-service-account-failed-to-fetch-certificate-access"></a>Kontot för IR-tjänsten kunde inte hämta certifikat åtkomst

#### <a name="symptoms"></a>Symtom

När du installerar IR med egen värd via Microsoft Integration Runtime Configuration Manager genereras ett certifikat med en betrodd certifikat utfärdare. Certifikatet kunde inte användas för att kryptera kommunikationen mellan två noder. 

Fel informationen visas som nedan: 

`Failed to change Intranet communication encryption mode: Failed to grant Integration Runtime service account the access of to the certificate 'XXXXXXXXXX'. Error code 103`

![Det gick inte att bevilja åtkomst till IR-tjänstkontot](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Orsak

Certifikatet använder KSP (Key Storage Provider), vilket inte stöds ännu. SHIR har bara stöd för CSP-certifikat (Cryptographic Service Provider) hittills.

#### <a name="resolution"></a>Lösning

CSP-certifikat rekommenderas för det här fallet.

**Lösning 1:** Använd kommandot nedan för att importera certifikatet:

```
Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx 
```

![Använd certutil](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Lösning 2:** Konvertering av certifikat:

openssl PKCS12-in .\xxxx.pfx. \ xxxx_new. pem-Password pass:*\<EnterPassword>*

openssl PKCS12-export-in. \ xxxx_new. pem-out xxxx_new. pfx

Före och efter konvertering:

![Innan certifikat ändringen](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Efter ändring av certifikat](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)


## <a name="self-hosted-ir-connectivity-issues"></a>Problem med IR-anslutning via egen värd

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Integration runtime med egen värd kan inte ansluta till moln tjänsten

#### <a name="symptoms"></a>Symtom

![Problem med IR-anslutning via egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Orsak 

Den egna värdbaserade integrerings körningen kan inte ansluta till Data Factory tjänsten (backend). Det här problemet orsakas vanligt vis av nätverks inställningar i brand väggen.

#### <a name="resolution"></a>Lösning

1. Kontrol lera om integration runtime-tjänsten körs.
    
   ![Status för IR-tjänsten med egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Om tjänsten körs går du vidare till steg 3.

1. Om ingen proxy har kon figurer ATS på den lokala integrerings körningen (vilket är standardinställningen) kör du följande PowerShell-kommando på den dator där den lokala integrerings körningen är installerad:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > URL: en för tjänsten kan variera beroende på din Data Factory plats. Du hittar tjänst-URL: en **ADF UI** under  >  **Connections**  >  **integrerings körningar** i ADF UI  >  **-anslutningar redigera egen värd för IR**  >  **Nodes**  >  **-noder Visa tjänst-URL: er**.
            
    Följande är det förväntade svaret:
            
    ![PowerShell-kommando svar](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Om du inte får det förväntade svaret använder du någon av följande metoder som passar din situation:
            
    * Om du får ett meddelande om att det inte gick att lösa problemet, finns det ett Domain Name System (DNS-problem). Kontakta nätverks teamet för att åtgärda problemet.
    * Om du får ett meddelande om att SSL/TLS-certifikat inte är betrott, kontrollerar du om certifikatet för https://wu2.frontend.clouddatahub.net/ är betrott på datorn och installerar sedan det offentliga certifikatet med hjälp av certifikat hanteraren. Den här åtgärden bör minimera problemet.
    * Gå till **Windows**  >  **logg boken (loggar)**  >  **program-och tjänst loggar**  >  **integration runtime** och kontrol lera eventuella problem som orsakas av DNS, brand Väggs regel eller företagets nätverks inställningar. (Om du upptäcker ett sådant problem stänger du anslutningen.) Eftersom alla företag har anpassade nätverks inställningar kan du kontakta nätverks teamet för att felsöka problemen.

1. Om proxyn har kon figurer ATS på integration runtime med egen värd, kontrollerar du att proxyservern kan komma åt tjänstens slut punkt. Ett exempel kommando finns i [PowerShell, webb förfrågningar och proxyservrar](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Följande är det förväntade svaret:
            
![PowerShell-kommando svar 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Synpunkter på proxy:
> *    Kontrol lera om proxyservern måste placeras i listan Betrodda mottagare. Om så är fallet ser du till att [dessa domäner](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) finns i listan Betrodda mottagare.
> *    Kontrol lera om TLS/SSL-certifikatet "wu2.frontend.clouddatahub.net/" är betrott på proxyservern.
> *    Om du använder Active Directory autentisering på proxyn ändrar du tjänst kontot till det användar konto som har åtkomst till proxyn som "Integration Runtime tjänst".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Fel meddelande: egen värd för integration runtime-noden/logiska SHIR är i ett inaktivt/"kör (begränsat)" tillstånd

#### <a name="cause"></a>Orsak 

Den egna värdbaserade integrerade runtime-noden kan ha en **inaktiv** status, som visas på följande skärm bild:

![Inaktiv Self-Hosted IR-nod](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Det här problemet uppstår när noder inte kan kommunicera med varandra.

#### <a name="resolution"></a>Lösning

1. Logga in på den nod-värdbaserade virtuella datorn. Under **program-och tjänst loggar**  >  **integration runtime** öppnar du Loggboken och filtrerar alla fel loggar.

1. Kontrol lera om fel loggen innehåller följande fel: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Om du ser det här felet kör du följande på en kommando rad: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Om du får följande fel meddelande kan du kontakta IT-avdelningen för att få hjälp med att åtgärda problemet. När du har lyckats använda Telnet kontaktar du Microsoft Support om du fortfarande har problem med status för integrerande-noden.
        
   ![Kommando rads fel](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Kontrol lera om fel loggen innehåller följande:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Lös problemet genom att prova en eller båda av följande metoder:
    - Lägg till alla noder i samma domän.
    - Lägg till IP-adressen som värd mappning i alla värdar för den värdbaserade virtuella datorn.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Anslutnings problem mellan IR med egen värd och Data Factory eller egen IR och data källa/mottagare

För att felsöka problem med nätverks anslutningen bör du veta hur du samlar in nätverks spårningen, förstår hur du använder det och [analyserar Netmon-spårningen](#how-to-analyze-netmon-trace) innan du använder Netmon-verktygen i reala fall från egen värd-IR.

#### <a name="symptoms"></a>Symtom

Ibland kan vi vid fel sökning av anslutnings problem, till exempel under en mellan egen värd-IR och Data Factory: 

![HTTP-begäran misslyckades](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Eller en mellan egen värd-IR och data källa/mottagare kommer vi att stöta på följande fel:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Lösning:

När du påträffar ovanstående problem kan du läsa följande instruktioner för att felsöka ytterligare:

Ta Netmon-spårningen och analysera vidare.
- För det första kan du ange filtret för att se eventuell återställning där från servern till klient sidan. I följande exempel kan du se server sidan Data Factory-Server.

    ![Data Factory-Server](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- När du hämtar återställnings paketet kan du hitta konversationen genom att följa TCP.

    ![Hitta konversation](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Sedan kan du hämta konverteringen mellan klienten och Data Factory servern nedan genom att ta bort filtret.

    ![Hämta konversation](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Baserat på den insamlade Netmon-spårningen kan vi se att det totala TTL-värdet (TimeToLive) är 64. Enligt standard- **TTL-och hopp gräns värden** som anges i [den här artikeln](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (som extraheras nedan) kan vi se att det är det Linux-system som återställer paketet och orsakar från koppling.

    Standardvärden för TTL och hopp varierar mellan olika operativ system, här är standardinställningarna för några få:
    - Linux kernel 2,4 (circa 2001): 255 för TCP, UDP och ICMP
    - Linux kernel 4,10 (2015): 64 för TCP, UDP och ICMP
    - Windows XP (2001): 128 för TCP, UDP och ICMP
    - Windows 10 (2015): 128 för TCP, UDP och ICMP
    - Windows Server 2008:128 för TCP, UDP och ICMP
    - Windows Server 2019 (2018): 128 för TCP, UDP och ICMP
    - macOS (2001): 64 för TCP, UDP och ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Den visas dock som 61 i stället för 64 i exemplet ovan, eftersom när nätverks paketet når målet måste det gå igenom olika hopp som routrar/nätverks enheter. Antalet routrar/nätverks enheter kommer att dras av i det slutliga TTL-värdet.
    I det här fallet kan vi se att återställningen kan skickas från Linux-systemet med TTL 64.

- Vi måste kontrol lera det fjärde hoppet från egen värd-IR för att kontrol lera var återställnings enheten kan komma från.
 
    *Nätverks paket från Linux system A med TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 lokal IR*

- I ideal fallet är TTL 128, vilket innebär att Windows system kör vår Data Factory. Som du ser i exemplet nedan, *128 – 107 = 21 hopp*, vilket innebär att 21 hopp för paketet har skickats från Data Factory till IR med egen värd under TCP 3-handskakningen.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Därför måste du engagera nätverks teamet för att kontrol lera vad det fjärde hoppet är från IR med egen värd. Om det är en brand vägg som Linux-system kan du kontrol lera eventuella loggar på varför enheten återställer paketet efter TCP 3-handskakningen. Men om du inte är säker på var du ska göra undersökningen kan du försöka hämta Netmon-spårningen från IR och brand vägg med egen värd under den problematiska tiden för att ta reda på vilken enhet som kan återställa det här paketet och orsaka från koppling. I så fall måste du också engagera ditt nätverks team för att gå vidare.

### <a name="how-to-analyze-netmon-trace"></a>Analysera Netmon-spårning

> [!NOTE] 
> Nedan följer en instruktion för Netmon-spårning. Eftersom Netmon-spårningen för närvarande inte stöds kan du utnyttja wireshark som samma.

När du försöker använda Telnet **8.8.8.8 888** med Netmon-spårningen, ska du se följande spårning:

![Netmon-spårning 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Netmon spårning 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Det innebär att du inte kan göra TCP-anslutning till **8.8.8.8** -Server sidan baserat på port **888**, så att du ser två **SynReTransmit** ytterligare paket där. Eftersom källan **Self-HOST2** inte kunde ansluta till **8.8.8.8** i det första paketet, kommer den att fortsätta att upprätta anslutningen.

> [!TIP]
> - Du kan klicka på **load filter**  ->  **standard filter**  ->  **adresser**  ->  **IPv4-adresser**.
> - Ange **IPv4. address = = 8.8.8.8** som filter och klicka på **tillämpa**. Därefter kan du bara se kommunikationen från den lokala datorn till mål- **8.8.8.8**.

![Filtrera adresser 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Filtrera adresser 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Nedan visas ett exempel på hur ett bra scenario skulle se ut. 

- Om Telnet **8.8.8.8 53** fungerar utan problem kan du se att TCP 3 hand skakning sker och att sessionen slutförs med TCP 4-handskakningen.

    ![Bästa scenario exempel 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![exempel på bästa scenario 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Baserat på ovanstående TCP 3-handskakning kan du se arbets flödet nedan:

    ![Arbets flöde för TCP 3 hand skakning](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- TCP 4-handskakningen för att avsluta sessionen och arbets flödet visas enligt följande:

    ![TCP 4-handskakning](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Arbets flöde för TCP 4 hand skakning](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>Ta emot e-post för att uppdatera nätverks konfigurationen för att tillåta kommunikation med nya IP-adresser

#### <a name="email-notification-from-microsoft"></a>E-postavisering från Microsoft

Du kan få e-postaviseringar via e-post, som rekommenderar att du uppdaterar nätverks konfigurationen för att tillåta kommunikation med nya IP-adresser för Azure Data Factory med 8 november 2020:

   ![E-postavisering](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>Så här avgör du om du påverkas av det här meddelandet

Detta meddelande påverkar följande scenarier:
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>Scenario 1: utgående kommunikation från egen värd Integration Runtime som körs lokalt bakom företags brand väggen
Så här avgör du om du påverkas:
- Du påverkas inte om du definierar brand Väggs regler baserat på FQDN-namn med hjälp av metoden som beskrivs i det här dokumentet: [brand Väggs konfiguration och inställningar för att konfigurera för IP-adress](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).
- Du kommer dock att påverkas om du uttryckligen aktiverar listan över tillåtna för utgående IP-adresser i företags brand väggen.

Åtgärd som ska vidtas om du påverkas: meddela nätverks infrastrukturens team att du vill uppdatera nätverks konfigurationen så att den använder de senaste Data Factory IP-adresserna senast den 8 november 2020.  Hämta de senaste IP-adresserna genom att gå till [service Tags IP-intervall nedladdnings länk](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>Scenario 2: utgående kommunikation från egen värd Integration Runtime som körs på en virtuell Azure-dator i ett kund hanterat Azure Virtual Network
Så här avgör du om du påverkas:
- Kontrol lera om du har regler för utgående NSG i ditt privata nätverk som innehåller Integration Runtime med egen värd. Om det inte finns några utgående begränsningar påverkas ingen påverkan.
- Om du har regler för utgående trafik kontrollerar du om du använder service tag eller inte. Om du använder service tag-koden behöver du inte ändra eller lägga till något som nya IP-adressintervall är under befintligt service tag. 
 ![Mål kontroll](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- Du kommer dock att påverkas om du uttryckligen aktiverar listan över tillåtna för utgående IP-adresser i NSG-regler på det virtuella Azure-nätverket.

Åtgärd som ska vidtas om du påverkas: meddela din nätverks infrastrukturs team att uppdatera NSG-regler i din Azure Virtual Network-konfiguration för att använda de senaste Data Factory IP-adresserna senast den 8 november 2020.  Hämta de senaste IP-adresserna genom att gå till [service Tags IP-intervall nedladdnings länk](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>Scenario 3: utgående kommunikation från SSIS Integration Runtime i kundens hanterade Azure Virtual Network
- Kontrol lera om du har utgående NSG-regler i ditt privata nätverk som innehåller SSIS Integration Runtime. Om det inte finns några utgående begränsningar påverkas ingen påverkan.
- Om du har regler för utgående trafik kontrollerar du om du använder service tag eller inte. Om du använder service tag-koden behöver du inte ändra eller lägga till något som nya IP-adressintervall är under befintligt service tag.
- Du kommer dock att påverkas om du uttryckligen aktiverar listan över tillåtna för utgående IP-adresser på din NSG-regel på det virtuella Azure-nätverket.

Åtgärd som ska vidtas om du påverkas: meddela din nätverks infrastrukturs team att uppdatera NSG-regler i din Azure Virtual Network-konfiguration för att använda de senaste Data Factory IP-adresserna senast den 8 november 2020.  Hämta de senaste IP-adresserna genom att gå till [service Tags IP-intervall nedladdnings länk](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Det gick inte att upprätta en förtroende relation för den säkra SSLTLS-kanalen 

#### <a name="symptoms"></a>Symtom

IR med egen värd kan inte ansluta till ADF-tjänsten.

Genom att kontrol lera händelse loggen för SHIR eller klient meddelande loggarna i CustomLogEvent-tabellen, hittas följande fel meddelande:

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

Kontrol lera Server certifikatet för ADF-tjänsten:

Det enklaste sättet är att öppna ADF-tjänstens URL i webbläsare, till exempel öppna https://eu.frontend.clouddatahub.net/ på den dator där SHIR är installerat och sedan Visa Server certifikat informationen:

  ![Kontrol lera Server certifikatet för ADF-tjänsten](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Kontrol lera Server certifikat Sök väg](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Orsak

Två möjliga orsaker till det här problemet:

- Rot certifikat utfärdaren för ADF service Server-certifikatet är inte betrott på den dator där SHIR är installerat. 
- Du använder proxy i din miljö och Server certifikatet för ADF-tjänsten ersätts av proxyservern, medan det ersatta Server certifikatet inte är betrott av den dator där SHIR är installerat.

#### <a name="resolution"></a>Lösning

- För orsak 1 kontrollerar du att ADF-servercertifikatet och certifikat kedjan är betrodd av den dator där SHIR är installerat.
- För orsak 2 kan du antingen lita på den ersatta rot certifikat utfärdaren på SHIR dator eller konfigurera proxyn så att den inte ersätter ADF-servercertifikat.

Se [den här artikeln](https://docs.microsoft.com/skype-sdk/sdn/articles/installing-the-trusted-root-certificate) för information om att lita på ett certifikat i Windows.

#### <a name="additional-info"></a>Ytterligare info
Vi håller på att lansera ett nytt SSL-certifikat, som är signerat från DigiCert, kontrol lera om DigiCert globala rot G2 är i den betrodda rot certifikat utfärdaren.

  ![DigiCert global root G2](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Om inte, laddar du ned det [här](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Delning av lokalt installerad integrationskörning (IR)

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Det finns inte stöd för att dela egen värd-IR från en annan klient 

#### <a name="symptoms"></a>Symtom

Du kan märka andra data fabriker (på olika klienter) vid försök att dela den lokala IR-filen från Azure Data Factory-gränssnittet, men kan inte dela den lokala IR-filen över data fabriker som finns på olika klienter.

#### <a name="cause"></a>Orsak

IR med egen värd kan inte delas mellan klienter.


## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp med fel sökning kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestanda guide för att mappa data flöden](concepts-data-flow-performance.md)