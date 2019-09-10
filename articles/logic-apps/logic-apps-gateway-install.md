---
title: Installera lokal datagateway – Azure Logic Apps
description: Innan du kan komma åt data lokalt från Azure Logic Apps kan du hämta och installera den lokala datagatewayen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 657bc704e33e89b1646dffa6123a27169e6c317a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860699"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installera lokal datagateway för Azure Logic Apps

Innan du kan ansluta till lokala data källor från Azure Logic Apps kan du hämta och installera den lokala datagatewayen på en lokal dator. Gatewayen fungerar som en brygga som ger snabb data överföring och kryptering mellan data källor lokalt (inte i molnet) och dina Logic Apps. Du kan använda samma Gateway-installation med andra moln tjänster, till exempel Power BI, Microsoft Flow, PowerApps och Azure Analysis Services. Information om hur du använder gatewayen med dessa tjänster finns i följande artiklar:

* [Microsoft Power BI lokal datagateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps lokal datagateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow lokal datagateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

Den här artikeln visar hur du hämtar, installerar och konfigurerar din lokala datagateway så att du kan komma åt lokala data källor från Azure Logic Apps. Du kan också lära dig mer om [hur data gatewayen fungerar](#gateway-cloud-service) senare i det här avsnittet.

<a name="supported-connections"></a>

Gatewayen stöder [lokala anslutningar](../connectors/apis-list.md#on-premises-connectors) i Azure Logic Apps för dessa data Källor:

* BizTalk Server 2016
* Filsystem
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Även om bara gatewayen inte tillkommer ytterligare kostnader, gäller [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md) för dessa anslutningar och andra åtgärder i Azure Logic Apps.

<a name="requirements"></a>

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

  * Du måste använda samma Azure-konto för att installera och administrera gatewayen. Under installationen kan du använda det här Azure-kontot för att koppla gatewayen på din dator till en Azure-prenumeration. Senare använder du samma Azure-konto när du skapar en Azure-resurs i Azure Portal för gateway-installationen. 

  * Du måste logga in med antingen ett arbets konto eller skol konto, även kallat ett *organisations* konto, som ser ut `username@contoso.com`så här. Du kan inte använda Azure B2B-konton (gäst) eller personliga Microsoft-konton @hotmail.com , @outlook.comtill exempel eller.

    > [!TIP]
    > Om du har registrerat dig för ett Office 365-erbjudande och inte angav din e-postadress för arbetet kan din `username@domain.onmicrosoft.com`adress se ut. Ditt konto lagras i en klient i en Azure Active Directory (Azure AD). I de flesta fall är användarens huvud namn (UPN) för ditt Azure AD-konto detsamma som din e-postadress.
    >
    > Om du vill använda en [Visual Studio Standard-prenumeration](https://visualstudio.microsoft.com/vs/pricing/) som är associerad med en Microsoft-konto måste du först [skapa en klient i Azure AD](../active-directory/develop/quickstart-create-new-tenant.md)eller använda standard katalogen. Lägg till en användare med ett lösen ord till katalogen och ge sedan användaren åtkomst till din prenumeration. 
    > Sedan kan du logga in under Gateway-installationen med det här användar namnet och lösen ordet.

* Här följer krav för den lokala datorn:

  **Minimi krav**

  * .NET Framework 4.6
  * 64-bitars version av Windows 7 eller Windows Server 2008 R2 (eller senare)

  **Rekommenderade krav**

  * 8 kärnor-CPU
  * 8 GB minne
  * 64-bitars version av Windows Server 2012 R2 eller senare
  * SSD-lagring (Solid-State Drive) för buffring

  > [!NOTE]
  > Gatewayen stöder inte Windows Server 2016 Core.

* **Relaterade överväganden**

  * Du kan bara installera den lokala datagatewayen på en lokal dator, inte en domänkontrollant. Men du behöver inte installera gatewayen på samma dator som data källan. Du behöver bara en gateway för alla dina data källor, så du behöver inte installera gatewayen för varje data källa.

    > [!TIP]
    > För att minimera svars tiden kan du installera gatewayen så nära som möjligt för din data källa, eller på samma dator, förutsatt att du har behörighet.

  * Installera gatewayen på en dator som finns i ett kabelanslutet nätverk, ansluten till Internet, alltid aktive rad och går inte i ström spar läge. I annat fall kan gatewayen inte köras och prestanda kan bli lidande över ett trådlöst nätverk.

  * Om du planerar att använda Windows-autentisering kontrollerar du att du installerar gatewayen på en dator som är medlem i samma Active Directory-miljö som dina data källor.

  * Den region som du väljer för din gateway-installation är samma plats som du måste välja när du senare skapar Azure Gateway-resursen för din Logic app. Som standard är den här regionen samma plats som din Azure AD-klient som hanterar ditt Azure-konto. Du kan dock ändra platsen under Gateway-installationen.

  * Gatewayen har två lägen: standard läge och personligt läge, som endast gäller för Power BI. Du kan inte ha mer än en gateway som körs i samma läge på samma dator.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installera datagateway

1. [Hämta och kör installations programmet för gatewayen på en lokal dator](https://aka.ms/on-premises-data-gateway-installer).

1. När installations programmet har öppnats väljer du **Nästa**.

   ![Installations introduktion](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Välj **lokal datagateway (rekommenderas)** , vilket är standard läge och välj sedan **Nästa**.

   ![Välj Gateway-läge](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. Granska minimi kraven, behåll standard installations Sök vägen, Godkänn användnings villkoren och välj sedan **Installera**.

   ![Granska krav och godkänn användnings villkoren](./media/logic-apps-gateway-install/accept-terms.png)

1. När gatewayen har installerats anger du e-postadressen för ditt organisations konto och väljer sedan **Logga**in, till exempel:

   ![Logga in med arbets-eller skol konto](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Du är nu inloggad på ditt konto.

1. Välj **Registrera en ny gateway på den här datorn** > **Nästa**. Det här steget registrerar din gateway-installation med [moln tjänsten Gateway](#gateway-cloud-service).

   ![Registrera gateway](./media/logic-apps-gateway-install/register-gateway.png)

1. Ange den här informationen för din gateway-installation:

   * Ett Gateway-namn som är unikt för Azure AD-klienten
   * Återställnings nyckeln, som måste innehålla minst åtta tecken, som du vill använda
   * Bekräftelse för återställnings nyckeln

   ![Konfigurera Gateway](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > Spara och behåll återställnings nyckeln på en säker plats. Du behöver den här nyckeln om du vill ändra plats, flytta, återställa eller ta över en gateway-installation.

   Observera alternativet att **lägga till i ett befintligt Gateway-kluster**, som du väljer när du installerar ytterligare gatewayer för [scenarier med hög tillgänglighet](#high-availability).

1. Kontrol lera regionen för gateway-moln tjänsten och [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) som används av din gateway-installation. Som standard är den här regionen samma plats som Azure AD-klienten för ditt Azure-konto.

   ![Kontrol lera region](./media/logic-apps-gateway-install/check-region.png)

1. Om du vill acceptera standard regionen väljer du **Konfigurera**. Men om standard regionen inte är den som är närmast dig kan du ändra regionen.

   *Varför ska du ändra region för din gateway-installation?*

   Om du till exempel vill minska svars tiden kan du ändra din gateways region till samma region som din Logic app. Eller så kan du välja den region som är närmast din lokala data källa. Din *gateway-resurs i Azure* och din Logic-app kan ha olika platser.

   1. Bredvid den aktuella regionen väljer du **ändra region**.

      ![Ändra region](./media/logic-apps-gateway-install/change-region.png)

   1. Öppna listan **Välj region** på nästa sida, Välj den region som du vill använda och välj sedan **Slutför**.

      ![Välj en annan region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Granska informationen i det slutliga bekräftelse fönstret. I det här exemplet används samma konto för Logic Apps, Power BI, PowerApps och Microsoft Flow, så gatewayen är tillgänglig för alla dessa tjänster. När du är klar väljer du **Stäng**.

   ![Färdig Gateway](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. [Skapa nu Azure-resursen för din gateway-installation](../logic-apps/logic-apps-gateway-connection.md).

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Stöd för hög tillgänglighet

För att undvika enskilda fel punkter för lokal data åtkomst kan du ha flera gateway-installationer (endast standard läge) med var och en på en annan dator och ange dem som ett kluster eller en grupp. På så sätt kan data begär Anden dirigeras till den andra gatewayen, och så vidare, om den primära gatewayen inte är tillgänglig. Eftersom du bara kan installera en standard-gateway på en dator måste du installera varje ytterligare gateway i klustret på en annan dator. Alla anslutningar som fungerar med den lokala datagatewayen stöder hög tillgänglighet. 

* Du måste redan ha minst en gateway-installation i samma Azure-prenumeration som den primära gatewayen och återställnings nyckeln för den installationen.

* Din primära Gateway måste köra Gateway-uppdateringen från november 2017 eller senare.

När du har konfigurerat din primära Gateway, väljer du **Lägg till i ett befintligt Gateway-kluster**när du går för att installera en annan gateway, väljer den primära gatewayen, som är den första gatewayen som du har installerat och anger återställnings nyckeln för den gatewayen. Mer information finns i [kluster med hög tillgänglighet för lokal datagateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Ändra plats, migrera, återställa eller ta över en befintlig gateway

Om du måste ändra gatewayens plats, flytta Gateway-installationen till en ny dator, återställa en skadad gateway eller bli ägare till en befintlig gateway, behöver du återställnings nyckeln som angavs under Gateway-installationen.

1. Kör installations programmet för gateway på den dator som har den befintliga gatewayen. Om du inte har de senaste Gateway-installations programmet [laddar du ned den senaste Gateway-versionen](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Innan du återställer gatewayen på den dator som har den ursprungliga Gateway-installationen måste du först avinstallera gatewayen på den datorn. Den här åtgärden kopplar från den ursprungliga gatewayen.
   > Om du tar bort eller tar bort ett Gateway-kluster för en moln tjänst kan du inte återställa klustret.

1. När installations programmet har öppnats loggar du in med samma Azure-konto som användes för att installera gatewayen.

1. Välj **migrera, Återställ eller ta över en befintlig gateway** > **härnäst**, till exempel:

   ![Välj "migrera, Återställ eller överköps en befintlig gateway"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Välj bland tillgängliga kluster och gatewayer och ange återställnings nyckeln för den valda gatewayen, till exempel:

   ![Välj Gateway](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Om du vill ändra region väljer du **ändra region**och sedan den nya regionen.

1. När du är klar väljer du **Konfigurera** så att du kan slutföra uppgiften.

## <a name="configure-proxy-or-firewall"></a>Konfigurera proxy eller brand vägg

Om din arbets miljö kräver att trafiken går via en proxyserver för att få åtkomst till Internet, kan den här begränsningen förhindra att den lokala datagatewayen ansluter till moln tjänsten för gateway och [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Mer information finns i [Konfigurera proxyinställningar för den lokala](https://docs.microsoft.com/power-bi/service-gateway-proxy)datagatewayen.

Kontrol lera om din proxyserver eller brand vägg kan blockera anslutningar genom att kontrol lera om datorn kan ansluta till Internet och Azure Service Bus. Kör följande kommando från en PowerShell-kommandotolk:

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

<a name="configure-ports"></a>

## <a name="configure-ports"></a>Konfigurera portar

Gatewayen skapar en utgående anslutning till Azure Service Bus och kommunicerar via utgående portar: TCP 443 (standard), 5671, 5672, 9350 till 9354. Gatewayen kräver inte inkommande portar. Läs mer om [Azure Service Bus och hybrid lösningar](../service-bus-messaging/service-bus-messaging-overview.md).

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

I vissa fall görs Azure Service Bus anslutningar med IP-adresser istället för fullständigt kvalificerade domän namn. Du kanske vill avblockera IP-adresserna för ditt data område i brand väggen. Om du vill tillåta åtkomst för IP-adresser i stället för domäner kan du hämta och använda [listan över IP-adressintervall Microsoft Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653). IP-adresserna i listan är i [CIDR-notation (Classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

### <a name="force-https-communication-with-azure-service-bus"></a>Tvinga HTTPS-kommunikation med Azure Service Bus

Vissa proxyservrar tillåter trafik genom enbart till portarna 80 och 443. Kommunikation med Azure Service Bus inträffar som standard på andra portar än 443. Du kan tvinga gatewayen att kommunicera med Azure Service Bus över HTTPS i stället för direkt TCP, men det kan minska prestanda avsevärt. Mer information finns i [FRAMTVINGA HTTPS-kommunikation med Azure Service Bus](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication#force-https-communication-with-azure-service-bus).

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-tjänstkonto

Som standard körs Gateway-installationen på den lokala datorn som ett Windows-tjänst konto med namnet "lokal datagateway-tjänst". Gateway-installationen använder `NT SERVICE\PBIEgwService` dock namnet på "logga in som"-autentiseringsuppgifter och har behörighet att logga in som en tjänst.

> [!NOTE]
> Ditt Windows-tjänstkonto skiljer sig från det konto som används för att ansluta till lokala data källor och från det Azure-konto som du använder när du loggar in på Cloud Services.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Starta om Gateway

Datagatewayen körs som en fönster tjänst, så som alla andra Windows-tjänster kan du starta och stoppa gatewayen på olika sätt. Mer information finns i [starta om en lokal datagateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

## <a name="tenant-level-administration"></a>Administration på klient nivå

För att få insyn i alla lokala datagatewayer i en Azure AD-klient kan globala administratörer i den klienten logga in på [Power Platform administrations Center](https://powerplatform.microsoft.com) som innehavaradministratör och välja alternativet **datagatewayer** . Mer information finns i [Administration på klient nivå för den lokala datagatewayen](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Så här fungerar gatewayen

Datagatewayen underlättar snabb och säker kommunikation mellan din Logic app, Gateway-moln tjänsten och den lokala data källan. Gateway-moln tjänsten krypterar och lagrar autentiseringsuppgifter för data källan och gateway-informationen. Tjänsten dirigerar även frågor och resultat mellan din Logic app, den lokala datagatewayen och din data källa.

Gatewayen fungerar med brand väggar och använder endast utgående anslutningar. All trafik kommer som säker utgående trafik från Gateway-agenten. Gatewayen vidarebefordrar data från lokala källor på krypterade kanaler via Azure Service Bus. Den här Service Bus skapar en kanal mellan gatewayen och den anropande tjänsten, men lagrar inte några data. Alla data som passerar genom gatewayen krypteras.

![Arkitektur för lokal datagateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Dessa steg beskriver vad som händer när en användare i molnet interagerar med ett element som är anslutet till din lokala data Källa:

1. Gateway-moln tjänsten skapar en fråga, tillsammans med de krypterade autentiseringsuppgifterna för data källan, och skickar frågan till kön för att gatewayen ska kunna bearbetas.

1. Gateway-moln tjänsten analyserar frågan och skickar begäran till Azure Service Bus.

1. Den lokala datagatewayen avsöker Azure Service Bus för väntande begär Anden.

1. Gatewayen hämtar frågan, dekrypterar autentiseringsuppgifterna och ansluter till data källan med dessa autentiseringsuppgifter.

1. Gatewayen skickar frågan till data källan för körning.

1. Resultaten skickas från data källan tillbaka till gatewayen och sedan till moln tjänsten Gateway. Gateway-moln tjänsten använder sedan resultaten.

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

* Gateway-installationen har redan registrerats och begärts av en annan gateway-resurs i Azure. Gateway-installationer visas inte i listan instanser när gateway-resurserna har skapats för dem. Om du vill kontrol lera dina gateway-registreringar i Azure Portal granskar du alla Azure-resurser med den **lokala Datagatewayens** typ för *alla* Azure-prenumerationer.

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

1. På den vänstra menyn väljer du **diagnostik**.

1. Under **Gateway-loggar**väljer du **Exportera loggar**.

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

1. Expandera **Loggboken (lokala)**  > **program-och tjänst loggar**.

1. Välj **lokal datagateway-tjänst**.

   ![Visa händelse loggar för gateway](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Granska prestanda för långsamma frågor

Om du hittar frågor som körs långsamt via gatewayen kan du aktivera ytterligare loggning som matar ut frågor och deras varaktighet. Dessa loggar kan hjälpa dig att hitta vilka frågor som är långsamma eller tids krävande. Om du vill justera prestanda för frågor kan du behöva ändra data källan, till exempel justera index för SQL Server frågor.

Följ dessa steg om du vill fastställa varaktigheten för en fråga:

1. Bläddra till samma plats som Gateway-klienten, som du normalt kan hitta här:```C:\Program Files\On-premises data gateway```

   Annars kan du hitta klient platsen genom att öppna tjänster-konsolen på samma dator, hitta en **lokal datagateway-tjänst**och visa **sökvägen till den körbara** egenskapen.

1. Öppna och redigera dessa konfigurationsfiler enligt beskrivningen:

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

1. Följ de här stegen för att hitta varaktigheten för en fråga:

   1. [Exportera](#logs) och öppna Gateway-loggen.

   1. Om du vill söka efter en fråga kan du söka efter en aktivitets typ, till exempel:

      | Aktivitetstyp | Beskrivning |
      |---------------|-------------|
      | MGEQ | Frågor som körs över ADO.NET |
      | MGEO | Frågor som körs över OLEDB |
      | MGEM | Frågor som körs från mashup-motorn |
      |||

   1. Observera den andra GUID, som är fråge-ID.

   1. Fortsätt att söka efter aktivitets typen tills du hittar en post med namnet "FireActivityCompletedSuccessfullyEvent" som har en varaktighet i millisekunder. Bekräfta att posten har samma förfrågnings-ID, till exempel:

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
