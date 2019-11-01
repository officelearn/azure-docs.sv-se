---
title: Arbeta med befintliga lokala proxyservrar och Azure AD | Microsoft Docs
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
ms.openlocfilehash: d305f3354e7b1af6d43f31f0dd5fe9f54ef3e66f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242274"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Arbeta med befintliga lokala proxyservrar

Den här artikeln beskriver hur du konfigurerar Azure Active Directory (Azure AD) Application Proxy-kopplingar så att de fungerar med utgående proxyservrar. Den är avsedd för kunder med nätverks miljöer som har befintliga proxyservrar.

Vi börjar med att titta på de här huvudsakliga distributions scenarierna:

* Konfigurera anslutningar för att kringgå dina lokala utgående proxyservrar.
* Konfigurera anslutningar för att använda en utgående proxy för att få åtkomst till Azure AD-programproxy.

Mer information om hur anslutningar fungerar finns i [förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kringgå utgående proxyservrar

Anslutningar har underliggande OS-komponenter som gör utgående begär Anden. Dessa komponenter försöker automatiskt hitta en proxyserver i nätverket med hjälp av WPAD (Web Proxy Auto-Discovery).

OS-komponenterna försöker hitta en proxyserver genom att utföra en DNS-sökning för WPAD. domainsuffix. Om sökningen matchar i DNS görs en HTTP-begäran till IP-adressen för WPAD. dat. Den här begäran blir proxyns konfigurations skript i din miljö. Anslutningen använder det här skriptet för att välja en utgående proxyserver. Anslutnings trafiken kan dock fortfarande gå igenom, på grund av ytterligare konfigurations inställningar som krävs på proxyn.

Du kan konfigurera anslutningen så att den kringgår din lokala proxy för att säkerställa att den använder direkt anslutning till Azure-tjänsterna. Vi rekommenderar den här metoden, så länge nätverks principen tillåter det, eftersom det innebär att du har en mindre konfiguration att underhålla.

Om du vill inaktivera utgående proxy-användning för anslutningen redigerar du filen C:\Program Files\Microsoft AAD App proxy Connector\ApplicationProxyConnectorService.exe.config och lägger till *system.net* -avsnittet som visas i följande kod exempel:

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

För att säkerställa att tjänsten Connector Updater också kringgå proxyn, gör en liknande ändring i filen ApplicationProxyConnectorUpdaterService. exe. config. Den här filen finns i C:\Program Files\Microsoft AAD App proxy Connector Updater.

Se till att göra kopior av de ursprungliga filerna, om du måste återgå till standardfilerna. config.

## <a name="use-the-outbound-proxy-server"></a>Använd den utgående proxyservern

Vissa miljöer kräver all utgående trafik för att gå via en utgående proxy, utan undantag. Det innebär att det inte finns något alternativ att kringgå proxyn.

Du kan konfigurera anslutnings trafiken så att den går genom den utgående proxyn, som du ser i följande diagram:

 ![Konfigurera kopplings trafik för att gå via en utgående proxy till Azure AD-programproxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

På grund av att endast utgående trafik behövs behöver du inte konfigurera inkommande åtkomst genom brand väggarna.

> [!NOTE]
> Application Proxy stöder inte autentisering till andra proxyservrar. Anslutnings-/uppdaterings kontots nätverks tjänst konton ska kunna ansluta till proxyn utan att anropas för autentisering.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Steg 1: konfigurera anslutningen och relaterade tjänster att gå via den utgående proxyn

Om WPAD har Aktiver ATS i miljön och kon figurer ATS korrekt identifierar anslutnings tjänsten automatiskt den utgående proxyservern och försöker använda den. Du kan dock uttryckligen konfigurera anslutningen att gå via en utgående proxy.

Det gör du genom att redigera Connector\ApplicationProxyConnectorService.exe.config-filen C:\Program\Microsoft AAD App proxy och lägga till *system.net* -avsnittet som visas i det här kod exemplet. Ändra *proxyserver: 8080* för att återspegla namnet på den lokala proxyservern eller IP-adressen och porten som den lyssnar på. Värdet måste ha prefixet http://även om du använder en IP-adress.

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

Konfigurera sedan Connector Updater-tjänsten så att den använder proxyn genom att göra en liknande ändring i C:\Program Files\Microsoft AAD App proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config-filen.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Steg 2: Konfigurera proxyservern för att tillåta trafik från anslutningen och relaterade tjänster att flöda genom

Det finns fyra aspekter att tänka på vid utgående proxy:

* Utgående proxy-regler
* Proxyautentisering
* Proxy-portar
* SSL-kontroll

#### <a name="proxy-outbound-rules"></a>Utgående proxy-regler

Tillåt åtkomst till följande webbadresser:

| URL | Hur den används |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure använder dessa webbadresser för att verifiera certifikat |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com<br>* . microsoftonline-p.com<br>*. msauth.net<br>* . msauthimages.net<br>*. msecnd.net<br>* . msftauth.net<br>*. msftauthimages.net<br>* . phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | Anslutningsprogrammet använder dessa webbadresser under registreringen. |

Om din brand vägg eller proxy låter dig konfigurera listan över tillåtna DNS-listor kan du tillåta anslutningar till \*. msappproxy.net och \*. servicebus.windows.net. Om inte måste du tillåta åtkomst till [Azure DataCenter IP-intervallen](https://www.microsoft.com/download/details.aspx?id=41653). IP-adressintervallen uppdateras varje vecka.

Om du inte kan tillåta anslutning av FQDN och behöver ange IP-adressintervall i stället, använder du följande alternativ:

* Tillåt anslutningen utgående åtkomst till alla destinationer.
* Tillåt anslutningen utgående åtkomst till alla [Azure datacenter IP-intervall](https://www.microsoft.com//download/details.aspx?id=41653). Utmaningen med att använda listan över IP-intervall för Azure-datacenter är att den uppdateras varje vecka. Du måste placera en process för att se till att dina åtkomst regler uppdateras i enlighet med detta. Om du bara använder en delmängd av IP-adresserna kan konfigurationen brytas.

#### <a name="proxy-authentication"></a>Proxyautentisering

Proxyautentisering stöds inte för närvarande. Vår nuvarande rekommendation är att tillåta anslutningen anonym åtkomst till Internet-destinationer.

#### <a name="proxy-ports"></a>Proxy-portar

Anslutningen gör utgående SSL-baserade anslutningar med hjälp av metoden CONNECT. Den här metoden ställer in en tunnel genom utgående proxy. Konfigurera proxyservern för att tillåta tunnel trafik till portarna 443 och 80.

> [!NOTE]
> När Service Bus körs över HTTPS används port 443. Service Bus försöker som standard direkt TCP-anslutningar och går tillbaka till HTTPS endast om direkta anslutningar Miss lyckas.

#### <a name="ssl-inspection"></a>SSL-kontroll

Använd inte SSL-inspektion för anslutnings trafiken, eftersom det orsakar problem med anslutnings trafiken. Anslutningen använder ett certifikat för att autentisera till Application Proxy-tjänsten och certifikatet kan gå förlorat under SSL-kontrollen.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Felsök problem med anslutnings proxy och tjänst anslutnings problem

Nu bör du se all trafik som flödar genom proxyservern. Om du har problem bör du ha följande felsöknings information.

Det bästa sättet att identifiera och felsöka anslutnings problem är att ta en nätverks avbildning när du startar kopplings tjänsten. Här följer några snabba tips om att fånga och filtrera nätverks spår.

Du kan använda valfritt övervaknings verktyg. I den här artikeln använde vi Microsoft Message Analyzer. Du kan [Ladda ned det från Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Följande exempel är speciella för Message Analyzer, men principerna kan tillämpas på alla analys verktyg.

### <a name="take-a-capture-of-connector-traffic"></a>Ta en bild av kopplings trafik

Utför följande steg för inledande fel sökning:

1. Stoppa tjänsten Azure AD-programproxy Connector från Services. msc.

   ![Azure AD-programproxy Connector-tjänsten i Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Kör Message Analyzer som administratör.
1. Välj **Starta lokal spårning**.
1. Starta tjänsten Azure AD-programproxy Connector.
1. Stoppa nätverks avbildningen.

   ![Skärm bild som visar knappen Stoppa nätverks insamling](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Kontrol lera om anslutnings trafiken kringgår utgående proxyservrar

Om du har konfigurerat din Application Proxy-anslutning för att kringgå proxyservrarna och ansluta direkt till Application Proxy-tjänsten, vill du söka i nätverks avbildningen efter misslyckade TCP-anslutnings försök.

Använd Message Analyzer-filtret för att identifiera de här försöken. Ange `property.TCPSynRetransmit` i filter rutan och välj **Använd**.

Ett SYN paket är det första paket som skickas för att upprätta en TCP-anslutning. Om det här paketet inte returnerar ett svar görs ett nytt försök med tillståndet. Du kan använda föregående filter för att se alla SYNs som har skickats om. Sedan kan du kontrol lera om dessa SYNs motsvarar den kopplings-relaterade trafiken.

Om du förväntar dig att anslutningen ska göra direkta anslutningar till Azure-tjänsterna, är SynRetransmit svar på port 443 en indikation på att du har problem med nätverks-eller brand väggen.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Kontrol lera om anslutnings trafiken använder utgående proxyservrar

Om du har konfigurerat din Application Proxy Connector-trafik för att gå igenom proxyservrarna vill du söka efter misslyckade HTTPS-anslutningar till proxyservern.

Om du vill filtrera nätverks avbildningen för de här anslutnings försöken anger du `(https.Request or https.Response) and tcp.port==8080` i Message Analyzer-filtret och ersätter 8080 med din proxy service-port. Välj **tillämpa** för att se filter resultaten.

Föregående filter visar bara HTTPs-begärandena och svar på/från proxyservern. Du letar efter de CONNECT-begäranden som visar kommunikationen med proxyservern. När det är klart får du ett HTTP-svar (200).

Om du ser andra svars koder, till exempel 407 eller 502, innebär det att proxyn kräver autentisering eller inte tillåter trafik av någon annan anledning. Nu ska du delta i support teamet för proxyservern.

## <a name="next-steps"></a>Nästa steg

* [Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
* Om du har problem med anslutnings problem kan du ställa din fråga i [Azure Active Directory-forumet](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) eller skapa en biljett med vårt support team.
