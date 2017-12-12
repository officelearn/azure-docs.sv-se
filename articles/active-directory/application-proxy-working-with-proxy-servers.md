---
title: Arbeta med befintliga lokala proxyservrar och Azure AD | Microsoft Docs
description: Beskriver hur du arbetar med befintliga lokala proxyservrar.
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: d8573ff43d9a2d0a356cb75db3abd7e107557a4e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Arbeta med befintliga lokala proxyservrar

Den här artikeln beskriver hur du konfigurerar Azure Active Directory (Azure AD) Application Proxy kopplingar att arbeta med utgående proxy-servrar. Det är avsett för kunder med nätverksmiljöer som har befintliga proxyservrar.

Vi börjar med att titta på dessa huvudsakliga distributionsscenarier:
* Konfigurera att kopplingar för att kringgå din lokala utgående proxyservrar.
* Konfigurera att kopplingar ska använda en utgående proxy för att komma åt Azure AD Application Proxy.

Mer information om hur kopplingar fungerar finns [förstå Azure AD Application Proxy kopplingar](application-proxy-understand-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kringgå utgående proxyservrar

Kopplingar har underliggande OS-komponenter som begär utgående. De här komponenterna försöker automatiskt hitta en proxyserver i nätverket med hjälp av Web Proxy Auto-Discovery (WPAD).

OS-komponenter kommer att försöka hitta en proxyserver genom att utföra en DNS-sökning för wpad.domainsuffix. Om sökningen matchas i DNS, görs sedan en HTTP-begäran till IP-adressen för wpad.dat. Denna begäran blir proxykonfigurationsskript i din miljö. Anslutningen används det här skriptet för att välja en utgående proxyserver. Dock kan connector trafik fortfarande inte går via, på grund av ytterligare konfigurationsinställningar som behövs på proxyservern.

Du kan konfigurera anslutningen för att kringgå proxyservern lokalt för att säkerställa att den använder direkt anslutning till Azure-tjänster. Vi rekommenderar den här metoden så länge nätverksprincipen tillåter det, eftersom det innebär att du har en mindre konfiguration att underhålla.

Om du vill inaktivera utgående proxy-användning för anslutningen, redigerar du filen C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config och lägga till den *system.net* avsnittet som visas i det här kodexemplet:

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
För att säkerställa att tjänsten Connector Updater också kringgår proxy, ändra liknande ApplicationProxyConnectorUpdaterService.exe.config-filen. Den här filen finns i C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Se till att göra kopior av de ursprungliga filerna om du behöver återgå till standard .config-filer.

## <a name="use-the-outbound-proxy-server"></a>Utgående proxyserver används

Vissa miljöer kräver all utgående trafik att gå via en utgående proxy utan undantag. Kringgå proxy är därför inte ett alternativ.

Du kan konfigurera connector trafiken gå igenom utgående proxy, som visas i följande diagram:

 ![Konfigurera anslutningen trafik att gå via en utgående proxy till Azure AD Application Proxy](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Det finns behöver du inte konfigurera inkommande åtkomst via dina brandväggar på grund av att endast utgående trafik.

>[!NOTE]
>Application Proxy stöder inte autentisering till andra proxyservrar. Tjänstkonton för koppling/updater nätverk ska kunna ansluta till proxyservern utan som anropas för autentisering.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Steg 1: Konfigurera anslutningen och tillhörande tjänster gå igenom utgående proxy

Om WPAD är aktiverad i miljön och korrekt konfigurerad, upptäcker kopplingen automatiskt utgående proxyserver och försök att använda den. Du kan uttryckligen konfigurera kopplingen för att gå igenom en utgående proxy.

Gör du genom att redigera filen C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config och lägga till den *system.net* avsnittet som visas i det här kodexemplet. Ändra *proxyserver:8080* återspeglar din lokala Proxyservernamn eller IP-adress och port som lyssnar på.

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

Konfigurera Connector Updater-tjänsten om du vill använda proxyservern genom att göra en liknande ändring i C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config-filen.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Steg 2: Konfigurera proxyn för att tillåta trafik från koppling och relaterade tjänster kan passera

Det finns fyra olika aspekter att tänka på utgående proxy:
* Proxy utgående regler
* Proxyautentisering
* Proxy-portar
* SSL-kontroll

#### <a name="proxy-outbound-rules"></a>Proxy utgående regler
Tillåt åtkomst till följande slutpunkter för connector service åtkomst:

* *. msappproxy.net
* *. servicebus.windows.net

Tillåt åtkomst till följande slutpunkter för inledande registrering:

* login.windows.net
* login.microsoftonline.com

Om du inte tillåter anslutningar av FQDN och måste du ange IP-adressintervall i stället kan du använda följande alternativ:

* Tillåt anslutningen utgående åtkomst till alla måldatorer.
* Tillåt anslutningen utgående åtkomst till alla de [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/en-gb/download/details.aspx?id=41653). Utmaningen med hjälp av listan över IP-adressintervall för Azure-datacenter är uppdateras varje vecka. Du måste placera en process för att säkerställa att din åtkomstregler uppdateras. Endast med en delmängd av IP-adresser kan orsaka konfigurationen att bryta.

#### <a name="proxy-authentication"></a>Proxyautentisering

Proxy-autentisering stöds inte för närvarande. Vår aktuell rekommendation är att kopplingen anonym åtkomst till Internet-mål.

#### <a name="proxy-ports"></a>Proxy-portar

Kopplingen gör utgående SSL-baserad anslutningar med hjälp av metoden CONNECT. Den här metoden anger i princip en tunnel genom den utgående proxyn. Konfigurera proxyservern så att portarna 443 och 80-tunneltrafik.

>[!NOTE]
>När Service Bus körs via HTTPS, använder port 443. Dock försöker TCP-direktanslutningar Service Bus som standard och faller tillbaka till HTTPS endast om direkt anslutning misslyckades.

#### <a name="ssl-inspection"></a>SSL-kontroll
Använd inte SSL-kontroll för connector-trafik, eftersom den orsakar problem för anslutningen. Anslutningen används ett certifikat för att autentisera till tjänsten Application Proxy och certifikatet kan gå förlorade under SSL-kontroll. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Felsöka problem med anslutningen proxy och anslutningsproblem för tjänsten
Du bör nu se all trafik som passerar genom proxyn. Om du har problem med bör följande felsökningsinformation hjälpa.

Det bästa sättet att identifiera och felsöka problem med anslutningen nätverksanslutningen är att ta en nätverksbild när kopplingstjänsten. Här följer några snabba tips på samla in och filtrera nätverksspår.

Du kan använda övervakningsverktyg önskat. Vi använde Microsoft Message Analyzer vid tillämpningen av den här artikeln. Du kan [ladda ned det från Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

I följande exempel är specifika för Message Analyzer, men principerna som kan tillämpas på alla verktyget.

### <a name="take-a-capture-of-connector-traffic"></a>Ta en avbildning av connector-trafik

Utför följande steg för inledande Felsökning:

1. Stoppa tjänsten Azure AD Application Proxy Connector från services.msc.

   ![Azure AD Application Proxy Connector-tjänsten i services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

2. Kör Message Analyzer som administratör.
3. Välj **starta lokala spårning**.

   ![Starta nätverksbild](./media/application-proxy-working-with-proxy-servers/start-local-trace.png)

3. Starta tjänsten Azure AD Application Proxy Connector.
4. Stoppa insamlingen nätverk.

   ![Stoppa insamlingen av nätverk](./media/application-proxy-working-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Kontrollera om koppling trafiken kringgår utgående proxyservrar

Om du har konfigurerat din Application Proxy connector för att kringgå proxy-servrar och ansluta direkt till Application Proxy-tjänsten som du vill söka i nätverksbild för misslyckade TCP-anslutningsförsök. 

Använd analysverktyg filtret för att identifiera dessa försök. Ange `property.TCPSynRetransmit` i filterrutan och välj **tillämpa**. 

SYN-paket är det första paketet skickas för att upprätta en TCP-anslutning. Om det här paketet inte returnerar ett svar, reattempted SYN. Du kan använda föregående filtret för att se alla att SYN-förfrågningar. Du kan sedan kontrollera om dessa SYN-förfrågningar motsvarar connector-relaterade trafik.

Om du räknar kopplingen för att ansluta direkt till Azure-tjänster är SynRetransmit svar på port 443 en indikation på att du har ett problem med nätverket eller brandväggen.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Kontrollera om koppling trafik använder utgående proxyservrar

Om du har konfigurerat Application Proxy connector-trafik att gå via proxy-servrar som du vill leta efter misslyckade https-anslutningar till proxyservern. 

Om du vill filtrera nätverksbild för dessa anslutningsförsök, ange `(https.Request or https.Response) and tcp.port==8080` ersätta 8080 med din tjänst Proxyport i filtret Message Analyzer. Välj **tillämpa** att visa filterresultaten. 

Föregående filtret visas bara HTTPs-begäranden och svar till eller från proxyporten. Du letar efter CONNECT-begäranden som visar kommunikation med proxyservern. Vid lyckas, kan du få ett OK (200) för HTTP-svar.

Om du ser andra svarskoder, till exempel 407 eller 502, det innebär att proxyservern kräver autentisering eller inte tillåter trafiken av någon anledning. Nu kan du kontaktar supportteamet proxy-server.

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure AD Application Proxy-kopplingar](application-proxy-understand-connectors.md)

- Om du har problem med anslutningen anslutningsproblem, kan du ställa din fråga i den [Azure Active Directory-forumet](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) eller skapa en biljett med vårt supportteam.
