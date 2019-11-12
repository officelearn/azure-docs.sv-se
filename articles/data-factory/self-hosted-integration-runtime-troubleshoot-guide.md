---
title: Felsöka integration runtime med egen värd i Azure Data Factory
description: Lär dig hur du felsöker problem med integration runtime med egen värd i Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907287"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Felsöka integration runtime med egen värd

Den här artikeln utforskar vanliga fel söknings metoder för integration runtime med egen värd i Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Vanliga fel och lösningar

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Fel meddelande: integration runtime med egen värd kan inte ansluta till moln tjänsten.

- **Symptom**: 

    ![Problem med IR-anslutning via egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Orsak**: den egna värdbaserade integrerings körningen kan inte ansluta till Data Factory-tjänsten (backend). Oftast är det inte troligt att det orsakas av nätverks inställningarna i brand väggen.

- **Upplösning**: 

    1. Kontrol lera att Windows-tjänsten Integration Runtime-tjänsten körs.
    
        ![Status för IR-tjänsten med egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Om Windows-tjänsten som visas i [1] körs följer du anvisningarna nedan:

        1. Om proxyn inte har kon figurer ATS på integration runtime med egen värd (standardinställningarna är ingen proxykonfiguration) kör du PowerShell-kommandot nedan på den dator där integration runtime med egen värd är installerat: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > URL: en för tjänsten kan variera beroende på Data Factory-platsen. URL: en för tjänsten finns under ADF UI-> Connections-> integration runtime – > Redigera lokal IR-> noder – > Visa tjänst-URL: er.
            
            Nedan visas förväntat svar:
            
            ![PowerShell-kommando svar](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Om svaret är ett annat, följer du anvisningarna nedan:
            
            * Om du får fel meddelandet "Det gick inte att matcha fjärrnamnet", finns det ett problem med DNS. Kontakta nätverks teamet för att få ett problem med DNS-matchningen. 
            * Om du får fel meddelandet "SSL/TLS-certifikat inte är betrott" kontrollerar du om certifikatet för "https://wu2.frontend.clouddatahub.net/" är betrott på datorn, installerar det offentliga certifikatet med hjälp av certifikat hanteraren, vilket bör minimera det här problemet.
            * Kontrol lera Windows-> logg boken (loggar) – > program-och tjänst loggar – > Integration Runtime för eventuella problem, som främst orsakats av DNS, brand Väggs regel och nätverks inställningar för företaget (Stäng anslutningen.) För det här problemet kan du delta i ditt nätverks team för ytterligare troubleshot, eftersom alla företag har anpassade nätverks inställningar.

        2. Om proxyn har kon figurer ATS på den lokala integrerings körningen, kontrollerar du om proxyservern kan komma åt vår tjänst slut punkt. Ett exempel kommando finns i [detta](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

            Nedan visas förväntat svar:
            
            ![PowerShell-kommando svar 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Synpunkter på proxy:
            > * Kontrol lera om proxyservern kräver vit listning. I så fall, har [dessa domäner](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) vit listas.
            > * Kontrol lera att TLS/SSL-certifikatet för "wu2.frontend.clouddatahub.net/" är betrott på proxyservern.
            > * Om du använder Active Directory-autentisering i proxy ändrar du tjänst kontot till det användar konto som har åtkomst till proxyn som "Integration Runtime tjänst".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Fel meddelande: egen värd för integration runtime-noden/logiska SHIR är i ett inaktivt/"kör (begränsat)" tillstånd

- **Orsak**: du kan se en IR-nod med egen värd i inaktiv status som visas på skärm bilden nedan:

    ![Inaktiv IR-nod med egen värd](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Det händer så om noderna inte kan kommunicera med varandra. 

- **Upplösning**: 

    Logga in på den nod som är värd för den virtuella datorn och öppna vyn händelse. i den program-och tjänst loggar som > Integration Runtime filtrerar du alla fel loggar. 

     1. Om fel loggen innehåller: 
    
        **Fel logg**: system. ServiceModel. EndpointNotFoundException: det gick inte att ansluta till net. TCP://xxxxxxx.bwld.com: 8060/ExternalService. svc/WorkerManager. Anslutnings försöket senast i tidsintervallet 00:00:00.9940994. TCP-felkod 10061: det gick inte att upprätta en anslutning eftersom mål datorn aktivt nekade den 10.2.4.10:8060.  ---> system .net. Sockets. SocketException: det gick inte att upprätta någon anslutning eftersom mål datorn aktivt nekade den 10.2.4.10:8060
    
           i system .net. Sockets. socket. DoConnect (slut punkt endPointSnapshot, SocketAddress socketAddress)
           
           i system .net. Sockets. socket. Connect (slut punkt remoteEP)
           
           vid system. ServiceModel. Channels. SocketConnectionInitiator. Connect (URI URI, tids gräns för TimeSpan)
    
        **Lösning:** starta kommando raden: telnet 10.2.4.10 8060
        
        Om du får följande fel meddelande kan du kontakta IT-avdelningen för att få hjälp med att åtgärda problemet. När du har lyckats använda Telnet kontaktar du Microsoft support om du fortfarande har problem med IR-nodens status.
        
        ![Kommando rads fel](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Om fel loggen innehåller:
     
        **Fel logg:** Det går inte att ansluta till Worker Manager: net. TCP://XXXXXX: 8060/ExternalService. svc/inga DNS-poster finns för värd azranlcir01r1. Ingen sådan värd är känd undantags information: system. ServiceModel. EndpointNotFoundException: det finns inga DNS-poster för värd-XXXXX. ---> system .net. Sockets. SocketException: ingen sådan värd är känd i system .net. DNS. GetAddrInfo (sträng namn) i system .net. DNS. InternalGetHostByName (sträng värdnamn, Boolean includeIPv6) på system .net. DNS. GetHostEntry (sträng hostNameOrAddress ) i system. ServiceModel. Channels. DnsCache. resolve (URI URI)---slut på stack spårning för inre undantag---Server Stack spårning: i system. ServiceModel. Channels. DnsCache. resolve (URI URI) 
    
        **Lösning:** En av de två följande åtgärderna kan hjälpa dig att lösa problemet:
         1. Lägg till alla noder i samma domän.
         2. Lägg till IP-till-värd-mappning i alla Hosts-filer för värdbaserade virtuella datorer.


## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory-bloggen](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestanda guide för ADF-mappning av data flöden](concepts-data-flow-performance.md)
