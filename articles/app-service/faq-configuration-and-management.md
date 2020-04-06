---
title: Vanliga frågor och frågor om konfiguration
description: Få svar på vanliga frågor om konfigurations- och hanteringsproblem för Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 9e7070e925d12df82adbc3683da5b10e48c5d4b0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668118"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Vanliga frågor och frågor om konfiguration och hantering för webbappar i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (Vanliga frågor) om konfigurations- och hanteringsproblem för [webbappfunktionen](https://azure.microsoft.com/services/app-service/web/)i Azure App Service .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Finns det begränsningar som jag bör vara medveten om om jag vill flytta App Service-resurser?

Om du planerar att flytta App Service-resurser till en ny resursgrupp eller prenumeration finns det några begränsningar att vara medveten om. Mer information finns i [Begränsningar för App-tjänsten](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hur använder jag ett eget domännamn för min webbapp?

Svar på vanliga frågor om hur du använder ett anpassat domännamn med din Azure-webbapp finns i vår sju minuter långa video [Lägg till ett anpassat domännamn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Videon ger en genomgång av hur du lägger till ett eget domännamn. Den beskriver hur du använder din egen webbadress i stället för *.azurewebsites.net-webbadressen med apptjänstens webbapp. Du kan också se en detaljerad genomgång av [hur du mappar ett eget domännamn](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hur köper jag en ny anpassad domän för min webbapp?

Mer information om hur du köper och konfigurerar en anpassad domän för apptjänstens webbapp finns [i Köpa och konfigurera ett anpassat domännamn i App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Hur laddar jag upp och konfigurerar ett befintligt TLS/SSL-certifikat för min webbapp?

Mer information om hur du laddar upp och konfigurerar ett befintligt anpassat TLS/SSL-certifikat finns i [Lägga till ett TLS/SSL-certifikat i apptjänstappen](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Hur köper och konfigurerar jag ett nytt TLS/SSL-certifikat i Azure för min webbapp?

Mer information om hur du köper och konfigurerar ett TLS/SSL-certifikat för apptjänstens webbapp finns i [Lägga till ett TLS/SSL-certifikat i apptjänstappen](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Hur flyttar jag application insights-resurser?

Azure Application Insights stöder för närvarande inte flyttåtgärden. Om den ursprungliga resursgruppen innehåller en Application Insights-resurs kan du inte flytta resursen. Om du inkluderar application insights-resursen när du försöker flytta en App Service-app misslyckas hela flyttåtgärden. Application Insights och App Service-planen behöver dock inte finnas i samma resursgrupp som appen för att appen ska fungera korrekt.

Mer information finns i [Begränsningar för App-tjänsten](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Var hittar jag en checklista för vägledning och lär mig mer om resursflyttningsåtgärder?

[Begränsningar för App-tjänsten](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) visar hur du flyttar resurser till antingen en ny prenumeration eller till en ny resursgrupp i samma prenumeration. Du kan få information om checklistan för resursflyttning, ta reda på vilka tjänster som stöder flyttåtgärden och lära dig mer om begränsningar i App Service och andra ämnen.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hur ställer jag in servertidszonen för min webbapp?

Så här anger du servertidszonen för webbappen:

1. Gå till menyn Programinställningar i Azure-portalen i din App **Service-prenumeration.**
2. Lägg till den här inställningen under **Appinställningar:**
    * Nyckel = WEBSITE_TIME_ZONE
    * Värde = *Den tidszon du vill ha*
3. Välj **Spara**.

De Apptjänster som körs i Windows finns i kolumnen **Tidszon** i artikeln [Standardtidszoner](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) för godkända värden. För apptjänsterna som körs på Linux anger du [TZ-databasnamnet](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) som tidszonsvärde. Här är ett exempel på TZ databasnamn: Amerika / Adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Varför misslyckas mina kontinuerliga WebJobs ibland?

Som standard tas webbappar bort om de är inaktiva under en viss tidsperiod. På så sätt kan systemet spara resurser. I Grundläggande och Standard-abonnemang kan du aktivera inställningen **Alltid på** för att hålla webbappen laddad hela tiden. Om webbappen körs kontinuerligt WebJobs bör du aktivera **Alltid på**, eller så kanske WebJobs inte fungerar tillförlitligt. Mer information finns i [Skapa ett webbnät som körs kontinuerligt](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hur får jag den utgående IP-adressen för min webbapp?

Så här hämtar du listan över utgående IP-adresser för din webbapp:

1. Gå till **menyn Egenskaper** på azure-portalen på webbappbladet.
2. Sök efter **utgående IP-adresser**.

Listan över utgående IP-adresser visas.

Mer information om hur du hämtar den utgående IP-adressen om din webbplats finns i en App Service-miljö finns i [Utgående nätverksadresser](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hur skaffar jag en reserverad eller dedikerad inkommande IP-adress för min webbapp?

Om du vill konfigurera en dedikerad eller reserverad IP-adress för inkommande samtal till din Azure-appwebbplats installerar och konfigurerar du ett IP-baserat TLS/SSL-certifikat.

Observera att om du vill använda en dedikerad eller reserverad IP-adress för inkommande samtal måste appserviceplanen finnas i en grundläggande eller högre serviceplan.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kan jag exportera mitt App Service-certifikat för att använda utanför Azure, till exempel för en webbplats som finns någon annanstans? 

Ja, du kan exportera dem för att använda utanför Azure. Mer information finns i [Vanliga frågor för App Service-certifikat och anpassade domäner](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kan jag exportera mitt App Service-certifikat som ska användas med andra Azure-molntjänster?

Portalen ger en förstklassig upplevelse för att distribuera ett App Service-certifikat via Azure Key Vault till App Service-appar. Vi har dock fått förfrågningar från kunder om att använda dessa certifikat utanför App Service-plattformen, till exempel med Virtuella Azure-datorer. Mer information om hur du skapar en lokal PFX-kopia av ditt App Service-certifikat så att du kan använda certifikatet med andra Azure-resurser finns i [Skapa en lokal PFX-kopia av ett App Service-certifikat](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Mer information finns i [Vanliga frågor för App Service-certifikat och anpassade domäner](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Varför visas meddelandet "Delvis lyckats" när jag försöker säkerhetskopiera min webbapp?

En vanlig orsak till säkerhetskopieringsfel är att vissa filer används av programmet. Filer som används är låsta medan du utför säkerhetskopian. Detta förhindrar att dessa filer säkerhetskopieras och kan resultera i statusen "Delvis lyckades". Du kan eventuellt förhindra att detta inträffar genom att utesluta filer från säkerhetskopieringsprocessen. Du kan välja att säkerhetskopiera bara vad som behövs. Mer information finns i [Säkerhetskopiering av bara de viktiga delarna av din webbplats med Azure-webbappar](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hur tar jag bort ett sidhuvud från HTTP-svaret?

Om du vill ta bort rubrikerna från HTTP-svaret uppdaterar du webbplatsens web.config-fil. Mer information finns i [Ta bort standardserverhuvuden på dina Azure-webbplatser](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Är App Service kompatibel med PCI Standard 3.0 och 3.1?

För närvarande följer funktionen webbappar i Azure App Service i enlighet med PCI Data Security Standard (DSS) version 3.0 Nivå 1. PCI DSS version 3.1 finns med i vår färdplan. Planering pågår redan för hur antagandet av den senaste standarden kommer att gå vidare.

PCI DSS version 3.1-certifiering kräver att du inaktiverar TLS (Transport Layer Security) 1.0. För närvarande är inaktivering av TLS 1.0 inte ett alternativ för de flesta App Service-abonnemang. Om du använder App Service Environment eller är villig att migrera din arbetsbelastning till App Service Environment kan du få större kontroll över din miljö. Detta innebär att inaktivera TLS 1.0 genom att kontakta Azure Support. Inom en snar framtid planerar vi att göra dessa inställningar tillgängliga för användarna.

Mer information finns i [Microsoft Azure App Service webbappefterlevnad med PCI Standard 3.0 och 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hur använder jag mellanlagringsmiljön och distributionsplatserna?

När du distribuerar webbappen till App Service i Standard- och Premium App Service-abonnemang kan du distribuera till en separat distributionsplats i stället för till standardproduktionsplatsen. Distributionsplatser är live webbappar som har sina egna värdnamn. Webbappinnehåll och konfigurationselement kan växlas mellan två distributionsplatser, inklusive produktionsplatsen.

Mer information om hur du använder distributionsplatser finns i [Konfigurera en mellanlagringsmiljö i App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hur kommer jag åt och granskar WebJob-loggar?

Så här granskar du WebJob-loggar:

1. Logga in på din [Kudu-webbplats.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Markera WebJob.
3. Välj knappen **Växla utdata.**
4. Om du vill hämta utdatafilen väljer du länken **Hämta.**
5. För enskilda körningar väljer du **Individuell åberopa**.
6. Välj knappen **Växla utdata.**
7. Välj nedladdningslänken.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Jag försöker använda hybridanslutningar med SQL Server. Varför visas meddelandet "System.OverflowException: Aritmetisk åtgärd resulterade i ett överflöd"?

Om du använder Hybrid-anslutningar för att komma åt SQL Server kan en Microsoft .NET-uppdatering den 10 maj 2016 orsaka att anslutningar misslyckas. Det här meddelandet kan visas:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Lösning

Undantaget orsakades av ett problem med HybridAnslutningshanteraren som sedan dess har åtgärdats. Se till att [uppdatera Hybrid Connection Manager](https://go.microsoft.com/fwlink/?LinkID=841308) för att lösa problemet.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hur lägger jag till en url-omskrivningsregel?

Om du vill lägga till en URL-omskrivningsregel skapar du en web.config-fil med relevanta konfigurationsposter i **mappen wwwroot.** Mer information finns i [Azure App Services: Förstå URL skriva om](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hur kontrollerar jag inkommande trafik till App Service?

På platsnivå har du två alternativ för att styra inkommande trafik till App Service:

* Aktivera dynamiska IP-begränsningar. Mer information om hur du aktiverar dynamiska IP-begränsningar finns i [IP- och domänbegränsningar för Azure-webbplatser](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Aktivera modulsäkerhet. Mer information om hur du aktiverar Modulsäkerhet finns i [ModSecurity-brandväggen för webbprogram på Azure-webbplatser](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Om du använder App Service Environment kan du använda [Barracuda-brandväggen](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hur blockerar jag portar i en App Service-webbapp?

I den delade klientmiljön för App-tjänsten är det inte möjligt att blockera specifika portar på grund av infrastrukturens natur. TCP-portar 4020, 4022 och 4024 kan också vara öppna för fjärrfelsökning i Visual Studio.

I App Service Environment har du full kontroll över inkommande och utgående trafik. Du kan använda Nätverkssäkerhetsgrupper för att begränsa eller blockera specifika portar. Mer information om App Service Environment finns i [Introduktion till App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hur fångar jag ett F12-spår?

Du har två alternativ för att fånga en F12-spårning:

* F12 HTTP-spårning
* F12-konsolutgång

### <a name="f12-http-trace"></a>F12 HTTP-spårning

1. Gå till din webbplats i Internet Explorer. Det är viktigt att logga in innan du gör nästa steg. Annars fångar F12-spårningen känsliga inloggningsdata.
2. Tryck på F12.
3. Kontrollera att fliken **Nätverk** är markerad och välj sedan den gröna **knappen Spela** upp.
4. Gör stegen som återskapar problemet.
5. Välj den röda **stoppknappen.**
6. Markera knappen **Spara** (diskikonen) och spara HAR-filen (i Internet Explorer och Microsoft Edge) *eller* högerklicka på HAR-filen och välj sedan **Spara som HAR med innehåll** (i Chrome).

### <a name="f12-console-output"></a>F12-konsolutgång

1. Välj fliken **Konsol.**
2. För varje flik som innehåller fler än noll objekt markerar du fliken (**Fel,** **Varning**eller **Information**). Om fliken inte är markerad är flikikonen grå eller svart när du flyttar markören bort från den.
3. Högerklicka i meddelandeområdet i fönstret och välj sedan **Kopiera alla**.
4. Klistra in den kopierade texten i en fil och spara sedan filen.

Om du vill visa en HAR-fil kan du använda [HAR-visningsprogrammet](https://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Varför får jag ett felmeddelande när jag försöker ansluta en App Service-webbapp till ett virtuellt nätverk som är anslutet till ExpressRoute?

Om du försöker ansluta en Azure-webbapp till ett virtuellt nätverk som är anslutet till Azure ExpressRoute misslyckas den. Följande meddelande visas: "Gateway är inte en VPN-gateway."

För närvarande kan du inte ha vpn-anslutningar från punkt till plats till ett virtuellt nätverk som är anslutet till ExpressRoute. En punkt-till-plats VPN och ExpressRoute kan inte samexistera för samma virtuella nätverk. Mer information finns i [Gränser och begränsningar för VPN-anslutningar för plats till plats.](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hur ansluter jag en App Service-webbapp till ett virtuellt nätverk som har en statisk routningsgateway (principbaserad)?

För närvarande stöds inte att ansluta en App Service-webbapp till ett virtuellt nätverk som har en statisk routningsgateway (principbaserad) gateway. Om ditt virtuella målnätverk redan finns måste det ha punkt-till-plats-VPN aktiverat, med en dynamisk routningsgateway, innan den kan anslutas till en app. Om din gateway är inställd på statisk routning kan du inte aktivera en punkt-till-plats-VPN. 

Mer information finns i [Integrera en app med ett virtuellt Azure-nätverk](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Varför kan jag bara skapa en apptjänstplan i apptjänstmiljön, även om jag har två anställda tillgängliga?

För att ge feltolerans kräver App Service Environment att varje arbetspool behöver minst en ytterligare beräkningsresurs. Den extra beräkningsresursen kan inte tilldelas en arbetsbelastning.

Mer information finns i [Så här skapar du en apptjänstmiljö](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Varför ser jag timeout när jag försöker skapa en apptjänstmiljö?

Ibland misslyckas det att skapa en apptjänstmiljö. I så fall visas följande fel i aktivitetsloggarna:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Lös detta genom att kontrollera att inget av följande villkor är sant:
* Undernätet är för litet.
* Undernätet är inte tomt.
* ExpressRoute förhindrar nätverksanslutningskraven för en App Service-miljö.
* En dålig nätverkssäkerhetsgrupp förhindrar nätverksanslutningskraven i en App Service-miljö.
* Påtvingad tunnel är påslagen.

Mer information finns i [Vanliga problem när du distribuerar (skapar) en ny Azure App Service Environment](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Varför kan jag inte ta bort min App Service-plan?

Du kan inte ta bort en App Service-plan om apptjänstappar är kopplade till App Service-planen. Innan du tar bort en apptjänstplan tar du bort alla associerade App Service-appar från App Service-planen.

## <a name="how-do-i-schedule-a-webjob"></a>Hur schemalägger jag ett WebJob??

Du kan skapa ett schemalagt WebJob med cron-uttryck:

1. Skapa en settings.job-fil.
2. I den här JSON-filen tar du med en schemaegenskap med hjälp av ett Cron-uttryck: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Mer information om schemalagda WebJobs finns i [Skapa ett schemalagt WebJob med hjälp av ett Cron-uttryck](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hur utför jag penetrationstester för min App Service-app?

Om du vill utföra penetrationstester [skickar du en begäran](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hur konfigurerar jag ett anpassat domännamn för en App Service-webbapp som använder Traffic Manager?

Mer information om hur du använder ett anpassat domännamn med en App Service-app som använder Azure Traffic Manager för belastningsutjämning finns i [Konfigurera ett anpassat domännamn för en Azure-webbapp med Traffic Manager](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mitt App Service Certificate har flaggats för bedrägeri. Hur gör jag för att lösa det?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Under domänverifieringen av ett köp av App Service-certifikat kan följande meddelande visas:

"Ditt intyg har flaggats för eventuellt bedrägeri. Begäran är för närvarande under granskning. Om certifikatet inte blir användbart inom 24 timmar kontaktar du Azure Support."

Som meddelandet anger kan det ta upp till 24 timmar att slutföra den här bedrägeriverifieringsprocessen. Under den här tiden fortsätter du att se meddelandet.

Om ditt App Service-certifikat fortsätter att visa det här meddelandet efter 24 timmar kör du följande PowerShell-skript. Skriptet kontaktar [certifikatleverantören](https://www.godaddy.com/) direkt för att lösa problemet.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hur fungerar autentisering och auktorisering i App Service?

Detaljerad dokumentation för autentisering och auktorisering i App Service finns i dokument för olika identifieringsproviders inloggningar:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-konto](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hur omdirigerar jag standarddomänen *.azurewebsites.net till min Azure-webbapps anpassade domän?

När du skapar en ny webbplats med hjälp av Web Apps i Azure tilldelas ett *standardwebbplatsnamn*.azurewebsites.net domän till din webbplats. Om du lägger till ett anpassat värdnamn på webbplatsen och inte vill att användarna ska kunna komma åt din standarddomän *.azurewebsites.net domän kan du omdirigera standard-URL:en. Mer information om hur du omdirigerar all trafik från webbplatsens standarddomän till din anpassade domän finns i [Omdirigera standarddomänen till din anpassade domän i Azure-webbappar](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hur tar jag reda på vilken version av .NET-versionen som är installerad i App Service?

Det snabbaste sättet att hitta den version av Microsoft .NET som är installerad i App Service är att använda Kudu-konsolen. Du kan komma åt Kudu-konsolen från portalen eller med hjälp av webbadressen till appen App Service. Detaljerade instruktioner finns i [Bestämma den installerade .NET-versionen i App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Varför fungerar inte automatisk skalning som förväntat?

Om Azure Autoscale inte har skalats in eller skalas ut webbappinstansen som förväntat, kanske du kör in i ett scenario där vi avsiktligt väljer att inte skala för att undvika en oändlig loop på grund av "flaxande". Detta händer vanligtvis när det inte finns en tillräcklig marginal mellan utskalnings- och skalningströsklarna. Mer information om hur du undviker att "flaxa" och läsa om andra metodtips för automatisk skalning finns i [Metodtips för automatisk skalning](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Varför skalas automatisk skalning ibland bara delvis?

Automatisk skalning utlöses när mått överskrider förkonfigurerade gränser. Ibland kanske du märker att kapaciteten bara är delvis fylld jämfört med vad du förväntade dig. Detta kan inträffa när antalet instanser du vill ha inte är tillgängliga. I det fallet fyller automatisk skalning delvis i med det tillgängliga antalet instanser. Automatisk skalning kör sedan ombalanseringslogiken för att få mer kapacitet. Den allokerar de återstående instanserna. Observera att det kan ta några minuter.

Om du inte ser det förväntade antalet instanser efter några minuter kan det bero på att den partiella påfyllningen var tillräckligt för att föra måtten inom gränserna. Automatisk skalning kan också ha skalats ned eftersom den nådde den nedre måttgränsen.

Om inget av dessa villkor gäller och problemet kvarstår skickar du en supportbegäran.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hur aktiverar jag HTTP-komprimering för mitt innehåll?

Om du vill aktivera komprimering både för statiska och dynamiska innehållstyper lägger du till följande kod i filen web.config på programnivå:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Du kan också ange de specifika dynamiska och statiska MIME-typer som du vill komprimera. Mer information finns i vårt svar på en forumfråga i [httpCompression-inställningar på en enkel Azure-webbplats](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hur migrerar jag från en lokal miljö till App Service?

Om du vill migrera webbplatser från Windows- och Linux-webbservrar till App Service kan du använda Migreringsassistenten för Azure App Service. Migreringsverktyget skapar webbappar och databaser i Azure efter behov och publicerar sedan innehållet. Mer information finns i [Migreringsassistenten för Azure App Service](https://appmigration.microsoft.com/).
