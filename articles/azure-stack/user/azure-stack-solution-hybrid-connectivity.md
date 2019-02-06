---
title: Konfigurera hybrid cloud anslutningen till Azure och Azure Stack | Microsoft Docs
description: Lär dig hur du konfigurerar molnet för hybridanslutning med Azure och Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2530f6f59ef458d5a7c2de5850d8fab322798ba3
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752670"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Självstudie: Konfigurera molnet hybridanslutning med Azure och Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan komma åt resurser med säkerhet i globala Azure och Azure Stack med hjälp av mönstret hybrid anslutning.

I den här självstudien skapar du en exempel-miljö för att:

> [!div class="checklist"]
> - Behåll data lokalt för att uppfylla sekretess- eller myndighetskrav, men har åtkomst till globala Azure-resurser.
> - Ha ett äldre system när du använder molnet skalade appdistribution och resurser i globala Azure.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack är en utökning av Azure. Azure Stack får du flexibilitet och utvecklingsmöjligheterna med molnberäkning i din lokala miljö och aktivera det enda hybridmolnet som hjälper dig att skapa och distribuera hybridappar var som helst.  
> 
> Faktabladet [designöverväganden för Hybridtillämpningar](https://aka.ms/hybrid-cloud-applications-pillars) granskar grundpelare för programkvalitet (placering, skalbarhet, tillgänglighet, återhämtning, hantering och säkerhet) för att utforma, distribuera och driva hybrid program. Designöverväganden kan optimera hybrid programdesign, minimerar utmaningar i produktionsmiljöer.


## <a name="prerequisites"></a>Förutsättningar

Några komponenter krävs för att skapa en hybriddistribution för anslutningen. Några av de här komponenterna tar tid att förbereda, så måste du planera på lämpligt sätt.

**Azure Stack**

En Azure-Partner för OEM/maskinvara kan distribuera ett Azure Stack för produktion och alla användare kan distribuera ett Azure Stack Development Kit (ASDK).

**Azure Stack-komponenter**

En Azure Stack-Operator måste distribuera App Service, skapa planer och erbjudanden, skapa en klientprenumeration och Lägg till Windows Server 2016-avbildning. Om du redan har några av de här komponenterna kan du kontrollera att de uppfyller kraven innan du påbörjar den här självstudien.

Den här självstudien förutsätter att du har några grundläggande kunskaper om Azure och Azure Stack. Mer information innan du påbörjar självstudiekursen, finns i följande artiklar:

 - [Introduktion till Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Viktiga begrepp för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Om du inte har en Azure-prenumeration skapar du ett  [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)  innan du börjar.
 - Skapa en [Web App](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) i Azure. Anteckna Webbappens URL, eftersom du behöver i självstudien.

### <a name="azure-stack"></a>Azure Stack

 - Använda din produktion Azure Stack eller distribuera Azure Stack Development Kit från https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Distribuera ASDK kan ta upp till 7 timmar, så planera på lämpligt sätt.

 - Distribuera [Apptjänst](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-tjänster till Azure Stack.
 - [Skapa planer och erbjudanden](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) i Azure Stack-miljön.
 - [Skapa klientprenumeration](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) i Azure Stack-miljön.

### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du uppfyller följande kriterier innan du börjar konfigurera hybrid molnanslutning:

 - Du behöver en extern offentlig IPv4-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
 - Alla resurser som distribueras i samma region/plats.

#### <a name="tutorial-example-values"></a>Kursexemplet värden

I exemplen i den här självstudien används följande värden. Du kan använda dessa värden för att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen. Mer information om VPN Gateway inställningar i allmänhet finns i [om VPN Gateway-inställningar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifikationer för anslutning:

 - **VPN-typ**: Routningsbaserad
 - **Anslutningstyp**: Plats-till-plats (IPsec)
 - **Gateway-typ**: VPN
 - **Azure anslutningsnamn**: Azure-Gateway-AzureStack-S2SGateway (portalen anges automatiskt – det här värdet)
 - **Azure Stack-anslutningsnamn**: AzureStack-Gateway-Azure-S2SGateway (portalen anges automatiskt – det här värdet)
 - **Delad nyckel**: Alla kompatibla med VPN-maskinvara med matchande värden på båda sidorna av anslutningen
 - **Prenumeration**: Alla önskade prenumerationer
 - **Resursgrupp**: Test-Infra

Nätverk och undernät IP-adresser:

| Azure/Azure Stack-anslutning | Namn | Undernät | IP-adress |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack-vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Virtuell nätverksgateway i Azure | Azure-Gateway |  |  |
| Virtuell nätverksgateway i Azure Stack | AzureStack-Gateway |  |  |
| Azure offentlig IP-adress | Azure-GatewayPublicIP |  | Identifiera när du skapar |
| Azure Stack offentlig IP-adress | AzureStack-GatewayPublicIP |  | Identifiera när du skapar |
| Azure Local Network Gateway | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Värdet för Azure Stack-offentlig IP-adress |
| Lokal nätverksgateway i Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Värdet för Azure offentlig IP-adress |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Skapa ett virtuellt nätverk i globala Azure och Azure Stack

Använd följande steg för att skapa ett virtuellt nätverk med hjälp av portalen. Du kan använda dessa [exempelvärden](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) om du använder den här artikeln som en vägledning. Men om du använder den här artikeln för att konfigurera en produktionsmiljö, ersätter du exempelinställningarna med dina egna värden.

> [!IMPORTANT]
> Du måste se till att det inte finns en överlappning av IP-adresser i Azure eller Azure Stack adressutrymmen för virtuellt nätverk.

Skapa ett virtuellt nätverk i Azure:

1. Använda webbläsaren för att ansluta till den [Azure-portalen](http://portal.azure.com/) och logga in med ditt Azure-konto.
2. Välj **skapa en resurs**. I den **Sök på marketplace** anger `virtual network`''. Hitta **virtuellt nätverk** i resultatlistan och välj sedan **virtuellt nätverk**.
3. Från den **Välj en distributionsmodell** väljer **Resource Manager**, och välj sedan **skapa**.
4. På **skapa virtuellt nätverk**, konfigurera VNet-inställningarna. Obligatoriska fältnamnen på avbildningarna föregås en röd asterisk.  När du anger ett giltigt värde ändras asterisken till en grön bockmarkering.

Skapa ett virtuellt nätverk i Azure Stack:

* Upprepa föregående steg (1-4) med hjälp av Azure Stack **innehavarportalen**.

## <a name="add-a-gateway-subnet"></a>Lägga till ett gatewayundernät

Innan du ansluter ditt virtuella nätverk till en gateway, måste du skapa gateway-undernätet för det virtuella nätverket som du vill ansluta till. Gatewaytjänsterna använder de IP-adresser som du anger i gateway-undernätet.

I den [Azure-portalen](http://portal.azure.com/), navigera till Resource Manager-nätverk där du vill skapa en virtuell nätverksgateway.

1. Välj det virtuella nätverket kan öppna den **virtuellt nätverk** sidan.
2. I **inställningar**väljer **undernät**.
3. På den **undernät** väljer **+ Gateway-undernät** att öppna den **Lägg till undernät** sidan.

    ![Lägg till gateway-undernät](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. Den **namn** för undernätet fylls automatiskt i med värdet ”GatewaySubnet”. Det här värdet krävs för Azure för att kunna identifiera undernätet som gateway-undernätet.
5. Ändra den **adressintervall** värden som motsvarar dina konfigurationskrav och välj sedan **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Skapa en virtuell nätverksgateway i Azure och Azure Stack

Använd följande steg för att skapa en virtuell nätverksgateway i Azure.

1. Till vänster på portalsidan väljer **+**  och ange ”virtuell nätverksgateway” i sökfältet.
2. I **resultat**väljer **virtuell nätverksgateway**.
3. I **virtuell nätverksgateway**väljer **skapa** att öppna den **Skapa virtuell nätverksgateway** sidan.
4. På **Skapa virtuell nätverksgateway**, anger du värdena för din nätverks-gateway enligt **kursexemplet värden**, och anges följande värden:

    - **SKU**: grundläggande
    - **Virtual Network**: Välj det virtuella nätverket som du skapade tidigare. Gateway-undernätet som du skapade väljs automatiskt.
    - **Första IP-konfiguration**:  Det här är den offentliga IP-Adressen för din Gateway.
        - Välj **skapa IP-gatewaykonfiguration**, vilket leder till den **Välj offentlig IP-adress** sidan.
        - Välj **+ Skapa nytt** att öppna den **skapa offentlig IP-adress** sidan.
        - Ange en **namn** för din offentliga IP-adress. Låt SKU: N som **grundläggande**, och välj sedan **OK** att spara dina ändringar.

       > [!Note]
       > VPN-Gateway stöder för närvarande endast dynamiska offentliga IP-adressallokering. Det betyder dock inte att IP-adressen ändras när den är tilldelad till din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Vid storleksändring, återställning eller annat internt Underhåll/uppgraderingar till din VPN-gateway ändra inte IP-adressen.

4. Kontrollera gatewayinställningarna.
5. Välj **skapa** att skapa VPN-gatewayen. Gateway-inställningarna verifieras och ikonen ”distribuerar virtuell nätverksgateway” visas på instrumentpanelen.

   >[!Note]
   >Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

    När gatewayen har skapats kan du se IP-adress som tilldelats genom att titta på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet. Välj enheten om du vill se mer information om gatewayen.

6. Upprepa föregående steg (1-5) på Azure Stack-distributioner.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Skapa lokal nätverksgateway i Azure och Azure Stack

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. Du ger platsen ett namn som Azure eller Azure Stack kan referera till och ange sedan:

- IP-adressen för den lokala VPN-enhet som du skapar en anslutning för.
- IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket.

  >[!Note]
  >Om ditt lokala nätverk ändras eller om du behöver ändra offentliga IP-adress för VPN-enheten, kan du enkelt uppdatera dessa värden senare.

1. I portalen, väljer **+ skapa en resurs**.
2. I sökrutan anger **lokal nätverksgateway**och välj sedan **RETUR** att söka. En lista med resultat returneras.
3. Välj **lokal nätverksgateway**och välj sedan **skapa** att öppna den **skapa lokal nätverksgateway** sidan.
4. På **skapa lokal nätverksgateway**, anger du värdena för din lokala nätverksgateway med hjälp av vår **kursexemplet värden**. Inkludera följande ytterligare värden.

    - **IP-adress**: Det här är den offentliga IP-adressen för VPN-enheten som du vill att Azure eller Azure Stack för att ansluta till. Ange en giltig offentlig IP-adress som inte ligger bakom en NAT-enhet så att Azure kan nå adressen. Om du inte har IP-adressen just nu, du kan använda ett värde i exemplet som en platshållare, men du måste gå tillbaka och ersätter platshållarna med offentliga IP-adressen för VPN-enheten. Azure kan inte ansluta till enheten förrän du anger en giltig adress.
    - **Adressutrymme**: Det här är adressintervallet för det nätverk som det lokala nätverket representerar. Du kan lägga till flera adressintervall. Se till att intervallen du anger inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. Använd dina egna värden om du vill ansluta till den lokala platsen, inte en exempelvärde.
    - **Konfigurera BGP-inställningar**: Använd endast vid konfigurering av BGP. Annars ska detta inte väljas.
    - **Prenumeration**: Kontrollera att korrekt prenumeration visas.
    - **Resursgrupp**: Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
    - **Plats**: Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du inte blir ombedd att göra detta.
5. När du har angett värdena som krävs, Välj **skapa** att skapa den lokala nätverksgatewayen.
6. Upprepa dessa steg (1-5) på Azure Stack-distributioner.

## <a name="configure-your-connection"></a>Konfigurera anslutningen

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. VPN-enhet som du konfigurerar kallas för en anslutning. Om du vill konfigurera anslutningen behöver du:

- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
- Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Navigera till virtuella nätverksgatewayer för att hitta offentliga IP-adressen för din VPN-gateway med Azure portal och välj namnet på din gateway.

Använd följande steg för att skapa en plats-till-plats-VPN-anslutning mellan din virtuella nätverksgateway och din lokala VPN-enhet.

1. I Azure-portalen väljer du **+ skapa en resurs**.
2. Sök efter **anslutningar**.
3. I **resultat**väljer **anslutningar**.
4. På **anslutning**väljer **skapa**.
5. På **Skapa anslutning**, konfigurera följande inställningar:

    - **Anslutningstyp**: Välj plats-till-plats (IPSec).
    - **Resursgrupp**: Välj en resursgrupp för testning.
    - **Virtuell nätverksgateway**: Välj den virtuella nätverksgatewayen som du skapade.
    - **Lokal nätverksgateway**: Välj den lokala nätverksgateway som du skapade.
    - **Anslutningsnamn**: Det fylls i automatiskt med värden från två gateways.
    - **Delad nyckel**: Det här värdet måste matcha det värde som du använder för din lokala VPN-enhet. Kursexemplet använder 'abc123', men du kan (och bör) använda ett mer komplext. Viktigt är att det här värdet måste vara samma värde som du anger när du konfigurerar VPN-enheten.
    - Värdena för **prenumeration**, **resursgrupp**, och **plats** korrigeras.

6. Välj **OK** att skapa anslutningen.

Du kan se anslutningen i den **anslutningar** för den virtuella nätverksgatewayen. Statusen kommer att gå från *okänd* till *ansluter*, och sedan till *lyckades*.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure molnmönster i [Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns).
