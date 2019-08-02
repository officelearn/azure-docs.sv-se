---
title: Installera lokal datagateway – Azure Logic Apps | Microsoft Docs
description: Innan du kan komma åt data lokalt från Azure Logic Apps kan du hämta och installera den lokala datagatewayen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 61a9b319b9ea44f766bc6f014b76bc48d15efc57
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598454"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installera lokal datagateway för Azure Logic Apps

Innan du kan ansluta till lokala data källor från Azure Logic Apps kan du hämta och installera den lokala datagatewayen på en lokal dator. Gatewayen fungerar som en brygga som ger snabb data överföring och kryptering mellan data källor lokalt (inte i molnet) och dina Logic Apps. Den här artikeln visar hur du kan ladda ned, installera och konfigurera din lokala datagateway. 

Du kan använda samma Gateway-installation med andra tjänster, till exempel Power BI, Microsoft Flow, PowerApps och Azure Analysis Services. Läs mer om [hur data gatewayen fungerar](#gateway-cloud-service).

<a name="supported-connections"></a>

Gatewayen stöder [lokala anslutningar](../connectors/apis-list.md#on-premises-connectors) i Azure Logic Apps för dessa data Källor:

*   BizTalk Server 2016
*   Filsystem
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   SharePoint Server
*   SQL Server
*   Teradata

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power BI lokal datagateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps lokal datagateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow lokal datagateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Förutsättningar

* Ett [arbets-eller skol konto](../active-directory/fundamentals/sign-up-organization.md) som har en [Azure-prenumeration](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) 

  Under Gateway-installationen loggar du in på det här kontot så att du kan associera Gateway-installationen med din Azure-prenumeration. 
  Senare använder du också samma konto när du skapar en Azure-resurs för din gateway-installation i Azure Portal. 
  Om du inte har någon Azure-prenumeration ännu kan du <a href="https://azure.microsoft.com/free/" target="_blank">Registrera dig för ett kostnads fritt Azure-konto</a>.

* Här följer krav för den lokala datorn:

  **Minimi krav**

  * .NET Framework 4.5.2
  * 64-bitars version av Windows 7 eller Windows Server 2008 R2 (eller senare)

  **Rekommenderade krav**

  * 8 kärnor-CPU
  * 8 GB minne
  * 64-bitars version av Windows Server 2012 R2 (eller senare)

* **Viktiga överväganden**

  * Du kan bara installera den lokala datagatewayen på en lokal dator, inte en domänkontrollant. Men du behöver inte installera gatewayen på samma dator som data källan. Dessutom behöver du bara en gateway för alla dina data källor, så du behöver inte installera gatewayen för varje data källa.

    > [!TIP]
    > För att minimera svars tiden kan du installera gatewayen så nära som möjligt för din data källa, eller på samma dator, förutsatt att du har behörighet.

  * Installera gatewayen på en dator som är ansluten till Internet, alltid aktive rad och gå *inte* i ström spar läge. Annars kan gatewayen inte köras. 
  Dessutom kan prestanda försämras i ett trådlöst nätverk.

  * Under installationen kan du bara logga in med ett [arbets-eller skol konto](../active-directory/sign-up-organization.md) som hanteras av Azure Active Directory (Azure AD), till exempel @contoso.onmicrosoft.com, och inte ett Azure B2B-konto (gäst) eller en personlig @hotmail.com Microsoft-konto, till exempel eller @outlook.com. 
  Se till att du använder samma inloggnings konto när du registrerar din gateway-installation i Azure Portal genom att skapa en gateway-resurs. 
  Du kan sedan välja denna gateway-resurs när du skapar anslutningen från din Logic app till din lokala data källa. 
  [Varför måste jag använda ett arbets-eller skol konto för Azure AD?](#why-azure-work-school-account)

  > [!TIP]
  > Om du har registrerat dig för ett Office 365-erbjudande och inte angav din faktiska e-postadress för arbetet, kan du ha en inloggnings adress som ser ut som i det här exemplet:`username@domain.onmicrosoft.com` 
  >
  > Om du vill använda en Microsoft-konto som har en [Visual Studio Standard-prenumeration](https://visualstudio.microsoft.com/vs/pricing/)måste du först [skapa en katalog (klient) i Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md)eller använda standard katalogen med din Microsoft-konto. 
  > Lägg till en användare med ett lösen ord till katalogen och ge sedan användaren åtkomst till din prenumeration. 
  > Sedan kan du logga in under Gateway-installationen med det här användar namnet och lösen ordet.

  * Den region som du väljer för din gateway-installation bestämmer var du vill registrera din gateway i Azure genom att skapa en Azure-resurs. 
  När du skapar den här Gateway-resursen i Azure måste du välja *samma* plats som gateway-installationen. Standard regionen är samma plats som din Azure AD-klient, som hanterar ditt Azure-konto, men du kan ändra platsen under Gateway-installationen.

  * Om du redan har en gateway som du har skapat med ett installations program som är tidigare än version 14.16.6317.4 kan du inte ändra gatewayens plats genom att köra det senaste installations programmet. Du kan dock använda det senaste installations programmet för att konfigurera en ny Gateway med den plats som du vill använda istället.
  
    Om du har ett tidigare Gateway-installations program än version 14.16.6317.4, men inte har installerat din gateway än, kan du ladda ned och använda det senaste installations programmet i stället.

## <a name="high-availability-support"></a>Stöd för hög tillgänglighet

Den lokala datagatewayen stöder hög tillgänglighet när du har mer än en gateway-installation och konfigurerat den som kluster. Om du har en befintlig gateway när du väljer att skapa en annan gateway kan du välja att skapa kluster med hög tillgänglighet. Dessa kluster ordnar gatewayer i grupper som kan hjälpa till att undvika enskilda fel punkter. Dessutom stöder alla lokala data Gateway-kopplingar hög tillgänglighet.

Om du vill använda den lokala datagatewayen granskar du följande krav och överväganden:

* Du måste redan ha minst en gateway-installation i samma Azure-prenumeration som den primära gatewayen och återställnings nyckeln för den installationen. 

* Din primära Gateway måste köra Gateway-uppdateringen från november 2017 eller senare.

När du har uppfyller dessa krav, väljer du **Lägg till i ett befintligt Gateway-kluster**när du skapar din nästa Gateway, väljer den primära gatewayen för klustret och anger återställnings nyckeln för den primära gatewayen.
Mer information finns i [kluster med hög tillgänglighet för lokal datagateway](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installera datagateway

1. [Hämta, Spara och kör installations programmet för gateway på en lokal dator](https://aka.ms/on-premises-data-gateway-installer).

2. Godkänn standard installations Sök vägen eller ange den plats på datorn där du vill installera gatewayen.

3. Granska och godkänn användnings villkoren och sekretess policyn och välj sedan **Installera**.

   ![Godkänn användnings villkoren och sekretess policyn](./media/logic-apps-gateway-install/accept-terms.png)

4. När gatewayen har installerats, ange e-postadressen för ditt arbets-eller skol konto och välj **Logga in**.

   ![Logga in med arbets-eller skol konto](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Välj **Registrera en ny gateway på den här datorn** > **Nästa**, som registrerar din gateway-installation med [Gateway-moln tjänsten](#gateway-cloud-service). 

   ![Registrera gateway](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Ange den här informationen för din gateway-installation:

   * Det namn som du vill använda för installationen 

   * Den återställnings nyckel som du vill skapa, som måste innehålla minst åtta tecken

     > [!IMPORTANT]
     > Spara och behåll återställnings nyckeln på en säker plats. Du behöver den här nyckeln när du ändrar gatewayens plats eller när du migrerar, återställer eller tar över en befintlig gateway.

   * Bekräftelse för återställnings nyckeln 

     ![Konfigurera Gateway](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Kontrol lera den region som valts för gateway-moln tjänsten och Azure Service Bus som används av din gateway-installation. 

   ![Kontrol lera region](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Om du vill ändra den här regionen efter att du har installerat din gateway behöver du återställnings nyckeln för den gateway-installationen. Du måste också avinstallera och installera om gatewayen. Mer information finns i [ändra plats, migrera, återställa eller ta över en befintlig gateway](#update-gateway-installation).

   *Varför ska du ändra region för din gateway-installation?* 

   Om du till exempel vill minska svars tiden kan du ändra din gateways region till samma region som din Logic app. 
   Eller så kan du välja den region som är närmast din lokala data källa. 
   Din *gateway-resurs i Azure* och din Logic-app kan ha olika platser.

8. Om du vill acceptera standard regionen väljer du **Konfigurera**. Om du vill ändra standard region följer du dessa steg:

   1. Bredvid den aktuella regionen väljer du **ändra region**. 

      ![Ändra region](./media/logic-apps-gateway-install/change-region.png)

   2. Öppna listan **Välj region** på nästa sida, Välj den region som du vill använda och välj sedan **Slutför**.

      ![Välj en annan region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. När bekräftelse sidan visas väljer du **Stäng**. 

   Installations programmet bekräftar att din gateway nu är online och redo att användas.

   ![Färdig Gateway](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Registrera nu din gateway i Azure genom att [skapa en Azure-resurs för din gateway-installation](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Ändra plats, migrera, återställa eller ta över en befintlig gateway

Om du måste ändra gatewayens plats, flytta Gateway-installationen till en ny dator, återställa en skadad gateway eller bli ägare till en befintlig gateway, behöver du återställnings nyckeln som angavs under Gateway-installationen. Den här åtgärden kopplar från den gamla gatewayen.

1. Från datorns **kontroll panel**går du till **program och funktioner**. I listan program väljer du **lokal datagateway**och väljer sedan **Avinstallera**.

2. [Installera om den lokala](https://aka.ms/on-premises-data-gateway-installer)datagatewayen.

3. När installations programmet har öppnats loggar du in med samma arbets-eller skol konto som användes tidigare för att installera gatewayen.

4. Välj **migrera, Återställ eller ta över en befintlig gateway**och välj sedan **Nästa**.

   ![Välj "migrera, Återställ eller överköps en befintlig gateway"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. Under **tillgängliga gatewayer** eller **tillgängliga Gateway-kluster**väljer du den gateway-installation som du vill ändra. Ange återställnings nyckeln för gateway-installationen. 

   ![Välj primär Gateway](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Om du vill ändra region väljer du **ändra region** och ny region.

7. När du är klar väljer du **Konfigurera**.

## <a name="configure-proxy-or-firewall"></a>Konfigurera proxy eller brand vägg

Den lokala datagatewayen skapar en utgående anslutning till [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Om din arbets miljö kräver att trafiken går via en proxyserver för att få åtkomst till Internet, kan den här begränsningen hindra datagatewayen från att ansluta till gateway-moln tjänsten. Du kan ta reda på om nätverket använder en proxyserver genom att läsa artikeln på superuser.com: 

[Hur gör jag för att vet du vilken proxyserver jag använder? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Information om hur du anger proxyinformation för din gateway finns i [Konfigurera proxyinställningar](https://docs.microsoft.com/power-bi/service-gateway-proxy). Kontrol lera om din proxyserver eller brand vägg kan blockera anslutningar genom att kontrol lera om datorn kan ansluta till Internet och [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Kör följande kommando från en PowerShell-kommandotolk:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Det här kommandot testar bara nätverks anslutningen och anslutningen till Azure Service Bus. Kommandot gör ingenting med gatewayen eller moln tjänsten för gateway som krypterar och lagrar dina autentiseringsuppgifter och gateway-information. 
>
> Det här kommandot är också tillgängligt i Windows Server 2012 R2 eller senare och Windows 8,1 eller senare. På tidigare versioner av operativ systemet kan du använda Telnet för att testa anslutningen. Läs mer om [Azure Service Bus och hybrid lösningar](../service-bus-messaging/service-bus-messaging-overview.md).

Resultatet bör se ut ungefär som i det här exemplet med **TcpTestSucceeded** inställt på **Sant**:

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

Om **TcpTestSucceeded** inte är inställt på **True**kan din gateway blockeras av en brand vägg. Om du vill vara omfattande ersätter du **dator namn** och **port** värden med värdena som anges under [Konfigurera portar](#configure-ports) i den här artikeln.

Brand väggen kan även blockera anslutningar som Azure Service Bus gör till Azure-datacentren. Om det här scenariot inträffar godkänner du (Avblockera) alla IP-adresser för dessa data Center i din region. För dessa IP-adresser, [Hämta listan med Azure IP-adresser här](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Konfigurera portar

Gatewayen skapar en utgående anslutning till [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) och kommunicerar via utgående portar: TCP 443 (standard), 5671, 5672, 9350 till 9354. Gatewayen kräver inte inkommande portar. Läs mer om [Azure Service Bus och hybrid lösningar](../service-bus-messaging/service-bus-messaging-overview.md).

Gatewayen använder följande fullständigt kvalificerade domän namn:

| Domännamn | Utgående portar | Beskrivning | 
| ------------ | -------------- | ----------- | 
| *. analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | Används för autentisering beroende på konfiguration. | 
| *.msftncsi.com | 443 | Används för att testa Internet anslutningen när gatewayen inte kan kontaktas av Power BI-tjänst. | 
| *.servicebus.windows.net | 443, 9350-9354 | Lyssnare på Service Bus Relay över TCP (kräver 443 för hämtning av Access Control-token) | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queueing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

I vissa fall görs Azure Service Bus anslutningar med IP-adresser istället för fullständigt kvalificerade domän namn. Så kanske du vill placera IP-adresserna för ditt dataområde i brandväggen i listan över tillåtna. Om du vill placera IP-adresser snarare än domäner i listan över tillåtna kan du hämta och använda listan över [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). IP-adresserna i listan är i [CIDR-notation (Classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

### <a name="force-https-communication-with-azure-service-bus"></a>Tvinga HTTPS-kommunikation med Azure Service Bus

Vissa proxyservrar tillåter trafik genom enbart till portarna 80 och 443. Kommunikation med Azure Service Bus inträffar som standard på andra portar än 443.
Du kan tvinga gatewayen att kommunicera med Azure Service Bus över HTTPS i stället för direkt TCP, men det kan minska prestanda avsevärt. Följ dessa steg om du vill utföra den här uppgiften:

1. Bläddra till platsen för den lokala datagateway-klienten, som du normalt kan hitta här:```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Annars kan du hitta klient platsen genom att öppna tjänster-konsolen på samma dator, hitta en **lokal datagateway-tjänst**och visa **sökvägen till** den körbara egenskapen.

2. Öppna den här konfigurations filen: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Ändra **ServiceBusSystemConnectivityModeString** -värdet från **identifiera automatiskt** till **https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-tjänstkonto

På den dator där du installerar den lokala datagatewayen körs gatewayen som ett Windows-tjänst konto med namnet "lokal datagateway-tjänst". Gatewayen använder dock "NT SERVICE\PBIEgwService"-namnet för inloggnings uppgifter för "logga in som". Som standard har gatewayen behörigheten "logga in som en tjänst" på den dator där du installerar gatewayen. Windows-tjänstkontot för gatewayen skiljer sig vanligt vis från det konto som du använder för att ansluta till lokala data källor och från det arbets-eller skol konto som du använder för att logga in på moln tjänster.

För att du ska kunna skapa och underhålla gatewayen i Azure Portal måste det här Windows-tjänstkontot ha minst **deltagar** behörighet. Se [Hantera åtkomst med RBAC och Azure Portal](../role-based-access-control/role-assignments-portal.md)för att kontrol lera behörigheterna. 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Starta om Gateway

Datagatewayen körs som en fönster tjänst, så som alla andra Windows-tjänster kan du starta och stoppa gatewayen på olika sätt. Du kan till exempel öppna en kommando tolk med förhöjd behörighet på den dator där gatewayen körs och köra något av kommandona:

* Stoppa tjänsten genom att köra det här kommandot:
  
  `net stop PBIEgwService`

* Starta tjänsten genom att köra det här kommandot:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Administration på klient nivå 

För närvarande finns det ingen enda plats där klient administratörer kan hantera alla gatewayer som andra användare har installerat och konfigurerat. Om du är innehavaradministratör kanske du vill att användarna i organisationen ska lägga till dig som administratör för varje gateway som de installerar. På så sätt kan du hantera alla gatewayer i din organisation via sidan Gateway-inställningar eller via [PowerShell-kommandon](/data-integration/gateway/service-gateway-powershell-support). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Hur fungerar gatewayen?

Datagatewayen underlättar snabb och säker kommunikation mellan din Logic app, Gateway-moln tjänsten och den lokala data källan. Gateway-moln tjänsten krypterar och lagrar autentiseringsuppgifter för data källan och gateway-informationen. Tjänsten dirigerar även frågor och resultat mellan din Logic app, den lokala datagatewayen och din data källa. 

Gatewayen fungerar med brand väggar och använder endast utgående anslutningar. All trafik kommer som säker utgående trafik från Gateway-agenten. Gatewayen vidarebefordrar data från lokala källor på krypterade kanaler via Azure Service Bus. Den här Service Bus skapar en kanal mellan gatewayen och den anropande tjänsten, men lagrar inte några data. Alla data som passerar genom gatewayen krypteras.

![diagram-för-lokal-data-Gateway-Flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Dessa steg beskriver vad som händer när en användare i molnet interagerar med ett element som är anslutet till din lokala data Källa:

1. Gateway-moln tjänsten skapar en fråga, tillsammans med de krypterade autentiseringsuppgifterna för data källan, och skickar frågan till kön för att gatewayen ska kunna bearbetas.

2. Gateway-moln tjänsten analyserar frågan och skickar begäran till Azure Service Bus.

3. Den lokala datagatewayen avsöker Azure Service Bus för väntande begär Anden.

4. Gatewayen hämtar frågan, dekrypterar autentiseringsuppgifterna och ansluter till data källan med dessa autentiseringsuppgifter.

5. Gatewayen skickar frågan till data källan för körning.

6. Resultaten skickas från data källan tillbaka till gatewayen och sedan till moln tjänsten Gateway. Gateway-moln tjänsten använder sedan resultaten.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="general"></a>Allmänt

**F**: Behöver jag en gateway för data källor i molnet, t. ex. Azure SQL Database? <br/>
**S**: Nej, gatewayen ansluter endast till lokala data källor.

**F**: Måste gatewayen installeras på samma dator som data källan? <br/>
**S**: Nej, gatewayen ansluter till data källan med hjälp av den angivna anslutnings informationen. Betrakta gatewayen som ett klient program i den här meningen. Gatewayen behöver bara kunna ansluta till det angivna Server namnet.

<a name="why-azure-work-school-account"></a>

**F**: Varför måste jag använda ett arbets-eller skol konto för att logga in? <br/>
**S**: Du kan bara använda ett arbets-eller skol konto när du installerar den lokala datagatewayen. Ditt inloggnings konto lagras i en klient som hanteras av Azure Active Directory (Azure AD). Vanligt vis matchar ditt Azure AD-kontos User Principal Name (UPN) e-postadressen.

**F**: Var lagras mina autentiseringsuppgifter? <br/>
**S**: De autentiseringsuppgifter som du anger för en data källa krypteras och lagras i gatewayens moln tjänst. Autentiseringsuppgifterna dekrypteras med den lokala datagatewayen.

**F**: Finns det några krav på bandbredd i nätverket? <br/>
**S**: Kontrol lera att nätverks anslutningen har ett lämpligt data flöde. Varje miljö är annorlunda och mängden data som skickas kan påverka resultatet. Testa [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)för att garantera en data flödes nivå mellan din lokala data källa och Azure-datacenter. För att mäta ditt data flöde kan du prova med ett externt verktyg som Azures hastighets test.

**F**: Vad är svars tiden för att köra frågor till en data Källa från gatewayen? Vilken är den bästa arkitekturen? <br/>
**S**: Du kan minska nätverks fördröjningen genom att installera gatewayen så nära data källan som möjligt. Om du kan installera gatewayen på den faktiska data källan minimerar detta närhet den svars tid som introduceras. Tänk också på närhet till Azure-datacenter. Om din tjänst exempelvis använder data centret västra USA och du har SQL Server som finns på en virtuell Azure-dator kan du vilja ha din virtuella Azure-dator i regionen Västra USA. Detta närhet minimerar svars tiden och undviker utgående kostnader på den virtuella Azure-datorn.

**F**: Hur skickas resultat tillbaka till molnet? <br/>
**S**: Resultaten skickas via Azure Service Bus.

**F**: Finns det några inkommande anslutningar till gatewayen från molnet? <br/>
**S**: Nej, gatewayen använder utgående anslutningar till Azure Service Bus.

**F**: Vad händer om jag blockerar utgående anslutningar? Vad behöver jag för att öppna? <br/>
**S**: Se de portar och värdar som används av gatewayen.

**F**: Vad heter den faktiska Windows-tjänsten? <br/>
**S**: På fliken tjänster i aktivitets hanteraren är tjänst namnet "PBIEgwService" eller Power BI Enterprise Gateway Service. I tjänst konsolen är tjänst namnet "lokal datagateway-tjänst". Windows-tjänsten använder "NT SERVICE\PBIEgwService" som tjänst-SID (SSID).

**F**: Kan gatewayens Windows-tjänst köras med ett Azure Active Directory konto? <br/>
**S**: Nej, Windows-tjänsten måste ha ett giltigt Windows-konto.

### <a name="disaster-recovery"></a>Haveriberedskap

**F**: Vilka alternativ är tillgängliga för haveri beredskap? <br/>
**S**: Du kan använda återställnings nyckeln för att återställa eller flytta en gateway. När du installerar gatewayen anger du återställnings nyckeln.

**F**: Vad är fördelen med återställnings nyckeln? <br/>
**S**: Återställnings nyckeln är ett sätt att migrera eller återställa Gateway-inställningarna efter en katastrof.

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet behandlar några vanliga problem som du kan ha medan du konfigurerar och använder den lokala datagatewayen.

**F**: Varför gick det inte att installera min Gateway? <br/>
**S**: Det här problemet kan inträffa om antivirus programmet på mål datorn är inaktuellt. Du kan antingen uppdatera antivirus program varan eller inaktivera antivirus program varan, men bara under Gateway-installationen och sedan aktivera program varan igen.

**F**: Varför visas inte min Gateway-installation när jag skapar en gateway-resurs i Azure? <br/>
**S**: Det här problemet kan inträffa av följande orsaker:

* Gateway-installationen har redan registrerats och begärts av en annan gateway-resurs i Azure. Gateway-installationer visas inte i listan instanser när gateway-resurserna har skapats för dem.
Om du vill kontrol lera dina gateway-registreringar i Azure Portal granskar du alla Azure-resurser med den **lokala** datagatewayens typ för *alla* Azure-prenumerationer. 

* Azure AD-identiteten för den person som installerade gatewayen skiljer sig från den person som loggade in på Azure Portal. Kontrol lera att du har loggat in med samma identitet som installerade gatewayen.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**F**: Var finns Gateway-loggarna? <br/>
**S**: Se avsnittet [ **loggar** ](#logs) längre fram i den här artikeln.

**F**: Hur kan jag se vilka frågor som skickas till den lokala data källan? <br/>
**S**: Du kan aktivera spårning av frågor som innehåller de frågor som skickas. Kom ihåg att ändra tillbaka frågan till det ursprungliga värdet när du är färdig med fel sökningen. När du lämnar spårningsfunktionen aktive rad skapas större loggar.

Du kan också titta på verktyg som data källan har för att spåra frågor. Du kan till exempel använda utökade händelser eller SQL-profiler för SQL Server och Analysis Services.

### <a name="outdated-gateway-version"></a>Inaktuell Gateway-version

Många problem kan vara en yta när gateway-versionen blir inaktuell. Som en bra allmän praxis ser du till att du har den senaste versionen. Om du inte har uppdaterat gatewayen i en månad eller längre, kan du överväga att installera den senaste versionen av gatewayen och se om du kan återskapa problemet.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Fel: Det gick inte att lägga till användaren i gruppen. (-2147463168 PBIEgwService Performance Log Users)

Du kan få det här felet om du försöker installera gatewayen på en domänkontrollant som inte stöds. Se till att du distribuerar gatewayen på en dator som inte är en domänkontrollant.

<a name="logs"></a>

### <a name="logs"></a>Logs

Starta alltid genom att samla in och granska Gateway-loggarna för att hjälpa dig att felsöka. Du kan samla in loggar på flera sätt, men det enklaste alternativet när du har installerat gatewayen är via gatewayens installations gränssnitt. 

1. Öppna installations programmet för den lokala datagatewayen på datorn.
2. På den vänstra menyn väljer du **diagnostik**.
3. Under **Gateway-loggar**väljer du **Exportera loggar**.

   ![Exportera loggar från Gateway-installationsprogrammet](./media/logic-apps-gateway-install/export-logs.png)

Här är andra platser där du kan hitta olika loggar:

| Loggtyp | Location | 
|----------|----------| 
| **Installations loggar** | %localappdata%\Temp\On-premises_data_gateway_<*yyyymmdd*>.<*number*>.log | 
| **Konfigurations loggar** | C:\Users\<*användar namn*> \AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator <*ÅÅÅÅMMDD*>. <*Number*>. log | 
| **Service loggar för Enterprise Gateway** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway <*ååååmmdd*>. <*nummer*>. log | 
||| 

**Händelse loggar**

Följ dessa steg om du vill söka efter händelse loggar för gatewayen:

1. På datorn med Gateway-installationen öppnar du **Loggboken**. 
2. Expandera **Loggboken (lokala)**  > **program-och tjänst loggar**. 
3. Välj **lokal datagateway-tjänst**.

   ![Visa händelse loggar för gateway](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Granska prestanda för långsamma frågor

Om du hittar frågor som körs långsamt via gatewayen kan du aktivera ytterligare loggning som matar ut frågor och deras varaktighet. Dessa loggar kan hjälpa dig att hitta vilka frågor som är långsamma eller tids krävande. Om du vill justera prestanda för frågor kan du behöva ändra data källan, till exempel justera index för SQL Server frågor.

Följ dessa steg om du vill fastställa varaktigheten för en fråga:

1. Bläddra till samma plats som Gateway-klienten, som du normalt kan hitta här:```C:\Program Files\On-premises data gateway```

   Annars kan du hitta klient platsen genom att öppna tjänster-konsolen på samma dator, hitta en **lokal datagateway-tjänst**och visa **sökvägen till** den körbara egenskapen.

2. Öppna och redigera dessa konfigurationsfiler enligt beskrivningen:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     I den här filen ändrar du **EmitQueryTraces** -värdet från **falskt** till **Sant** så att din gateway kan logga frågor som skickas från gatewayen till en data Källa:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Om du aktiverar EmitQueryTraces-inställningen kan logg storleken öka avsevärt beroende på gatewayens användning. När du har granskat loggarna, se till att återställa EmitQueryTraces till **false** igen, i stället för att lämna den här inställningen för lång sikt.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Om du vill visa detaljerade poster för gateway-loggen, inklusive poster som visar varaktighet, ändrar du **TracingVerbosity** -värdet från **4** till **5** genom att utföra något av stegen: 

     * I den här konfigurations filen ändrar du **TracingVerbosity** -värdet från **4** till **5** 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Öppna installations programmet för gateway, Välj **diagnostik**, aktivera **ytterligare loggning**och välj sedan **Använd**:

       ![Aktivera ytterligare loggning](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Om du aktiverar TracingVerbosity-inställningen kan logg storleken öka avsevärt beroende på gatewayens användning. När du har granskat loggarna kontrollerar du att du inaktiverar **ytterligare loggning** i installations programmet för gatewayen eller återställer TracingVerbosity till **4** igen i konfigurations filen i stället för att lämna den här inställningen för lång sikt.

3. Följ de här stegen för att hitta varaktigheten för en fråga:

   1. [Exportera](#logs) och öppna Gateway-loggen.

   2. Om du vill söka efter en fråga kan du söka efter en aktivitets typ, till exempel: 

      | Aktivitetstyp | Beskrivning | 
      |---------------|-------------| 
      | MGEQ | Frågor som körs över ADO.NET. | 
      | MGEO | Frågor som körs över OLEDB. | 
      | MGEM | Frågor som körs från mashup-motorn. | 
      ||| 

   3. Observera den andra GUID, som är fråge-ID.

   4. Fortsätt att söka efter aktivitets typen tills du hittar en post med namnet "FireActivityCompletedSuccessfullyEvent" som har en varaktighet i millisekunder. 
   Bekräfta att posten har samma förfrågnings-ID, till exempel:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > Posten "FireActivityCompletedSuccessfullyEvent" är en utförlig post och loggas inte om inte inställningen "TracingVerbosity" är på nivå 5.

### <a name="trace-traffic-with-fiddler"></a>Spåra trafik med Fiddler

[Fiddler](https://www.telerik.com/fiddler) är ett kostnads fritt verktyg från Telerik som övervakar http-trafik. Du kan granska trafiken med Power BI-tjänst från klient datorn. Den här tjänsten kan visa fel och annan relaterad information.

## <a name="next-steps"></a>Nästa steg
    
* [Ansluta till lokala data från Logic Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Enterprise-integrerings funktioner](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Anslutningsprogram för Azure Logic Apps](../connectors/apis-list.md)
