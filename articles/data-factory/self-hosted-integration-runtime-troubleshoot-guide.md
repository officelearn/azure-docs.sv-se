---
title: Felsöka självvärd för integrationskörning i Azure Data Factory
description: Lär dig hur du felsöker problem med självvärd integrering i Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414882"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Felsöka självvärd integreringskörning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs vanliga felsökningsmetoder för självvärdbaserad integrationskörning i Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Vanliga fel och lösningar

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Felmeddelande: Självvärdbaserad integrationskörning kan inte ansluta till molntjänsten

![Problem med IR-anslutning med egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Orsak 

Den självvärderade integrationskörningen kan inte ansluta till datafabrikstjänsten (backend). Det här problemet orsakas vanligtvis av nätverksinställningarna i brandväggen.

#### <a name="resolution"></a>Lösning

1. Kontrollera om tjänsten integrationskörning körs.
    
   ![Status för IR-tjänst med självvärd](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Om tjänsten körs går du vidare till steg 3.

1. Om det inte finns någon proxy konfigurerad på den självvärderade integrationskörningen (vilket är standardinställningen) kör du följande PowerShell-kommando på datorn där den självvärderade integrationskörningen är installerad:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Tjänstens URL kan variera beroende på din datafabrik. Du hittar tjänst-URL:en under **ADF-anslutningsanslutningars** > **Connections** > **integreringskörningar** > **Redigera självvärderade IR-körtlar** > **Nodes** > Visa**tjänstadresser**.
            
    Följande är det förväntade svaret:
            
    ![Kommandosvar i PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Om du inte får det förväntade svaret använder du någon av följande metoder som är lämpliga för din situation:
            
    * Om meddelandet "Fjärrnamn inte kunde matchas" visas ett DNS-problem (Domain Name System). Kontakta nätverksteamet för att åtgärda problemet.
    * Om du får meddelandet "ssl/tls cert is not trusted" kontrollerar du om certifikatet för https://wu2.frontend.clouddatahub.net/ är betrott på datorn och installerar sedan det offentliga certifikatet med hjälp av Certifikathanteraren. Den här åtgärden bör minska problemet.
    * Gå till **Windows** > **Loggboken (loggar)** > **Program och tjänster Loggar** > **Integration Runtime** och kontrollera eventuella fel som orsakas av DNS, en brandväggsregel eller företagsnätverksinställningar. (Om du hittar ett sådant fel, med våld stänga anslutningen.) Eftersom alla företag har anpassade nätverksinställningar kontaktar du nätverksteamet för att felsöka dessa problem.

1. Om "proxy" har konfigurerats på den självvärderade integrationskörningen kontrollerar du att proxyservern kan komma åt tjänstslutpunkten. Ett exempelkommando finns i [PowerShell, webbbegäranden och proxyservrar](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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
            
![Powershell-kommandosvar 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy överväganden:
> *    Kontrollera om proxyservern behöver placeras i listan Betrodda mottagare. Om så är fallet kontrollerar du att [dessa domäner](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) finns med i listan Betrodda mottagare.
> *    Kontrollera om TLS/SSL-certifikatet "wu2.frontend.clouddatahub.net/" är betrott på proxyservern.
> *    Om du använder Active Directory-autentisering på proxyn ändrar du tjänstkontot till användarkontot som kan komma åt proxyn som "Integration Runtime Service".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Felmeddelande: Självvärdförd integrationskörningsnod/ logisk SHIR är inaktiv/ "Körs (begränsad)"

#### <a name="cause"></a>Orsak 

Den självvärderade integrerade runtime-noden kan ha en **inaktiv** status, som visas i följande skärmbild:

![Inaktiv IR-nod med självvärd värd](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Det här problemet uppstår när noder inte kan kommunicera med varandra.

#### <a name="resolution"></a>Lösning

1. Logga in på den virtuella datorn som inte är värd för noden. Öppna Loggboken för integrering av **program och tjänster** > under Program och tjänster loggar**integrationskörningen**och filtrerar alla felloggar.

1. Kontrollera om en fellogg innehåller följande fel: 
    
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
        
1.    Check whether the error log contains the following:

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
