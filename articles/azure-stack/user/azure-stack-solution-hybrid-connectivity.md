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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 8b5f59d3fea402efa50bdafd7fc0439a93051e69
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Självstudier: Konfigurera molnet för hybridanslutning med Azure och Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan komma åt resurser med säkerhet i globala Azure och Azure-stacken använder mönstret för hybrid-anslutning. 

I den här självstudiekursen skapar du en exempel-miljö till:

> [!div class="checklist"]
> - Behåll data lokalt för sekretess eller regelkrav, men har tillgång till globala Azure-resurser.
> - Underhålla en äldre medan molnet skalade appdistribution och -resurser i globala Azure.

## <a name="prerequisites"></a>Förutsättningar

Några komponenter krävs för att skapa en hybrid anslutningen distributionen och kan ta lite tid att förbereda. 

En Azure OEM/maskinvara Partner kan distribuera ett Azure-Stack för produktion och alla användare kan distribuera en ASDK. Operatör Azure stacken måste också distribuera App Service, skapa planer och erbjudanden, skapa en klientprenumeration och lägga till Windows Server 2016-avbildningen. 

Om du redan har vissa av dessa komponenter, kontrollera att de uppfyller krav innan du börjar.

Det här avsnittet förutsätter också att du har viss erfarenhet av Azure och Azure-stacken. Om du vill veta mer innan du fortsätter bör du börja med dessa avsnitt:
 - [Introduktion till Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure-stacken viktiga begrepp](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
 - Skapa en [Webbapp](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) i Azure. Anteckna den nya Web App-URL, eftersom den används senare.

### <a name="azure-stack"></a>Azure Stack
 - Använd din produktion Azure Stack eller distribuera Azure Stack Development Kit länkade nedan:
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - Installationen tar vanligtvis några timmar att slutföra, så planera på lämpligt sätt.
 - Distribuera [Apptjänst](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-tjänster till Azure-stacken. 
 - [Skapa Plan/erbjudanden](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) i Azure Stack-miljön. 
 - [Skapa klientprenumeration](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) i Azure Stack-miljön. 


### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

 - Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
 - Se till att alla resurser som distribueras i samma region/plats.

#### <a name="example-values"></a>Exempelvärden

Vi använder följande värden i exemplen. Du kan använda dessa värden om du vill skapa en testmiljö eller referera till dem att bättre förstå exemplen i den här artikeln. Mer information om VPN-gatewayinställningar finns i [Om VPN Gateway-inställningar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifikationer för anslutningen:
 - **VPN-typ**: ruttbaserad
 - **Anslutningstypen**: plats-till-plats (IPsec)
 - **Gateway-typ**: VPN
 - **Azure anslutningsnamn**: Azure-Gateway-AzureStack-S2SGateway (portalen kommer Autofyll värdet)
 - **Azure Stack anslutningsnamn**: AzureStack-Gateway-Azure-S2SGateway (portalen kommer Autofyll värdet)
 - **Delad nyckel**: alla kompatibla med VPN-maskinvara med matchande värdena på båda sidor av anslutningen
 - **Prenumerationen**: något föredrar prenumeration
 - **Resursgruppen**: Test-infrastruktur

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

> [!note]  
> Du måste se till att det finns ingen överlappning av IP-adresser i Azure eller Azure-stacken vNet-adressutrymmen. 

Skapa ett vNet i Resource Manager-distributionsmodellen med hjälp av Azure portal. Använd [exempelvärden](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) om du använder de här stegen som en vägledning. Om du inte genomför de här stegen som en vägledning, bör du ersätter värdena med dina egna. 

1. Öppna en webbläsare, navigera till [Azure Portal](http://portal.azure.com/) och logga in med ditt Azure-konto.
2. Klicka på **Skapa en resurs**. I den **söka marketplace** anger `virtual network`''. Leta upp **för virtuella nätverk** returnerade i listan och öppna den **virtuellt nätverk** sidan.
3. Nästan längst ned på sidan virtuella nätverk från den **Välj en distributionsmodell** väljer **Resource Manager**, och välj sedan **skapa**. Då öppnas sidan ”Skapa virtuell nätverksgateway”.
4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga.
5. Upprepa stegen från den **innehavarportalen** i Azure-stacken.

## <a name="add-a-gateway-subnet"></a>Lägga till ett gatewayundernät

Innan du ansluter det virtuella nätverket till en gateway, måste du skapa gateway-undernätet för det virtuella nätverk som du vill ansluta till. Gatewaytjänsterna använder de IP-adresser som finns angivna i gatewayundernätet.

I [portalen](http://portal.azure.com/) går du till det virtuella Resource Manager-nätverket för vilket du vill skapa en virtuell nätverksgateway.

1. I den **inställningar** på VNet sidan, Välj **undernät** att expandera den **undernät** sidan.
2. På den **undernät** väljer **+ gatewayundernät** att öppna den **Lägg till undernät** sidan.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för Azure så att undernätet som gateway-undernätet. Justera det automatiskt ifylld **adressintervall** värdena för att matcha konfigurationskrav, välj sedan **OK** längst ned på sidan för att skapa undernätet.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Skapa en virtuell nätverksgateway i Azure och Azure Stack

1. På vänster sida av portalsidan väljer + och ange virtuell nätverksgateway i sökningen. I **resultat**, leta upp och markera **virtuell nätverksgateway**.
2. Längst ned i den **virtuell nätverksgateway** väljer **skapa**. Då öppnas sidan **Skapa virtuell nätverksgateway**.
3. På den **Skapa virtuell nätverksgateway** anger värden för din virtuella nätverksgateway enligt anvisningarna i Exempelvärden plus ytterligare värden som beskrivs nedan.
    - **SKU**: grundläggande
    - **Virtuellt nätverk**: Välj det virtuella nätverk som du skapade tidigare. Den väljer automatiskt gateway-undernätet som du skapade tidigare. 
    - **Första IP-konfiguration**: Detta är offentliga IP-Adressen för din Gateway. 
        - Klicka på den **skapa gateway IP-konfiguration** och det gör att den **Välj offentlig IP-adress** sidan.
        - Klicka på **+ Skapa nytt** att öppna den **skapa offentlig IP-adress** sidan.
        - Ange en **namn** för din offentliga IP-adress. Lämna SKU som **grundläggande**och välj **OK** längst ned på den här sidan om du vill spara ändringarna.

    > [!note]  
    > VPN-Gateway stöder för närvarande endast dynamiska offentliga IP-adressallokering. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

4. Verifiera inställningarna. 
5. Klicka på **Skapa** för att börja skapa VPN-gatewayen. Inställningarna verifieras och ikonen "Distribuerar virtuell nätverksgateway" visas på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

    När gatewayen har skapats kan du se IP-adressen som har tilldelats till den genom att visa det virtuella nätverket på portalen. Gatewayen visas som en ansluten enhet. Du kan välja den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.
6. Upprepa dessa steg på Azure Stack-distribution.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Skapa lokal nätverksgateway i Azure och Azure-stacken

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. Du namnge platsen som Azure eller Azure-stacken kan referera till den och sedan ange IP-adressen för den lokala VPN-enhet som du skapar en anslutning. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Om ditt lokala nätverk ändras eller om du behöver ändra den offentliga IP-adressen för VPN-enheten, kan du enkelt uppdatera värden senare.

1. I portalen, Välj **+ skapa en resurs för**.
2. I sökrutan anger **lokal nätverksgateway**, tryck på **RETUR** att söka. En lista med resultat returneras. Klicka på **lokal nätverksgateway**och välj den **skapa** för att öppna den **skapa lokal nätverksgateway** sidan.
3. På den **skapa lokala nätverket gateway sida**, ange värden för din lokala nätverksgateway enligt anvisningarna i vårt exempelvärden plus ytterligare värden som beskrivs nedan.
    - **IP-adressen**: Detta är den offentliga IP-adressen för VPN-enhet som du vill Azure eller Azure-Stack för att ansluta till. Ange en giltig offentlig IP-adress. IP-adressen får inte vara bakom en NAT och måste kunna nås av Azure. Om du inte har IP-adressen just nu kan du använda de värden som visas i exemplet. Du behöver dock gå tillbaka och ersätta platshållar-IP-adressen med den offentliga IP-adressen för din VPN-enhet. Azure kommer annars inte kunna ansluta.
    - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. Använda egna värden om du vill ansluta till din lokala plats inte de värden som visas i exemplet.
    - **Konfigurera inställningar för BGP**: använda endast när du konfigurerar BGP. Annars ska detta inte väljas.
    - **Prenumerationen**: Kontrollera att korrekt prenumeration visas.
    - **Resursgruppen**: Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
    - **Plats**: Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.
4. När du är klar med att ange värden väljer du den **skapa** längst ned på sidan för att skapa den lokala nätverksgatewayen.
5. Upprepa dessa steg på Azure Stack-distribution.

## <a name="configure-your-connection"></a>Konfigurera anslutningen

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du din VPN-enhet, kallas även en anslutning. När du konfigurerar anslutningen, behöver du följande:
    - En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
    - Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. För att hitta offentliga IP-adressen för din VPN-gateway med hjälp av Azure portal, gå till virtuella nätverksgatewayer och välj sedan namnet på din gateway.
Skapa VPN-anslutningen för plats-till-plats mellan din virtuella nätverksgateway och din lokala VPN-enhet.
1. I portalen, Välj **+ skapa en resurs för**.
2. I sökrutan anger **anslutningar**, tryck på **RETUR** att söka. En lista med resultat returneras. Klicka på **anslutningar**, klicka på knappen Skapa för att öppna den **skapa anslutningar** sidan.
3. På den **skapa anslutningar** konfigurera värden för anslutningen.
     - Grunderna:
        1. **Anslutningstypen**: Välj plats-till-plats (IPSec).
        2. **Resursgruppen**: (Välj resursgruppen test)
     - Inställningar för:
        1. **Virtuell nätverksgateway**: Välj den virtuella nätverksgatewayen som du skapade tidigare.
        2. **Lokal nätverksgateway**: Välj den lokala nätverksgateway som du skapade tidigare. 
        3. **Anslutningsnamn**: detta automatiskt fylla i med värden från två gateways. 
        4. **Delad nyckel**: det här värdet måste matcha värdet som du använder för din lokala lokala VPN-enhet. I det här exemplet använder vi abc123. Du kan (och bör) dock använda ett mer komplext värde. Det är viktigt att värdet du anger här är samma värde som du anger när du konfigurerade VPN-enheten.
    - De återstående värdena för **Prenumeration**, **Resursgrupp** och **Plats** är låsta.
4. Klicka på **OK** för att skapa din anslutning. Ser du skapar anslutningen flash på skärmen.
5. Du kan visa anslutningen i den ** anslutningar sida av den virtuella nätverksgatewayen. Status kommer att gå från **okänd** till **ansluta**, och sedan till **lyckades**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Cloud mönster i [designmönster](https://docs.microsoft.com/azure/architecture/patterns).
