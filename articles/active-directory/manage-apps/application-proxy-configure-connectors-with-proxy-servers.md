---
title: Arbeta med befintliga lokala proxyservrar och Azure AD | Microsoft Docs
description: Beskriver hur du arbetar med befintliga lokala proxyservrar.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 6409b9313aa9b036e24ea50435659b3653ac01e0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720109"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Arbeta med befintliga lokala proxyservrar

Den här artikeln förklarar hur du ställer in Azure Active Directory (Azure AD) Application Proxy-kopplingar att arbeta med utgående proxy-servrar. Den är avsedd för kunder med nätverksmiljöer som har befintliga proxyservrar.

Vi börjar genom att titta på dessa huvudsakliga scenarier:
* Konfigurera anslutningar för att kringgå din lokala utgående proxyservrar.
* Konfigurera att kopplingar ska använda en utgående proxy för att komma åt Azure AD-programproxy.

Mer information om hur anslutningsappar fungerar finns i [alternativ för Azure AD-programproxyn kopplingar](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kringgå utgående proxyservrar

Kopplingar har underliggande OS-komponenter som gör att utgående begäranden. De här komponenterna försöker automatiskt hitta en proxyserver i nätverket med hjälp av Web Proxy Auto-Discovery (WPAD).

OS-komponenter som försöker hitta en proxyserver genom att utföra en DNS-sökning för wpad.domainsuffix. Om sökningen matchar i DNS, görs sedan en HTTP-begäran till IP-adressen för wpad.dat. Den här begäran blir proxykonfigurationsskript i din miljö. Anslutningsappen använder det här skriptet för att välja en utgående proxy-server. Men kan anslutningen trafik fortfarande inte går via, på grund av fler konfigurationsinställningar som krävs på proxyn.

Du kan konfigurera anslutningsprogrammet för att kringgå proxyservern på plats för att säkerställa att den använder direkt anslutning till Azure-tjänster. Vi rekommenderar den här metoden så länge nätverksprincipen tillåter det, eftersom det innebär att du har en mindre konfiguration att underhålla.

För att inaktivera utgående proxy-användning för anslutningen, redigerar du C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config-filen och Lägg till den *system.net* avsnittet som visas i detta kodexempel:

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
För att säkerställa att tjänsten Connector Updater kringgår proxyn, ändra liknande ApplicationProxyConnectorUpdaterService.exe.config-filen. Den här filen finns i C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Tänk på att göra kopior av de ursprungliga filerna om du behöver att återgå till standard .config-filer.

## <a name="use-the-outbound-proxy-server"></a>Använda utgående proxy-server

Vissa kräver all utgående trafik att gå via en utgående proxy, utan undantag. Kringgå proxyn är därför inte ett alternativ.

Du kan konfigurera anslutningen trafik gå igenom utgående proxy som visas i följande diagram:

 ![Konfigurera anslutningen trafik att gå via en utgående proxy till Azure AD Application Proxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Till följd av med endast utgående trafik, finns inget behov att konfigurera inkommande åtkomst genom dina brandväggar.

>[!NOTE]
>Programproxyn har inte stöd för autentisering till andra proxyservrar. Anslutning/updater network service-konton ska kunna ansluta till proxyservern utan att företagsobjekt för autentisering.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Steg 1: Konfigurera anslutningen och relaterade tjänster gå igenom utgående proxy

Om WPAD är aktiverad i miljön och korrekt konfigurerad, identifierar anslutningen automatiskt utgående proxyserver och försök att använda den. Du kan uttryckligen Konfigurera anslutningen att gå via en utgående proxy.

Du gör detta genom att redigera filen C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config och lägga till den *system.net* avsnittet som visas i det här kodexemplet. Ändra *proxyserver:8080* att återspegla din lokala Proxyservernamn eller IP-adress och den port som lyssnar på. Värdet måste innehålla prefixet http:// även om du använder en IP-adress.

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

Konfigurera Connector Updater-tjänsten om du vill använda proxyservern genom att göra en liknande ändring till C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config-fil.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Steg 2: Konfigurera proxyn för att tillåta trafik från anslutningen och relaterade tjänster kan passera

Det finns fyra aspekter att tänka på vid utgående proxy:
* Proxy utgående regler
* Proxyautentisering
* Proxy-portar
* SSL-kontroll

#### <a name="proxy-outbound-rules"></a>Proxy utgående regler
Tillåt åtkomst till följande webbadresser:

| URL | Hur den används |
| --- | --- |
| \*.msappproxy.net<br>\*. servicebus.windows.net | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure använder dessa webbadresser för att verifiera certifikat |
| login.windows.net<br>login.microsoftonline.com | Anslutningsprogrammet använder dessa webbadresser under registreringen. |

Om din brandvägg eller proxyserver kan du DNS-listan över tillåtna program, kan du lista över tillåtna anslutningar till \*. msappproxy.net och \*. servicebus.windows.net. Om inte, du vill tillåta åtkomst till den [Azure DataCenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). IP-adressintervall uppdateras varje vecka.


Om du inte kan du ansluta efter FQDN och måste du ange IP-intervall i stället, kan du använda följande alternativ:

* Tillåt anslutningen utgående åtkomst till alla måldatorer.
* Tillåt åtkomst till utgående anslutning till alla de [Azure datacenter IP-adressintervall](https://www.microsoft.com//download/details.aspx?id=41653). Den stora utmaningen med hjälp av listan med Azure-datacenter IP-adressintervall är uppdaterad varje vecka. Du måste placera en process för att säkerställa att din åtkomstregler uppdateras. Endast med hjälp av en delmängd av IP-adresser kan det leda till att din konfiguration och dela.

#### <a name="proxy-authentication"></a>Proxyautentisering

Proxy-autentisering stöds inte för närvarande. Våra aktuella rekommendation är att ge connector anonym åtkomst till Internet-mål.

#### <a name="proxy-ports"></a>Proxy-portar

Anslutningen gör utgående SSL-baserade anslutningar med hjälp av metoden CONNECT. Den här metoden är i stort sett ställer in en tunnel via utgående proxy. Konfigurera proxyserver för att tillåta tunnlar för att portarna 443 och 80.

>[!NOTE]
>När Service Bus körs via HTTPS, använder port 443. Dock försöker direkt TCP-anslutningar Service Bus som standard och faller tillbaka till HTTPS om direktanslutning inte.

#### <a name="ssl-inspection"></a>SSL-kontroll
Använd inte SSL-kontroll för connector-trafik, eftersom det orsakar problem för connector-trafik. Anslutningen använder ett certifikat för att autentisera till tjänsten Application Proxy och certifikatet kan gå förlorade under SSL-kontroll. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Felsöka problem med anslutningen proxy och anslutningsproblem för tjänsten
Du bör nu se all trafik som passerar genom proxyn. Om du har problem med ska följande felsökningsinformation hjälpa.

Det bästa sättet att identifiera och Felsök problem med nätverksanslutningen connector är att ta en nätverksbild vid start kopplingstjänsten. Här följer några snabba tips på samla in och filtrera nätverksspår.

Du kan använda övervakning verktyg som helst. För den här artikeln använde vi Microsoft Message Analyzer. Du kan [ladda ned det från Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

I följande exempel är specifika för Message Analyzer, men principerna kan tillämpas på alla analysverktyget.

### <a name="take-a-capture-of-connector-traffic"></a>Ta en avbildning av connector-trafik

Utför följande steg för inledande Felsökning:

1. Stoppa tjänsten Azure AD Application Proxy Connector från services.msc.

   ![Azure AD Application Proxy Connector-tjänsten i services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Kör Message Analyzer som administratör.
3. Välj **starta lokala spårning**.

   ![Starta nätverksbild](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Starta Azure AD Application Proxy Connector-tjänsten.
4. Stoppa insamlingen nätverk.

   ![Stoppa nätverksbild](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Kontrollera om koppling trafiken kringgår utgående proxyservrar

Om du har konfigurerat programproxy-kopplingen för att kringgå proxy-servrar och ansluta direkt till Application Proxy-tjänsten som du vill söka i nätverksbild för misslyckade TCP-anslutningsförsök. 

Med filtret Message Analyzer för att identifiera dessa försök. Ange `property.TCPSynRetransmit` i filterrutan och välj **tillämpa**. 

SYN-paket är det första paketet skickas för att upprätta en TCP-anslutning. Om det här paketet inte returnerar något svar, är SYN återförsöks. Du kan använda föregående filtret för att se alla att Sym. Du kan sedan kontrollera om de här Sym motsvarar all trafik som connector-relaterade.

Om du förväntar dig connector att ansluta direkt till Azure-tjänster är en indikation på att du har ett problem med nätverket eller brandväggen SynRetransmit svar på port 443.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Kontrollera om connector-trafik använder utgående proxyservrar

Om du har konfigurerat Application Proxy connector trafiken gå igenom proxyservrar som du vill leta efter misslyckade https-anslutningar till proxyservern. 

Om du vill filtrera nätverksbild för dessa anslutningsförsök, ange `(https.Request or https.Response) and tcp.port==8080` i filtret Message Analyzer ersätta 8080 med din tjänst Proxyport. Välj **tillämpa** att visa filterresultaten. 

Föregående filtret visar bara HTTPs-begäranden och svar till och från proxyporten. Du letar efter CONNECT-begäranden som visar kommunikation med proxyservern. Vid en lyckad distribution, kan du få ett svar för HTTP-OK (200).

Om du ser andra svarskoder, till exempel 407 eller 502, som innebär att proxyservern kräver autentisering eller inte tillåter trafiken av någon anledning. Nu kan engagera du supportteamet proxy server.

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)

- Om du har problem med anslutningen anslutningsproblem kan du ställa din fråga i den [Azure Active Directory-forumet](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) eller skapa ett ärende med vårt supportteam.
