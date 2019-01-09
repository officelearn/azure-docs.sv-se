---
title: Ansluta Azure-SSIS integration runtime till ett virtuellt nätverk | Microsoft Docs
description: Lär dig mer om att ansluta Azure-SSIS integration runtime till ett Azure-nätverk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/08/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8693c5e255020e30c2e8ed52a3199712089e4503
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119092"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk
Anslut till din Azure-SSIS integration runtime (IR) till en Azure-nätverk i följande scenarier: 

- Du vill ansluta till lokala datalager från SSIS-paket som körs på Azure-SSIS Integration Runtime. 

- Du är värd för katalogdatabasen SQL Server Integration Services (SSIS) i Azure SQL Database med virtuellt nätverk tjänstens slutpunkter/hanterad instans. 

 Azure Data Factory kan du ansluta Azure-SSIS integration runtime till ett virtuellt nätverk som skapats via den klassiska distributionsmodellen eller Azure Resource Manager-distributionsmodellen. 

> [!IMPORTANT]
> Det klassiska virtuella nätverket används för närvarande längre, så Använd Azure Resource Manager-nätverk i stället.  Växla för att använda Azure Resource Manager-nätverk så snart som möjligt om du redan använder det klassiska virtuella nätverket.

## <a name="access-to-on-premises-data-stores"></a>Åtkomst till lokala databaser
Om SSIS-paket åtkomst till datalager som enda offentliga molnet, behöver du inte ansluta till Azure-SSIS IR till ett virtuellt nätverk. Om SSIS-paket åtkomst till lokala datalager, måste du ansluta Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket. 

Här följer några viktiga saker att Observera: 

- Om det ingen finns virtuellt nätverk som är ansluten till ditt lokala nätverk, först skapa en [Azure Resource Manager-nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) eller en [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering Runtime kan anslutas till. Konfigurera sedan en plats-till-plats [VPN-gatewayanslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) eller [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) anslutning från det virtuella nätverket till ditt lokala nätverk. 

- Om det finns en befintlig Azure Resource Manager eller klassiska virtuella nätverk som anslutits till ditt lokala nätverk på samma plats som din Azure-SSIS IR, kan du ansluta IR till det virtuella nätverket. 

- Om det finns ett befintligt klassiska virtuella nätverk som är anslutna till ditt lokala nätverk på en annan plats från din Azure-SSIS IR måste du först skapa en [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS IR att ansluta till. Konfigurera sedan en [klassisk till klassiskt virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) anslutning. Eller så kan du skapa en [Azure Resource Manager-nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) för din Azure-SSIS integration runtime kan anslutas till. Konfigurera en [klassisk till Azure Resource Manager-nätverk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning. 
 
- Om det finns en befintlig Azure Resource Manager virtuellt nätverk är anslutna till ditt lokala nätverk på en annan plats från din Azure-SSIS IR måste du först skapa en [Azure Resource Manager-nätverk](../virtual-network/quick-create-portal.md##create-a-virtual-network) för din Azure-SSIS IR för att ansluta till. Konfigurera en virtuell nätverksanslutning för Azure Resource Manager till Azure Resource Manager. Du kan också skapa en [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS IR att ansluta till. Konfigurera sedan en [klassisk till Azure Resource Manager-nätverk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning. 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Vara värd för SSIS-katalogdatabasen i Azure SQL Database med virtuellt nätverk tjänstens slutpunkter/hanterad instans
Om SSIS-katalogen finns i Azure SQL Database med virtuella nätverksslutpunkter eller hanterad instans, kan du ansluta till din Azure-SSIS IR till: 

- Samma virtuella nätverk 
- Ett annat virtuellt nätverk som har en nätverk-till-network-anslutning med det som används för den hanterade instansen 

Om du vara värd för SSIS-katalog i Azure SQL Database med tjänstslutpunkter i virtuella nätverk, se till att du ansluter din Azure-SSIS IR till samma virtuella nätverk och undernät.

Om du sammanfogar din Azure-SSIS IR till samma virtuella nätverk som den hanterade instansen ska du kontrollera att Azure-SSIS IR är i ett annat undernät än den hanterade instansen. Om du ansluter din Azure-SSIS IR till ett annat virtuellt nätverk än den hanterade instansen rekommenderar vi virtuell nätverkspeering (som är begränsad till samma region) eller ett virtuellt nätverk för virtuell nätverksanslutning. Se [Anslut ditt program till Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).

I samtliga fall kan du bara distribuera det virtuella nätverket via Azure Resource Manager-distributionsmodellen.

I följande avsnitt innehåller mer information. 

## <a name="requirements-for-virtual-network-configuration"></a>Krav för konfiguration av virtuellt nätverk
-   Se till att `Microsoft.Batch` är en registrerad provider i prenumeration för ditt virtuella nätverksundernät som är värd för Azure-SSIS IR. Om du använder klassiskt virtuellt nätverk kan även ansluta `MicrosoftAzureBatch` till rollen klassisk virtuell Datordeltagare för det virtuella nätverket. 

-   Kontrollera att du har behörigheterna som krävs. Se [behörigheter som krävs för](#perms).

-   Välj rätt undernät som värd för Azure-SSIS IR. Se [Välj undernätet](#subnet). 

-   Om du använder egna Domain Name Services (DNS)-server i det virtuella nätverket, se [Domain Name Services server](#dns_server). 

-   Om du använder en Nätverkssäkerhetsgrupp (NSG) på undernätet, se [nätverkssäkerhetsgrupp](#nsg) 

-   Om du använder Azure Express Route eller konfigurera användardefinierad väg (UDR), se [Använd Azure ExpressRoute eller användaren användardefinierade vägen](#route). 

-   Kontrollera att resursgruppen för det virtuella nätverket kan skapa och ta bort vissa Azure-nätverksresurser. Se [krav för resursgruppen](#resource-group). 

Här är ett diagram som visar krävs anslutningar för din Azure-SSIS IR:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> Behörigheter som krävs

Den användare som skapar Azure-SSIS Integration Runtime måste ha följande behörigheter:

- Om du kopplar din SSIS IR till ett Azure Resource Manager-nätverk, har du två alternativ:

  - Använda inbyggda *Nätverksdeltagare* roll. Den här rollen som medföljer den *Microsoft.Network/\**  behörigheter, som har ett mycket större omfång än nödvändigt.

  - Skapa en anpassad roll som innehåller endast nödvändiga *Microsoft.Network/virtualNetworks/\*/join/åtgärd* behörighet. 

- Om du kopplar din SSIS IR till ett klassiskt virtuellt nätverk, rekommenderar vi att du använder inbyggt *klassisk virtuell Datordeltagare* roll. Annars måste du definiera en anpassad roll med behörighet att ansluta det virtuella nätverket.

### <a name="subnet"></a> Välj undernätet
-   Markera inte GatewaySubnet för att distribuera en Azure-SSIS Integration Runtime, eftersom den är avsedda för virtuella nätverksgatewayer. 

-   Kontrollera att det undernät som du väljer har tillräckligt med tillgängliga adressutrymmet för Azure-SSIS IR som ska användas. Lämna minst 2 * antal för IR-noder i tillgängliga IP-adresser. Azure reserverar vissa IP-adresser i varje undernät och det går inte att använda dessa adresser. Första och sista IP-adresserna i undernäten är reserverade för protokollöverensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i [finns det några begränsningar med IP-adresser inom dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Använd inte ett undernät som enbart är upptagen med andra Azure-tjänster (till exempel SQL Database Managed Instance, App Service, osv.). 

### <a name="dns_server"></a> Domain Name Services-server 
Om du vill använda din egen Domain Name Services (DNS)-server i ett virtuellt nätverk som är ansluten genom din Azure-SSIS integration runtime kan du se till att den kan matcha värdnamn för offentlig Azure (till exempel ett Azure Storage blobnamn, `<your storage account>.blob.core.windows.net`). 

Du bör följande steg: 

-   Konfigurera anpassad DNS för att vidarebefordra begäranden till Azure DNS. Du kan vidarebefordra olösta DNS-poster för IP-adressen för Azures rekursiva matchare (168.63.129.16) på din egen DNS-server. 

-   Ställ in anpassad DNS som primär och Azure DNS som sekundär för det virtuella nätverket. Registrera IP-adressen för Azures rekursiva matchare (168.63.129.16) som en sekundär DNS-server om DNS-servern är inte tillgänglig. 

Mer information finns i [namnmatchning som använder en egen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Nätverkssäkerhetsgrupp
Om du behöver implementera en nätverkssäkerhetsgrupp (NSG) för det undernät som används av din Azure-SSIS integration runtime Tillåt inkommande/utgående trafik via följande portar: 

| Riktning | Transport-protokoll | Källa | Källportsintervall | Mål | Målportsintervall | Kommentarer |
|---|---|---|---|---|---|---|
| Inkommande | TCP | AzureCloud<br/>(eller större område som Internet) | * | VirtualNetwork | 29876, 29877 (om du ansluter IR till ett virtuellt nätverk för Azure Resource Manager) <br/><br/>10100, 20100 och 30100 (om du ansluter IR till ett klassiskt virtuellt nätverk)| Data Factory-tjänsten använder dessa portar för att kommunicera med noderna i din Azure-SSIS integration runtime i det virtuella nätverket. <br/><br/> Om du skapar en NSG på undernätsnivå eller inte konfigurerar Data Factory alltid en NSG på nivån över nätverkskort (NIC) som anslutna till virtuella datorer som är värdar för Azure-SSIS IR. Endast inkommande trafik från Data Factory IP-adresser på de angivna portarna tillåts av den NSG för NIC-nivå. Även om du öppnar dessa portar för Internet-trafiken på undernätverksnivån i är trafik från IP-adresser som inte är Data Factory IP-adresser blockerad på nätverkskortnivån. |
| Utgående | TCP | VirtualNetwork | * | AzureCloud<br/>(eller större område som Internet) | 443 | Noderna på din Azure-SSIS integration runtime i virtuella nätverk använda den här porten för att komma åt Azure-tjänster, till exempel Azure Storage och Azure Event Hubs. |
| Utgående | TCP | VirtualNetwork | * | Internet | 80 | Noder på din Azure-SSIS integration runtime i det virtuella nätverket använder den här porten för att hämta listan över återkallade certifikat från Internet. |
| Utgående | TCP | VirtualNetwork | * | SQL<br/>(eller större område som Internet) | 1433, 11000 11999, 14000 14999 | Noderna på din Azure-SSIS integration runtime i virtuellt nätverk använder dessa portar för att få åtkomst till SSISDB värd för din Azure SQL Database-server – detta gäller inte för SSISDB som värd för hanterad instans. |
||||||||

### <a name="route"></a> Använda Azure ExpressRoute eller användardefinierad väg
Du kan ansluta en [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) krets i den virtuella nätverksinfrastrukturen för att utöka ditt lokala nätverk till Azure. 

En vanlig konfiguration är att använda Tvingad tunneltrafik (annonsera 0.0.0.0/0 till det virtuella nätverket en BGP-väg) som styr utgående Internet-trafik från virtual network-flöde till lokala nätverksinstallation för granskning och loggning. Det här flödet i nätverkstrafiken delar anslutning mellan Azure-SSIS IR i det virtuella nätverket med beroende Azure Data Factory-tjänster. Lösningen är att definiera en (eller flera) [användardefinierade vägar (Udr)](../virtual-network/virtual-networks-udr-overview.md) på det undernät som innehåller Azure-SSIS IR. En UDR definierar undernät-specifika vägar som respekteras i stället för BGP-väg. 

Eller du kan definiera användardefinierade vägar (Udr) för att framtvinga utgående Internet-trafik från undernätet som är värd för den Azure-SSIS IR till ett annat undernät som är värd för en virtuell nätverksinstallation som en brandvägg eller en DMZ-värd för granskning och loggning. 

I båda fallen kan du tillämpa en väg med 0.0.0.0/0 med nästa hopptyp som **Internet** i undernät som är värd för den Azure-SSIS IR så att kommunikationen mellan Data Factory-tjänsten och Azure-SSIS IR är kan lyckas. 

![Lägg till en rutt](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Om du är orolig för att förlora möjligheten att granska utgående Internet-trafik från undernätet, du kan också lägga till en NSG-regel på undernätet för att begränsa utgående mål till [Azure Datacenter IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653). 

Se [den här PowerShell.skript](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) ett exempel. Du måste köra skriptet varje vecka för att hålla Azure data center IP-adresslistan uppdaterade. 

### <a name="resource-group"></a> Krav för resursgrupp
-   Azure-SSIS IR måste skapa vissa nätverksresurser under samma resursgrupp som det virtuella nätverket. Dessa resurser inkluderar följande:
    -   En Azure belastningsutjämnare med namnet  *<Guid>- azurebatch cloudserviceloadbalancer*.
    -   En Azure offentlig IP-adress med namnet  *<Guid>- azurebatch cloudservicepublicip*.
    -   En work nätverkssäkerhetsgrupp, med namnet  *<Guid>- azurebatch cloudservicenetworksecuritygroup*. 

-   Se till att du inte har någon lås på den resursgrupp eller prenumeration som det virtuella nätverket tillhör. Om du konfigurerar ett skrivskyddat Lås eller ett borttagningsskydd, kan starta och stoppa IR misslyckas eller låser sig. 

-   Kontrollera att du inte har en Azure-princip som förhindrar att följande resurser skapas under den resursgrupp eller prenumeration som det virtuella nätverket tillhör: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Azure-portalen (Data Factory-användargränssnitt)
Det här avsnittet visar hur du ansluter till en befintlig Azure-SSIS runtime till ett virtuellt nätverk (klassisk eller Azure Resource Manager) med hjälp av Azure-portalen och Data Factory-Användargränssnittet. Först måste du konfigurera det virtuella nätverket på lämpligt sätt innan du ansluter din Azure-SSIS IR till den. Gå igenom något av följande två avsnitt baserat på vilken typ av ditt virtuella nätverk (klassisk eller Azure Resource Manager). Fortsätt sedan med det tredje avsnittet för att ansluta till din Azure-SSIS IR till det virtuella nätverket. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Använda portalen för att konfigurera ett virtuellt Azure Resource Manager-nätverk
Du måste konfigurera ett virtuellt nätverk innan du kan ansluta till en Azure-SSIS IR till den. 

1. Starta Microsoft Edge eller Google Chrome. Användargränssnittet för Data Factory stöds för närvarande bara i dessa webbläsare. 

1. Logga in på [Azure Portal](https://portal.azure.com). 

1. Välj **fler tjänster**. Filtrera på och välj **virtuella nätverk**. 

1. Filtrera efter och välj ditt virtuella nätverk i listan. 

1. På den **virtuellt nätverk** väljer **egenskaper**. 

1. Välj kopieringsknappen för **resurs-ID** att kopiera resurs-ID för det virtuella nätverket till Urklipp. Spara ID: T från Urklipp i OneNote eller en fil. 

1. Välj **undernät** på den vänstra menyn. Se till att antalet **tillgängliga adresser** är större än noder i din Azure-SSIS integration runtime. 

1. Kontrollera att Azure Batch-providern är registrerad i Azure-prenumeration som har det virtuella nätverket. Du kan också registrera Azure Batch-providern. Om du redan har ett Batch-konto i din prenumeration, registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory-portalen, Azure Batch-provider registreras automatiskt åt dig.) 

   a. I Azure-portalen väljer du **prenumerationer** på den vänstra menyn. 

   b. Välj din prenumeration. 

   c. Välj **resursprovidrar** till vänster och bekräfta att **Microsoft.Batch** är en registrerad provider. 

   ![Bekräftelse av ”Registered” status](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.Batch** i listan, för att registrera den, [skapa en tom Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Använda portalen för att konfigurera ett klassiskt virtuellt nätverk
Du måste konfigurera ett virtuellt nätverk innan du kan ansluta till en Azure-SSIS IR till den. 

1. Starta Microsoft Edge eller Google Chrome. Användargränssnittet för Data Factory stöds för närvarande bara i dessa webbläsare. 

1. Logga in på [Azure Portal](https://portal.azure.com). 

1. Välj **fler tjänster**. Filtrera på och välj **virtuella nätverk (klassiska)**. 

1. Filtrera efter och välj ditt virtuella nätverk i listan. 

1. På den **virtuella nätverk (klassiskt)** väljer **egenskaper**. 

   ![Resurs-ID för klassiskt virtuellt nätverk](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Välj kopieringsknappen för **resurs-ID** att kopiera resurs-ID för det klassiska virtuella nätverket till Urklipp. Spara ID: T från Urklipp i OneNote eller en fil. 

1. Välj **undernät** på den vänstra menyn. Se till att antalet **tillgängliga adresser** är större än noder i din Azure-SSIS integration runtime. 

   ![Antal tillgängliga adresser i det virtuella nätverket](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Ansluta till **MicrosoftAzureBatch** till den **klassisk virtuell Datordeltagare** roll för det virtuella nätverket. 

    a. Välj **åtkomstkontroll (IAM)** på den vänstra menyn och välj den **rolltilldelningar** fliken. 

    ![”Access control” och ”Lägg till” knappar](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Välj **Lägg till rolltilldelning**.

    c. På den **Lägg till rolltilldelning** väljer **klassisk virtuell Datordeltagare** för **rollen**. Klistra in **ddbf3205-c6bd-46ae-8127-60eb93363864** i den **Välj** och väljer sedan **Microsoft Azure Batch** i listan över sökresultat. 

    ![Sökresultat på ”Lägg till rolltilldelning” sida](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Välj **spara** att spara inställningarna och stänga sidan. 

    ![Spara inställningar för åtkomst](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Kontrollera att du ser **Microsoft Azure Batch** i listan över deltagare. 

    ![Bekräfta Azure Batch åtkomst](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Kontrollera att Azure Batch-providern är registrerad i Azure-prenumeration som har det virtuella nätverket. Du kan också registrera Azure Batch-providern. Om du redan har ett Batch-konto i din prenumeration, registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory-portalen, Azure Batch-provider registreras automatiskt åt dig.) 

   a. I Azure-portalen väljer du **prenumerationer** på den vänstra menyn. 

   b. Välj din prenumeration. 

   c. Välj **resursprovidrar** till vänster och bekräfta att **Microsoft.Batch** är en registrerad provider. 

   ![Bekräftelse av ”Registered” status](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.Batch** i listan, för att registrera den, [skapa en tom Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Anslut Azure-SSIS IR till ett virtuellt nätverk
1. Starta Microsoft Edge eller Google Chrome. Användargränssnittet för Data Factory stöds för närvarande bara i dessa webbläsare. 

1. I den [Azure-portalen](https://portal.azure.com)väljer **datafabriker** på den vänstra menyn. Om du inte ser **datafabriker** på menyn, Välj **fler tjänster**, och klicka sedan **datafabriker** i den **information + analys**avsnittet. 

   ![Listan med datafabriker](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Välj din data factory med Azure-SSIS integration runtime i listan. Du kan se startsidan för din datafabrik. Välj den **författare och distribuera** panelen. Du kan se Användargränssnittet för Data Factory på en separat flik. 

   ![Datafabrikens startsida](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. I Användargränssnittet för Data Factory växlar du till den **redigera** fliken **anslutningar**, och växla till den **Integreringskörningar** fliken. 

   ![Fliken ”integreringskörningar”](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Om din Azure-SSIS IR körs i integration runtime-listan, Välj den **stoppa** knappen i den **åtgärder** kolumn för din Azure-SSIS IR. Du kan inte redigera en IR tills du stoppar den. 

   ![Stoppa IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. I listan integration runtime väljer du den **redigera** knappen i den **åtgärder** kolumn för din Azure-SSIS IR. 

   ![Redigera integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. På den **allmänna inställningar** för den **av Integration Runtime** väljer **nästa**. 

   ![Allmänna inställningar för IR-installation](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)

1. På den **SQL-inställningar** , ange administratörslösenordet och välj **nästa**. 

   ![SQL Server-inställningar för IR-installation](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)

1. På den **avancerade inställningar** gör du följande åtgärder: 

   a. Markera kryssrutan för **Välj ett virtuellt nätverk för din Azure-SSIS-Integreringskörning ska ansluta till och låt Azure-tjänsterna konfigurera behörigheter/inställningar**. 

   b. För **typ**väljer du om det virtuella nätverket är ett klassiskt virtuellt nätverk eller en Azure Resource Manager-nätverk. 

   c. För **namn på virtuellt nätverk**, Välj ditt virtuella nätverk. 

   d. För **Undernätsnamn**, Välj ditt undernät i det virtuella nätverket. 

   e. Klicka på **VNet verifiering** och om det lyckas klickar du på **uppdatering**. 

   ![Avancerade inställningar för IR-installation](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Nu, kan du börja IR med hjälp av den **starta** knappen i den **åtgärder** kolumn för din Azure-SSIS IR. Det tar cirka 20 till 30 minuter att starta en Azure-SSIS IR. 

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk
Du måste konfigurera ett virtuellt nätverk innan du kan ansluta till din Azure-SSIS IR till den. För att automatiskt konfigurera behörigheter/inställningar för virtuella nätverk för din Azure-SSIS integration runtime att ansluta till det virtuella nätverket, lägger du till följande skript:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Skapa en Azure-SSIS IR och koppla den till ett virtuellt nätverk
Du kan skapa en Azure-SSIS IR och ansluta den till ett virtuellt nätverk på samma gång. Det fullständiga skriptet och instruktioner finns i [skapar ett Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Ansluta till en befintlig Azure-SSIS IR till ett virtuellt nätverk
Skriptet i den [skapar ett Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) artikeln visar hur du skapar en Azure-SSIS IR och ansluta den till ett virtuellt nätverk i samma skript. Om du har en befintlig Azure-SSIS IR kan du utföra följande steg för att ansluta till det virtuella nätverket: 
1. Stoppa Azure-SSIS IR. 
1. Konfigurera Azure-SSIS IR för att ansluta till det virtuella nätverket. 
1. Starta Azure-SSIS IR. 

### <a name="define-the-variables"></a>Definiera variabler
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Stoppa Azure-SSIS IR
Stoppa Azure-SSIS integration runtime innan du kan ansluta den till ett virtuellt nätverk. Det här kommandot släpper alla dess noder och stoppar fakturering:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurera inställningar för virtuella nätverk för Azure-SSIS IR att ansluta till
```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Konfigurera Azure-SSIS IR
För att konfigurera Azure-SSIS integration runtime för att ansluta till det virtuella nätverket, kör den `Set-AzureRmDataFactoryV2IntegrationRuntime` kommando: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Starta Azure-SSIS IR
Om du vill starta Azure-SSIS integration runtime, kör du följande kommando: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Det här kommandot tar 20 till 30 minuter att slutföra.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS runtime finns i följande avsnitt: 
- [Azure SSIS-integreringskörning](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integreringskörningar generellt sätt, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR. Azure SQL Database används som värd för SSIS-katalogen. 
- [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökas med självstudien och innehåller instruktioner för hur du använder Azure SQL Database med virtuellt nätverk tjänstens slutpunkter/hanterad instans som värd för SSIS-katalogen och ansluter IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut din Azure-SSIS IR genom att lägga till noder. 
