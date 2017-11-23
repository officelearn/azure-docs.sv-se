---
title: "Konfiguration av vanliga frågor och svar för Azure-webbappar | Microsoft Docs"
description: "Få svar på vanliga frågor och svar om konfiguration och hantering av problem för funktionen Web Apps i Azure App Service."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 92cbc36ac2a566cf5dfbb2f7b3347973bab5ee8c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfiguration och hantering av vanliga frågor och svar för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om konfiguration och hantering av problem för den [funktionen Web Apps i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Finns det några begränsningar som jag bör känna till om du vill flytta Apptjänst resurser?

Om du planerar att flytta Apptjänst resurser till en ny resursgrupp eller prenumeration finns det några begränsningar som du bör känna till. Mer information finns i [Apptjänst begränsningar](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hur använder ett anpassat domännamn för webbappen?

Svar på vanliga frågor om hur du använder ett anpassat domännamn med ditt Azure webbapp finns i vår sju minutersvideo [lägga till ett anpassat domännamn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Videon erbjuder en genomgång av hur du lägger till ett anpassat domännamn. Beskriver hur du använder din egen URL i stället för den *. azurewebsites.net URL med din App Service webbapp. Du kan också se en detaljerad genomgång av [så att mappa ett anpassat domännamn](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hur köpa en ny anpassad domän för webbappen?

Information om hur du köper och konfigurera en anpassad domän för din webbapp i App Service finns [köpa och konfigurera ett anpassat domännamn i App Service](custom-dns-web-site-buydomains-web-app.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Hur gör överför och konfigurera ett SSL-certifikat för webbappen?

Information om hur du överför och konfigurerar ett befintligt anpassat SSL-certifikat finns [binda ett befintligt anpassat SSL-certifikat till en Azure-webbapp](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Hur gör köp och konfigurera ett nytt SSL-certifikat i Azure för webbappen?

Information om hur du köper och ställa in ett SSL-certifikat för din webbapp i App Service finns [Lägg till ett SSL-certifikat i appen Apptjänst](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Hur jag för att flytta Application Insights-resurser?

Azure Application Insights stöder för närvarande inte flyttas. Om din ursprungliga resursgrupp innehåller en Application Insights-resurs, kan du inte flytta den här resursen. Om du inkluderar Application Insights-resursen när du försöker flytta en Apptjänst-app, flytta hela åtgärden misslyckas. Dock behöver Application Insights och programtjänstplanen inte finnas i samma resursgrupp som appen för appen ska fungera korrekt.

Mer information finns i [Apptjänst begränsningar](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Var kan jag hitta en checklista för vägledning och lära dig mer om resursen flytta åtgärder?

[Apptjänst begränsningar](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) visar hur du flyttar resurser till en ny prenumeration eller till en ny resursgrupp med samma prenumeration. Du kan hämta information om resurs flytta checklista, Läs mer om tjänster som stöder flyttåtgärden och mer information om begränsningar för App Service och andra avsnitt.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hur ställer jag in serverns tidszon för webbappen

Ange tidszonen för server för ditt webbprogram:

1. I Azure-portalen i din App Service-prenumeration går du till den **programinställningar** menyn.
2. Under **appinställningar**, lägga till den här inställningen:
    * Nyckel = WEBSITE_TIME_ZONE
    * Värde = *tidszon som du vill använda*
3. Välj **Spara**.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Varför min kontinuerliga Webbjobb Ibland misslyckas?

Som standard inaktiveras webbappar om de är inaktiv under en angiven tidsperiod. Detta gör att systemet spara resurser. I Basic och Standard planer, kan du aktivera den **alltid på** anger att webbprogrammet lästs in hela tiden. Om ditt webbprogram kör kontinuerliga Webbjobb, bör du aktivera **alltid på**, eller WebJobs kanske inte körs på ett tillförlitligt sätt. Mer information finns i [skapa ett Webbjobb som körs kontinuerligt](web-sites-create-web-jobs.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hur skaffar jag utgående IP-adressen för webbappen

Att hämta listan över utgående IP-adresser för ditt webbprogram:

1. I Azure-portalen på web app-bladet går du till den **egenskaper** menyn.
2. Sök efter **utgående ip-adresser**.

Lista över utgående IP-adresser visas.

Om din webbplats finns i Apptjänst-miljö för PowerApps, information om hur du hämtar din utgående IP-adress finns [utgående nätverksadresser](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hur skaffar jag en reserverad eller dedikerade inkommande IP-adress för webbappen

Om du vill konfigurera en dedikerad eller reserverade IP-adress för inkommande anrop till din webbplats för Azure-program, installera och konfigurera ett IP-baserade SSL-certifikat.

Observera att din App Service-plan måste vara för att använda en dedikerad eller reserverade IP-adress för inkommande anrop, i en grundläggande eller senare serviceplan.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kan jag exportera min App Service certifikat utanför Azure, såsom för en webbplats finns den någon annanstans? 

Apptjänstcertifikat betraktas som Azure-resurser. De är inte avsedda att användas utanför Azure-tjänster. Du kan inte exportera dem för användning utanför Azure. Mer information finns i [vanliga frågor och svar för apptjänstcertifikat och anpassade domäner](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kan jag exportera min App Service-certifikat som ska användas med andra Azure-molntjänster?

Portalen innehåller en förstklassig miljö för att distribuera ett certifikat för App Service via Azure Key Vault till Apptjänst-appar. Men har vi fått begäranden från kunderna att använda dessa certifikat utanför den Apptjänst-plattformen, till exempel med virtuella datorer i Azure. Information om hur du skapar en lokal PFX-kopia av din App Service-certifikat så att du kan använda certifikatet med andra Azure-resurser finns [skapa en lokal PFX-kopia av ett certifikat för App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Mer information finns i [vanliga frågor och svar för apptjänstcertifikat och anpassade domäner](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Varför visas meddelandet ”lyckades delvis” när jag försöker att säkerhetskopiera webbappen?

En vanlig orsak till Säkerhetskopieringsfel är att vissa filer som används av programmet. Filer som används är låsta när du säkerhetskopierar. Detta förhindrar att de här filerna säkerhetskopieras och kan resultera i statusen ”delvis har slutförts”. Du kan eventuellt förhindra detta genom att utesluta filer från säkerhetskopieringen. Du kan välja att säkerhetskopiera som krävs. Mer information finns i [säkerhetskopiera de viktiga delarna av webbplatsen med Azure webbappar](http://www.zainrizvi.io/2015/06/05/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hur tar jag bort ett sidhuvud från HTTP-svaret?

Uppdatera din webbplats web.config-filen för att ta bort pakethuvudena från HTTP-svaret. Mer information finns i [ta bort standard server rubriker på Azure-webbplatser](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Är Apptjänst som är kompatibel med PCI Standard 3.0 och 3.1?

För närvarande är funktionen Web Apps i Azure App Service kompatibla med PCI Data Security Standard (DSS) version 3.0 nivå 1. PCI DSS version 3.1 finns på vår översikt. Planera pågår redan för hur senaste standarden kommer att fortsätta.

PCI DSS version 3.1 certifikatutfärdare kräver inaktiverar Transport Layer Security (TLS) 1.0. Inaktivera TLS 1.0 är för närvarande inte ett alternativ för de flesta App Service-planer. Om du använder Apptjänstmiljö eller är beredd att migrera din arbetsbelastning till Apptjänst-miljö kan du få större kontroll över din miljö. Detta innebär att inaktivera TLS 1.0 genom att kontakta Azure-supporten. I den nära framtiden kommer planerar vi att dessa inställningar är tillgängliga för användare.

Mer information finns i [Microsoft Azure App Service web appkompatibilitet med PCI Standard 3.0 och 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hur använder fristående miljö och distribution platserna?

I Standard- och Premium App Service-planer, när du distribuerar ditt webbprogram till App Service kan du distribuera till en separat distributionsplats i stället för till standard produktionsplatsen. Distributionsplatser är live-webb-appar som har sina egna värdnamn. Web app innehåll och konfiguration element kan växlas mellan två distributionsplatser, inklusive produktionsplatsen.

Mer information om hur du använder distributionsplatser finns [skapa mellanlagringsmiljön i App Service](web-sites-staged-publishing.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hur gör åtkomst och granska loggarna för Webbjobb?

Granska Webbjobb loggar:

1. Logga in på ditt [Kudu webbplats](https://*yourwebsitename*.scm.azurewebsites.net).
2. Välj Webbjobbet.
3. Välj den **växla utdata** knappen.
4. Ladda ned filen, Välj den **hämta** länk.
5. Enskilda körs Välj **enskilda anropa**.
6. Välj den **växla utdata** knappen.
7. Välj länken.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Jag försöker använda Hybridanslutningar med SQL Server. Varför visas meddelandet ”System.OverflowException: aritmetiska operationen orsakade spill”?

Om du använder Hybridanslutningar för att få åtkomst till SQL Server kan orsaka en Microsoft .NET-uppdatering på 10 maj 2016 anslutningar misslyckas. Du kan se det här meddelandet:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Lösning

Undantaget orsakades av ett problem med Hybridanslutningshanteraren som sedan har åtgärdats. Se till att [uppdatera din Hybridanslutningshanteraren](https://go.microsoft.com/fwlink/?LinkID=841308) att lösa problemet.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Hur jag för att lägga till eller redigera en URL-omskrivning regel?

Om du vill lägga till eller redigera en URL-omskrivning om regeln:

1. Konfigurera Internet Information Services (IIS) Manager så att den ansluter till din Apptjänst-webbapp. Information om hur du ansluter IIS-hanteraren till App Service finns [fjärradministration av Azure webbplatser med hjälp av IIS-hanteraren](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. I IIS-hanteraren, Lägg till eller redigera en URL-omskrivning regel. Information om hur du lägger till eller redigera en URL-omskrivning regel finns [skapa omarbetning regler för URL: en omarbetning modulen](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hur kan jag styra inkommande trafik till App Service?

På webbplatsnivå har du två alternativ för att styra inkommande trafik till App Service:

* Aktivera dynamisk IP-begränsningar. Information om hur du aktiverar dynamisk IP-begränsningar finns [IP- och begränsningar för Azure websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Aktivera modulen säkerhet. Information om hur du aktiverar säkerhet för modulen finns [Brandvägg för ModSecurity webbaserade program på Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Om du använder Apptjänst-miljö kan du använda [Barracuda brandväggen](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hur blockerar portar i en App Service webbapp?

I Apptjänst-miljö för delade klient går inte att blockera specifika portar på grund av arten av infrastrukturen. TCP-portar 4016 och 4018 4020 också vara öppen för Visual Studio fjärrfelsökning.

I Apptjänst-miljön har du full kontroll över inkommande och utgående trafik. Du kan använda Nätverkssäkerhetsgrupper för att begränsa eller blockerar specifika portar. Mer information om Apptjänst-miljö finns [introduktion till Apptjänstmiljö](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hur jag för att avbilda en F12 trace?

Har du två alternativ för att samla in en F12-spårning:

* F12 http-spårning
* F12 konsolens utdata

### <a name="f12-http-trace"></a>F12 http-spårning

1. Gå till webbplatsen i Internet Explorer. Det är viktigt att logga in innan du gör i nästa steg. Annars avbildar F12 spårningen känsliga inloggning data.
2. Tryck på F12.
3. Kontrollera att den **nätverk** är markerad och välj sedan gröna **spela upp** knappen.
4. Gör som återskapa problemet.
5. Välj röda **stoppa** knappen.
6. Välj den **spara** knappen (diskikonen) och spara filen HAR (i Internet Explorer och Edge) *eller* Högerklicka på filen HAR och välj sedan **Spara som HAR med innehåll** (i Chrome).

### <a name="f12-console-output"></a>F12 konsolens utdata

1. Välj den **konsolen** fliken.
2. Välj fliken för varje flik som innehåller fler än noll objekt (**fel**, **varning**, eller **Information**). Om inte är markerat på fliken är tab-ikon grå eller svart när du flyttar markören från den.
3. Högerklicka i meddelandeområdet i fönstret och välj sedan **kopiera alla**.
4. Klistra in den kopierade texten i en fil och sedan spara filen.

Om du vill visa en fil HAR kan du använda den [HAR viewer](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Varför visas ett fel när jag försöker ansluta en Apptjänst webbprogrammet till ett virtuellt nätverk som är ansluten till ExpressRoute?

Om du försöker ansluta en Azure-webbapp till ett virtuellt nätverk som har anslutit till Azure ExpressRoute misslyckas. Följande meddelande visas: ”Gateway är inte en VPN-gateway”.

Du kan för närvarande inte punkt-till-plats VPN-anslutningar till ett virtuellt nätverk som är ansluten till ExpressRoute. En punkt-till-plats VPN- och ExpressRoute kan inte samexistera för samma virtuella nätverk. Mer information finns i [ExpressRoute- och plats-till-plats VPN-anslutningar gränser och begränsningar](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hur ansluter en Apptjänst-webbapp till ett virtuellt nätverk som har en statisk routning (principbaserad) gateway?

För närvarande kan stöds ansluta en webbapp i App Service till ett virtuellt nätverk som har en statisk routning (principbaserad) gateway inte. Om det virtuella nätverket målet redan finns måste den ha punkt-till-plats VPN är aktiverat innan den kan anslutas till en app med en dynamisk routning gateway. Om din gateway är inställd på statisk routning, kan du inte aktivera en punkt-till-plats-VPN. 

Mer information finns i [integrera en app med Azure-nätverk](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>I min Apptjänst-miljö, varför kan jag skapa endast en App Service-plan, trots att jag har två personer som är tillgängliga?

För att ge feltolerans kräver Apptjänst-miljön att varje arbetspool måste minst en ytterligare beräkningsresurser. Ytterligare beräkningsresurser kan inte tilldelas en arbetsbelastning.

Mer information finns i [så här skapar du en Apptjänst-miljö](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Varför ser jag timeout när jag försöker skapa en Apptjänst-miljö?

Ibland kan misslyckas skapa en Apptjänst-miljö. I så fall kan se du följande fel i loggarna för aktiviteten:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Kontrollera att ingen av följande villkor är sant för att lösa problemet:
* Undernätet är för liten.
* Undernätet är inte tom.
* ExpressRoute förhindrar anslutningskrav av en Apptjänst-miljö.
* En felaktig Nätverkssäkerhetsgrupp förhindrar anslutningskrav av en Apptjänst-miljö.
* Tvingad tunneling är aktiverat.

Mer information finns i [frekventa problem när du distribuerar (skapa) en ny Azure Apptjänst-miljön](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Varför kan jag ta bort App Service-plan

Du kan inte ta bort en apptjänstplan om Apptjänst-appar som är associerade med App Service-plan. Ta bort alla associerade Apptjänst-appar från App Service-plan innan du tar bort en programtjänstplan.

## <a name="how-do-i-schedule-a-webjob"></a>Hur jag för att schemalägga ett Webbjobb?

Du kan skapa ett schemalagda Webbjobb med hjälp av Cron-uttryck:

1. Skapa en settings.job-fil.
2. I JSON-filen är en schema-egenskap med ett Cron-uttryck: 
    ```
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Läs mer om schemalagda WebJobs [skapa schemalagda Webbjobb med hjälp av ett Cron-uttryck](web-sites-create-web-jobs.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hur gör intrång testa för Apptjänst-app?

Att utföra intrång tester [skicka en begäran om](https://security-forms.azure.com/penetration-testing/terms).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Konfigurera ett anpassat domännamn för en Apptjänst-webbapp som använder Traffic Manager?

Information om hur du använder ett anpassat domännamn med en Apptjänst-appar som använder Azure Traffic Manager för belastningsutjämning finns [och konfigurera ett anpassat domännamn för en Azure-webbapp med Traffic Manager](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Min App Tjänstcertifikatet har flaggats för bedrägeri. Hur lösa problemet?

Under domänverifiering från inköpsdatumet en Apptjänst-certifikat, kan du se följande meddelande:

”Ditt certifikat har flaggats för eventuellt bedrägeriförsök. Begäran är för närvarande granskas. Om certifikatet inte är användbar inom 24 timmar, kontakta Azure Support ”.

När meddelandet anger kan verifieringsprocessen bedrägeri ta upp till 24 timmar. Under denna tid fortsätter du att se meddelandet.

Om din App tjänstcertifikat fortsätter att visa det här meddelandet efter 24 timmar, kör du följande PowerShell-skript. Skriptet kontakter i [certifikatutfärdaren](https://www.godaddy.com/) direkt för att lösa problemet.

```
Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hur autentisering och auktorisering fungerar i App Service?

Detaljerad dokumentation för autentisering och auktorisering i Apptjänst finns i dokumentation för olika identifiera providern inloggningar:
* [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Microsoft-konto](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hur omdirigera standard *. azurewebsites.net domän till anpassad domän för min Azure webbapp?

När du skapar en ny webbplats med hjälp av Web Apps i Azure, standard *sitename*. azurewebsites.net tilldelas till platsen. Om du lägger till ett anpassat värdnamn din webbplats och inte vill att användarna ska kunna få åtkomst till din standard *. azurewebsites.net domän, kan du omdirigera standard-URL. Information om hur du dirigera om all trafik från standarddomän för din webbplats till den anpassade domänen finns [omdirigera standarddomänen till domänen i Azure web apps](http://www.zainrizvi.io/2016/04/07/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hur tar jag reda på vilken version av .NET version är installerad i App Service?

Det är det snabbaste sättet att hitta version av Microsoft .NET som är installerad i App Service med hjälp av Kudu-konsolen. Du kan öppna Kudu-konsolen från portalen eller med hjälp av URL-Adressen för din Apptjänst-app. Detaljerade instruktioner finns [avgör den installerade .NET-versionen i App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Varför inte Autoskala fungerar som förväntat?

Om Azure Autoskalningsfunktionen har inte skalas i eller utskalad web app-instansen som du väntade dig kanske körs i ett scenario där vi avsiktligt välja inte att skala för att undvika en oändlig loop på grund av ”växlar”. Detta inträffar vanligtvis när det inte finns en tillräcklig marginal mellan skalbar och skala i tröskelvärden. Information om hur du undviker ”växlar” och Läs mer om andra Autoskala metodtips finns [Autoskala metodtips](../monitoring-and-diagnostics/insights-autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Varför Autoskala ibland skalas endast delvis?

Autoskala utlöses när mått överstiger förkonfigurerade gränser. Ibland kanske du märker att är bara delvis fyllda jämfört med vad du förväntade dig. Detta kan inträffa när antalet instanser som du vill använda inte är tillgängliga. I det scenariot fyller Autoskala delvis in med det tillgängliga antalet instanser. Autoskala körs sedan balansera logik för att få mer kapacitet. Den allokerar återstående instanser. Observera att det kan ta några minuter.

Om du inte ser det förväntade antalet instanser efter några minuter, kanske eftersom delvis påfyllning var tillräckligt för att sätta mått inom gränserna. Eller så kanske har skalats Autoskala eftersom den nått den nedre gränsen för mått.

Om ingen av dessa villkor gäller och problemet kvarstår kan du skicka en supportbegäran.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hur aktiverar jag HTTP-komprimering för min innehåll?

Lägg till följande kod på programnivå web.config-filen om du vill aktivera komprimering både för statiska och dynamiska typer av innehåll:

```
<system.webServer>
<urlCompression doStaticCompression="true" doDynamicCompression="true" />
< /system.webServer>
```

Du kan också ange vilka dynamiska och statiska MIME-typer som du vill komprimera. Mer information finns i vår svar på en forum fråga i [httpCompression inställningarna på en enkel Azure-webbplatsen](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hur jag migrera från en lokal miljö till App Service?

Om du vill migrera platser från Windows- och Linux-webbservrar till App Service, kan du använda Azure App Service Migration Assistant. Migreringsverktyget skapar webbappar och databaser i Azure efter behov och sedan publicerar innehållet. Mer information finns i [Azure App Service Migration Assistant](https://www.migratetoazure.net/).
