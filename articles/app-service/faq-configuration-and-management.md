---
title: Konfiguration av vanliga frågor och svar – Azure Apptjänst | Microsoft Docs
description: Få svar på vanliga frågor och svar om konfiguration och hantering av problem för funktionen Web Apps i Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5ab47165118b68e91c1218be35c6f88aa55350e2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982615"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfiguration och hantering av vanliga frågor och svar för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om konfiguration och hantering av problem för den [Web Apps-funktionen i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Finns det begränsningar jag bör känna till om jag vill flytta App Service-resurser?

Om du planerar att flytta App Service-resurser till en ny resursgrupp eller prenumeration, finns det några begränsningar som du bör tänka på. Mer information finns i [begränsningar för App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hur kan jag använda ett anpassat domännamn för min webbapp?

Svar på vanliga frågor om hur du använder ett anpassat domännamn med Azure-webbappen, finns i vår videon på sju [lägga till ett anpassat domännamn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Videon erbjuder en genomgång av hur du lägger till ett anpassat domännamn. Beskriver hur du använder din egen URL i stället för den *. azurewebsites.net URL med App Service web Apps. Du kan också se en detaljerad genomgång av [mappa ett anpassat domännamn](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hur köper jag en ny anpassad domän för min webbapp?

Om du vill lära dig mer om att köpa och konfigurera en anpassad domän för din App Service web Apps, se [köp och konfigurera ett anpassat domännamn i App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Hur jag för att ladda upp och konfigurera ett befintligt SSL-certifikat för min webbapp?

Information om hur du överför och konfigurerar ett befintligt anpassat SSL-certifikat finns [binda ett befintligt anpassat SSL-certifikat till en Azure webbapp](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Hur jag för att köpa och konfigurera ett nytt SSL-certifikat i Azure för min webbapp?

Om du vill lära dig mer om att köpa och konfigurera ett SSL-certifikat för App Service web Apps, se [lägga till ett SSL-certifikat till App Service-appen](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Hur flyttar jag Application Insights-resurser?

Azure Application Insights stöder för närvarande inte flyttåtgärden. Om din ursprungliga resursgruppen innehåller en Application Insights-resurs kan flytta du inte den här resursen. Om du inkluderar Application Insights-resursen när du försöker flytta en App Service-app, flytta hela åtgärden misslyckas. Application Insights och App Service-planen behöver inte dock finnas i samma resursgrupp som app för appen ska fungera korrekt.

Mer information finns i [begränsningar för App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Var kan jag hitta en checklista för vägledning och lära dig mer om resursen flyttar operations?

[Begränsningar för App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) visar hur du flyttar resurser till en ny prenumeration eller till en ny resursgrupp i samma prenumeration. Du kan få information om resource move checklista, Lär dig vilka tjänster som stöder flyttåtgärden och lär dig mer om begränsningar för App Service och annan information.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hur ställer jag in serverns tidszon för min webbapp?

Ange serverns tidszon för din webbapp:

1. I Azure-portalen i din App Service-prenumeration går du till den **programinställningar** menyn.
2. Under **appinställningar**, lägga till den här inställningen:
    * Nyckel = WEBSITE_TIME_ZONE
    * Värde = *tidszonen som du vill*
3. Välj **Spara**.

Se den **tidszon** kolumnen i den [standard tidszoner](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) artikel för godkända värden.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Varför min kontinuerliga WebJobs Ibland misslyckas?

Som standard inaktiveras webbappar om de är inaktiva för en angiven tidsperiod. På så sätt kan systemet spara resurser. I Basic och Standard-planer, du kan aktivera den **Always On** inställningen att behålla webbappen lästs in hela tiden. Om ditt webbprogram kör kontinuerliga WebJobs, bör du aktivera **alltid på**, eller WebJobs kanske inte körs på ett tillförlitligt sätt. Mer information finns i [skapa ett Webbjobb som körs kontinuerligt](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hur hittar jag utgående IP-adressen för min webbapp?

Hämta listan över utgående IP-adresser för din webbapp:

1. I Azure portal, på din webbappsbladet, går du till den **egenskaper** menyn.
2. Sök efter **utgående ip-adresser**.

Lista över utgående IP-adresser visas.

Läs hur du hämtar den utgående IP-adressen om din webbplats finns i en App Service Environment i [utgående nätverksadresser](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hur får jag en reserverade eller dedikerade inkommande IP-adress för min webbapp?

Om du vill konfigurera en dedikerad eller reserverad IP-adress för inkommande anrop som görs till din Azure-app-webbplats, installera och konfigurera ett IP-baserad SSL-certifikat.

Observera att App Service-planen måste vara för att använda en dedikerad eller reserverad IP-adress för inkommande anrop, i en grundläggande eller högre service-plan.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kan jag exportera App Service-certifikat för användning utanför Azure, till exempel för en webbplats som ligger någon annanstans? 

App Service-certifikat betraktas som Azure-resurser. De är inte avsedda för användning utanför din Azure-tjänster. Du kan inte exportera dem för användning utanför Azure. Mer information finns i [frågor och svar om App Service-certifikat och anpassade domäner](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kan jag exportera min App Service-certifikat som ska användas med andra Azure-molntjänster?

Portalen ger en förstklassig upplevelse för att distribuera en App Service-certifikat via Azure Key Vault till App Service-appar. Men har vi fått begäranden från kunder att använda certifikaten utanför App Service plattform, till exempel med Azure Virtual Machines. Läs hur du skapar en lokal PFX-kopia av din App Service-certifikat så att du kan använda certifikatet med andra Azure-resurser i [skapa en lokal PFX-kopia av en App Service certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Mer information finns i [frågor och svar om App Service-certifikat och anpassade domäner](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Varför visas meddelandet ”lyckades delvis” när jag försöker att säkerhetskopiera min webbapp?

En vanlig orsak till Säkerhetskopieringsfel är att vissa filer som används av programmet. Filer som används är låsta när du säkerhetskopierar. Detta förhindrar att de här filerna säkerhetskopieras och kan resultera i statusen ”lyckades delvis”. Du kan eventuellt förhindra detta genom att exkludera filer från säkerhetskopieringsprocessen. Du kan välja att säkerhetskopiera som krävs. Mer information finns i [säkerhetskopiera bara viktiga delar av din webbplats med Azure web apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hur tar jag bort en rubrik från HTTP-svar?

Uppdatera din webbplats web.config-filen för att ta bort rubrikerna från HTTP-svaret. Mer information finns i [ta bort standardserver rubriker på dina Azure-webbplatser](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Är App Service som är kompatibelt med PCI-Standard 3.0 och 3.1?

För närvarande kan är funktionen Web Apps i Azure App Service kompatibelt med PCI Data Security Standard (DSS) version 3.0 nivå 1. PCI DSS version 3.1 finns i vår översikt. Planera pågår redan för hur senaste standarden kommer att fortsätta.

PCI DSS-certifieringen version 3.1 kräver inaktiverar Transport Layer Security (TLS) 1.0. Inaktivera TLS 1.0 är för närvarande inte ett alternativ för de flesta App Service-planer. Om du använder App Service-miljö eller inte vill migrera din arbetsbelastning till App Service Environment, kan du få större kontroll över din miljö. Detta innebär att inaktivera TLS 1.0 genom att kontakta supporten för Azure. Inom en snar framtid planerar vi att dessa inställningar är tillgängliga för användare.

Mer information finns i [Microsoft Azure App Service web app efterleva PCI-Standard 3.0 och 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hur använder jag mellanlagringsplatser miljö och distribution?

I Standard och Premium App Service-planer när du distribuerar din webbapp till App Service kan du distribuera till en separat distributionsplats i stället för till standard produktionsplatsen. Distributionsplatser är live-webbappar som har sina egna värdnamn. Web app innehåll och konfiguration element kan bytas mellan två distributionsfack, inklusive produktionsplatsen.

Mer information om hur du använder distributionsplatser finns i [ställa in en mellanlagringsmiljö i App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hur jag för att komma åt och granska WebJob-loggar?

Granska WebJob-loggar:

1. Logga in på din [Kudu-webbplatsen](https://*yourwebsitename*.scm.azurewebsites.net).
2. Välj Webbjobbet.
3. Välj den **växla utdata** knappen.
4. Ladda ned utdatafilen, markera den **hämta** länk.
5. Enskilda körningar, Välj **enskilda anropa**.
6. Välj den **växla utdata** knappen.
7. Välj länken.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Jag försöker använda Hybridanslutningar med SQL Server. Varför visas meddelandet ”System.OverflowException: Aritmetisk operation orsakade spill ”?

Om du använder Hybridanslutningar för att ansluta till SQL Server, orsaka en Microsoft .NET-uppdatering för den 10 maj 2016, anslutningar misslyckas. Du kan se det här meddelandet:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Lösning

Undantaget orsakades av ett problem med Hybridanslutningshanteraren som sedan har åtgärdats. Se till att [uppdatera din Hybridanslutningshanterare](https://go.microsoft.com/fwlink/?LinkID=841308) att lösa problemet.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Hur jag för att lägga till eller redigera en URL-omskrivningsregel?

Lägga till eller redigera en URL-omskrivningsregel:

1. Konfigurera Internet Information Services (IIS) Manager så att den ansluter till din App Service-webbapp. Läs hur du ansluter IIS-hanteraren till App Service i [fjärradministration av Azure-webbplatser med hjälp av IIS-hanteraren](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. I IIS-hanteraren, lägga till eller redigera en URL-omskrivningsregel. Om du vill lära dig mer om att lägga till eller redigera en URL-omskrivningsregel, se [skapa omskrivningsregler för URL-omskrivningsregler modulen](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hur kan jag styra inkommande trafik till App Service?

På webbplatsnivå har du två alternativ för att styra inkommande trafik till App Service:

* Aktivera dynamisk IP-begränsningar. Läs hur du aktiverar dynamisk IP-restriktioner i [IP- och domänbegränsningar för Azure-webbplatser](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Aktivera modulen säkerhet. Läs hur du aktiverar modulen säkerhet i [ModSecurity waf på Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Om du använder App Service Environment kan du använda [Barracuda brandväggen](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hur jag för att blockera portar i en webbapp i App Service?

I App Service delad klient miljön går inte att blockera specifika portar på grund av infrastrukturen. TCP-portar 4016 och 4018 4020 kanske också öppna för fjärrfelsökning för Visual Studio.

I App Service Environment har du fullständig kontroll över inkommande och utgående trafik. Du kan använda Nätverkssäkerhetsgrupper för att begränsa eller blockerar specifika portar. Läs mer om App Service Environment [introduktion till App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hur jag för att avbilda en F12 trace?

Har du två alternativ för att samla in en F12 spårning:

* F12 HTTP-spårning
* F12 konsolens utdata

### <a name="f12-http-trace"></a>F12 HTTP-spårning

1. Gå till din webbplats i Internet Explorer. Det är viktigt att logga in innan du utför nästa steg. I annat fall F12 spårningen samlar in känslig inloggningsdata.
2. Tryck på F12.
3. Kontrollera att den **nätverk** fliken är markerad och välj sedan gröna **spela upp** knappen.
4. Gör som återskapa problemet.
5. Välj röda **stoppa** knappen.
6. Välj den **spara** knappen (diskikonen) och spara HAR-filen (i Internet Explorer och Microsoft Edge) *eller* Högerklicka HAR-filen och välj sedan **Spara som HAR med innehåll**(i Chrome).

### <a name="f12-console-output"></a>F12 konsolens utdata

1. Välj den **konsolen** fliken.
2. För varje flik som innehåller mer än noll objekt, väljer du fliken (**fel**, **varning**, eller **Information**). Om fliken inte har valts, är tab-ikon grått och svart när du flyttar markören bort från den.
3. Högerklicka i meddelandeområdet i fönstret och välj sedan **kopiera alla**.
4. Klistra in den kopierade texten i en fil och spara filen.

Om du vill visa en HAR-filen som du kan använda den [HAR viewer](https://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Varför får jag ett fel när jag försöker ansluta en App Service-webbapp till ett virtuellt nätverk som är anslutet till ExpressRoute?

Om du försöker ansluta en Azure-webbapp till ett virtuellt nätverk som har anslutit till Azure ExpressRoute, misslyckas det. Följande meddelande visas: ”Gatewayen är inte en VPN-gateway”.

Du kan för närvarande inte punkt-till-plats VPN-anslutningar till ett virtuellt nätverk som är anslutet till ExpressRoute. En punkt-till-plats VPN och ExpressRoute kan inte samexistera för samma virtuella nätverk. Mer information finns i [ExpressRoute och plats-till-plats VPN-anslutningar gränser och begränsningar](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hur ansluter jag en App Service-webbapp till ett virtuellt nätverk som har en statisk routning (principbaserad) gateway?

För närvarande kan stöds ansluta en App Service-webbapp till ett virtuellt nätverk som har en statisk routning (principbaserad) gateway inte. Om ditt virtuella nätverk för målet redan finns, måste den ha punkt-till-plats-VPN aktiveras innan den kan anslutas till en app med en dynamisk routningsgateway. Om din gateway har angetts till statisk routning, kan du inte aktivera en punkt-till-plats-VPN. 

Mer information finns i [integrera en app med Azure-nätverk](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>I min App Service Environment, varför kan jag skapa bara en App Service-plan, trots att jag har två tillgängliga arbetare?

Om du vill tillhandahålla feltolerans kräver App Service Environment att varje arbetarpool måste minst en ytterligare beräkningsresurs. Ytterligare beräkningsresurs kan inte tilldelas en arbetsbelastning.

Mer information finns i [så här skapar du en App Service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Varför ser jag timeout-fel när jag försöker skapa en App Service Environment?

Ibland misslyckas skapar en App Service Environment. I så fall kan se du följande fel i aktivitetsloggarna:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Kontrollera att inget av följande villkor är sant för att lösa problemet:
* Undernätet är för liten.
* Undernätet är inte tom.
* ExpressRoute förhindrar anslutningskrav för en App Service Environment.
* En felaktig Nätverkssäkerhetsgrupp förhindrar anslutningskrav för en App Service Environment.
* Tvingad tunneltrafik är aktiverat.

Mer information finns i [vanligaste problemen vid distribution (skapande) en ny Azure App Service Environment](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Varför kan jag inte ta bort min App Service-plan?

Du kan inte ta bort en App Service-plan om alla App Service-appar som är associerade med App Service-planen. Innan du tar bort en App Service plan kan du ta bort alla tillhörande App Service-appar från App Service-planen.

## <a name="how-do-i-schedule-a-webjob"></a>Hur jag för att schemalägga ett WebJob?

Du kan skapa ett schemalagt Webbjobb med hjälp av Cron-uttryck:

1. Skapa filen settings.job.
2. I det här JSON-fil innehåller en schema-egenskap med hjälp av ett Cron-uttryck: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Läs mer om schemalagda WebJobs [skapa ett schemalagt Webbjobb med hjälp av ett Cron-uttryck](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hur utför penetrationstest för min App Service-app?

Att utföra penetrationstester, [ansöka om](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hur konfigurerar jag ett anpassat domännamn för en App Service-webbapp som använder Traffic Manager?

Läs hur du använder ett anpassat domännamn med en App Service-app som använder Azure Traffic Manager för belastningsutjämning i [och konfigurera ett anpassat domännamn för en Azure-webbapp med Traffic Manager](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Min App Service-certifikat har flaggats som för bedrägeri. Hur löser jag det?

Under domänverifiering av en App Service certificate-inköp visas följande meddelande:

”Ditt certifikat har flaggats för potentiellt bedrägeri. Begäran är för närvarande under granskning. Om certifikatet inte är användbar inom 24 timmar, kontakta supporten för Azure ”.

När meddelandet indikerar, kan bedrägeri verifieringen ta upp till 24 timmar att slutföra. Under denna tid fortsätter du att se meddelandet.

Om din App Service certificate fortsätter att visa det här meddelandet efter 24 timmar, kör följande PowerShell-skript. Skript-kontakter den [certifikatleverantör](https://www.godaddy.com/) direkt för att lösa problemet.

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hur fungerar autentisering och auktorisering i App Service?

Detaljerad dokumentation för autentisering och auktorisering i App Service finns i dokumenten för olika identifiera providern inloggningar:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-konto](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hur jag för att omdirigera standard *. azurewebsites.net-domänen till min Azure webbapps-anpassad domän?

När du skapar en ny webbplats med hjälp av Web Apps i Azure, en standard *sitename*. azurewebsites.net-domänen är tilldelad till webbplatsen. Om du lägger till ett anpassat värdnamn till din plats och inte vill att användarna ska kunna komma åt din standard *. azurewebsites.net-domänen, du kan omdirigera standard-URL. Om du vill lära dig att dirigera all trafik från din webbplats standarddomän till din anpassade domän, se [omdirigera standarddomän till din anpassade domän i Azure web apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hur tar jag reda på vilken version av .NET-versionen i App Service?

Det är det snabbaste sättet att se vilken version av Microsoft .NET som installeras i App Service med hjälp av Kudu-konsolen. Du kan komma åt Kudu-konsolen från portalen eller med hjälp av URL: en för din App Service-app. Detaljerade anvisningar finns i [fastställa den installerade .NET-versionen i App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Varför inte automatisk skalning fungerar som förväntat?

Om Azure Autoscale inte har skalas i eller skala ut web app-instansen som du väntade dig, kanske körs i ett scenario där vi avsiktligt inte väljer att skala för att undvika en oändlig loop på grund av ”växlar”. Detta inträffar vanligtvis när det finns inte en tillräcklig marginal mellan tröskelvärden för skalning och skala in. Läs hur du undviker ”växlar” och Läs mer om andra Metodtips för autoskalning i [Metodtips för autoskalning](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Varför automatisk skalning ibland skala endast delvis?

Automatisk skalning utlöses när måttet överskrider förkonfigurerade gränser. Ibland kanske du märker att är endast delvis fyllda jämfört med vad du förväntade dig. Detta kan inträffa när antalet instanser som du vill inte är tillgängliga. I det här scenariot fyller delvis automatisk skalning in med det tillgängliga antalet instanser. Automatisk skalning körs sedan ombalansering logik för att få mer kapacitet. Den allokerar återstående instanser. Observera att det kan ta några minuter.

Om du inte ser det förväntade antalet instanser efter ett par minuter, kanske eftersom delvis påfyllning var tillräckligt för att ta med mått som är inom gränserna. Eller, automatisk skalning kanske har skalats ned eftersom den har nått den nedre gränsen för mått.

Om ingen av dessa villkor gäller och problemet kvarstår kan du skicka en supportförfrågan.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hur aktiverar jag HTTP-komprimering för mitt innehåll?

Lägg till följande kod i filen web.config på programnivå om du vill aktivera komprimering både för statisk och dynamisk innehållstyper:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Du kan också ange vilka dynamiska och statiska MIME-typer som du vill komprimera. Mer information finns i våra svar på frågor i forum [httpCompression inställningar på en enkel Azure webbplats](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hur migrerar jag från en lokal miljö till App Service?

Om du vill migrera webbplatser från Windows och Linux-webbservrar till App Service, kan du använda Azure App Service Migration Assistant. Migreringsverktyget skapar webbappar och databaser i Azure efter behov och sedan publicerar innehållet. Mer information finns i [Azure App Service Migration Assistant](https://www.migratetoazure.net/).
