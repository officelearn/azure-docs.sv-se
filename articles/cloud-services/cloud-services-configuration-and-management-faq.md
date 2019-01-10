---
title: Problem med konfiguration och hantering för vanliga frågor om Microsoft Azure Cloud Services | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om konfiguration och hantering för Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: dd7351d2948526905c91f2eac52e48b25cf063ac
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191436"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Konfiguration och av hanteringsproblem för Azure Cloud Services: Vanliga frågor och svar (FAQ)

Den här artikeln innehåller vanliga frågor och svar om konfiguration och hantering av problem för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också läsa den [VM-storlek för Cloud Services-sidan](cloud-services-sizes-specs.md) storlek information.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certifikat**

- [Varför är certifikatkedja med mitt Cloud Service SSL-certifikat som är ofullständig?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Vad är syftet med den ”Windows Azure Tools kryptering för certifikattillägg”?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Hur kan jag skapa en begäran om certifikatsignering (CSR) utan ”RDP-ing” i till instansen?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Mitt Cloud Service Management-certifikat upphör att gälla. Hur du förnyar det?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Så här automatiserar du installationen av den huvudsakliga SSL certificate(.pfx) och mellanliggande certificate(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Vad är syftet med ”Microsoft Azure Service Management för MachineKey” certifikatet?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Övervakning och loggning**

- [Vilka är de kommande Cloud Service-funktionerna i Azure portal som kan hjälpa att hantera och övervaka program?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Varför sluta skriva till loggkatalogen i IIS?](#why-does-iis-stop-writing-to-the-log-directory)
- [Hur aktiverar jag WAD loggning för Cloud Services?](#how-do-i-enable-wad-logging-for-cloud-services)

**Nätverkskonfiguration**

- [Hur ställer jag in tidsgränsen för inaktivitet för Azure load balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Hur associerar jag statisk IP-adress till min molntjänst?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Vilka är funktionerna och egenskaperna som Azure basic-IP-adresser/ID och DDOS tillhandahåller?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Så här aktiverar du HTTP/2 på Cloud Services VM?](#how-to-enable-http2-on-cloud-services-vm)

**Behörigheter**

- [Kan Microsoft interna tekniker fjärrskrivbord till Cloud Service-instanser utan tillstånd?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Jag kan inte fjärrskrivbord till Cloud Service VM med hjälp av RDP-filen. Jag får följande fel: Ett autentiseringsfel inträffade (kod: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Skalning**

- [Jag kan inte skalas bortom X instanser](#i-cannot-scale-beyond-x-instances)
- [Hur kan jag konfigurera automatisk skalning baserat på minne mått?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Generisk**

- [Hur gör jag för att lägga till ”nosniff” till min webbplats?](#how-do-i-add-nosniff-to-my-website)
- [Hur jag för att anpassa IIS för en webbroll?](#how-do-i-customize-iis-for-a-web-role)
- [Vad är kvotgränsen för min molntjänst?](#what-is-the-quota-limit-for-my-cloud-service)
- [Varför visas enheten på den virtuella datorn Cloud Service mycket lite ledigt diskutrymme?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Hur kan jag lägga till ett tillägg för program mot skadlig kod för min molntjänster på ett automatiserat sätt?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Så här aktiverar du (Servernamnindikator) för molntjänster?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Hur kan jag lägga till taggar till min Azure-molntjänst?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Azure-portalen visas inte SDK-version i min molntjänst. Hur får jag som?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Jag vill stänga av Molntjänsten i flera månader. Hur du minskar fakturering kostnaden för tjänst i molnet utan att förlora IP-adressen?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certifikat

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Varför är certifikatkedja med mitt Cloud Service SSL-certifikat som är ofullständig?
    
Vi rekommenderar att kunder installerar fullständig certifikatkedjan (löv-certifikat, mellanliggande certifikat och rotcertifikatet) i stället för bara lövcertifikatet. När du installerar bara lövcertifikatet kan du förlita dig på Windows för att skapa certifikatkedjan genom att gå listan över betrodda certifikat. Om tillfälliga nätverks- eller DNS-problem uppstår i Azure eller Windows Update när Windows försöker verifiera certifikatet kan betraktas certifikatet ogiltig. Det här problemet kan undvikas genom att installera hela certifikatkedjan. Blogg på [så här installerar du ett länkat SSL-certifikat](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) visar hur du gör detta.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Vad är syftet med den ”Windows Azure Tools kryptering för certifikattillägg”?

Dessa certifikat skapas automatiskt när ett tillägg läggs till Molntjänsten. Oftast det här är tillägget WAD eller RDP-tillägget, men det kan vara andra, till exempel program mot skadlig kod eller Logginsamlaren tillägget. Dessa certifikat används bara för att kryptera och dekryptera den privata konfigurationen för tillägget. Förfallodatumet är aldrig markerad, så det ingen spelar roll om certifikatet har upphört att gälla. 

Du kan ignorera dessa certifikat. Om du vill rensa certifikat som du tar bort alla. Azure genereras ett fel om du försöker ta bort ett certifikat som används.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hur kan jag skapa en begäran om certifikatsignering (CSR) utan ”RDP-ing” i till instansen?

Se följande Riktlinjedokument:

[Hämta ett certifikat för användning med Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR är bara en textfil. Det behöver inte skapas från datorn där certifikatet slutändan ska användas. Även om det här dokumentet är avsedd för en App Service, CSR-skapande är generisk och gäller även för molntjänster.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Mitt Cloud Service Management-certifikat upphör att gälla. Hur du förnyar det?

Du kan använda följande PowerShell-kommandon för att förnya certifikat för enhetshantering:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

Den **Get-AzurePublishSettingsFile** skapar ett nytt certifikat i **prenumeration** > **Hanteringscertifikat** i Azure-portalen. Det ser ut som ”YourSubscriptionNam]-[CurrentDate] - autentiseringsuppgifter” namnet på det nya certifikatet.

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Så här automatiserar du installationen av den huvudsakliga SSL certificate(.pfx) och mellanliggande certificate(.p7b)?

Du kan automatisera den här uppgiften med hjälp av ett startskript (batch/cmd/PowerShell) och registrera det startskriptet i tjänstdefinitionsfilen. Lägga till både startskript och certifikat (.p7b-fil) i projektmappen i samma katalog för startskriptet.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Vad är syftet med ”Microsoft Azure Service Management för MachineKey” certifikatet?

Det här certifikatet används för att kryptera datornycklar på webbroller i Azure. Om du vill veta mer, Kolla in det här dokumentet [https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731].

Mer information finns i följande artiklar:
- [Hur du konfigurerar och köra startåtgärder för en molntjänst](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Vanliga startuppgifter för Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Övervakning och loggning

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Vilka är de kommande Cloud Service-funktionerna i Azure portal som kan hjälpa att hantera och övervaka program?

Möjlighet att generera ett nytt certifikat för Remote Desktop Protocol (RDP) kommer snart. Du kan också köra det här skriptet:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Möjlighet att välja blob eller lokal för dina csdef och cscfg överför plats kommer snart. Med hjälp av [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), du kan ange ett värde för varje plats.

Du kan övervaka mått på instansnivå. Ytterligare övervakningsfunktionerna som är tillgängliga i [så övervaka Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Varför sluta skriva till loggkatalogen i IIS?
Du har förbrukat lokala lagringskvoten för att skriva till loggkatalogen. Du åtgärdar det, kan du göra något av tre saker:
* Aktivera diagnostik för IIS och diagnostiken flyttats regelbundet till blob-lagring.
* Ta manuellt bort loggfiler från loggningskatalog.
* Öka kvotgränsen för lokala resurser.

Mer information finns i följande dokument:
* [Lagra och visa diagnostikdata i Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
* [IIS-loggar sluta skriva i molntjänst](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Hur aktiverar jag WAD loggning för Cloud Services?
Du kan aktivera loggning för Windows Azure Diagnostics SÄKERHETSSPECIFIKA med följande alternativ:
1. [Aktivera från Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Aktivera med hjälp av .net-kod](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Aktivera via Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Du kan använda för att få de aktuella WAD inställningarna för din molntjänst [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd eller du kan visa den via portalen från bladet för ”molntjänster--> tillägg”.


## <a name="network-configuration"></a>Nätverkskonfiguration

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hur ställer jag in tidsgränsen för inaktivitet för Azure load balancer?
Du kan ange timeout-värdet i din (csdef) tjänstdefinitionsfilen så här:

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
Se [nya: Konfigurerbara Idle Timeout för Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) för mer information.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hur associerar jag statisk IP-adress till min molntjänst?
Om du vill konfigurera en statisk IP-adress, måste du skapa en reserverad IP. Den här reserverade IP-Adressen kan associeras till en ny molntjänst eller till en befintlig distribution. Se följande dokument för mer information:
* [Så här skapar du en reserverad IP-adress](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reserverad IP-adressen för en befintlig molntjänst](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associera en reserverad IP-adress till en ny molntjänst](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associera en reserverad IP-adress till en distribution som körs](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associera en reserverad IP-adress till en molntjänst med hjälp av en tjänstkonfigurationsfil](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Vilka är funktionerna och egenskaperna som Azure basic-IP-adresser/ID och DDOS tillhandahåller?
Azure har IP-adresser/ID: N i datacenter fysiska servrar till att skydda mot hot. Kunder kan också distribuera lösningar för säkerhetsprogram från tredje part, till exempel brandväggar för webbprogram, nätverkets brandväggar, program mot skadlig kod, intrångsidentifiering, förebyggande system (IDS/IPS) med mera. Mer information finns i [skydda dina data och tillgångar och uppfylla globala säkerhetsstandarderna](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft övervakar kontinuerligt servrar, nätverk och program för att identifiera hot. Azures intrångsidentifiering multipronged hothantering metoden använder, distributed denial of service (DDoS) mot attacker, penetrationstester testning, maskininlärningsbaserad analys, avvikelseidentifiering och maskininlärning att ständigt förbättra dess defense och minska riskerna. Microsoft Antimalware för Azure skyddar Azure Cloud Services och virtuella datorer. Du har möjlighet att distribuera säkerhetslösningar från tredje part dessutom till exempel brandväggar för web application, nätverkets brandväggar, program mot skadlig kod, intrång och förebyggande system (IDS/IPS) med mera.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Så här aktiverar du HTTP/2 på Cloud Services VM?

Windows 10 och Windows Server 2016 levereras med stöd för HTTP/2 på både klienten och servern sida. Om dina klienter (webbläsare) ansluter till IIS-servern via TLS som förhandlar HTTP/2 via TLS-tillägg, så du behöver inte göra ändringar på serversidan. Detta är eftersom via TLS, h2-14-huvud som anger användning av HTTP/2 skickas som standard. Om du å andra sidan skickar klienten en uppgradering rubrik att uppgradera till HTTP/2, måste du göra ändringen nedan på serversidan för att säkerställa att uppgradera fungerar och du som avslutas med en HTTP/2-anslutning. 

1. Kör regedit.exe.
2. Bläddra till registernyckeln: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Skapa ett nytt DWORD-värde med namnet **DuoEnabled**.
4. Ange värdet till 1.
5. Starta om servern.
6. Gå till din **standardwebbplats** och under **bindningar**, skapa en ny TLS-bindning med ett självsignerat certifikat som nyss skapade. 

Mer information finns i:

- [HTTP/2 i IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 i Windows 10: Webbläsare, appar och webbservern](https://channel9.msdn.com/Events/Build/2015/3-88)
         

De här stegen kan automatiseras via en startåtgärd så att varje gång en ny PaaS-instans skapas, det kan göra ändringar ovan i systemregistret. Mer information finns i [hur du konfigurerar och köra startåtgärder för en molntjänst](cloud-services-startup-tasks.md).

 
När det har gjorts, kan du kontrollera om HTTP/2 har aktiverats eller inte genom att använda någon av följande metoder:

- Aktivera protokollversion i IIS-loggar och titta på IIS-loggar. HTTP/2 visas i loggarna. 
- Aktivera F12 Utvecklarverktyget i Internet Explorer-/ Microsoft Edge och växla till fliken nätverk för att verifiera protokollet. 

Mer information finns i [HTTP/2 i IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Behörigheter

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hur kan jag för att implementera rollbaserad åtkomst för Cloud Services?
Cloud Services stöder inte rollbaserad åtkomst-modellen för åtkomstkontroll (RBAC) eftersom den inte är en Azure Resource Manager-baserade tjänst.

Se [förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Fjärrskrivbord

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Kan Microsoft interna tekniker fjärrskrivbord till Cloud Service-instanser utan tillstånd?
Microsoft följer en strikt process som inte tillåter internt tekniker för fjärrskrivbord i din molntjänst utan skriftligt tillstånd (e-post eller andra skriftligt meddelande) från ägaren eller deras utsedda.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Jag kan inte fjärrskrivbord till Cloud Service VM med hjälp av RDP-filen. Jag får följande fel: Ett autentiseringsfel inträffade (kod: 0x80004005)

Det här felet kan uppstå om du använder RDP-fil från en dator som är ansluten till Azure Active Directory. Följ dessa steg för att lösa problemet:

1. Högerklicka på RDP-filen som du hämtade och välj sedan **redigera**.
2. Lägg till ”&#92;” som prefix innan användarnamnet. Till exempel använda **. \username** i stället för **användarnamn**.

## <a name="scaling"></a>Skalning

### <a name="i-cannot-scale-beyond-x-instances"></a>Jag kan inte skalas bortom X instanser
Din Azure-prenumeration har en gräns för antalet kärnor som du kan använda. Skalning fungerar inte om du har använt alla tillgängliga kärnor. Till exempel om du har en gräns på 100 kärnor, innebär det du kan ha 100 instanser av A1 storlek virtuella datorer för din molntjänst eller 50 A2 storlek instanser av virtuella datorer.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hur kan jag konfigurera automatisk skalning baserat på minne mått?

Autoskala baserat på minne mått för Cloud Services stöds inte för närvarande. 

Du kan använda Application Insights för att undvika problemet. Automatisk skalning har stöd för Application Insights som en källa för mått och kan skala instansantalet roll baserat på gästen mått, till exempel ”minnet”.  Du måste konfigurera Application Insights i Cloud Service-paketet projektfilen (*.cspkg) och aktivera Azure-diagnostiktillägget på tjänsten för att implementera den här prestation.

Mer information om hur du använder ett anpassat mått via Application Insights för att konfigurera automatisk skalning på molntjänster finns i [komma igång med automatisk skalning med anpassat mått i Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Läs mer om hur du integrerar Azure Diagnostics med Application Insights för Cloud Services, [skicka molntjänst, virtuell dator eller Service Fabric diagnostiska data till Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Läs mer om att aktivera Application Insights för Cloud Services [Application Insights för Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Läs mer om hur du aktiverar Azure Diagnostics-loggning för Cloud Services, [konfigurera diagnostik för Azure Cloud Services och virtuella datorer](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Generisk

### <a name="how-do-i-add-nosniff-to-my-website"></a>Hur gör jag för att lägga till ”nosniff” till min webbplats?
Om du vill förhindra att klienter kontroll MIME-typer, lägger du till en inställning i din *web.config* fil.

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

Du kan också lägga till detta som en inställning i IIS. Använd följande kommando med den [vanliga startuppgifter](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artikeln.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Hur jag för att anpassa IIS för en webbroll?
Använd IIS startskriptet från den [vanliga startuppgifter](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artikeln.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Vad är kvotgränsen för min molntjänst?
Se [tjänstspecifika begränsar](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Varför visas enheten på den virtuella datorn Cloud Service mycket lite ledigt diskutrymme?
Detta är förväntat och den bör inte orsaka några problem i ditt program. Journaler är aktiverad för % approot % enhet i Azure PaaS virtuella datorer som i stort sett förbrukar dubbla mängden utrymme som filer tar normalt upp. Men det finns flera saker att känna till som i stort sett omvandla det till ett icke-problem.

% Approot % diskens storlek beräknas som < storlek för .cspkg + max journalstorleken > + en marginal ledigt utrymme eller 1,5 GB, beroende på vilket som är större. Storleken på den virtuella datorn påverkar inte den här beräkningen. (VM-storleken påverkar bara storleken på den temporära C:-enheten.) 

Det stöds inte för att skriva till % approot % enheten. Om du skriver Azure-datorn, måste du göra detta i en tillfällig LocalStorage (eller andra alternativet, till exempel Blob storage, Azure Files, osv.). Så är mängden ledigt utrymme på % approot %-mappen olämpligt. Om du inte är säker på om ditt program skriver till % approot % enhet kan du alltid kan låta tjänsten kör några få dagar och jämför den ”före” och ”efter” storlekar. 

Azure kommer inte att skriva något till % approot % enheten. När den virtuella Hårddisken skapas från din .cspkg och monteras i Azure VM, är det enda som kan skriva till den här enheten ditt program. 

Journal-inställningar är icke konfigurerbara, så du inte kan inaktivera den.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hur kan jag lägga till ett tillägg för program mot skadlig kod för min molntjänster på ett automatiserat sätt?

Du kan aktivera tillägget mot skadlig kod med hjälp av PowerShell-skript i startåtgärden. Följ stegen i de här artiklarna du implementerar den: 
 
- [Skapa en startåtgärd för PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Mer information om distributionsscenarier för program mot skadlig kod och hur du aktiverar det från portalen finns i [distributionsscenarier för program mot skadlig kod](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Så här aktiverar du (Servernamnindikator) för molntjänster?

Du kan aktivera SNI i Cloud Services med någon av följande metoder:

**Metod 1: Använd PowerShell**

SNI-bindningen kan konfigureras med hjälp av PowerShell-cmdleten **New WebBinding** i en startåtgärd för en molntjänst rollinstans enligt nedan:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Enligt [här](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags kan vara något av värden enligt följande:

|Värde|Betydelse|
------|------
|0|Inga SNI|
|1|SNI är aktiverat |
|2 |Icke SNI bindning som använder centrala Certificate Store|
|3|Lagra SNI-bindning som använder centralt certifikat |
 
**Metod 2: Använd kod**

SNI-bindningen kan också konfigureras via kod i rollstart enligt beskrivningen på den här [blogginlägget](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Med hjälp av någon av metoderna ovan måste respektive certifikat (*.pfx) för specifika värdnamn installeras först för rollinstanser med hjälp av en startåtgärd eller via kod för SNI-bindningen ska fungera.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hur kan jag lägga till taggar till min Azure-molntjänst? 

Molntjänst är en klassisk resurs. Endast resurser som skapats via Azure Resource Manager support taggar. Du kan inte lägga till taggar till klassiska resurser, till exempel molntjänst. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure-portalen visas inte SDK-version i min molntjänst. Hur får jag som?

Vi arbetar med att tillhandahålla den här funktionen på Azure portal. Du kan under tiden kan använda följande PowerShell-kommandon för att hämta SDK-version:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Jag vill stänga av Molntjänsten i flera månader. Hur du minskar fakturering kostnaden för tjänst i molnet utan att förlora IP-adressen?

En redan distribuerad tjänst i molnet debiteras för beräkning och lagring som används. Så även om du stänger av den virtuella Azure-datorn debiteras fortfarande du för lagring. 

Här är vad du kan göra för att minska din fakturering utan att förlora IP-adressen för din tjänst:

1. [Reservera IP-adress](../virtual-network/virtual-networks-reserved-public-ip.md) innan du tar bort distributioner.  Du debiteras endast för den här IP-adressen. Mer information om IP-adress fakturering finns i [IP-adresser priser](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Ta bort distributioner. Ta inte bort xxx.cloudapp.net, så att du kan använda den för framtiden.
3. Om du vill distribuera om Molntjänsten med hjälp av samma reserverad IP-Adressen som du reserverade i din prenumeration, se [reserverade IP-adresser för molntjänster och virtuella datorer](https://azure.microsoft.com/blog/reserved-ip-addresses/).

