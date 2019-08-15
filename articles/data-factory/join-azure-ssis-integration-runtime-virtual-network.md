---
title: Gå med i Azure-SSIS integration runtime till ett virtuellt nätverk | Microsoft Docs
description: Lär dig hur du ansluter Azure-SSIS integration runtime till ett virtuellt Azure-nätverk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 49422d73a63f1bcde267aac3a9b75e9977970cc9
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951938"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk
När du använder SQL Server Integration Services (SSIS) i Azure Data Factory (ADF) bör du ansluta Azure-SSIS Integration Runtime (IR) till ett virtuellt Azure-nätverk i följande scenarier: 

- Du vill ansluta till lokala data lager från SSIS-paket som körs på din Azure-SSIS IR utan att konfigurera/hantera en egen värd för IR som proxy. 

- Du är värd för SSIS Catalog-databasen (SSISDB) i Azure SQL Database med tjänst slut punkter för virtuella nätverk/hanterade instanser i ett virtuellt nätverk. 

Med ADF kan du ansluta Azure-SSIS IR till ett virtuellt nätverk som skapats via den klassiska distributions modellen eller distributions modellen för Azure Resource Manager. 

> [!IMPORTANT]
> Det klassiska virtuella nätverket är för närvarande inaktuellt, så Använd Azure Resource Manager virtuella nätverket i stället.  Om du redan använder det klassiska virtuella nätverket växlar du till att använda Azure Resource Manager virtuella nätverket så snart som möjligt.

## <a name="access-to-on-premises-data-stores"></a>Åtkomst till lokala data lager
Om dina SSIS-paket endast använder offentliga moln data lager behöver du inte ansluta Azure-SSIS IR till ett virtuellt nätverk. Om dina SSIS-paket har åtkomst till lokala data lager, kan du antingen ansluta Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket eller konfigurera/hantera en lokal IR as-proxy för Azure-SSIS IR, se [Konfigurera lokal IR som en Proxy för Azure-SSIS IR-](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) artikel. När du ansluter din Azure-SSIS IR till ett virtuellt nätverk finns det några viktiga saker att tänka på: 

- Om det inte finns något befintligt virtuellt nätverk som är anslutet till ditt lokala nätverk måste du först skapa ett [Azure Resource Manager virtuellt nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) eller ett [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för att kunna ansluta till Azure-SSIS integration Runtime. Konfigurera sedan en [VPN gateway-anslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) för plats-till-plats eller [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) -anslutning från det virtuella nätverket till ditt lokala nätverk. 

- Om det finns en befintlig Azure Resource Manager eller ett klassiskt virtuellt nätverk som är anslutet till ditt lokala nätverk på samma plats som din Azure-SSIS IR, kan du ansluta IR till det virtuella nätverket. 

- Om det finns ett befintligt klassiskt virtuellt nätverk som är anslutet till ditt lokala nätverk på en annan plats än Azure-SSIS IR kan du först skapa ett [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för Azure-SSIS IR att ansluta till. Konfigurera sedan en [klassisk virtuell nätverks anslutning som är klassisk](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) . Du kan också skapa ett [Azure Resource Manager virtuellt nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) för Azure-SSIS integration runtime för att ansluta. Konfigurera sedan ett [klassiskt-till-Azure Resource Manager virtuell nätverks](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning. 
 
- Om det finns ett befintligt Azure Resource Manager virtuellt nätverk som är anslutet till ditt lokala nätverk på en annan plats än Azure-SSIS IR, kan du först skapa ett [Azure Resource Manager virtuellt nätverk](../virtual-network/quick-create-portal.md##create-a-virtual-network) för Azure-SSIS IR för att ansluta. Konfigurera sedan en Azure Resource Manager-till-Azure Resource Manager virtuell nätverks anslutning. Du kan också skapa ett [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för Azure-SSIS IR för att ansluta. Konfigurera sedan en [Klassisk-till-Azure Resource Manager virtuell nätverks](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning. 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Vara värd för SSIS-katalog databasen i Azure SQL Database med tjänst slut punkter för virtuella nätverk/hanterad instans
Om SSIS-katalogen finns i Azure SQL Database med tjänst slut punkter för virtuella nätverk eller hanterade instanser i ett virtuellt nätverk kan du ansluta Azure-SSIS IR till: 

- Samma virtuella nätverk 
- Ett annat virtuellt nätverk som har en nätverks anslutning till en nätverks anslutning med det som används för den hanterade instansen 

Om du är värd för SSIS-katalogen i Azure SQL Database med tjänst slut punkter för virtuella nätverk, se till att du ansluter till Azure-SSIS IR till samma virtuella nätverk och undernät.

Om du ansluter Azure-SSIS IR till samma virtuella nätverk som den hanterade instansen kontrollerar du att Azure-SSIS IR finns i ett annat undernät än den hanterade instansen. Om du ansluter Azure-SSIS IR till ett annat virtuellt nätverk än den hanterade instansen, rekommenderar vi antingen virtuell nätverks-peering (som är begränsad till samma region) eller ett virtuellt nätverk till en virtuell nätverks anslutning. Se [ansluta ditt program till Azure SQL Database Hanterad instans](../sql-database/sql-database-managed-instance-connect-app.md).

I samtliga fall kan det virtuella nätverket bara distribueras via Azure Resource Manager distributions modell.

I följande avsnitt finns mer information. 

## <a name="requirements-for-virtual-network-configuration"></a>Krav för konfiguration av virtuellt nätverk
-   Se till att `Microsoft.Batch` är en registrerad Provider under prenumerationen på ditt virtuella nätverk under nät som är värd för Azure-SSIS IR. Om du använder det klassiska virtuella nätverket kan du också `MicrosoftAzureBatch` ansluta till den klassiska rollen virtuell dator deltagare för det virtuella nätverket. 

-   Kontrol lera att du har de behörigheter som krävs. Se de [behörigheter som krävs](#perms).

-   Välj rätt undernät som värd för Azure-SSIS IR. Se [Välj under nätet](#subnet). 

-   Om du använder en egen DNS-server (Domain Name Services) på det virtuella nätverket, se [Domain Name Services-server](#dns_server). 

-   Om du använder en nätverks säkerhets grupp (NSG) i under nätet, se [nätverks säkerhets grupp](#nsg) 

-   Om du använder Azure Express Route eller konfigurerar användardefinierad väg (UDR) kan du läsa [Använd Azure ExpressRoute eller användardefinierad väg](#route). 

-   Kontrol lera att resurs gruppen för det virtuella nätverket kan skapa och ta bort vissa Azure-nätverks resurser. Se [krav för resurs grupp](#resource-group). 

-   Om du anpassar din Azure-SSIS IR enligt beskrivningen i den [anpassade installationen för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) -artikel, allokeras dina Azure-SSIS IR-noder av privata IP-adresser från ett fördefinierat område med 172.16.0.0-172.31.255.255, så se till att den privata IP-adressen adress intervallen för dina virtuella/lokala nätverk kolliderar inte med det här intervallet.

Här är ett diagram som visar de anslutningar som krävs för din Azure-SSIS IR:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Behörigheter som krävs

Den användare som skapar Azure-SSIS-Integration Runtime måste ha följande behörigheter:

- Om du ansluter din SSIS-IR till ett Azure Resource Manager virtuellt nätverk har du två alternativ:

  - Använd den inbyggda rollen *nätverks deltagare* . Den här rollen ingår i _Microsoft. Network/\*_  permission, som har en mycket större omfattning än vad som behövs.

  - Skapa en anpassad roll som endast innehåller nödvändig _Microsoft. Network/virtualNetworks//Join/Action\*-_ behörighet. 

- Om du ansluter din SSIS-IR till ett klassiskt virtuellt nätverk, rekommenderar vi att du använder den inbyggda *klassiska rollen virtuell dator deltagare* . Annars måste du definiera en anpassad roll som innehåller behörighet att ansluta till det virtuella nätverket.

### <a name="subnet"></a>Välj under nätet
-   Välj inte GatewaySubnet för att distribuera en Azure-SSIS Integration Runtime, eftersom den är dedikerad för virtuella nätverks-gatewayer. 

-   Kontrol lera att det undernät du väljer har tillräckligt med ledigt adress utrymme för att Azure-SSIS IR ska kunna användas. Lämna minst 2 * IR-nodnummer i tillgängliga IP-adresser. Azure reserverar vissa IP-adresser i varje undernät och de här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Använd inte ett undernät som uteslutande används av andra Azure-tjänster (till exempel SQL Database Hanterad instans, App Service osv.). 

### <a name="dns_server"></a>Domain Name Services-server 
Om du behöver använda din egen DNS-server (Domain Name Services) i ett virtuellt nätverk som är anslutet av din Azure-SSIS integration runtime, kontrollerar du att den kan matcha globala Azure-värdnamn (till exempel ett Azure Storage `<your storage account>.blob.core.windows.net`-BLOB-namn). 

Följande steg rekommenderas: 

-   Konfigurera anpassad DNS att vidarebefordra begär anden till Azure DNS. Du kan vidarebefordra olösta DNS-poster till IP-adressen för Azures rekursiva matchare (168.63.129.16) på din egen DNS-server. 

-   Konfigurera den anpassade DNS-servern som primär och Azure DNS som sekundär för det virtuella nätverket. Registrera IP-adressen för Azures rekursiva matchare (168.63.129.16) som en sekundär DNS-server om din egen DNS-server inte är tillgänglig. 

Mer information finns i [namn matchning som använder din egen DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Nätverks säkerhets grupp
Om du behöver implementera en nätverks säkerhets grupp (NSG) för under nätet som används av din Azure-SSIS integration runtime kan du tillåta inkommande/utgående trafik via följande portar: 

| Direction | Transport protokoll | Source | Käll port intervall | Mål | Mål Port intervall | Kommentar |
|---|---|---|---|---|---|---|
| Inkommande | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (om du ansluter IR till ett Azure Resource Manager virtuellt nätverk) <br/><br/>10100, 20100, 30100 (om du ansluter IR till ett klassiskt virtuellt nätverk)| Den Data Factory tjänsten använder dessa portar för att kommunicera med noderna i Azure-SSIS integration runtime i det virtuella nätverket. <br/><br/> Oavsett om du skapar en NSG på under näts nivå eller inte, konfigurerar Data Factory alltid en NSG på nivån för nätverkskorten som är anslutna till de virtuella datorer som är värdar för Azure-SSIS IR. Det är bara inkommande trafik från Data Factory IP-adresser på de angivna portarna som tillåts av NSG på NÄTVERKSKORTs nivå. Även om du öppnar dessa portar till Internet trafik på under näts nivån blockeras trafik från IP-adresser som inte Data Factory IP-adresser på NÄTVERKSKORTs nivån. |
| Utgående | TCP | VirtualNetwork | * | AzureCloud<br/>(eller större omfattning som Internet) | 443 | Noderna i din Azure-SSIS integration runtime i det virtuella nätverket använder den här porten för att få åtkomst till Azure-tjänster, till exempel Azure Storage och Azure Event Hubs. |
| Utgående | TCP | VirtualNetwork | * | Internet | 80 | Noderna i din Azure-SSIS integration runtime i det virtuella nätverket använder den här porten för att hämta listan över återkallade certifikat från Internet. |
| Utgående | TCP | VirtualNetwork | * | SQL<br/>(eller större omfattning som Internet) | 1433, 11000-11999 | Noderna i din Azure-SSIS integration runtime i det virtuella nätverket använder dessa portar för att få åtkomst till SSISDB som finns på Azure SQL Database-servern. Om din Azure SQL Database Server anslutnings princip är inställd på **proxy** i ställetför omdirigering krävs bara port 1433. Den här utgående säkerhets regeln kan inte tillämpas på SSISDB som finns i den hanterade instansen i det virtuella nätverket. |
||||||||

### <a name="route"></a>Använd Azure-ExpressRoute eller användardefinierad väg
Du kan ansluta en [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -krets till din virtuella nätverks infrastruktur för att utöka ditt lokala nätverk till Azure. 

En vanlig konfiguration är att använda Tvingad tunnel trafik (annonsera en BGP-väg, 0.0.0.0/0 till det virtuella nätverket) som framtvingar utgående Internet trafik från det virtuella nätverks flödet till en lokal nätverks enhet för inspektion och loggning. Detta trafikflöde bryter anslutningen mellan Azure-SSIS IR i det virtuella nätverket med beroende Azure Data Factory tjänster. Lösningen är att definiera en (eller flera) [användardefinierade vägar (UDR)](../virtual-network/virtual-networks-udr-overview.md) på det undernät som innehåller Azure-SSIS IR. En UDR definierar de undernät-/regionsspecifika vägar som följer i stället för BGP-vägen. 

Du kan också definiera användardefinierade vägar (UDR) för att tvinga utgående Internet trafik från under nätet som är värd för Azure-SSIS IR till ett annat undernät, som är värd för en Virtual Network-apparat som en brand vägg eller en DMZ-värd för inspektion och loggning. 

I båda fallen ska du använda en router med värdet 0.0.0.0/0 med nästa hopp typ som **Internet** på det undernät som är värd för Azure-SSIS IR, så att kommunikationen mellan den Data Factory tjänsten och Azure-SSIS är IR kan lyckas. 

![Lägg till en rutt](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Om du är orolig för att förlora möjligheten att kontrol lera utgående Internet trafik från det under nätet kan du också lägga till en NSG-regel i under nätet för att begränsa utgående mål till [Azure Data Center IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653). 

Se [det här PowerShell-skriptet](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) för ett exempel. Du måste köra skriptet varje vecka för att se till att Azure Data Center-IP-adress listan är aktuell. 

### <a name="resource-group"></a>Krav för resurs grupp
-   Azure-SSIS IR måste skapa vissa nätverks resurser i samma resurs grupp som det virtuella nätverket. Dessa resurser omfattar följande:
    -   En Azure Load Balancer med namnet  *\<GUID >-azurebatch-cloudserviceloadbalancer*.
    -   En offentlig Azure-IP-adress med namnet  *\<GUID >-azurebatch-cloudservicepublicip*.
    -   En nätverks säkerhets grupp med namnet  *\<GUID >-azurebatch-cloudservicenetworksecuritygroup*. 

-   Se till att du inte har något resurs lås på den resurs grupp eller prenumeration som det virtuella nätverket tillhör. Om du konfigurerar antingen ett skrivskyddat lås eller ett borttagnings lås, kan det hända att det inte går att komma igång med IR eller stoppa. 

-   Kontrol lera att du inte har någon Azure-princip som förhindrar att följande resurser skapas under den resurs grupp eller prenumeration som det virtuella nätverket tillhör: 
    -   Microsoft. Network/belastningsutjämnare 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft. Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory UI)
Det här avsnittet visar hur du ansluter en befintlig Azure-SSIS runtime till ett virtuellt nätverk (klassisk eller Azure Resource Manager) med hjälp av Azure Portal och Data Factory användar gränssnittet. Först måste du konfigurera det virtuella nätverket på rätt sätt innan du ansluter till Azure-SSIS IR. Gå igenom något av följande två avsnitt baserat på typen av ditt virtuella nätverk (klassisk eller Azure Resource Manager). Fortsätt sedan med det tredje avsnittet för att ansluta Azure-SSIS IR till det virtuella nätverket. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Använd portalen för att konfigurera ett Azure Resource Manager virtuellt nätverk
Du måste konfigurera ett virtuellt nätverk innan du kan ansluta en Azure-SSIS IR till den. 

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöds Data Factory-gränssnittet bara i de webbläsarna. 

1. Logga in på [Azure Portal](https://portal.azure.com). 

1. Välj **fler tjänster**. Filtrera och välj **virtuella nätverk**. 

1. Filtrera efter och välj ditt virtuella nätverk i listan. 

1. På sidan **virtuellt nätverk** väljer du **Egenskaper**. 

1. Välj kopierings knappen för **resurs-ID** för att kopiera resurs-ID för det virtuella nätverket till Urklipp. Spara ID: t från Urklipp i OneNote eller en fil. 

1. Välj **undernät** på den vänstra menyn. Se till att antalet **tillgängliga adresser** är större än noderna i din Azure-SSIS integration Runtime. 

1. Verifiera att Azure Batch-providern är registrerad i Azure-prenumerationen som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch konto i din prenumeration registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory Portal registreras Azure Batch leverantören automatiskt åt dig.) 

   a. I Azure Portal väljer du **prenumerationer** på den vänstra menyn. 

   b. Välj din prenumeration. 

   c. Välj **resurs leverantörer** till vänster och bekräfta att **Microsoft. batch** är en registrerad Provider. 

   ![Bekräftelse av status registrerad](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft. batch** i listan, för att registrera den, [skapar du ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Använd portalen för att konfigurera ett klassiskt virtuellt nätverk
Du måste konfigurera ett virtuellt nätverk innan du kan ansluta en Azure-SSIS IR till den. 

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöds inte Data Factory-gränssnittet i dessa webbläsare. 

1. Logga in på [Azure Portal](https://portal.azure.com). 

1. Välj **fler tjänster**. Filtrera och välj **virtuella nätverk (klassisk)** . 

1. Filtrera efter och välj ditt virtuella nätverk i listan. 

1. På sidan **virtuellt nätverk (klassisk)** väljer du **Egenskaper**. 

   ![Klassiskt virtuellt nätverks resurs-ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Välj kopierings knappen för **resurs-ID** för att kopiera resurs-ID för det klassiska nätverket till Urklipp. Spara ID: t från Urklipp i OneNote eller en fil. 

1. Välj **undernät** på den vänstra menyn. Se till att antalet **tillgängliga adresser** är större än noderna i din Azure-SSIS integration Runtime. 

   ![Antal tillgängliga adresser i det virtuella nätverket](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Anslut **MicrosoftAzureBatch** till den **klassiska rollen virtuell dator deltagare** för det virtuella nätverket. 

    a. Välj **åtkomst kontroll (IAM)** på den vänstra menyn och välj fliken **roll tilldelningar** . 

    ![Knapparna "åtkomst kontroll" och "Lägg till"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Välj **Lägg till rolltilldelning**.

    c. På sidan **Lägg till roll tilldelning** väljer du **klassisk virtuell dator deltagare** för **roll**. Klistra in **ddbf3205-c6bd-46ae-8127-60eb93363864** i rutan **Välj** och välj sedan **Microsoft Azure Batch** i listan med Sök resultat. 

    ![Sök resultat på sidan Lägg till roll tilldelning](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Spara inställningarna och Stäng sidan genom att välja **Spara** . 

    ![Spara åtkomst inställningar](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Bekräfta att du ser **Microsoft Azure Batch** i listan över bidrags givare. 

    ![Bekräfta Azure Batch åtkomst](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verifiera att Azure Batch-providern är registrerad i Azure-prenumerationen som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch konto i din prenumeration registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory Portal registreras Azure Batch leverantören automatiskt åt dig.) 

   a. I Azure Portal väljer du **prenumerationer** på den vänstra menyn. 

   b. Välj din prenumeration. 

   c. Välj **resurs leverantörer** till vänster och bekräfta att **Microsoft. batch** är en registrerad Provider. 

   ![Bekräftelse av status registrerad](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft. batch** i listan, för att registrera den, [skapar du ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Anslut Azure-SSIS IR till ett virtuellt nätverk
1. Starta Microsoft Edge eller Google Chrome. För närvarande stöds Data Factory-gränssnittet bara i de webbläsarna. 

1. I [Azure Portal](https://portal.azure.com)väljer du **data fabriker** på den vänstra menyn. Om du inte ser **data fabriker** på menyn väljer du **fler tjänster**och välj **data fabriker** i avsnittet **information + analys** . 

   ![Lista över data fabriker](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Välj din data fabrik med Azure-SSIS integration runtime i listan. Du ser start sidan för din data fabrik. Välj panelen **författare & distribuera** . Du ser Data Factory användar gränssnitt på en separat flik. 

   ![Datafabrikens startsida](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. I Data Factory användar gränssnitt växlar du till fliken **Redigera** , väljer **anslutningar**och växlar till fliken integrerings **körningar** . 

   ![Fliken "integrerings körningar"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Om din Azure-SSIS IR körs, i listan integration runtime, väljer du **stopp** -knappen i kolumnen **åtgärder** för din Azure-SSIS IR. Du kan inte redigera en IR förrän du stoppar den. 

   ![Stoppa IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. I listan integration runtime väljer du knappen **Redigera** i kolumnen **åtgärder** för din Azure-SSIS IR. 

   ![Redigera integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Gå igenom sidorna **allmänna inställningar** och **SQL-inställningar** på panelen **integration runtime installation** genom att klicka på knappen **Nästa** . 

1. På sidan **Avancerade inställningar** utför du följande åtgärder: 

   a. Markera kryss rutan **Markera ett VNet...** . 

   b. För **prenumeration**väljer du din Azure-prenumeration under vilken du kan i sin tur välja ett befintligt virtuellt nätverk. 
  
   c. För **VNet-namn**väljer du ditt virtuella nätverk. 

   d. För **under näts namn**väljer du under nätet i det virtuella nätverket. 

   e. Om du även vill konfigurera/hantera en egen värd-IR som proxy för din Azure-SSIS IR, markerar du kryss rutan konfigurera egen värd.. **.** och läser [Konfigurera IR som proxy för Azure-SSIS IR-](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) artikel.

   f. Klicka på verifierings knappen för **virtuellt nätverk** och om det är klart klickar du på knappen **Nästa** . 

   ![Avancerade inställningar för IR-installation](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. På sidan **Sammanfattning** granskar du alla inställningar för Azure-SSIS IR och klickar på knappen **Uppdatera** .

1. Nu kan du starta Azure-SSIS IR genom att klicka på **Start** -knappen i kolumnen **åtgärder** för din Azure-SSIS IR. Det tar cirka 20 till 30 minuter att starta Azure-SSIS IR som ansluter till ett virtuellt nätverk. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk
Du måste konfigurera ett virtuellt nätverk innan du kan ansluta till Azure-SSIS IR. Lägg till följande skript för att automatiskt konfigurera behörigheter/inställningar för virtuella nätverk för Azure-SSIS integration runtime för att ansluta till det virtuella nätverket:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Skapa en Azure-SSIS IR och Anslut den till ett virtuellt nätverk
Du kan skapa en Azure-SSIS IR och ansluta den till ett virtuellt nätverk på samma tid. Fullständiga skript och instruktioner finns i [skapa en Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Anslut en befintlig Azure-SSIS IR till ett virtuellt nätverk
Skriptet i artikeln [skapa en Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) visar hur du skapar en Azure-SSIS IR och ansluter den till ett virtuellt nätverk i samma skript. Om du har en befintlig Azure-SSIS IR utför du följande steg för att ansluta den till det virtuella nätverket: 
1. Stoppa Azure-SSIS IR. 
1. Konfigurera Azure-SSIS IR för att ansluta till det virtuella nätverket. 
1. Starta Azure-SSIS IR. 

### <a name="define-the-variables"></a>Definiera variablerna
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Stoppa Azure-SSIS IR
Stoppa Azure-SSIS integration runtime innan du kan ansluta den till ett virtuellt nätverk. Detta kommando frigör alla noder och avslutar faktureringen:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurera virtuella nätverks inställningar för Azure-SSIS IR för anslutning
```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Konfigurera Azure-SSIS IR
Om du vill konfigurera Azure-SSIS integration runtime för att ansluta till det virtuella nätverket `Set-AzDataFactoryV2IntegrationRuntime` kör du kommandot: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Starta Azure-SSIS IR
Starta Azure-SSIS integration runtime genom att köra följande kommando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Det här kommandot tar 20 till 30 minuter att slutföra.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS integration runtime finns i följande avsnitt: 
- [Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller konceptuell information om integrerings körningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här självstudien innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR. Den använder Azure SQL Database som värd för SSIS-katalogen. 
- [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln är utökad i självstudien och innehåller instruktioner om hur du använder Azure SQL Database med tjänst slut punkter för virtuella nätverk/hanterade instanser i ett virtuellt nätverk som värd för SSIS-katalogen och ansluter till Azure-SSIS IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om din Azure-SSIS IR och ger status beskrivningar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort Azure-SSIS IR. Det visar också hur du skalar upp Azure-SSIS IR genom att lägga till noder.
