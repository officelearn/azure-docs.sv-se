---
title: Konfigurera molnet för hybridanslutning med Azure och Azure-stacken | Microsoft Docs
description: Lär dig mer om att konfigurera molnet för hybridanslutning med Azure och Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 72c5c4b0f0ab752bb02e6bee7cd038afca44ee1b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605203"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Självstudier: Konfigurera molnet för hybridanslutning med Azure och Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan komma åt resurser med säkerhet i globala Azure och Azure-stacken använder mönstret för hybrid-anslutning.

I den här självstudiekursen skapar du en exempel-miljö till:

> [!div class="checklist"]
> - Behåll data lokalt för att uppfylla sekretess- och regelkrav men har tillgång till globala Azure-resurser.
> - Underhålla en äldre medan molnet skalade appdistribution och -resurser i globala Azure.

## <a name="prerequisites"></a>Förutsättningar

Några komponenter krävs för att skapa en hybriddistribution för anslutningen. Vissa av dessa komponenter kan ta tid att förbereda, så måste du planera på lämpligt sätt.

**Azure Stack**

En Azure OEM/maskinvara Partner kan distribuera ett Azure-Stack för produktion, och alla användare kan distribuera ett Azure Stack Development Kit (ASDK).

**Azure Stack-komponenter**

En Operator för Azure-stacken måste distribuera App Service, skapa planer och erbjudanden, skapa en klientprenumeration och lägga till Windows Server 2016-avbildningen. Om du redan har vissa av dessa komponenter, kontrollera att de uppfyller kraven innan du börjar den här kursen.

Den här kursen förutsätter att du har några grundläggande kunskaper om Azure och Azure-stacken. Mer information innan du startar guiden, finns i följande artiklar:

 - [Introduktion till Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure-stacken viktiga begrepp](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
 - Skapa en [Webbapp](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) i Azure. Anteckna Web App-URL eftersom du behöver i kursen.

### <a name="azure-stack"></a>Azure Stack

 - Använda din produktion Azure Stack eller distribuera Azure Stack Development Kit från https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Distribuera ASDK kan ta upp till 7 timmar, så planera på lämpligt sätt.

 - Distribuera [Apptjänst](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-tjänster till Azure-stacken.
 - [Skapa planer och erbjudanden](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) i Azure Stack-miljö.
 - [Skapa klientprenumeration](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) i Azure Stack-miljön.

### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du uppfyller följande krav innan du börjar konfigurera molnet hybridanslutning:

 - Du behöver ett externt Internetriktade offentlig IPv4-adress för VPN-enhet. Den här IP-adressen kan inte finnas bakom en NAT
 - Alla resurser som distribueras i samma region/plats.

#### <a name="tutorial-example-values"></a>Självstudiekurs exempelvärden

I exemplen i den här kursen används följande värden. Du kan använda dessa värden om du vill skapa en testmiljö eller referera till dem för att bättre förstå exemplen. Mer information om VPN-gatewayinställningar finns i [Om VPN Gateway-inställningar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifikationer för anslutningen:

 - **VPN-typ**: ruttbaserad
 - **Anslutningstypen**: plats-till-plats (IPsec)
 - **Gateway-typ**: VPN
 - **Azure anslutningsnamn**: Azure-Gateway-AzureStack-S2SGateway (portalen kommer Autofyll värdet)
 - **Azure Stack anslutningsnamn**: AzureStack-Gateway-Azure-S2SGateway (portalen kommer Autofyll värdet)
 - **Delad nyckel**: alla kompatibla med VPN-maskinvara med matchande värdena på båda sidor av anslutningen
 - **Prenumerationen**: något föredrar prenumeration
 - **Resursgruppen**: Test-infrastruktur

Nätverk och undernät IP-adresser:

| Azure-/ Azure Stack-anslutning | Namn | Undernät | IP-adress |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure-vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack-vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure virtuell nätverksgateway | Azure-Gateway |  |  |
| Azure-stacken virtuell nätverksgateway | AzureStack-Gateway |  |  |
| Azure offentlig IP-adress | Azure-GatewayPublicIP |  | Fastställa när skapas |
| Azure-stacken offentlig IP-adress | AzureStack GatewayPublicIP |  | Fastställa när skapas |
| Azure lokal nätverksgateway | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Azure-stacken offentliga IP-värde |
| Azure-stacken lokal nätverksgateway | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure offentliga IP-värde |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Skapa ett virtuellt nätverk i globala Azure och Azure-stacken

Använd följande steg för att skapa ett virtuellt nätverk med hjälp av portalen. Du kan använda dessa [exempelvärden](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) om du använder den här artikeln som en vägledning. Men om du använder den här artikeln för att konfigurera en produktionsmiljö, ersätter du exempel inställningarna med egna värden.

> [!IMPORTANT]
> Du måste se till att det inte finns en överlappning av IP-adresser i Azure eller Azure-stacken vNet-adressutrymmen.

Skapa ett vNet i Azure:

1. Använder webbläsaren för att ansluta till den [Azure-portalen](http://portal.azure.com/) och logga in med ditt Azure-konto.
2. Välj **skapar du en resurs**. I den **söka marketplace** anger `virtual network`''. Hitta **för virtuella nätverk** i resultatlistan och välj sedan **virtuellt nätverk**.
3. Från den **Välj en distributionsmodell** väljer **Resource Manager**, och välj sedan **skapa**.
4. På **skapa virtuellt nätverk**, konfigurera VNet-inställningarna. Obligatoriska fältnamnen på avbildningarna föregås med en röd asterisk.  När du anger ett giltigt värde ändras asterisken till en grön bock.

Skapa ett vNet i Azure Stack:

* Upprepa föregående steg (1-4) med hjälp av Azure-stacken **innehavarportalen**.

## <a name="add-a-gateway-subnet"></a>Lägga till ett gatewayundernät

Innan du ansluter det virtuella nätverket till en gateway, måste du skapa gateway-undernät för det virtuella nätverket som du vill ansluta till. Gateway-tjänster använder IP-adresser som du anger i gateway-undernätet.

I den [Azure-portalen](http://portal.azure.com/), navigera till det virtuella nätverket för Resource Manager där du vill skapa en virtuell nätverksgateway.

1. Välj vNet att öppna den **för virtuella nätverk** sidan.
2. I **inställningar**väljer **undernät**.
3. På den **undernät** väljer **+ gatewayundernät** att öppna den **Lägg till undernät** sidan.

    ![Lägg till gateway-undernät](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. Den **namn** för undernätet fylls i automatiskt med värdet 'GatewaySubnet'. Det här värdet krävs för Azure så att undernätet som gateway-undernätet.
5. Ändra den **adressintervall** värden som tillhandahålls för att matcha konfigurationskrav och välj sedan **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Skapa en virtuell nätverksgateway i Azure och Azure Stack

Använd följande steg för att skapa en virtuell nätverksgateway i Azure.

1. På vänster sida av portalsidan väljer **+** och ange virtuell nätverksgateway i sökfältet.
2. I **resultat**väljer **virtuell nätverksgateway**.
3. I **virtuell nätverksgateway**väljer **skapa** att öppna den **Skapa virtuell nätverksgateway** sidan.
4. På **Skapa virtuell nätverksgateway**, ange värden för din nätverks-gateway som visas i **självstudiekursen exempelvärden**, och följande värden:

    - **SKU**: grundläggande
    - **Virtuellt nätverk**: Välj det virtuella nätverk som du skapade tidigare. Gateway-undernätet som du skapat är markerad.
    - **Första IP-konfiguration**: Detta är offentliga IP-Adressen för din Gateway.
        - Välj **skapa gateway IP-konfiguration**, vilket leder till den **Välj offentlig IP-adress** sidan.
        - Välj **+ Skapa nytt** att öppna den **skapa offentlig IP-adress** sidan.
        - Ange en **namn** för din offentliga IP-adress. Lämna SKU som **grundläggande**, och välj sedan **OK** att spara ändringarna.

       > [!Note]
       > VPN-Gateway stöder för närvarande endast dynamiska offentliga IP-adressallokering. Det betyder dock inte att IP-adressen ändras när det har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Ändra storlek på, återställa eller andra internt Underhåll/uppgraderingar till din VPN-gateway ändrar inte IP-adress.

4. Verifiera gatewayinställningarna.
5. Välj **skapa** att skapa VPN-gatewayen. Gatewayinställningarna verifieras och panelen ”distribuerar virtuell nätverksgateway” visas på instrumentpanelen.

   >[!Note]
   >Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

    När gatewayen har skapats kan se du IP-adress som tilldelats genom att titta på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet. Välj enheten du vill se mer information om gateway.

6. Upprepa föregående steg (1-5) för Azure Stack-distribution.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Skapa lokal nätverksgateway i Azure och Azure-stacken

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. Du namnge platsen som Azure eller Azure-stacken kan referera till och ange sedan:

- IP-adressen på den lokala VPN-enhet som du skapar en anslutning för.
- IP-adressprefixet som vidarebefordras via VPN-gatewayen till VPN-enhet. Adressprefixen du anger är de prefix som finns på det lokala nätverket.

  >[!Note]
  >Om ändringarna lokala nätverk eller om du behöver ändra offentliga IP-adressen för VPN-enhet, kan du enkelt uppdatera dessa värden senare.

1. I portalen, Välj **+ skapa en resurs för**.
2. I sökrutan anger **lokal nätverksgateway**och välj **RETUR** att söka. En lista med resultat returneras.
3. Välj **lokal nätverksgateway**och välj **skapa** att öppna den **skapa lokal nätverksgateway** sidan.
4. På **skapa lokal nätverksgateway**, ange värden för din lokala nätverksgateway med hjälp av vår **självstudiekursen exempelvärden**. Innehåller följande värden.

    - **IP-adressen**: Detta är den offentliga IP-adressen för VPN-enhet som du vill Azure eller Azure-Stack för att ansluta till. Ange en giltig offentlig IP-adress som inte finns bakom en NAT, så Azure kan nå adressen. Om du inte har just nu IP-adress, kan du använda ett värde från exempel som platshållare, men du måste gå tillbaka och ersätta platshållaren med den offentliga IP-adressen för VPN-enhet. Azure kan inte ansluta till enheten förrän du anger en giltig adress.
    - **Adressutrymmet**: Detta är adressintervallet för nätverket som representerar den här lokala nätverket. Du kan lägga till flera adressintervall. Kontrollera att de intervall som du anger inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. Använd egna värden om du vill ansluta till lokalt platsen, inte ett exempelvärde.
    - **Konfigurera inställningar för BGP**: använda endast när du konfigurerar BGP. Annars ska detta inte väljas.
    - **Prenumerationen**: Kontrollera att korrekt prenumeration visas.
    - **Resursgruppen**: Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välj en som du redan har skapat.
    - **Plats**: Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din virtuella nätverk finns i, men du behöver inte göra det.
5. När du har angett värdena som krävs, Välj **skapa** att skapa den lokala nätverksgatewayen.
6. Upprepa dessa steg (1-5) för Azure Stack-distribution.

## <a name="configure-your-connection"></a>Konfigurera anslutningen

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. VPN-enhet som du konfigurerar kallas för en anslutning. Om du vill konfigurera anslutningen måste du:

- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
- Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. För att hitta offentliga IP-adressen för din VPN-gateway med hjälp av Azure portal, gå till virtuella nätverksgatewayer och välj sedan namnet på din gateway.

Använd följande steg för att skapa en plats-till-plats VPN-anslutning mellan din virtuella nätverksgateway och din lokala VPN-enhet.

1. Välj i Azure-portalen **+ skapa en resurs för**.
2. Sök efter **anslutningar**.
3. I **resultat**väljer **anslutningar**.
4. På **anslutning**väljer **skapa**.
5. På **Skapa anslutning**, konfigurera följande inställningar:

    - **Anslutningstypen**: Välj plats-till-plats (IPSec).
    - **Resursgruppen**: väljer du din testgrupp för resursen.
    - **Virtuell nätverksgateway**: Välj den virtuella nätverksgatewayen som du skapade.
    - **Lokal nätverksgateway**: Välj den lokala nätverksgateway som du skapade.
    - **Anslutningsnamn**: detta fylls i automatiskt med värden från två gateways.
    - **Delad nyckel**: värdet måste matcha värdet som du använder för din lokala lokala VPN-enhet. I kursen exempel används 'abc123', men du kan (och bör) använda något mer komplicerad. Viktigt är att det här värdet måste vara samma värde som du anger när du konfigurerar VPN-enhet.
    - Värdena för **prenumeration**, **resursgruppen**, och **plats** korrigeras.

6. Välj **OK** att skapa anslutningen.

Du kan se anslutningen i den **anslutningar** sida av den virtuella nätverksgatewayen. Status kommer att gå från *okänd* till *ansluta*, och sedan till *lyckades*.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Cloud mönster i [designmönster](https://docs.microsoft.com/azure/architecture/patterns).
