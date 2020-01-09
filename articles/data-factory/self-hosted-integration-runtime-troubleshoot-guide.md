---
title: Felsöka integration runtime med egen värd i Azure Data Factory
description: Lär dig hur du felsöker problem med integration runtime med egen värd i Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: b8492e8934c782451fb77d5a0ff56b96c34c9a00
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439878"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Felsöka integration runtime med egen värd

Den här artikeln utforskar vanliga fel söknings metoder för integration runtime med egen värd i Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Vanliga fel och lösningar

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Fel meddelande: integration runtime med egen värd kan inte ansluta till moln tjänsten

![Problem med IR-anslutning via egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Orsak 

Den egna värdbaserade integrerings körningen kan inte ansluta till Data Factory tjänsten (backend). Det här problemet orsakas vanligt vis av nätverks inställningar i brand väggen.

#### <a name="resolution"></a>Upplösning

1. Kontrol lera om integration runtime-tjänsten körs.
    
   ![Status för IR-tjänsten med egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Om tjänsten körs går du vidare till steg 3.

1. Om ingen proxy har kon figurer ATS på den lokala integrerings körningen (vilket är standardinställningen) kör du följande PowerShell-kommando på den dator där den lokala integrerings körningen är installerad:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > URL: en för tjänsten kan variera beroende på din Data Factory plats. URL: en för tjänsten finns under **ADF UI** > **anslutningar** > **integration runtimes** > **Redigera IR** > - **noder** med egen värd > **Visa tjänst-URL: er**.
            
    Följande är det förväntade svaret:
            
    ![PowerShell-kommando svar](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Om du inte får det förväntade svaret använder du någon av följande metoder som passar din situation:
            
    * Om du får ett meddelande om att det inte gick att lösa problemet, finns det ett Domain Name System (DNS-problem). Kontakta nätverks teamet för att åtgärda problemet.
    * Om du får ett meddelande om att SSL/TLS-certifikat inte är betrott, kontrollerar du om certifikatet för https://wu2.frontend.clouddatahub.net/ är betrott på datorn och installerar sedan det offentliga certifikatet med hjälp av certifikat hanteraren. Den här åtgärden bör minimera problemet.
    * Gå till **Windows** > **logg boken (loggar)**  > **program-och tjänst loggar** > **integration runtime** och kontrol lera eventuella problem som orsakas av DNS, brand Väggs regel eller företagets nätverks inställningar. (Om du upptäcker ett sådant problem stänger du anslutningen.) Eftersom alla företag har anpassade nätverks inställningar kan du kontakta nätverks teamet för att felsöka problemen.

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
> * Kontrol lera om proxyservern måste placeras i listan Betrodda mottagare. Om så är fallet ser du till att [dessa domäner](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) finns i listan Betrodda mottagare.
> * Kontrol lera om TLS/SSL-certifikatet "wu2.frontend.clouddatahub.net/" är betrott på proxyservern.
> * Om du använder Active Directory autentisering på proxyn ändrar du tjänst kontot till det användar konto som har åtkomst till proxyn som "Integration Runtime tjänst".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Fel meddelande: egen värd för integration runtime-noden/logiska SHIR är i ett inaktivt/"kör (begränsat)" tillstånd

#### <a name="cause"></a>Orsak 

Den egna värdbaserade integrerade runtime-noden kan ha en **inaktiv** status, som visas på följande skärm bild:

![Inaktiv IR-nod med egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Det här problemet uppstår när noder inte kan kommunicera med varandra.

#### <a name="resolution"></a>Upplösning

1. Logga in på den nod-värdbaserade virtuella datorn. Under **program-och tjänst loggar** > **Integration Runtime**öppnar du Loggboken och filtrerar alla fel loggar.

1. Kontrol lera om fel loggen innehåller följande fel: 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.  Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
