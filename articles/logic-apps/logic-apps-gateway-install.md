---
title: Installera lokala datagateway - Azure Logic Apps | Microsoft Docs
description: Innan du komma åt datakällor lokalt installera lokala datagateway för snabb överföring och kryptering mellan datakällor lokalt och logikappar
keywords: komma åt data på lokala, dataöverföring, kryptering och datakällor
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 63ec26325e045d2ddc027194377e1604d083d82c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300545"
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Installera den lokala datagatewayen för Azure Logic Apps

Innan dina logic apps kan komma åt datakällor lokalt, måste du installera och konfigurera den lokala datagatewayen. Gatewayen fungerar som en brygga som ger snabb överföring och kryptering mellan lokala system och dina logic apps. Gatewayen som vidarebefordrar data från lokala datakällor på krypterade kanaler via Azure Service Bus. Det kommer all trafik som säkra utgående trafik från gateway-agenten. Lär dig mer om [hur datagateway fungerar](#gateway-cloud-service).

Gatewayen stöder anslutningar till dessa datakällor lokalt:

*   BizTalk Server 2016
*   DB2  
*   Filsystem
*   Informix
*   MQ
*   MySQL
*   Oracle-databas
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   SharePoint
*   SQL Server
*   Teradata

Dessa steg visar hur du först installera den lokala datagatewayen innan du [upprätta en anslutning mellan gateway och dina logic apps](./logic-apps-gateway-connection.md). Läs mer om kopplingar som stöds, [kopplingar för Azure Logikappar](https://docs.microsoft.com/azure/connectors/apis-list). 

Information om hur du använder en gateway med andra tjänster finns i följande artiklar:

*   [Microsoft Power BI lokala datagateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services lokala datagateway](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow lokala datagateway](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps lokala datagateway](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>Krav

**minsta**

* 4.5 för .NET framework
* 64-bitars version av Windows 7 eller Windows Server 2008 R2 (eller senare)

**Rekommenderas**

* 8 kärnor CPU
* 8 GB minne
* 64-bitarsversionen av Windows 2012 R2 (eller senare)

**Viktiga överväganden**

* Installera datagateway lokalt på en lokal dator.
Du kan inte installera gatewayen på en domänkontrollant.

   > [!TIP]
   > Du behöver installera gatewayen på samma dator som datakällan. Du kan installera gatewayen så nära som möjligt till datakällan eller på samma dator, förutsatt att du har behörighet för att minimera svarstider.

* Installera inte gatewayen på en dator som stängs av, försätts i strömsparläge, eller inte ansluta till Internet eftersom gatewayen inte kan köras under dessa omständigheter. Gateway-prestanda kan dessutom lidande över ett nätverk.

* Under installationen kan du behöva logga in med en [arbets- eller skolkonto](https://docs.microsoft.com/azure/active-directory/sign-up-organization) som hanteras av Azure Active Directory (Azure AD), inte ett Microsoft-konto.

  > [!TIP]
  > Om du vill använda ett Microsoft-konto som har en Visual Studio med MSDN-prenumeration först [skapa en katalog (klient) i Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md) med ditt Microsoft-konto eller Använd standardkatalogen. Lägga till en användare med ett lösenord i katalogen och ge den åtkomsten till din prenumeration. Du kan logga in under gatewayinstallationen av med det här användarnamnet och lösenordet.

  Du måste använda samma arbets- eller skolkonto senare i Azure-portalen när du skapar och associerar en gateway-resurs med gatewayinstallationen. Du kan sedan välja den här gatewayresursen när du skapar anslutningen mellan din logikapp och lokala datakällan. [Varför måste jag använda en Azure AD-arbets- eller skolkonto?](#why-azure-work-school-account)

  > [!TIP]
  > Om du har registrerat dig för ett erbjudande för Office 365 och gick inte att få din faktiska arbetet i e-post, din adress för inloggningen kan se ut som jeff@contoso.onmicrosoft.com. 

* Om du har en befintlig gateway som konfigurerats med ett installationsprogram som är äldre än version 14.16.6317.4 kan ändra du inte plats för din gateway genom att köra den senaste versionen installatören. Du kan dock använda senaste installationsprogrammet för att ställa in en ny gateway med den plats som du vill använda i stället.
  
  Om du har en gateway-installationsprogram som är äldre än version 14.16.6317.4, men du inte har installerat din gateway ännu, kan du hämtar och använder den senaste versionen installatören.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Installera datagateway

1. [Hämta och kör installationsprogrammet för gateway på en lokal dator](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Granska och Godkänn användningsvillkoren och sekretesspolicyn.

3. Ange sökvägen på den lokala datorn där du vill installera gatewayen.

4. När du uppmanas logga in med ditt Azure arbets- eller skolkonto inte ett Microsoft-konto.

   ![Logga in med Azure arbets- eller skolkonto](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Nu registrera din installerade gateway med den [gateway-Molntjänsten](#gateway-cloud-service). Välj **registrera en ny gateway på den här datorn**.

   Gateway-Molntjänsten krypterar och lagrar dina autentiseringsuppgifter för datakälla och gateway-information. 
   Tjänsten skickar också frågor och deras resultat mellan din logikapp, lokala datagateway och datakällan lokalt.

6. Ange ett namn för din gateway-installation. Skapa en återställningsnyckel och sedan bekräfta din återställningsnyckeln. 

   > [!IMPORTANT] 
   > Återställningsnyckeln måste innehålla minst åtta tecken. Kontrollera att du sparar och hålla nyckeln på en säker plats. Du måste också den här nyckeln om du vill migrera, Återställ eller ta över en befintlig gateway.

   1. Om du vill ändra standardregion för gateway-Molntjänsten och Azure Service Bus som används av gatewayinstallationen, Välj **ändra Region**.

      ![Ändra region](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      Standardregionen är den region som är associerade med Azure AD-klienten.

   2. I nästa steg, öppna den **väljer Region** att välja en annan region.

      ![Välj en annan region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Du kan till exempel välja samma region som din logikapp eller väljer du regionen som är närmast datakällan lokalt så att du kan minska svarstiden. Gateway-resurs och logik appen kan ha olika platser.

      > [!IMPORTANT]
      > Du kan inte ändra den här regionen efter installationen. Den här regionen avgör också och begränsar den plats där du kan skapa Azure-resurs för din gateway. Så när du skapar din gateway-resurs i Azure, se till att platsen för resursen matchar den region som du valde under gatewayinstallationen.
      > 
      > Om du vill använda en annan region för din gateway senare måste du ställa in en ny gateway.

   3. När du är klar kan du välja **klar**.

7. Nu följer du stegen i Azure-portalen så att du kan [skapa en Azure-resurs för din gateway](../logic-apps/logic-apps-gateway-connection.md). 

Lär dig mer om [hur datagateway fungerar](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Migrera, Återställ eller ta över en befintlig gateway

Om du vill utföra dessa uppgifter måste du ha återställningsnyckeln som angavs när gatewayen har installerats.

1. Datorns Start-menyn, Välj **lokala datagateway**.

2. När installationsprogrammet har öppnats, logga in med samma Azure arbets- eller skolkonto som tidigare användes för att installera gatewayen.

3. Välj **migrera, Återställ eller ta över en befintlig gateway**.

4. Ange namn och återställa nyckeln för den gateway som du vill migrera, Återställ eller ta över.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-tjänstkontot

Lokala datagateway körs som en Windows-tjänst och har konfigurerats att använda `NT SERVICE\PBIEgwService` tjänsten inloggningsuppgifter för Windows. Gatewayen har som standard rättigheten ”logga in som en tjänst” på datorn där du installerar en gateway. Om du vill skapa och hantera gatewayen i Azure-portalen, Windows-tjänstkontot måste ha minst **deltagare** behörigheter. 

> [!NOTE]
> Windows-tjänstkontot skiljer sig från det konto som används för att ansluta till lokala data källor, och från Azure arbets- eller skolkonto som används för att logga in till molntjänster.

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>Startar du om gatewayen

Precis som alla andra Windows-tjänster, kan du starta och stoppa tjänsten på flera olika sätt. Du kan till exempel öppna en kommandotolk med förhöjd behörighet på den dator där gatewayen körs och kör antingen dessa kommandon:

* Kör det här kommandot för att stoppa tjänsten:
  
    `net stop PBIEgwService`

* Om du vill starta tjänsten, kör du kommandot:
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>Konfigurera en brandvägg eller proxyserver

Gatewayen skapar en utgående anslutning till [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). För att ge information om proxy för din gateway, se [konfigurera proxyinställningar](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Om du vill kontrollera om din brandvägg eller proxyserver, kan blockera anslutningar, bekräfta om datorn faktiskt kan ansluta till internet och [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Kör kommandot från en PowerShell-kommandotolk:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Det här kommandot kan endast test nätverksanslutning och anslutningen till Azure Service Bus. Så har kommandot ingenting att göra med en gateway eller gateway-Molntjänsten som krypterar och lagrar dina autentiseringsuppgifter och gateway-information. 
>
> Det här kommandot är också bara tillgängligt på Windows Server 2012 R2 eller senare och Windows 8.1 eller senare. Du kan använda Telnet tidigare OS-version för att testa anslutningen. Lär dig mer om [Azure Service Bus och hybridlösningar](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Resultatet bör se ut ungefär så här:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Om **TcpTestSucceeded** inte har angetts till **SANT**, du kan blockeras av en brandvägg. Om du vill ska vara omfattande ersätta den **ComputerName** och **Port** värden med de värden som anges [konfigurera portar](#configure-ports) i den här artikeln.

Brandväggen kan också blockera anslutningar som Azure Service Bus gör att Azure-datacenter. Om det här scenariot inträffar kan godkänna (avblockera) alla IP-adresser för de datacenter i din region. För de IP-adresserna [hämta Azure IP-adresslistan här](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Konfigurera portar

Gatewayen skapar en utgående anslutning till [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) och kommunicerar på utgående portar: TCP 443 (standard), 5671, 5672, 9350 via 9354. Gatewayen kräver inte ingående portar. Lär dig mer om [Azure Service Bus och hybridlösningar](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| Domännamn | Utgående portar | Beskrivning |
| ------------ | -------------- | ----------- |
| *. analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Avancerade Message Queuing-protokollet (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Lyssnare på Service Bus Relay via TCP (kräver 443 för åtkomstkontroll token) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *. msftncsi.com | 443 | Används för att testa internet-anslutning när gatewayen inte kan nås av Power BI-tjänsten. | 
||||

Om du behöver godkänna IP-adresser i stället för domänerna som du kan hämta och använda den [Microsoft Azure Datacenter IP-intervall lista](https://www.microsoft.com/download/details.aspx?id=41653). I vissa fall kan görs Azure Service Bus-anslutningar med IP-adress i stället för fullständigt kvalificerade domännamn.

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-data-gateway-work"></a>Hur fungerar datagateway?

Datagatewayen underlättar snabb och säker kommunikation mellan din logikapp, gateway-Molntjänsten och lokala datakällan. 

![diagram-for-on-premises-data-gateway-Flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

När användaren i molnet som interagerar med ett element som är ansluten till din lokala datakälla:

1. Gateway-Molntjänsten skapar en fråga, tillsammans med krypterade autentiseringsuppgifterna för datakällan, och skickar frågan till kön för gatewayen att bearbeta.

2. Gateway-Molntjänsten analyserar frågan och skickar begäran till Azure Service Bus.

3. Lokala datagateway avsöker Azure Service Bus för väntande begäranden.

4. Gatewayen hämtar frågan dekrypterar autentiseringsuppgifterna och ansluter till datakällan med autentiseringsuppgifterna.

5. Gatewayen skickar frågan till datakällan för körning.

6. Resultatet skickas från datakällan, tillbaka till gatewayen och sedan till gateway-Molntjänsten. Gateway-Molntjänsten använder sedan resultaten.

<a name="faq"></a>

## <a name="tenant-level-administration"></a>Nivån innehavaradministration 

Det finns för närvarande inga enda plats där innehavaradministratörer kan hantera alla gateways som andra användare har installerat och konfigurerat.  Om du är en Innehavaradministratör rekommenderar vi att du uppmanar användarna i din organisation att lägga till som en administratör i varje gateway installeras. Detta gör att du kan hantera alla gateways i din organisation via inställningssidan Gateway eller via [PowerShell-kommandon](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="general"></a>Allmänt

**Q**: behöver jag en gateway för datakällor i molnet, till exempel SQL Azure? <br/>
**En**: Nej En gateway ansluter till endast lokala datakällor.

**Q**: har en gateway installeras på samma dator som datakällan? <br/>
**En**: Nej Gatewayen ansluter till datakällan med hjälp av informationen som angavs. Överväg att gatewayen som ett klientprogram på detta sätt. Gatewayen måste bara möjligheten att ansluta till namnet på servern som har angetts.

<a name="why-azure-work-school-account"></a>

**Q**: Varför måste jag använda en Azure arbets- eller skolkonto för att logga in? <br/>
**En**: du kan bara använda en Azure arbets eller skolkonto när du installerar den lokala datagatewayen. Din inloggning konto lagras i en klient som hanteras av Azure Active Directory (AD Azure). Azure AD-kontot användarens huvudnamn (UPN) matchar vanligtvis e-postadress.

**Q**: var lagras mina autentiseringsuppgifter? <br/>
**En**: de autentiseringsuppgifter som du anger för en datakälla krypteras och lagras i gateway-Molntjänsten. Autentiseringsuppgifterna dekrypteras på lokala datagateway.

**Q**: finns det några krav för nätverksbandbredd? <br/>
**En**: Vi rekommenderar att nätverksanslutningen har bra genomströmning. Alla miljöer skiljer sig, och mängden data som skickas påverkar resultaten. Med hjälp av ExpressRoute kan hjälpa till att garantera en nivå av dataflödet mellan lokala och Azure-datacenter.
Du kan använda verktyg från tredje part Azure hastighet testa appen för att mäta din genomflöde.

**Q**: Vad är svarstiden för att köra frågor till en datakälla från gateway? Vad är den bästa arkitekturen? <br/>
**En**: Installera gatewayen för att minska Nätverksfördröjningen som nära datakällan som möjligt. Om du kan installera gatewayen på den faktiska datakällan, minimerar den här närhet svarstiden införs. Överväg att Datacenter för. Till exempel om din tjänst använder västra USA datacenter, och du har SQL Server finns i en Azure VM, ska Azure VM vara i västra USA för. Den här närhet minimerar fördröjning och undviker utgång avgifter på Azure VM.

**Q**: hur resultatet skickas tillbaka till molnet? <br/>
**En**: resultat skickas via Azure Service Bus.

**Q**: finns det några inkommande anslutningar till gatewayen från molnet? <br/>
**En**: Nej Gatewayen använder utgående anslutningar till Azure Service Bus.

**Q**: Vad händer om jag blockera utgående anslutningar? Vad behöver jag att öppna? <br/>
**En**: Se portar och värdar som används av gateway-servern.

**Q**: det faktiska Windows-tjänsten som kallas?<br/>
**En**: I tjänster gatewayen kallas Power BI Enterprise Gateway-tjänsten.

**Q**: kan gateway Windows-tjänsten körs med ett Azure Active Directory-konto? <br/>
**En**: Nej Windows-tjänsten måste ha ett giltigt Windows-konto. Som standard körs tjänsten med tjänst-SID NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap

**Q**: vilka alternativ som är tillgängliga för katastrofåterställning? <br/>
**En**: du kan använda återställningsnyckeln för att återställa eller flytta en gateway. När du installerar en gateway måste du ange återställningsnyckeln.

**Q**: Vad är fördelen med återställningsnyckeln? <br/>
**En**: återställningsnyckeln är ett sätt att migrera eller återställa gatewayinställningarna efter en katastrof.

**Q**: finns det några planer för att aktivera scenarier med hög tillgänglighet med gatewayen? <br/>
**En**: vissa kopplingar stöder hög tillgänglighet scenarier, till exempel filsystem kopplingen och andra på sättet. Mer information finns i [kluster med hög tillgänglighet för lokala datagateway](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: hur kan jag se vilka frågor som ska skickas till den lokala datakällan? <br/>
**En**: du kan aktivera frågespårning som innehåller de förfrågningar som skickas. Kom ihåg att ändra frågan spåra tillbaka till det ursprungliga värdet när du är klar felsökning. Lämna frågespårning aktiverad skapar större loggar.

Du kan också titta på Verktyg som datakällan har för spårning frågor. Du kan till exempel använda Extended Events eller SQL Profiler för SQL Server och Analysis Services.

**Q**: där är gateway-loggarna? <br/>
**En**: Se verktyg senare i den här artikeln.

### <a name="update-to-the-latest-version"></a>Uppdatera till den senaste versionen

Många problem kan ansluta när gateway-versionen blir inaktuella. Kontrollera att du använder den senaste versionen som allmän bra. Om du inte har uppdaterat en gateway för en månad eller längre, kan du du överväga att installera den senaste versionen av gatewayen och se om du kan återskapa problemet.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Fel: Det gick inte att lägga till användaren i gruppen. (-2147463168 PBIEgwService användare)

Du kan få detta fel om du försöker installera gatewayen på en domänkontrollant, vilket inte stöds. Se till att du distribuerar gatewayen på en dator som inte är en domänkontrollant.

## <a name="tools"></a>Verktyg

### <a name="collect-logs-from-the-gateway-configurer"></a>Samla in loggar från gatewayen configurer

Du kan samla in flera loggar för gateway. Börja alltid med loggar!

#### <a name="installer-logs"></a>Installationsloggarna

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Av konfigurationsloggar

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Enterprise gateway tjänstloggar

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Händelseloggar

Du kan hitta loggar Data Management Gateway och PowerBIGateway under **program- och tjänstloggar**.

### <a name="fiddler-trace"></a>Fiddler spårning

[Fiddler](http://www.telerik.com/fiddler) är ett kostnadsfritt verktyg från Telerik som övervakar HTTP-trafik. Du kan se den här trafiken med Power BI-tjänsten från klientdatorn. Den här tjänsten kan visa fel och annan relaterad information.

## <a name="next-steps"></a>Nästa steg
    
* [Ansluta till lokala data från logikappar](../logic-apps/logic-apps-gateway-connection.md)
* [Enterprise integration-funktioner](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Kopplingar för Azure Logic Apps](../connectors/apis-list.md)
