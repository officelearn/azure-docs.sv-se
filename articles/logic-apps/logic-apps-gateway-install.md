---
title: Installera en lokal datagateway – Azure Logic Apps | Microsoft Docs
description: Innan du kan komma åt data lokalt från Logikappar i Azure, hämta och installera den lokala datagatewayen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 91d1369b9197f6ef941d981aa9cf7539b4554d0c
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065808"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installera en lokal datagateway för Azure Logic Apps

Innan du kan ansluta till lokala datakällor från Azure Logic Apps, hämta och installera den lokala datagatewayen på en lokal dator. Gatewayen fungerar som en brygga som ger snabb dataöverföring och kryptering mellan datakällor lokalt (inte i molnet) och dina logic apps. Den här artikeln visar hur du kan hämta, installera och konfigurera din lokala datagateway. 

Du kan använda samma gatewayinstallationen med andra tjänster, till exempel Power BI, Microsoft Flow, PowerApps och Azure Analysis Services. Läs mer om [hur datagateway fungerar](#gateway-cloud-service).

<a name="supported-connections"></a>

Gatewayen stöder [lokala anslutningsappar](../connectors/apis-list.md#on-premises-connectors) i Azure Logic Apps för dessa datakällor:

*   BizTalk Server 2016
*   Filsystem
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle-databas
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   SharePoint Server
*   SQL Server
*   Teradata

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power BI lokala datagatewayen](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps lokal datagateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow lokal datagateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Förutsättningar

* En [arbets- eller skolkonto](../active-directory/fundamentals/sign-up-organization.md) som har en [Azure-prenumeration](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) 

  Under gatewayinstallationen kan logga du in till det här kontot så att du kan associera gatewayinstallationen med din Azure-prenumeration. 
  Senare kan använda du också samma konto när du skapar en Azure-resurs för din gateway-installation i Azure-portalen. 
  Om du inte har en Azure-prenumeration än, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>.

* Här följer kraven för den lokala datorn:

  **Minimikrav**

  * .NET framework 4.5.2
  * 64-bitars version av Windows 7 eller Windows Server 2008 R2 (eller senare)

  **Rekommenderade krav**

  * Processor med 8 kärnor
  * 8 GB minne
  * 64-bitars version av Windows Server 2012 R2 (eller senare)

* **Att tänka på**

  * Du kan installera den lokala datagatewayen endast på en lokal dator, inte en domänkontrollant. Men behöver du installera gatewayen på samma dator som datakällan. Dessutom behöver du bara en gateway för alla dina datakällor, så du behöver installera gatewayen för varje datakälla.

    > [!TIP]
    > Du kan installera gatewayen så nära som möjligt till datakällan eller på samma dator, förutsatt att du har behörighet för att minimera svarstider.

  * Installera gatewayen på en dator som är ansluten till internet, alltid aktiverat, och *inte* viloläge. I annat fall kan inte gatewayen köras. 
  Dessutom försämras prestandan på trådlösa nätverk.

  * Under installationen, kan du bara logga in med en [arbets- eller skolkonto](../active-directory/sign-up-organization.md) som hanteras av Azure Active Directory (Azure AD), till exempel @contoso.onmicrosoft.com, och inte en Azure B2B (Gäst)-konto eller ett personligt Microsoft-konto, till exempel @hotmail.com eller @outlook.com. 
  Kontrollera att du använder samma konto logga in när du registrerar din gateway-installation i Azure portal genom att skapa en resurs för gatewayen. 
  Sedan kan du välja den här gatewayresursen när du skapar anslutningen från logikappen till din lokala datakälla. 
  [Varför måste jag använder en Azure AD-arbets- eller skolkonto?](#why-azure-work-school-account)

  > [!TIP]
  > Om du har registrerat dig för ett Office 365-erbjudande och inte angav din faktiska e-postadress, kan du ha en inloggning-adress som ser ut som i följande exempel: `username@domain.onmicrosoft.com` 
  >
  > Att använda ett microsoftkonto som har en [Visual Studio Standardprenumeration](https://visualstudio.microsoft.com/vs/pricing/), första [skapa en katalog (klient) i Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md), eller använda standardkatalogen med ditt Microsoft-konto. 
  > Lägga till en användare med ett lösenord i katalogen och ge användaråtkomst till din prenumeration. 
  > Du kan logga in under installationen av gateway med den här användarnamn och lösenord.

  * Den region som du väljer för din gateway-installation anger den plats där du senare registrera din gateway i Azure genom att skapa en Azure-resurs. 
  När du skapar den här gatewayresursen i Azure måste du välja den *samma* plats som din gateway-installation. Standardregionen är samma plats som din Azure AD-klient som hanterar din Azure-konto, men du kan ändra platsen under installationen av gateway.

  * Om du redan har en gateway som du skapade med ett installationsprogram tidigare än version 14.16.6317.4 kan ändra du inte plats för din gateway genom att köra det senaste installationsprogrammet. Du kan dock använda det senaste installationsprogrammet för att ställa in en ny gateway med den plats du vill i stället.
  
    Om du har en gateway-installationsprogram som är äldre än version 14.16.6317.4, men du inte har installerat din gateway ännu, kan du hämta och använda det senaste installationsprogrammet i stället.

## <a name="high-availability-support"></a>Support med hög tillgänglighet

Den lokala datagatewayen stöder hög tillgänglighet när du har mer än en gateway-installation och lägga upp dem som kluster. Om du har en befintlig gateway när du går för att skapa en annan gateway kan skapa du också kluster för hög tillgänglighet. Dessa kluster ordna gatewayer i grupper som kan hjälpa dig att undvika enskilda felpunkter. Dessutom stöd alla lokala data gateway kopplingar nu för hög tillgänglighet.

Om du vill använda den lokala datagatewayen, granska dessa krav och överväganden:

* Du måste redan ha minst en gateway-installation i samma Azure-prenumeration som den primära gatewayen och av återställningsnyckeln för installationen. 

* Din primära gateway måste köra gatewayuppdateringen från November 2017 eller senare.

När du har uppfyllt dessa krav när du skapar din nästa gateway, Välj **lägga till ett befintlig gatewaykluster**, Välj den primära gatewayen för klustret och ange återställningsnyckeln för den primära gatewayen.
Mer information finns i [kluster med hög tillgänglighet för den lokala datagatewayen](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installera datagateway

1. [Hämta, spara och kör installationsprogrammet för gateway på en lokal dator](https://aka.ms/on-premises-data-gateway-installer).

2. Acceptera standardsökvägen eller ange platsen på datorn där du vill installera gatewayen.

3. Gå igenom och godkänner användningsvillkoren och sekretesspolicyn och välj sedan **installera**.

   ![Godkänn användningsvillkoren och sekretesspolicyn](./media/logic-apps-gateway-install/accept-terms.png)

4. När gatewayen har installerats kan ange den e-postadressen för ditt arbets- eller skolkonto konto och välj **logga in**.

   ![Logga in med arbets-eller skolkonto](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Välj **registrera en ny gateway på den här datorn** > **nästa**, som registrerar din gateway-installation med den [gatewaymolntjänsten](#gateway-cloud-service). 

   ![Registrera gateway](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Ange den här informationen för din gateway-installation:

   * Namnet som du vill använda för din installation 

   * Återställningsnyckeln du vill skapa, som måste ha minst åtta tecken

     > [!IMPORTANT]
     > Spara och Behåll din återställningsnyckel ska sparas på en säker plats. Du behöver den här nyckeln när du ändrar gatewayens plats, eller när du migrera, återställa eller ta över en befintlig gateway.

   * Bekräftelse för återställningsnyckeln 

     ![Konfigurera gateway](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Kontrollera den region som valts för gateway-Molntjänsten och Azure Service Bus som används av din gateway-installation. 

   ![Kontrollera region](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Om du vill ändra den här regionen när du har installerat din gateway, behöver du återställningsnyckeln för att installationen av gateway. Dessutom måste du avinstallera och installera om gatewayen. Mer information finns i [ändra platsen, migrera, återställa eller ta över befintlig gateway](#update-gateway-installation).

   *Varför ändra regionen för din gateway-installation?* 

   Om du vill minska svarstiden, kan du till exempel ändra regionen för din gateway till samma region som din logikapp. 
   Eller du kan välja regionen som är närmast den lokala datakällan. 
   Din *gatewayresursen i Azure* och din logikapp kan ha olika platser.

8. Om du vill acceptera standardregion, Välj **konfigurera**. Eller, om du vill ändra standardregion, Följ dessa steg:

   1. Markera bredvid den aktuella regionen **ändra Region**. 

      ![Ändra region](./media/logic-apps-gateway-install/change-region.png)

   2. På nästa sida, öppna den **väljer Region** väljer du den regionen och välja **klar**.

      ![Välj en annan region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. När bekräftelsesidan visas, väljer **Stäng**. 

   Installationsprogrammet bekräftar att din gateway är nu online och redo att användas.

   ![Klar gateway](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Nu registrera din gateway i Azure genom [skapar en Azure-resurs för din gateway-installation](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Ändra platsen, migrera, återställa eller ta över befintlig gateway

Om du måste ändra platsen för din gateway, flytta din gateway-installation till en ny dator, Återställ en skadad gateway eller överta ägarskapet för en befintlig gateway, måste återställningsnyckeln som angavs under installationen av gateway. Den här åtgärden kopplar från den gamla gatewayen.

1. Från din dator **Kontrollpanelen**går du till **program och funktioner**. Välj i programlistan **lokal datagateway**, och välj sedan **avinstallera**.

2. [Installera om den lokala datagatewayen](https://aka.ms/on-premises-data-gateway-installer).

3. När installationsprogrammet öppnas och logga in med samma arbets- eller skolkonto som användes tidigare för att installera gatewayen.

4. Välj **migrera, återställa eller ta över en befintlig gateway**, och välj sedan **nästa**.

   ![Välj ”migrera, återställa eller ta över en befintlig gateway”](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. Under **tillgängliga gateways** eller **tillgängliga gatewaykluster**, Välj den gateway-installation som du vill ändra. Ange återställningsnyckeln för gateway-installation. 

   ![Välj primär gateway](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Om du vill ändra regionen, Välj **ändra Region** och det nya området.

7. När du är klar väljer **konfigurera**.

## <a name="configure-proxy-or-firewall"></a>Konfigurera proxy eller brandvägg

Den lokala datagatewayen skapar en utgående anslutning till [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Om din arbetsmiljö kräver att trafik går igenom en proxy för åtkomst till internet, hindra den här begränsningen datagateway ansluter till gateway-Molntjänsten. Granska den här artikeln på superuser.com för att avgöra om nätverket använder en proxyserver: 

[Hur vet jag vilken proxyserver jag använder? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

För att tillhandahålla proxyinformation för din gateway, se [konfigurera proxyinställningar](https://docs.microsoft.com/power-bi/service-gateway-proxy). Du kan kontrollera om proxyservern eller brandväggen blockerar anslutningar genom att bekräfta om din dator verkligen kan ansluta till internet och [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Kör följande kommando från en PowerShell-kommandotolk:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Det här kommandot testar endast nätverksanslutningen och anslutning till Azure Service Bus. Kommandot göra inte något med gatewayen eller gateway-Molntjänsten som krypterar och lagrar dina autentiseringsuppgifter och gateway-informationen. 
>
> Dessutom det här kommandot är endast tillgänglig på Windows Server 2012 R2 eller senare, och Windows 8.1 eller senare. Du kan använda Telnet på tidigare OS-versioner för att testa anslutningen. Läs mer om [lösningar för Azure Service Bus och hybrid](../service-bus-messaging/service-bus-messaging-overview.md).

Resultaten bör likna det här exemplet med **TcpTestSucceeded** inställd **SANT**:

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

Om **TcpTestSucceeded** inte är inställt på **SANT**, din gateway kan blockeras av en brandvägg. Om du vill ska vara omfattande ersätter den **ComputerName** och **Port** värden med de värden som anges [konfigurera portar](#configure-ports) i den här artikeln.

Brandväggen kan även blockera anslutningarna som Azure Service Bus gör att Azure-datacenter. Om det här scenariot inträffar kan du godkänna (avblockera) alla IP-adresser för dessa datacenter i din region. För dessa IP-adresser [hämta Azure-IP-adresslistan här](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Konfigurera portar

Gatewayen skapar en utgående anslutning till [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) och kommunicerar via utgående portar: TCP 443 (standard), 5671, 5672, 9350 till 9354. Gatewayen behöver inga ingående portar. Läs mer om [lösningar för Azure Service Bus och hybrid](../service-bus-messaging/service-bus-messaging-overview.md).

Gatewayen använder de här fullständigt kvalificerade domännamn:

| Domännamn | Utgående portar | Beskrivning | 
| ------------ | -------------- | ----------- | 
| *. analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | Används för autentisering beroende på konfiguration. | 
| *. msftncsi.com | 443 | Används för att testa Internetanslutningen om gatewayen inte kan nås av Power BI-tjänsten. | 
| *.servicebus.windows.net | 443, 9350-9354 | Lyssnare på Service Bus Relay via TCP (kräver 443 för Access Control tokenförvärv) | 
| *.servicebus.windows.net | 5671-5672 | Avancerade Message Queuing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

I vissa fall görs Azure Service Bus-anslutningar med IP-adresser i stället för fullständigt kvalificerade domännamn. Så kanske du vill placera IP-adresserna för ditt dataområde i brandväggen i listan över tillåtna. Om du vill placera IP-adresser snarare än domäner i listan över tillåtna kan du hämta och använda listan över [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). IP-adresser i den här listan finns i [Classless Inter-Domain Routing CIDR-](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) notation.

### <a name="force-https-communication-with-azure-service-bus"></a>Tvinga HTTPS-kommunikation med Azure Service Bus

Vissa proxyservrar kan trafik endast till portarna 80 och 443. Som standard sker kommunikationen med Azure Service Bus via andra portar än 443.
Du kan tvinga gatewayen att kommunicera med Azure Service Bus via HTTPS i stället för direkt TCP, men detta kan avsevärt minska prestanda. Följ dessa steg om du vill utföra den här aktiviteten:

1. Bläddra till platsen för den lokala data gatewayklienten, som vanligtvis finns här: ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   I annat fall för att hitta klientens plats, öppna konsolen tjänster på samma dator, hitta **lokala datagatewaytjänsten**, och visa den **sökvägen till körbara filen** egenskapen.

2. Öppna det här *configuration* fil: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Ändra den **ServiceBusSystemConnectivityModeString** värdet från **AutoDetect** till **Https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-tjänstkontot

Gatewayen körs som ett Windows service-konto med namnet ”lokal datagateway” på datorn där du installerar den lokala datagatewayen. Gatewayen använder dock ”NT SERVICE\PBIEgwService”-namn för autentiseringsuppgifterna ”logga in som”-konto. Som standard har gatewayen ”logga in som en tjänst” behörighet på den dator där du installerar gatewayen. Windows-tjänstkontot för gatewayen vanligtvis skiljer sig från det konto som du använder för att ansluta till lokala datakällor och från arbetet eller skolkonto som du använder för att logga in på molntjänster.

Om du vill skapa och underhålla gatewayen i Azure-portalen måste den här Windows-tjänstkontot måste ha minst **deltagare** behörigheter. Du kan kontrollera behörigheterna [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md). 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Starta om gatewayen

Datagateway körs som en Windows-tjänst, så som alla andra Windows-tjänsten, du kan starta och stoppa gatewayen på olika sätt. Du kan till exempel öppna en kommandotolk med förhöjd behörighet på den dator där gatewayen körs och kör kommandot:

* Kör följande kommando för att stoppa tjänsten:
  
  `net stop PBIEgwService`

* Om du vill starta tjänsten kör du följande kommando:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Administration på klientnivå 

Det finns för närvarande inte någon enskild plats där klientadministratörer kan hantera alla gatewayer som andra användare har installerat och konfigurerat. Om du är en Innehavaradministratör kan du be användarna i organisationen att lägga till dig som administratör för varje gateway som de installerar. På så sätt kan du hantera alla gatewayer i din organisation via sidan Gateway-inställningar eller via [PowerShell-kommandon](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Hur fungerar gatewayen?

Datagateway underlättar snabb och säker kommunikation mellan din logikapp, gateway-Molntjänsten och din lokala datakälla. Gatewaymolntjänsten krypterar och lagrar dina autentiseringsuppgifter för datakälla och gateway-informationen. Tjänsten dirigerar också frågor och deras resultat mellan din logikapp, en lokal datagateway och datakällan lokalt. 

Gatewayen fungerar med brandväggar och använder endast utgående anslutningar. All trafik som samlas in som säker utgående trafik från gateway-agenten. Gatewayen vidarebefordrar data från lokala källor på krypterade kanaler via Azure Service Bus. Det här service bus skapar en kanal mellan gatewayen och anropa tjänsten, men lagras inte några data. Alla data som överförs via gatewayen är krypterad.

![diagram-for-on-premises-data-gateway-Flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Dessa steg beskriver vad som händer när en användare i molnet som interagerar med ett element som är ansluten till din lokala datakälla:

1. Gateway-Molntjänsten skapar en fråga, tillsammans med de krypterade autentiseringsuppgifterna för datakällan, och skickar frågan till i kön för gatewayen ska bearbeta.

2. Gatewaymolntjänsten analyserar frågan och skickar en begäran till Azure Service Bus.

3. Den lokala datagatewayen avsöker Azure Service Bus för väntande begäranden.

4. Gatewayen hämtar frågan, dekrypterar autentiseringsuppgifterna och ansluter till datakällan med autentiseringsuppgifterna.

5. Gatewayen skickar frågan till datakällan för körning.

6. Resultaten skickas från datakällan tillbaka till gatewayen och sedan till gateway-Molntjänsten. Gateway-Molntjänsten använder sedan resultaten.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="general"></a>Allmänt

**FRÅGOR OCH**: Behöver jag en gateway för datakällor i molnet, till exempel Azure SQL Database? <br/>
**EN**: Nej, gatewayen som ansluter till lokala datakällor endast.

**FRÅGOR OCH**: Har en gateway att installeras på samma dator som datakällan? <br/>
**EN**: Nej, gatewayen som ansluter till datakällan med den angivna anslutningsinformationen. Överväg att gatewayen som ett klientprogram i detta avseende. Gatewayen måste bara möjligheten att ansluta till servernamnet som har angetts.

<a name="why-azure-work-school-account"></a>

**FRÅGOR OCH**: Varför måste jag använda ett arbets- eller skolkonto konto för att logga in? <br/>
**EN**: Du kan bara använda ett arbets- eller skolkonto konto när du installerar den lokala datagatewayen. Ditt inloggningskonto lagras i en klient som hanteras av Azure Active Directory (AD Azure). Vanligtvis är matchar din Azure AD-konto användarens huvudnamn (UPN) den e-postadressen.

**FRÅGOR OCH**: Var lagras mina autentiseringsuppgifter? <br/>
**EN**: De autentiseringsuppgifter som du anger för en datakälla krypteras och lagras i gatewayens molntjänst. Autentiseringsuppgifterna dekrypteras i den lokala datagatewayen.

**FRÅGOR OCH**: Finns det några krav på bandbredd i nätverket? <br/>
**EN**: Kontrollera att nätverksanslutningen har bra dataflöde. Varje miljö är annorlunda och mängden data som skickas kan påverka resultatet. För att garantera en dataflödesnivå mellan din lokala datakälla och Azure-datacenter, försök [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). För att mäta ditt dataflöde kan du prova något externt verktyg, till exempel Azure Speed Test.

**FRÅGOR OCH**: Vad är svarstiden för att köra frågor till en datakälla från gatewayen? Vad är den bästa arkitekturen? <br/>
**EN**: För att förkorta Nätverksfördröjningen ska du installera gatewayen som nära datakällan som möjligt. Om du kan installera gatewayen på den faktiska datakällan, minimerar svarstiden i den här närhet. Överväg även närhet till Azure-datacenter. Till exempel om tjänsten använder datacenter för västra USA och du har SQL Server på en Azure virtuell dator, du sedan dina virtuella Azure-datorer i regionen västra USA för. Den här närhet minimerar svarstiden och undviker onödiga avgifter för Azure VM.

**FRÅGOR OCH**: Hur skickas resultaten tillbaka till molnet? <br/>
**EN**: Resultaten skickas via Azure Service Bus.

**FRÅGOR OCH**: Finns det några inkommande anslutningar till gatewayen från molnet? <br/>
**EN**: Nej, gatewayen använder utgående anslutningar till Azure Service Bus.

**FRÅGOR OCH**: Vad händer om jag blockerar utgående anslutningar? Vad behöver jag att öppna? <br/>
**EN**: Se vilka portar och värdar som används av gatewayen.

**FRÅGOR OCH**: Vad heter den faktiska Windows-tjänsten? <br/>
**EN**: Tjänstnamnet är ”PBIEgwService” eller Power BI Enterprise Gateway Service på fliken tjänster i Aktivitetshanteraren. Tjänstnamnet är ”On-premises data gateway-tjänsten” i konsolen tjänster. Windows-tjänsten använder ”NT SERVICE\PBIEgwService” som tjänst SID (SSID).

**FRÅGOR OCH**: Gatewayens Windows-tjänst kan köra med ett Azure Active Directory-konto? <br/>
**EN**: Nej, den Windows-tjänsten måste ha ett giltigt Windows-konto.

### <a name="disaster-recovery"></a>Haveriberedskap

**FRÅGOR OCH**: Vilka alternativ är tillgängliga för haveriberedskap? <br/>
**EN**: Du kan använda återställningsnyckeln för att återställa eller flytta en gateway. När du installerar gatewayen anger du återställningsnyckeln.

**FRÅGOR OCH**: Vad är fördelen med återställningsnyckeln? <br/>
**EN**: Återställningsnyckeln är ett sätt att migrera eller återställa gatewayinställningarna efter en katastrof.

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet behandlar några vanliga problem som du kan ha när du ställer in och använder den lokala datagatewayen.

**FRÅGOR OCH**: Varför Mina gatewayinstallationen misslyckas? <br/>
**EN**: Det här problemet kan inträffa om ett antivirusprogram på måldatorn är inaktuell. Du kan uppdatera ett antivirusprogram eller inaktivera antivirusprogrammet men endast under installationen av gateway och aktivera sedan programmet igen.

**FRÅGOR OCH**: Varför ser jag min gatewayinstallationen när jag skapar gatewayresursen i Azure? <br/>
**EN**: Det här problemet kan bero på följande:

* Din gateway-installation är redan registrerat och ägs av en annan gateway-resurs i Azure. Gateway-installationer visas inte i instanslistan över när gateway-resurser skapas för dessa.
Kontrollera din gateway-registreringar i Azure portal genom att granska alla dina Azure-resurser med den **lokala Datagatewayer** skriver för *alla* Azure-prenumerationer. 

* Azure AD-identitet för den person som installerade gatewayen skiljer sig från den person som loggat in på Azure-portalen. Kontrollera att du har loggat in med samma identitet som installerade gatewayen.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**FRÅGOR OCH**: Var finns gatewayloggarna? <br/>
**EN**: Se den [ **loggar** avsnittet](#logs) senare i den här artikeln.

**FRÅGOR OCH**: Hur kan jag se vilka frågor som skickats till den lokala datakällan? <br/>
**EN**: Du kan aktivera frågespårning, som innehåller de förfrågningar som skickas. Kom ihåg att ändra frågan spåra tillbaka till det ursprungliga värdet efter felsökningen. Lämna frågespårning aktiverad skapar större loggar.

Du kan också leta i verktyg som datakällan har för att spåra frågor. Du kan till exempel använda Extended Events eller SQL Profiler för SQL Server och Analysis Services.

### <a name="outdated-gateway-version"></a>Inaktuell gateway-version

Många problem kan uppstå om gatewayversionen gateway-versionen blir inaktuella. Allmän bra tips är att kontrollera att du har den senaste versionen. Om du inte har uppdaterat din gateway i en månad eller längre, kan du överväga att installera den senaste versionen av gatewayen och se om du kan återskapa problemet.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Fel: Det gick inte att lägga till användare i gruppen. (-2147463168 PBIEgwService Performance Log Users)

Du kan få det här felet om du försöker installera gatewayen på en domänkontrollant som inte stöds. Kontrollera att du har distribuerat gatewayen på en dator som inte är en domänkontrollant.

<a name="logs"></a>

### <a name="logs"></a>Logs

Hjälper dig att felsöka och alltid börja med att samla in och granska gateway-loggar. Du har flera olika sätt för att samla in loggar, men det enklaste alternativet när du har installerat gatewayen är via användargränssnittet för installationsprogrammet för gateway. 

1. Öppna installationsprogrammet för den lokala data gateway på din dator.
2. På menyn till vänster väljer **diagnostik**.
3. Under **gatewayloggarna**väljer **Exportloggar**.

   ![Exportera loggar från installationsprogrammet för gateway](./media/logic-apps-gateway-install/export-logs.png)

Här följer andra platser där du hittar flera loggar:

| Loggtyp | Plats | 
|----------|----------| 
| **Installationsloggar** | %localappdata%\Temp\On-premises_data_gateway_ <*ÅÅÅÅMMDD*>. <*nummer*> .log | 
| **Konfigurationsloggar** | C:\Users\<*användarnamn*> \AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator <*ÅÅÅÅMMDD*>. <*nummer*>. log | 
| **Företagsgatewayens tjänstloggar** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway <*ÅÅÅÅMMDD*>. <*nummer*> .log | 
||| 

**Händelseloggar**

Följ dessa steg för att hitta händelseloggarna för gatewayen:

1. På datorn där gateway-installationen, öppnar den **Loggboken**. 
2. Expandera **Loggboken (lokal)** > **applikationer och tjänsteloggar**. 
3. Välj **lokala datagatewaytjänsten**.

   ![Visa händelseloggar för gateway](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="telemetry"></a>Telemetri

För ytterligare övervakning och felsökning, kan du aktivera och samla in telemetri. 

1. Bläddra till platsen för den lokala data gatewayklienten, som vanligtvis finns här: ```C:\Program Files\On-premises data gateway```

   I annat fall för att hitta klientens plats, öppna konsolen tjänster på samma dator, hitta **lokala datagatewaytjänsten**, och visa den **sökvägen till körbara filen** egenskapen.

2. Öppna det här *configuration* fil: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Ändra den **SendTelemetry** värde att **SANT**:

   ```html
   <setting name="SendTelemetry" serializeAs="String">
      <value>true</value>
   </setting>
   ```

4. Spara dina ändringar och starta sedan om Windows-tjänsten.

### <a name="review-slow-query-performance"></a>Granska långsamma frågeprestanda

Om du har hittat frågor som körs långsamt via gatewayen kan aktivera du ytterligare loggning som visar frågor och deras varaktighet. Dessa loggar kan hjälpa dig att hitta vilka frågor som är långsam eller körs under lång tid. Du kan behöva ändra din datakälla, till exempel, justera index för SQL Server-frågor för att justera prestanda för frågor.

Följ dessa steg för att fastställa hur länge en fråga:

1. Bläddra till samma plats som gatewayklienten, som vanligtvis finns här: ```C:\Program Files\On-premises data gateway```

   I annat fall för att hitta klientens plats, öppna konsolen tjänster på samma dator, hitta **lokala datagatewaytjänsten**, och visa den **sökvägen till körbara filen** egenskapen.

2. Öppna och redigera konfigurationsfilerna enligt beskrivningen:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     I den här filen, ändra den **EmitQueryTraces** värdet från **FALSKT** till **SANT** så att din gateway kan logga frågor som skickats från gatewayen till en datakälla:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Aktivera EmitQueryTraces inställningen kan avsevärt öka loggstorleken baserat på gatewayens användning. När du har granskat loggarna ska du återställa EmitQueryTraces till **FALSKT** igen, i stället för att lämna den här inställningen på långsiktigt.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Om du vill att din gateway utförliga poster, inklusive poster som visar varaktighet, ändra den **TracingVerbosity** värdet från **4** till **5** genom att utföra antingen steg: 

     * I den här konfigurationsfilen ändrar den **TracingVerbosity** värdet från **4** till **5** 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Öppna installationsprogrammet för gateway, Välj **diagnostik**, aktivera **ytterligare loggning**, och välj sedan **tillämpa**:

       ![Aktivera ytterligare loggning](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Aktivera TracingVerbosity inställningen kan avsevärt öka loggstorleken baserat på gatewayens användning. När du har granskat loggarna kan kontrollera att du stänger av **ytterligare loggning** i installationsprogrammet för gateway eller Återställ TracingVerbosity till **4** igen i konfigurationsfilen, i stället för att lämna den här inställningen på långsiktigt.

3. Följ dessa steg för att hitta varaktigheten för en fråga:

   1. [Exportera](#logs) och öppna gatewayloggen.

   2. För att hitta en fråga, söker du efter en aktivitetstyp, till exempel: 

      | Aktivitetstyp | Beskrivning | 
      |---------------|-------------| 
      | MGEQ | Frågor som körs över ADO.NET. | 
      | MGEO | Frågor som körs över OLEDB. | 
      | MGEM | Frågor som körs från Mashup-motorn. | 
      ||| 

   3. Obs andra GUID, vilket är den som begär-ID.

   4. Fortsätta söka efter aktivitetstypen tills du hittar en post med namnet ”FireActivityCompletedSuccessfullyEvent” som har en varaktighet i millisekunder. 
   Bekräfta att posten har samma begäran-ID, till exempel:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > Posten ”FireActivityCompletedSuccessfullyEvent” är en utförlig post och loggas inte om inte inställningen ”TracingVerbosity” är på nivå 5.

### <a name="trace-traffic-with-fiddler"></a>Spåra trafik med Fiddler

[Fiddler](http://www.telerik.com/fiddler) är ett kostnadsfritt verktyg från Telerik som övervakar HTTP-trafik. Du kan granska den här trafiken med Power BI-tjänsten från klientdatorn. Den här tjänsten kan visa fel och annan relaterad information.

## <a name="next-steps"></a>Nästa steg
    
* [Ansluta till lokala data från logikappar](../logic-apps/logic-apps-gateway-connection.md)
* [Enterprise integration-funktioner](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Anslutningsappar för Azure Logic Apps](../connectors/apis-list.md)
