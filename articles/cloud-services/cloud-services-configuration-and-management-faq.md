---
title: Vanliga frågor om konfiguration och hantering
titleSuffix: Azure Cloud Services
description: I den här artikeln visas vanliga frågor om konfiguration och hantering för Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 40abd048b047bbece79b7c05d36a1fb189a4f28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656933"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Konfigurations- och hanteringsproblem för Azure Cloud Services: Vanliga frågor och svar (vanliga frågor)

Den här artikeln innehåller vanliga frågor och svar om konfigurations- och hanteringsproblem för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också läsa [sidan VM-storlek](cloud-services-sizes-specs.md) för molntjänster för storleksinformation.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certifikat**

- [Varför är certifikatkedjan för mitt Cloud Service SSL-certifikat ofullständig?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Vad är syftet med "Windows Azure Tools Encryption Certificate for Extensions"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Hur kan jag generera en begäran om certifikatsignering (CSR) utan "RDP-ing" i instansen?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Mitt cloud servicehanteringscertifikat löper ut. Hur förnyar man det?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Hur automatiserar man installationen av huvud-SSL-certifikat(.pfx) och mellanliggande certifikat(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Vad är syftet med certifikatet "Microsoft Azure Service Management for MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Övervakning och loggning**

- [Vilka är de kommande Molntjänstfunktionerna i Azure-portalen som kan hjälpa till att hantera och övervaka program?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Varför slutar IIS skriva till loggkatalogen?](#why-does-iis-stop-writing-to-the-log-directory)
- [Hur aktiverar jag WAD-loggning för molntjänster?](#how-do-i-enable-wad-logging-for-cloud-services)

**Nätverkskonfiguration**

- [Hur ställer jag in tidsgränsen för inaktiv för Azure load balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Hur kopplar jag en statisk IP-adress till min molntjänst?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Vilka funktioner och funktioner finns som Azure basic IPS/IDS och DDOS tillhandahåller?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Hur aktiverar du HTTP/2 på virtuell molntjänster?](#how-to-enable-http2-on-cloud-services-vm)

**Behörigheter**

- [Kan Microsofts interna tekniker fjärrskrivbord till Cloud Service-instanser utan tillstånd?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Jag kan inte fjärrskrivbord till virtuell molntjänst med hjälp av RDP-filen. Jag får följande fel: Ett autentiseringsfel har inträffat (Kod: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Skalning**

- [Jag kan inte skala bortom X-instanser](#i-cannot-scale-beyond-x-instances)
- [Hur konfigurerar jag automatisk skalning baserat på minnesmått?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Allmänna**

- [Hur lägger jag till "nosniff" på min webbplats?](#how-do-i-add-nosniff-to-my-website)
- [Hur anpassar jag IIS för en webbroll?](#how-do-i-customize-iis-for-a-web-role)
- [Vad är kvotgränsen för min molntjänst?](#what-is-the-quota-limit-for-my-cloud-service)
- [Varför visar enheten på min virtuella molntjänst väldigt lite ledigt diskutrymme?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Hur lägger jag till ett antimalware-tillägg för mina molntjänster på ett automatiserat sätt?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Hur aktiverar du SNI (Server Name Indication) för molntjänster?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Hur lägger jag till taggar i min Azure Cloud Service?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Azure-portalen visar inte SDK-versionen av min molntjänst. Hur kan jag få det?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Jag vill stänga av molntjänsten i flera månader. Hur kan man minska faktureringskostnaden för Molntjänsten utan att förlora IP-adressen?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certifikat

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Varför är certifikatkedjan för mitt Cloud Service SSL-certifikat ofullständig?
    
Vi rekommenderar att kunderna installerar hela certifikatkedjan (lövcert, mellanliggande certifikat och rotcert) i stället för bara lövcertifikatet. När du installerar bara lövcertifikatet förlitar du dig på att Windows ska skapa certifikatkedjan genom att gå på CTL. Om återkommande nätverks- eller DNS-problem uppstår i Azure eller Windows Update när Windows försöker validera certifikatet kan certifikatet anses ogiltigt. Genom att installera hela certifikatkedjan kan det här problemet undvikas. Bloggen på [Hur man installerar ett kedjat SSL-certifikat](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) visar hur man gör detta.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Vad är syftet med "Windows Azure Tools Encryption Certificate for Extensions"?

Dessa certifikat skapas automatiskt när ett tillägg läggs till i molntjänsten. Oftast är detta WAD-tillägget eller RDP-tillägget, men det kan vara andra, till exempel tillägget Antimalware eller Log Collector. Dessa certifikat används endast för att kryptera och dekryptera den privata konfigurationen för tillägget. Utgångsdatumet kontrolleras aldrig, så det spelar ingen roll om certifikatet har upphört att gälla. 

Du kan ignorera dessa certifikat. Om du vill rensa certifikaten kan du prova att ta bort dem alla. Azure genererar ett fel om du försöker ta bort ett certifikat som används.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hur kan jag generera en begäran om certifikatsignering (CSR) utan "RDP-ing" i instansen?

Se följande vägledningsdokument:

[Skaffa ett certifikat för användning med Windows Azure-webbplatser (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Csr är bara en textfil. Det behöver inte skapas från den maskin där certifikatet slutligen kommer att användas.Även om det här dokumentet är skrivet för en App-tjänst är CSR-skapandet allmänt och gäller även för molntjänster.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Mitt cloud servicehanteringscertifikat löper ut. Hur förnyar man det?

Du kan använda följande PowerShell-kommandon för att förnya dina hanteringscertifikat:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get-AzurePublishSettingsFile** skapar ett nytt hanteringscertifikat i > **prenumerationshanteringscertifikat** i Azure-portalen. **Subscription** Namnet på det nya certifikatet ser ut som "YourSubscriptionNam]-[CurrentDate]-credentials".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Hur automatiserar man installationen av huvud-SSL-certifikat(.pfx) och mellanliggande certifikat(.p7b)?

Du kan automatisera den här uppgiften med hjälp av ett startskript (batch/cmd/PowerShell) och registrera startskriptet i tjänstdefinitionsfilen. Lägg till både startskriptet och certifikatfilen(.p7b) i projektmappen i samma katalog med startskriptet.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Vad är syftet med certifikatet "Microsoft Azure Service Management for MachineKey"?

Det här certifikatet används för att kryptera datornycklar på Azure Web Roles. Mer information finns i [den här rekommendationen](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731).

Mer information finns i följande artiklar:
- [Konfigurera och köra startuppgifter för en molntjänst](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Vanliga startuppgifter för Molntjänsten](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Övervakning och loggning

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Vilka är de kommande Molntjänstfunktionerna i Azure-portalen som kan hjälpa till att hantera och övervaka program?

Möjligheten att generera ett nytt certifikat för RDP (Remote Desktop Protocol) kommer snart. Du kan också köra det här skriptet:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Möjlighet att välja blob eller lokal för din csdef och cscfg ladda upp plats kommer snart. Med hjälp av [Ny AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0)kan du ange varje platsvärde.

Möjlighet att övervaka mått på instansnivå. Ytterligare övervakningsfunktioner finns i [How to Monitor Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Varför slutar IIS skriva till loggkatalogen?
Du har uttömt den lokala lagringskvoten för att skriva till loggkatalogen.För att rätta till detta kan du göra en av tre saker:
* Aktivera diagnostik för IIS och få diagnostiken regelbundet flyttad till bloblagring.
* Ta bort loggfiler manuellt från loggningskatalogen.
* Öka kvotgränsen för lokala resurser.

Mer information finns i följande dokument:
* [Lagra och visa diagnostikdata i Azure Storage](/azure/storage/common/storage-introduction)
* [IIS-loggar slutar skriva i Molntjänst](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Hur aktiverar jag WAD-loggning för molntjänster?
Du kan aktivera LOGGNING AV Windows Azure Diagnostics (WAD) via följande alternativ:
1. [Aktivera från Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Aktivera via .NET-kod](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Aktivera via Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

För att få de aktuella WAD-inställningarna för din molntjänst kan du använda [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd eller så kan du visa den via portalen från bladet "Cloud Services --> Extensions".


## <a name="network-configuration"></a>Nätverkskonfiguration

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hur ställer jag in tidsgränsen för inaktiv för Azure load balancer?
Du kan ange tidsgränsen i filen för tjänstdefinition (csdef) så här:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Se [Ny: Konfigurerbar inaktiv timeout för Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) för mer information.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hur kopplar jag en statisk IP-adress till min molntjänst?
Om du vill konfigurera en statisk IP-adress måste du skapa en reserverad IP. Den här reserverade IP-adressen kan associeras till en ny molntjänst eller till en befintlig distribution. Mer information finns i följande dokument:
* [Så här skapar du en reserverad IP-adress](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Reservera IP-adressen för en befintlig molntjänst](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associera en reserverad IP till en ny molntjänst](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associera en reserverad IP till en distribution som körs](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Associera en reserverad IP till en molntjänst med hjälp av en tjänstkonfigurationsfil](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Vilka funktioner och funktioner finns som Azure basic IPS/IDS och DDOS tillhandahåller?
Azure har IPS/IDS på datacenterfysiska servrar för att skydda mot hot. Dessutom kan kunder distribuera säkerhetslösningar från tredje part, till exempel brandväggar för webbprogram, nätverksbrandvägger, antimalware, intrångsidentifiering, förebyggande system (IDS/IPS) med mera. Mer information finns i [Skydda dina data och tillgångar och följa globala säkerhetsstandarder](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft övervakar kontinuerligt servrar, nätverk och program för att upptäcka hot. Azures multipronged threat-management-metod använder intrångsidentifiering, distribuerad denial-of-service (DDoS) attackförebyggande, penetrationstestning, beteendeanalys, avvikelseidentifiering och maskininlärning för att ständigt stärka sitt försvar och minska riskerna. Microsoft Antimalware för Azure skyddar Azure Cloud Services och virtuella datorer. Du har möjlighet att distribuera säkerhetslösningar från tredje part dessutom, till exempel brandväggar för webbprogram, nätverksbrandväggar, antimalware, intrångsidentifiering och förebyggande system (IDS/IPS) med mera.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Hur aktiverar du HTTP/2 på virtuell molntjänster?

Windows 10 och Windows Server 2016 har stöd för HTTP/2 på både klient- och serversidan. Om din klient (webbläsare) ansluter till IIS-servern via TLS som förhandlar HTTP/2 via TLS-tillägg behöver du inte göra några ändringar på serversidan. Detta beror på att h2-14-huvudet som anger användningen av HTTP/2 skickas som standard via TLS. Om klienten å andra sidan skickar ett uppgraderingshuvud för att uppgradera till HTTP/2 måste du göra ändringen nedan på serversidan för att säkerställa att uppgraderingen fungerar och att du får en HTTP/2-anslutning. 

1. Kör regedit.exe.
2. Bläddra till registernyckeln: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Skapa ett nytt DWORD-värde med namnet **DuoEnabled**.
4. Ange värdet till 1.
5. Starta om servern.
6. Gå till **standardwebbplatsen** och skapa en ny TLS-bindning under **Bindningar**med det självsignerade certifikat som just har skapats. 

Mer information finns i:

- [HTTP/2 på IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 i Windows 10: Webbläsare, appar och webbserver](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Dessa steg kan automatiseras via en startuppgift, så att när en ny PaaS-instans skapas kan den göra ändringarna ovan i systemregistret. Mer information finns i [Så här konfigurerar och kör du startuppgifter för en molntjänst](cloud-services-startup-tasks.md).

 
När detta har gjorts kan du kontrollera om HTTP/2 har aktiverats eller inte med någon av följande metoder:

- Aktivera protokollversion i IIS-loggar och titta i IIS-loggarna. Det kommer att visa HTTP /2 i loggarna. 
- Aktivera F12 Developer Tool i Internet Explorer eller Microsoft Edge och växla till fliken Nätverk för att verifiera protokollet. 

Mer information finns i [HTTP/2 på IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Behörigheter

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hur implementerar jag rollbaserad åtkomst för molntjänster?
Cloud Services stöder inte den rollbaserade åtkomstkontrollmodellen (RBAC) eftersom det inte är en Azure Resource Manager-baserad tjänst.

Se [Förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Fjärrskrivbord

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Kan Microsofts interna tekniker fjärrskrivbord till Cloud Service-instanser utan tillstånd?
Microsoft följer en strikt process som inte tillåter interna tekniker att fjärrskrivbord till din molntjänst utan skriftligt tillstånd (e-post eller annan skriftlig kommunikation) från ägaren eller deras designee.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Jag kan inte fjärrskrivbord till virtuell molntjänst med hjälp av RDP-filen. Jag får följande fel: Ett autentiseringsfel har inträffat (Kod: 0x80004005)

Det här felet kan uppstå om du använder RDP-filen från en dator som är ansluten till Azure Active Directory. Följ dessa anvisningar för att lösa problemet:

1. Högerklicka på den RDP-fil som du hämtade och välj sedan **Redigera**.
2. Lägg till "&#92;" som prefix före användarnamnet. Använd till exempel **.\användarnamn** i stället för **användarnamn**.

## <a name="scaling"></a>Skalning

### <a name="i-cannot-scale-beyond-x-instances"></a>Jag kan inte skala bortom X-instanser
Din Azure-prenumeration har en gräns för hur många kärnor du kan använda. Skalning fungerar inte om du har använt alla tillgängliga kärnor. Om du till exempel har en gräns på 100 kärnor kan du ha 100 100 100 100 100 förekomster av virtuella datorer i A1-storlek för molntjänsten eller 50 A2-förekomster av virtuella datorer.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hur konfigurerar jag automatisk skalning baserat på minnesmått?

Automatisk skalning baserat på minnesmått för en molntjänst stöds för närvarande inte. 

Om du vill lösa problemet kan du använda Application Insights. Automatisk skalning stöder Application Insights som en måttkälla och kan skala antalet rollinstanser baserat på gästmått som "Minne".  Du måste konfigurera Application Insights i din Cloud Service-projektpaketfil (*.cspkg) och aktivera Azure Diagnostics-tillägg på tjänsten för att implementera den här bedriften.

Mer information om hur du använder ett anpassat mått via Application Insights för att konfigurera Automatisk skalning på Molntjänster finns i [Komma igång med automatisk skala efter anpassat mått i Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Mer information om hur du integrerar Azure Diagnostics med Application Insights for Cloud Services finns i [Skicka molntjänst, virtuell dator eller diagnostikdata för tjänstinfrastruktur till Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Mer information om hur du aktiverar Application Insights för Molntjänster finns i [Application Insights för Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Mer information om hur du aktiverar Azure Diagnostics Logging for Cloud Services finns i [Konfigurera diagnostik för Azure Cloud Services och virtuella datorer](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Allmänna

### <a name="how-do-i-add-nosniff-to-my-website"></a>Hur lägger jag till "nosniff" på min webbplats?
Om du vill förhindra att klienter sniffar MIME-typerna lägger du till en inställning i filen *web.config.*

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Du kan också lägga till detta som en inställning i IIS. Använd följande kommando med artikeln [vanliga startuppgifter.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Hur anpassar jag IIS för en webbroll?
Använd IIS-startskriptet från artikeln [med vanliga startuppgifter.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Vad är kvotgränsen för min molntjänst?
Se [Tjänstspecifika gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Varför visar enheten på min virtuella molntjänst väldigt lite ledigt diskutrymme?
Detta är förväntat och det bör inte orsaka något problem för ditt program. Journalering är aktiverat för %approot%-enheten i virtuella Azure PaaS-datorer, som i princip förbrukar dubbelt så mycket utrymme som filer normalt tar upp. Men det finns flera saker att vara medveten om som i huvudsak förvandla detta till en icke-fråga.

%approot%-enhetsstorleken \<beräknas som storleken på .cspkg + max journalstorlek + en marginal med ledigt utrymme> eller 1,5 GB, beroende på vilket som är störst. Storleken på den virtuella datorn har ingen betydelse för den här beräkningen. (Vm-storleken påverkar bara storleken på den tillfälliga C:-enheten.) 

Det stöds inte att skriva till %approot%-enheten. Om du skriver till den virtuella Azure-datorn måste du göra det i en tillfällig LocalStorage-resurs (eller något annat alternativ, till exempel Blob-lagring, Azure-filer osv.). Så mängden ledigt utrymme på mappen %approot% är inte meningsfull. Om du inte är säker på om ditt program skriver till %approot%-enheten kan du alltid låta tjänsten köras i några dagar och sedan jämföra storlekarna "före" och "efter". 

Azure skriver inget till %approot%-enheten. När den virtuella hårddisken har skapats från din .cspkg och monteras i Azure VM, är det enda som kan skriva till den här enheten ditt program. 

Journalinställningarna kan inte konfigureras, så du kan inte stänga av den.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hur lägger jag till ett antimalware-tillägg för mina molntjänster på ett automatiserat sätt?

Du kan aktivera tillägget Antimalware med PowerShell-skript i startuppgiften. Följ stegen i de här artiklarna för att implementera den: 
 
- [Skapa en startuppgift för PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Mer information om scenarier för distribution mot skadlig kod och hur du aktiverar den från portalen finns i [Scenarier för distribution mot skadlig kod](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Hur aktiverar du SNI (Server Name Indication) för molntjänster?

Du kan aktivera SNI i Molntjänster med någon av följande metoder:

**Metod 1: Använd PowerShell**

SNI-bindningen kan konfigureras med PowerShell-cmdlet **New-WebBinding** i en startuppgift för en Cloud Service-rollinstans enligt nedan:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Som beskrivs [här](https://technet.microsoft.com/library/ee790567.aspx)kan $sslFlags vara ett av värdena som följande:

|Värde|Betydelse|
------|------
|0|Ingen SNI|
|1|SNI aktiverat|
|2|Icke SNI-bindning som använder Central Certificate Store|
|3|SNI-bindning som använder centralcertifikatarkivet|
 
**Metod 2: Använd kod**

SNI-bindningen kan också konfigureras via kod i rollstarten enligt beskrivningen i det här [blogginlägget:](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Med någon av metoderna ovan måste respektive certifikat (*.pfx) för de specifika värdnamnen först installeras på rollinstanserna med hjälp av en startuppgift eller via kod för att SNI-bindningen ska gälla.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hur lägger jag till taggar i min Azure Cloud Service? 

Cloud Service är en klassisk resurs. Endast resurser som skapats via supporttaggar för Azure Resource Manager. Du kan inte använda taggar på klassiska resurser, till exempel Molntjänst. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure-portalen visar inte SDK-versionen av min molntjänst. Hur kan jag få det?

Vi arbetar med att föra den här funktionen på Azure-portalen. Under tiden kan du använda följande PowerShell-kommandon för att hämta SDK-versionen:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Jag vill stänga av molntjänsten i flera månader. Hur kan man minska faktureringskostnaden för Molntjänsten utan att förlora IP-adressen?

En redan distribuerad molntjänst faktureras för den beräkning och lagring som används. Så även om du stänger av den virtuella Azure-datorn debiteras du fortfarande för lagringen. 

Här är vad du kan göra för att minska din fakturering utan att förlora IP-adressen för din tjänst:

1. [Reservera IP-adressen](../virtual-network/virtual-networks-reserved-public-ip.md) innan du tar bort distributionerna.  Du debiteras endast för den här IP-adressen. Mer information om ip-adressfakturering finns i [PRISSÄTTNINGEN för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Ta bort distributionerna. Ta inte bort xxx.cloudapp.net, så att du kan använda den för framtiden.
3. Om du vill distribuera om molntjänsten med samma reserv-IP som du reserverade i din prenumeration läser du [Reserverade IP-adresser för molntjänster och virtuella datorer](https://azure.microsoft.com/blog/reserved-ip-addresses/).

