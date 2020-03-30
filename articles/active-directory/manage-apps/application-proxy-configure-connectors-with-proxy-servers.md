---
title: Arbeta med befintliga lokala proxyservrar och Azure AD | Microsoft-dokument
description: Beskriver hur du arbetar med befintliga lokala proxyservrar.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe3a63e119fed6825982b9de13bc78cb7da5415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481406"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Arbeta med befintliga lokala proxyservrar

I den här artikeln beskrivs hur du konfigurerar Azure Active Directory (Azure AD) Application Proxy-kopplingar för att arbeta med utgående proxyservrar. Den är avsedd för kunder med nätverksmiljöer som har befintliga proxyservrar.

Vi börjar med att titta på dessa huvudsakliga distributionsscenarier:

* Konfigurera kopplingar för att kringgå lokala utgående proxyservrar.
* Konfigurera kopplingar för att använda en utgående proxy för att komma åt Azure AD Application Proxy.

Mer information om hur anslutningsappar fungerar finns i avsnittet om att [förstå anslutningsappar för Azure AD-programproxy](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kringgå utgående proxyservrar

Anslutningsappar har underliggande OS-komponenter som gör att utgående begäranden. De här komponenterna försöker automatiskt hitta en proxyserver i nätverket med hjälp av Web Proxy Auto-Discovery (WPAD).

OS-komponenterna försöker hitta en proxyserver genom att utföra en DNS-sökning efter wpad.domänsuffix. Om sökningen matchar i DNS görs en HTTP-begäran till IP-adressen för wpad.dat. Den här begäran blir proxykonfigurationsskriptet i din miljö. Anslutningsappen använder det här skriptet för att välja en utgående proxyserver. Anslutningsappens trafik kommer dock kanske inte igenom på grund av ytterligare konfigurationsinställningar som behövs på proxyn.

Du kan konfigurera anslutningsappen till att kringgå den lokala proxyn för att säkerställa att den använder direktanslutning till Azure-tjänsterna. Vi rekommenderar den här metoden förutsatt att din nätverksprincip tillåter det, eftersom det innebär att det blir en färre konfiguration att underhålla.

Om du vill inaktivera utgående proxyanvändning för kopplingen redigerar du filen C:\Program\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config och lägger till *system.net* avsnittet som visas i det här kodexemplet:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Om du vill vara säker på att tjänsten Anslutningsuppdatering även kringgår proxyn gör du en liknande ändring av filen ApplicationProxyConnectorUpdaterService.exe.config. Den här filen finns på C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Se till att göra kopior av originalfilerna i fall du behöver återställa till de standardmässiga .config-filerna.

## <a name="use-the-outbound-proxy-server"></a>Använda den utgående proxyservern

Vissa miljöer kräver att all utgående trafik går igenom en utgående proxy, utan undantag. Därför är det inte ett alternativ att kringgå proxyn.

Du kan konfigurera anslutningstrafiken så att den går igenom den utgående proxyn, som visas i följande diagram:

 ![Konfigurera anslutningstrafik så att den går igenom en utgående proxy till Azure AD Application Proxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Som ett resultat av att endast ha utgående trafik behöver du inte konfigurera inkommande åtkomst via brandväggarna.

> [!NOTE]
> Programproxy stöder inte autentisering till andra proxyservrar. Nätverkstjänstkonton för anslutnings-/updater-anslutningstjänsten bör kunna ansluta till proxyn utan att ifrågasättas för autentisering.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Steg 1: Konfigurera anslutningen och relaterade tjänster för att gå igenom den utgående proxyn

Om WPAD är aktiverat i miljön och konfigurerat på rätt sätt identifierar anslutningen automatiskt den utgående proxyservern och försöker använda den. Du kan dock uttryckligen konfigurera kopplingen så att den går igenom en utgående proxy.

Det gör du genom att redigera filen C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config och lägga till *system.net* avsnittet som visas i det här kodexemplet. Ändra *proxyservern:8080* för att återspegla ditt lokala proxyservernamn eller IP-adress och porten som den lyssnar på. Värdet måste ha prefixet http:// även om du använder en IP-adress.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Konfigurera sedan tjänsten Connector Updater så att proxyn används genom att göra en liknande ändring av filen C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Steg 2: Konfigurera proxyn så att trafik från anslutningen och relaterade tjänster kan flöda genom

Det finns fyra aspekter att tänka på den utgående proxy:

* Regler för utgående proxy
* Proxyautentisering
* Proxyportar
* TLS inspektion

#### <a name="proxy-outbound-rules"></a>Regler för utgående proxy

Tillåt åtkomst till följande webbadresser:

| URL | Hur den används |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Kopplingen använder dessa url:er för att verifiera certifikat |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*.microsoftonline.com<br>*.microsoftonline-p.com<br>*.msauth.net<br>*.msauthimages.net<br>*.msecnd.net<br>*.msftauth.net<br>*.msftauthimages.net<br>*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Anslutningsprogrammet använder dessa webbadresser under registreringen. |

Om du kan konfigurera DNS-tillåt-listor i brandväggen \*eller proxyn kan \*du tillåta anslutningar till .msappproxy.net och .servicebus.windows.net. Om inte måste du tillåta åtkomst till [Azure DataCenter IP-intervallen](https://www.microsoft.com/download/details.aspx?id=41653). IP-adressintervallen uppdateras varje vecka.

Om du inte kan tillåta anslutning via FQDN och behöver ange IP-intervall i stället använder du följande alternativ:

* Tillåt utgående anslutning till alla destinationer.
* Tillåt den utgående åtkomsten till alla [AZURE datacenter-IP-intervall](https://www.microsoft.com//download/details.aspx?id=41653). Utmaningen med att använda listan över Azure datacenter IP-intervall är att det uppdateras varje vecka. Du måste införa en process för att se till att dina åtkomstregler uppdateras i enlighet med detta. Om du bara använder en delmängd av IP-adresserna kan konfigurationen brytas.

#### <a name="proxy-authentication"></a>Proxyautentisering

Proxyautentisering stöds för närvarande inte. Vår nuvarande rekommendation är att tillåta anslutning anonym åtkomst till Internet destinationer.

#### <a name="proxy-ports"></a>Proxyportar

Anslutningen gör utgående TLS-baserade anslutningar med hjälp av CONNECT-metoden. Den här metoden ställer i huvudsak in en tunnel genom den utgående proxyn. Konfigurera proxyservern så att tunnelning tillåts till portarna 443 och 80.

> [!NOTE]
> När Service Bus kör över HTTPS används port 443. Som standard försöker Dock Service Bus styra TCP-anslutningar och återgår till HTTPS endast om direkt anslutning misslyckas.

#### <a name="tls-inspection"></a>TLS inspektion

Använd inte TLS-inspektion för anslutningstrafiken eftersom det orsakar problem för anslutningstrafiken. Anslutningen använder ett certifikat för att autentisera till application proxy-tjänsten och det certifikatet kan gå förlorat under TLS-inspektionen.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Felsöka problem med anslutningsproxy och problem med tjänstanslutningen

Nu bör du se all trafik som flyter genom proxyn. Om du har problem bör följande felsökningsinformation vara till hjälp.

Det bästa sättet att identifiera och felsöka anslutningsproblem för anslutning till anslutning är att ta en nätverksinsamling när du startar anslutningstjänsten. Här är några snabba tips om hur du samlar in och filtrerar nätverksspårningar.

Du kan använda det övervakningsverktyg du väljer. I den här artikeln använde vi Microsoft Message Analyzer. Du kan [ladda ner den från Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Följande exempel är specifika för Message Analyzer, men principerna kan tillämpas på alla analysverktyg.

### <a name="take-a-capture-of-connector-traffic"></a>Ta en fångst av anslutningstrafik

För inledande felsökning gör du följande:

1. Från services.msc stoppar du Azure AD Application Proxy Connector-tjänsten.

   ![Azure AD-programproxyanslutningstjänst i services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Kör Message Analyzer som administratör.
1. Välj **Starta lokal spårning**.
1. Starta Azure AD Application Proxy Connector-tjänsten.
1. Stoppa nätverksfångsten.

   ![Skärmbild visar knappen Stoppa nätverksinspelning](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Kontrollera om anslutningstrafiken kringgår utgående proxyservrar

Om du har konfigurerat application proxy-anslutningen för att kringgå proxyservrarna och ansluta direkt till application proxy-tjänsten, vill du söka i nätverksfångsten efter misslyckade TCP-anslutningsförsök.

Använd filtret Message Analyzer för att identifiera dessa försök. Ange `property.TCPSynRetransmit` i filterrutan och välj **Använd**.

Ett SYN-paket är det första paketet som skickas för att upprätta en TCP-anslutning. Om det här paketet inte returnerar något svar sätts SYN på igen. Du kan använda filtret föregående för att se eventuella återsända SYN:er. Sedan kan du kontrollera om dessa SYN motsvarar någon anslutningsrelaterad trafik.

Om du förväntar dig att anslutningen ska göra direkta anslutningar till Azure-tjänsterna är SynRetransmit-svar på port 443 en indikation på att du har ett nätverks- eller brandväggsproblem.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Kontrollera om anslutningstrafiken använder utgående proxyservrar

Om du har konfigurerat anslutningstrafiken för Application Proxy så att den gick igenom proxyservrarna vill du leta efter misslyckade https-anslutningar till proxyn.

Om du vill filtrera nätverksinfångsten för dessa anslutningsförsök anger du `(https.Request or https.Response) and tcp.port==8080` i filtret Message Analyzer och ersätter 8080 med proxytjänstporten. Välj **Använd** om du vill visa filterresultaten.

Filtret föregående visar bara HTTPs-begäranden och svar på/från proxyporten. Du letar efter CONNECT-begäranden som visar kommunikation med proxyservern. När du lyckas får du ett HTTP OK -svar (200).

Om du ser andra svarskoder, till exempel 407 eller 502, innebär det att proxyn kräver autentisering eller inte tillåter trafiken av någon annan anledning. Nu angriper du supportteamet för proxyservern.

## <a name="next-steps"></a>Nästa steg

* [Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
* Om du har problem med anslutningsproblem med anslutning till anslutning till anslutning till anslutning kan du ställa din fråga i [Azure Active Directory-forumet](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) eller skapa en biljett med vårt supportteam.
