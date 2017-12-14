---
title: "Konfiguration och hantering av problem för Microsoft Azure Cloud Services FAQ | Microsoft Docs"
description: "Den här artikeln innehåller vanliga frågor om konfiguration och hantering av Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: genli
ms.openlocfilehash: 35c8e2a2029b3f29b45004c1308de8b3a108f698
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Konfiguration och hantering av problem för Azure Cloud Services: vanliga frågor (FAQ)

Den här artikeln innehåller vanliga frågor och svar om konfiguration och hantering av problem för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också kontakta den [Cloud Services VM-storlek sidan](cloud-services-sizes-specs.md) storlek information.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>Hur lägger jag till ”nosniff” till min webbplats?
För att hindra klienter från identifiering MIME-typer, lägger du till en inställning i din *web.config* fil.

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

Du kan också lägga till detta som en inställning i IIS. Använd följande kommando med den [vanliga uppgifter för Start](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artikel.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>Hur anpassar IIS för en webbroll?
Använd IIS startskriptet från den [vanliga uppgifter för Start](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artikel.

## <a name="i-cannot-scale-beyond-x-instances"></a>Det går inte att skala upp X instanser
Azure-prenumerationen har en gräns på antal kärnor som du kan använda. Skalning fungerar inte om du har använt alla tillgängliga kärnor. Till exempel om du har en gräns på 100 kärnor, innebär det du kan ha 100 A1 storlek virtuella instanser för din molntjänst eller 50 A2 storlek instanser för virtuella datorer.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hur kan jag Implementera rollbaserad åtkomst för molntjänster?
Cloud Services stöder inte rollbaserad åtkomstkontroll (RBAC)-modellen eftersom den inte är en Azure Resource Manager-baserad tjänst.

Se [Azure RBAC kontra klassiska prenumerationsadministratörer](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hur ställer jag in timeout vid inaktivitet för Azure belastningsutjämnare
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
Se [New: konfigurerbara Idle Timeout för Azure-belastningsutjämnaren](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) för mer information.

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Kan Microsoft interna engineers RDP till molnet tjänstinstanser utan behörighet?
Microsoft följer en strikt process som inte tillåter internt engineers till RDP till Molntjänsten utan skriftligt tillstånd (e-post eller andra skriftligt meddelande) från ägare eller deras utsedda.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Varför är certifikatkedjan för Mina moln tjänsten SSL-certifikat som är ofullständig?
Vi rekommenderar att kunder installera hela certifikatkedjan (löv certifikat, mellanliggande certifikat och rot-certifikat) i stället för bara lövcertifikatet. När du installerar bara lövcertifikatet du förlita dig på Windows för att skapa certifikatkedjan genom att gå listan över betrodda certifikat. Återkommande nätverks- eller DNS-problem inträffar i Azure eller Windows Update när Windows försöker verifiera certifikatet anses certifikatet ogiltiga. Genom att installera hela certifikatkedjan kan undvikas problemet. Blogg på [hur du installerar en länkad SSL-certifikatet](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) visar hur du gör detta.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hur associera en statisk IP-adress till min Molntjänsten?
Om du vill konfigurera en statisk IP-adress, måste du skapa en reserverad IP. Den här reserverade IP: N kan vara kopplad till en ny molntjänst eller till en befintlig distribution. Finns i följande dokument för ytterligare information:
* [Så här skapar du en reserverad IP-adress](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reserverad IP-adressen för en befintlig molntjänst](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associera en reserverad IP-adress till en ny molntjänst](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associera en reserverad IP-adress till en aktiv distribution](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associera en reserverad IP-adress till en molntjänst med hjälp av en konfigurationsfil för tjänsten](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Vad är kvotgräns för tjänst i molnet?
Se [tjänstspecifika begränsar](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Varför visas enheten på min molntjänst för virtuell dator mycket lite ledigt diskutrymme?
Detta är förväntat och den bör inte orsaka eventuella problem i tillämpningsprogrammet. Journalnivåer är aktiverat för % uproot % enheten i Azure PaaS virtuella datorer, vilket i praktiken innebär att dubbla mängden utrymme som filer tar normalt upp. Men det finns flera saker du bör känna till som i stort sett göra detta till ett icke-problem.

Storlek % approot % enhet beräknas som < storleken på .cspkg + max journalstorleken > + en marginal ledigt utrymme eller 1,5 GB, beroende på vilket som är större. Storleken på den virtuella datorn påverkar inte den här beräkningen. (VM-storlek påverkar endast storleken på den tillfälliga C:-enheten.) 

Den stöds inte för att skriva till % approot % enhet. Om du skriver till Azure VM, måste du göra det i en tillfällig LocalStorage resurs (eller andra alternativ, till exempel Blob storage, Azure-filer, osv.). Mängden ledigt utrymme på mappen % approot % är därför inte beskrivande. Om du inte är säker på om ditt program skrivs till % approot % enheten kan du alltid låta tjänsten kör för några dagar och jämför den ”före” och ”efter” storlekar. 

Azure kommer inte att skriva något % approot % enheten. När den virtuella Hårddisken skapas från din .cspkg och monterat på Azure VM, är det enda som kan skriva till den här enheten ditt program. 

Ändringsjournalen inställningarna är icke konfigurerbara, så du inte kan inaktivera den.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Vad är funktioner och möjligheter som ger Azure basic IP-adresser/ID: N och DDOS?
Azure har IP-adresser/ID: N i datacenter fysiska servrar att skydda mot hot. Dessutom kan kan kunderna distribuera säkerhetslösningar som från tredje part, till exempel web application brandväggar, nätverkets brandväggar, program mot skadlig kod, intrångsidentifiering, förebyggande system (ID: N/IP-adresser) och mer. Mer information finns i [skydda dina data och tillgångar och uppfylla globala säkerhetskrav](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft övervakar kontinuerligt servrar, nätverk och program för att identifiera hot. Azures intrångsidentifiering multipronged hothantering metoden använder, distribuerade denial of service (DDoS) mot attacker, intrång tester, beteendebaserade analytics, avvikelseidentifiering och maskininlärning att ständigt förbättra sin skydd och minska riskerna. Microsoft Antimalware för Azure skyddar Azure-molntjänster och virtuella datorer. Du har möjlighet att distribuera säkerhetslösningar från tredje part dessutom till exempel brandväggar för web application, nätverkets brandväggar, program mot skadlig kod, intrång upptäcka och förhindra system (ID: N/IP-adresser) och mycket mer.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Varför sluta skriva till loggkatalogen i IIS?
Du har uttömt alla lokala lagringskvoten för att skriva till loggkatalogen. Om du vill korrigera detta, kan du göra något av tre saker:
* Aktivera diagnostik för IIS och diagnostiken flyttats regelbundet till blob storage.
* Ta manuellt bort loggfilerna från loggningskatalog.
* Öka kvotgränsen för lokala resurser.

Mer information finns i följande dokument:
* [Lagra och visa diagnostikdata i Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
* [IIS-loggar slutar skriva i Molntjänsten](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Vad är syftet med ”Windows Azure verktyg kryptering för certifikattillägg”?
Dessa certifikat skapas automatiskt när ett tillägg läggs till i Molntjänsten. Oftast filnamnstillägget BOMULLSTUSS eller RDP-tillägget, men kan det bero på andra, till exempel program mot skadlig kod eller Logginsamlaren tillägget. Dessa certifikat används bara för att kryptera och dekryptera den privata konfigureringen för tillägget. Utgångsdatumet kontrolleras aldrig, så det ingen spelar roll om certifikatet har upphört att gälla. 

Du kan ignorera dessa certifikat. Om du vill rensa certifikat som kan du försöka ta bort alla. Azure genereras ett fel om du försöker ta bort ett certifikat som används.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hur kan jag skapa en begäran om certifikatsignering (CSR) utan ”RDP-ing” i till instansen?
Se följande Riktlinjedokument:

>[Hämta ett certifikat för användning med Windows Azure webbplatser (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Observera att en CSR är bara en textfil. Det behöver inte skapas från datorn där certifikatet slutligen ska användas. Även om det här dokumentet är avsedd för en Apptjänst, CSR-skapa är generisk och gäller även för molntjänster.

## <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hur lägger jag till ett tillägg för program mot skadlig kod för min molntjänster på ett automatiserat sätt?

Du kan aktivera tillägget för program mot skadlig kod med hjälp av PowerShell-skript i aktiviteten startades. Följ stegen i följande artiklar för att implementera den: 
 
- [Skapa en startåtgärd för PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Ange AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Mer information om scenarier för distribution av program mot skadlig kod och hur du aktiverar från portalen finns [scenarier för distribution av program mot skadlig kod](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

## <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Så här aktiverar du servern Servernamnsindikation (SNI) för molntjänster?

Du kan aktivera SNI i Cloud Services med någon av följande metoder:

### <a name="method-1-use-powershell"></a>Metod 1: Använd PowerShell

SNI-bindning kan konfigureras med hjälp av PowerShell-cmdleten **ny WebBinding** i en startåtgärd för en cloud service rollinstans enligt nedan:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Enligt [här](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags kan vara någon av värdena som följer:

|Värde|Betydelse|
------|------
|0|Inga SNI|
|1|SNI aktiverad |
|2 |Ej SNI bindning som använder centralt certifikatarkiv|
|3|Lagra SNI-bindning som använder centralt certifikat |
 
### <a name="method-2-use-code"></a>Metod 2: Använda kod

SNI-bindning kan också konfigureras via koden i rollen startade enligt beskrivningen på detta [blogginlägget](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Med hjälp av någon av ovanstående metoder måste respektive certifikat (*.pfx) för specifika värdnamn installeras först på rollinstanser som använder en startåtgärd eller via kod för SNI-bindningen ska fungera.

## <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hur kan jag lägga till taggar min Azure Cloud Service? 

Molntjänst är en klassisk resurs. Endast resurser som skapats via Azure Resource Manager support taggar. Du kan inte använda taggar klassiska resurser, till exempel tjänst i molnet. 

## <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Vad är kommande Cloud Service-funktionerna i Azure-portalen som hjälper dig att hantera och övervaka program?

* Möjligheten att generera ett nytt certifikat för Remote Desktop Protocol (RDP) kommer snart. Du kan också köra det här skriptet:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
* Möjlighet att välja blob- eller lokal för csdef- och cscfg överför plats kommer snart. Med hjälp av [ny AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0), du kan ange ett värde för varje plats.
* Möjlighet att övervaka på instansnivå. Ytterligare övervakningsfunktioner som är tillgängliga i [så övervakaren molntjänster](cloud-services-how-to-monitor.md).


## <a name="how-to-enable-http2-on-cloud-services-vm"></a>Så här aktiverar du HTTP-2 på Cloud Services VM?

Windows 10 och Windows Server 2016 levereras med stöd för HTTP-2 på både klient- och serversidan. Om din klient (webbläsare) ansluter till IIS-servern via TLS som förhandlar HTTP/2 via TLS-utökningar och du behöver inte göra några ändringar på serversidan. Detta beror på att h2-14-huvud som anger användning av HTTP-2 skickas över TLS, som standard. Om å andra sidan skickar klienten en uppgradering rubrik att uppgradera till HTTP-2, måste du utföra ändringen nedan på serversidan för att säkerställa att uppgradera fungerar och slutar med en HTTP-2-anslutning. 

1. Kör regedit.exe.
2. Bläddra till registernyckeln: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Skapa ett nytt DWORD-värde med namnet **DuoEnabled**.
4. Ange värdet till 1.
5. Starta om servern.
6. Gå till din **standardwebbplats** och under **bindningar**, skapa en ny TLS-bindning med det självsignerade certifikatet som precis har skapat. 

Mer information finns i:

- [HTTP-2 på IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP-2 i Windows 10: webbläsare, appar och webbservern](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Observera att ovanstående steg kan automatiseras via en startåtgärd så att när en ny PaaS-instans skapas, du kan göra ändringar ovan i systemregistret. Mer information finns i [hur du konfigurerar och kör Start-aktiviteter för en molntjänst](cloud-services-startup-tasks.md).

 
När detta har gjorts, kan du kontrollera om HTTP-2 har aktiverats eller inte genom att använda någon av följande metoder:

- Aktivera protokollversion i IIS-loggar och titta i IIS-loggar. HTTP-2 visas i loggarna. 
- Aktivera F12 Utvecklarverktyget i Internet Explorer/kant och gå till fliken nätverk för att verifiera protokollet. 

Mer information finns i [HTTP-2 på IIS](https://blogs.iis.net/davidso/http2).

## <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure-portalen visar inte SDK-versionen av tjänst i molnet. Hur kan jag som?

Vi arbetar på att den här funktionen på Azure-portalen. Under tiden kan använda du följande PowerShell-kommandon för att hämta SDK-version:

    Get-AzureService -ServiceName "<Cloud service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

## <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Det går inte att Fjärrskrivbord för Cloud Service VM med hjälp av RDP-filen. Jag får följande fel: ett autentiseringsfel har uppstått (kod: 0x80004005)

Det här felet kan uppstå om du använder RDP-filen från en dator som är ansluten till Azure Active Directory. Följ dessa steg för att lösa problemet:

1. Högerklicka på RDP-filen som du hämtade och välj **redigera**.
2. Lägg till ”& #92”; som prefix före användarnamnet. Till exempel använda **. \username** i stället för **användarnamn**.

## <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Jag vill stänga av Molntjänsten för flera månader. Hur du minskar fakturering kostnaden för Molntjänsten utan att förlora IP-adressen?

En redan distribuerad tjänst i molnet hämtar debiteras för beräkning och lagring som används. Så även om du stänger av den virtuella Azure-datorn kommer du fortfarande få debiteras för lagring. 

Här är vad du kan göra för att minska faktureringen utan att förlora IP-adressen för din tjänst:

1. [Reservera IP-adress](../virtual-network/virtual-networks-reserved-public-ip.md) innan du tar bort distributioner.  Du kommer bara att debiteras för den här IP-adress. Läs mer om IP-adress fakturering [IP-adresser priser](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Ta bort distributioner. Ta inte bort xxx.cloudapp.net, så att du kan använda den för framtida.
3. Om du vill distribuera om Molntjänsten med hjälp av samma reserverad IP som du har reserverats i din prenumeration, se [reserverade IP-adresser för molntjänster och virtuella datorer](https://azure.microsoft.com/blog/reserved-ip-addresses/).

## <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Min Cloud Service Management certifikatet upphör att gälla. Hur du förnyar det?

Du kan använda följande PowerShell-kommandon för att förnya din Management-certifikat:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

Den **Get-AzurePublishSettingsFile** skapar ett nytt certifikat i **prenumeration** > **Hanteringscertifikat** i Azure-portalen. Det ser ut som ”YourSubscriptionNam]-[CurrentDate] - autentiseringsuppgifter” namnet på det nya certifikatet.

## <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hur konfigurerar Autoskala baserat på minne mått?

Autoskala baserat på minne mått för en molntjänster stöds inte för närvarande. 

Du kan använda Application Insights för att undvika problemet. Autoskala stöder Application Insights som en källa för mått och kan skalas rollinstansantal baserat på gästen mått som ”minne”.  Du måste konfigurera Application Insights i Molntjänsten paketet projektfilen (*.cspkg) och aktivera tillägg för Azure-diagnostik på tjänsten för att implementera den här prestation.

Mer information om hur du använder ett anpassat mått via Application Insights för att konfigurera automatisk skalning på molntjänster finns [Kom igång med Autoskala efter anpassade mått i Azure](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)


Mer information om hur du integrerar Azure Diagnostics med Application Insights för molntjänster finns [skicka Cloud Service, virtuell dator eller Service Fabric diagnostikdata till Application Insights](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)

Läs mer om att aktivera Application Insights för molntjänster [Application Insights för Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Mer information om hur du aktiverar loggning av Azure-diagnostik för molntjänster finns [konfigurera diagnostik för Azure-molntjänster och virtuella datorer](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Hur att automatisera installationen av huvudsakliga SSL certifikat (.pfx) och mellanliggande certificate(.p7b)?

Du kan automatisera den här uppgiften med hjälp av ett startskript (PowerShell-batch/cmd) och registrera det startskriptet i tjänstdefinitionsfilen. Lägga till både startskript och certifikat (.p7b-fil) i projektmappen i samma katalog för startskriptet.

Mer information finns i följande artiklar:
- [Konfigurera och köra startade aktiviteter för en tjänst i molnet](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks)
- [Vanliga uppgifter för start av Molntjänsten](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks-common)