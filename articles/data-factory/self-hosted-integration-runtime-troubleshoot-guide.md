---
title: Felsöka integration runtime med egen värd i Azure Data Factory
description: Lär dig hur du felsöker problem med integration runtime med egen värd i Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/24/2020
ms.author: abnarain
ms.openlocfilehash: e77d621d5699c434e691de0a523e58e49166d8d6
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85315038"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Felsöka integration runtime med egen värd

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för integration runtime med egen värd i Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Vanliga fel och lösningar

### <a name="error-message"></a>Felmeddelande: 

`Self-hosted integration runtime can't connect to cloud service`

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
   > URL: en för tjänsten kan variera beroende på din Data Factory plats. Du hittar tjänst-URL: en **ADF UI**under  >  **Connections**  >  **integrerings körningar**i ADF UI  >  **-anslutningar redigera egen värd för IR**  >  **Nodes**  >  **-noder Visa tjänst-URL: er**.
            
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
> *    Kontrol lera om proxyservern måste placeras i listan Betrodda mottagare. Om så är fallet ser du till att [dessa domäner](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) finns i listan Betrodda mottagare.
> *    Kontrol lera om TLS/SSL-certifikatet "wu2.frontend.clouddatahub.net/" är betrott på proxyservern.
> *    Om du använder Active Directory autentisering på proxyn ändrar du tjänst kontot till det användar konto som har åtkomst till proxyn som "Integration Runtime tjänst".

### <a name="error-message"></a>Felmeddelande: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>Orsak 

Den egna värdbaserade integrerade runtime-noden kan ha en **inaktiv** status, som visas på följande skärm bild:

![Inaktiv IR-nod med egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Det här problemet uppstår när noder inte kan kommunicera med varandra.

#### <a name="resolution"></a>Lösning

1. Logga in på den nod-värdbaserade virtuella datorn. Under **program-och tjänst loggar**  >  **integration runtime**öppnar du Loggboken och filtrerar alla fel loggar.

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


## <a name="troubleshoot-connectivity-issue"></a>Felsök anslutnings problem

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Felsök anslutnings problem mellan IR och Data Factory med egen värd eller IR och data källa/mottagare

För att felsöka problem med nätverks anslutningen bör du veta hur du [samlar in nätverks spårningen](#how-to-collect-netmon-trace), förstår hur du använder det och [analyserar Netmon-spårningen](#how-to-analyze-netmon-trace) innan du använder Netmon-verktygen i reala fall från egen värd-IR.

Ibland kan vi vid fel sökning av anslutnings problem, till exempel under en mellan egen värd-IR och Data Factory: 

![HTTP-begäran misslyckades](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Eller en mellan egen värd-IR och data källa/mottagare kommer vi att stöta på följande fel:

**Fel meddelande:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Fel meddelande:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Lösning:** När du påträffar ovanstående problem kan du läsa följande instruktioner för att felsöka ytterligare:

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

### <a name="how-to-collect-netmon-trace"></a>Samla in Netmon-spårning

1.  Hämta Netmon-verktygen från [den här webbplatsen](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)och installera det på serverdatorn (vilken server som har problem) och klienten (till exempel lokal IR-överföring).

2.  Skapa en mapp, till exempel, i följande sökväg: *D:\netmon*. Se till att det finns tillräckligt med utrymme för att spara loggen.

3.  Avbilda IP-och portinformation. 
    1. Starta en kommando tolk.
    2. Välj Kör som administratör och kör följande kommando:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Avbilda Netmon-spårningen (nätverks paket).
    1. Starta en kommando tolk.
    2. Välj Kör som administratör och kör följande kommando:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. Du kan använda tre olika kommandon för att avbilda nätverks sidan:
        - Alternativ A: RoundRobin File Command (detta fångar bara en fil och skriver över gamla loggar).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Alternativ B: kommandot kedjad fil (Detta skapar en ny fil om 200 MB har nåtts).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Alternativ C: schemalagt fil kommando.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Tryck på **CTRL + C** för att stoppa avbilden av NetMon-spårningen.
 
> [!NOTE]
> Om du bara kan samla in Netmon-spåret på klient datorn kan du hämta serverns IP-adress så att du kan analysera spårningen.

### <a name="how-to-analyze-netmon-trace"></a>Analysera Netmon-spårning

När du försöker använda Telnet **8.8.8.8 888** med ovanstående Netmon-spårning, kommer du att se spårningen nedan:

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


## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp med fel sökning kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Sidan Microsoft Q&en fråga](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow-forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestanda guide för att mappa data flöden](concepts-data-flow-performance.md)
