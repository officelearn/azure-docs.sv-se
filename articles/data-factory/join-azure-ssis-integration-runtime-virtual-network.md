---
title: Ansluta en Azure SSIS-integreringskörning till ett virtuellt nätverk
description: Lär dig hur du ansluter till en Azure-SSIS-integreringskörning till ett virtuellt Azure-nätverk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: c899d3b4d132ee51679f8c7b4fd1c578417500db
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605273"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Ansluta en Azure SSIS-integreringskörning till ett virtuellt nätverk

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du använder SQL Server Integration Services (SSIS) i Azure Data Factory bör du ansluta till din Azure-SSIS-integrationskörning (IR) till ett virtuellt Azure-nätverk i följande scenarier:

- Du vill ansluta till lokala datalager från SSIS-paket som körs på din Azure-SSIS IR utan att konfigurera eller hantera en självvärd IR som proxy. 

- Du vill vara värd för SSIS-katalogdatabas (SSISDB) i en Azure SQL-databas med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt. 

- Du vill ansluta till Azure-resurser som konfigurerats med slutpunkter för virtuella nätverkstjänster från SSIS-paket som körs på din Azure-SSIS IR.

- Du vill ansluta till datalager/resurser som konfigurerats med IP-brandväggsregler från SSIS-paket som körs på din Azure-SSIS IR.

Med Data Factory kan du ansluta till din Azure-SSIS IR till ett virtuellt nätverk som skapats via den klassiska distributionsmodellen eller Azure Resource Manager-distributionsmodellen.

> [!IMPORTANT]
> Det klassiska virtuella nätverket är inaktuellt, så använd det virtuella Azure Resource Manager-nätverket i stället.  Om du redan använder det klassiska virtuella nätverket växlar du till det virtuella Azure Resource Manager-nätverket så snart som möjligt.

[Konfigurera en Azure-SQL Server Integration Services (SSIS) integration runtime (IR) för att ansluta till ett virtuellt nätverk](tutorial-deploy-ssis-virtual-network.md) handledning visar de minsta stegen via Azure portal. Den här artikeln utökar självstudien och beskriver alla valfria uppgifter:

- Om du använder virtuellt nätverk (klassiskt).
- Om du tar med egna offentliga IP-adresser för Azure-SSIS IR.
- Om du använder din egen DNS-server (Domain Name System).
- Om du använder en nätverkssäkerhetsgrupp (NSG) i undernätet.
- Om du använder Azure ExpressRoute eller en användardefinierad väg (UDR).
- Om du använder anpassad Azure-SSIS IR.
- Om du använder Azure Powershell-etablering.

## <a name="access-to-on-premises-data-stores"></a>Tillgång till lokala datalager

Om dina SSIS-paket får åtkomst till lokala datalager kan du ansluta till din Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket. Eller så kan du konfigurera och hantera en självvärd IR som proxy för din Azure-SSIS IR. Mer information finns i [Konfigurera en självvärd IR som proxy för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Kom ihåg följande viktiga punkter när du ansluter din Azure-SSIS IR till ett virtuellt nätverk: 

- Om inget virtuellt nätverk är anslutet till ditt lokala nätverk skapar du först ett [virtuellt Azure Resource Manager-nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) för din Azure-SSIS IR att ansluta till. Konfigurera sedan en [VPN-gateway-anslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) från plats till plats eller [ExpressRoute-anslutning](../expressroute/expressroute-howto-linkvnet-classic.md) från det virtuella nätverket till det lokala nätverket. 

- Om ett virtuellt Azure Resource Manager-nätverk redan är anslutet till ditt lokala nätverk på samma plats som din Azure-SSIS IR kan du ansluta IR till det virtuella nätverket. 

- Om ett klassiskt virtuellt nätverk redan är anslutet till ditt lokala nätverk på en annan plats än din Azure-SSIS IR kan du skapa ett [virtuellt Azure Resource Manager-nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) för din Azure-SSIS IR att gå med i. Konfigurera sedan en virtuell nätverksanslutning för [klassiska till Azure Resource Manager.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 
 
- Om ett virtuellt Azure Resource Manager-nätverk redan är anslutet till ditt lokala nätverk på en annan plats än din Azure-SSIS IR kan du först skapa ett [virtuellt Azure Resource Manager-nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) för din Azure-SSIS IR att gå med i. Konfigurera sedan en virtuell nätverksanslutning för Azure Resource Manager-to-Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Värd för SSIS-katalogen i SQL Database

Om du är värd för din SSIS-katalog i en Azure SQL-databas med slutpunkter för virtuella nätverkstjänster kontrollerar du att du ansluter till din Azure-SSIS IR till samma virtuella nätverk och undernät.

Om du är värd för din SSIS-katalog i en hanterad instans med privat slutpunkt kontrollerar du att du ansluter till din Azure-SSIS IR till samma virtuella nätverk, men i ett annat undernät än den hanterade instansen. Om du vill ansluta till din Azure-SSIS IR till ett annat virtuellt nätverk än den hanterade instansen rekommenderar vi antingen virtuell nätverks peering (som är begränsad till samma region) eller en anslutning från virtuellt nätverk till virtuellt nätverk. Mer information finns i [Ansluta ditt program till Azure SQL Database-hanterad instans](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="access-to-azure-services"></a>Åtkomst till Azure-tjänster

Om dina SSIS-paket får åtkomst till Azure-resurser som stöder slutpunkter för [virtuella nätverkstjänster](../virtual-network/virtual-network-service-endpoints-overview.md) och du vill skydda åtkomsten till dessa resurser från Azure-SSIS IR, kan du ansluta till din Azure-SSIS IR till ett virtuellt nätverksundernät som konfigurerats för slutpunkter för virtuella nätverkstjänster och sedan lägga till en virtuell nätverksregel till relevanta Azure-resurser för att tillåta åtkomst från samma undernät.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Åtkomst till datakällor som skyddas av IP-brandväggsregeln

Om dina SSIS-paket får åtkomst till datalager/resurser som endast tillåter specifika statiska offentliga IP-adresser och du vill skydda åtkomsten till dessa resurser från Azure-SSIS IR, kan du ta med egna [offentliga IP-adresser](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) för Azure-SSIS IR när du ansluter den till ett virtuellt nätverk och sedan lägga till en IP-brandväggsregel till relevanta resurser för att tillåta åtkomst från dessa IP-adresser.

I samtliga fall kan det virtuella nätverket endast distribueras via Distributionsmodellen för Azure Resource Manager.

Följande avsnitt innehåller mer information. 

## <a name="virtual-network-configuration"></a>Konfiguration av virtuellt nätverk

Konfigurera det virtuella nätverket så att det uppfyller följande krav: 

- Kontrollera att `Microsoft.Batch` det är en registrerad provider under prenumerationen på ditt virtuella nätverksundernät som är värd för Azure-SSIS IR. Om du använder ett klassiskt `MicrosoftAzureBatch` virtuellt nätverk kan du också gå med i rollen Klassisk virtuell datormedarbetare för det virtuella nätverket. 

- Kontrollera att du har de behörigheter som krävs. Mer information finns i [Konfigurera behörigheter](#perms).

- Välj rätt undernät som ska vara värd för Azure-SSIS IR. Mer information finns i [Markera undernätet](#subnet). 

- Om du tar med egna offentliga IP-adresser för Azure-SSIS IR läser [du Välj statiska offentliga IP-adresser](#publicIP)

- Om du använder din egen DNS-server (Domain Name System) i det virtuella nätverket läser [du Konfigurera DNS-servern](#dns_server). 

- Om du använder en nätverkssäkerhetsgrupp (NSG) i undernätet läser [du Konfigurera en NSG](#nsg). 

- Om du använder Azure ExpressRoute eller en användardefinierad väg (UDR) läser [du Använda Azure ExpressRoute eller en UDR](#route). 

- Kontrollera att det virtuella nätverkets resursgrupp (eller de offentliga IP-adressernas resursgrupp om du tar med egna offentliga IP-adresser) kan skapa och ta bort vissa Azure-nätverksresurser. Mer information finns i [Konfigurera resursgruppen](#resource-group). 

- Om du anpassar din Azure-SSIS IR enligt beskrivningen i [Anpassad installation för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)får dina Azure-SSIS IR-noder privata IP-adresser från ett fördefinierat intervall på 172.16.0.0 till 172.31.255.255. Så se till att de privata IP-adressintervallen för dina virtuella eller lokala nätverk inte kolliderar med det här intervallet.

Det här diagrammet visar de anslutningar som krävs för din Azure-SSIS IR:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>Ställ in behörigheter

Användaren som skapar Azure-SSIS IR måste ha följande behörigheter:

- Om du ansluter till din SSIS IR till ett virtuellt Azure Resource Manager-nätverk har du två alternativ:

  - Använd den inbyggda nätverksdeltagarerollen. Den här rollen levereras med _behörigheten Microsoft.Network/\* _ som har ett mycket större utrymme än nödvändigt.

  - Skapa en anpassad roll som bara innehåller nödvändig _microsoft.network/virtualNetworks/\*/join/action-behörighet._ Om du också vill ta med egna offentliga IP-adresser för Azure-SSIS IR när du ansluter till ett virtuellt Azure Resource Manager-nätverk, inkludera även _behörigheten Microsoft.Network/publicIPAddresses/*/join/action_ i rollen.

- Om du ansluter din SSIS IR till ett klassiskt virtuellt nätverk rekommenderar vi att du använder rollen inbyggt klassiskt virtuellt datordeltagare. Annars måste du definiera en anpassad roll som innehåller behörigheten att ansluta till det virtuella nätverket.

### <a name="select-the-subnet"></a><a name="subnet"></a>Markera undernätet

När du väljer ett undernät: 

- Välj inte GatewaySubnet för att distribuera en Azure-SSIS IR. Den är avsedd för virtuella nätverksgateways. 

- Kontrollera att det undernät du väljer har tillräckligt med ledigt adressutrymme för Azure-SSIS IR att använda. Lämna tillgängliga IP-adresser för minst två gånger IR-nodnumret. Azure reserverar vissa IP-adresser i varje undernät. Dessa adresser kan inte användas. De första och sista IP-adresserna för undernäten är reserverade för protokollkonformans och ytterligare tre adresser används för Azure-tjänster. Mer information finns i [Finns det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Använd inte ett undernät som uteslutande används av andra Azure-tjänster (till exempel SQL Database-hanterad instans, AppTjänst och så vidare). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Välj statiska offentliga IP-adresser

Om du vill ta med egna statiska offentliga IP-adresser för Azure-SSIS IR när du ansluter till ett virtuellt nätverk kontrollerar du att de uppfyller följande krav:

- Exakt två oanvända som inte redan är associerade med andra Azure-resurser ska anges. Den extra kommer att användas när vi regelbundet uppgraderar din Azure-SSIS IR. Observera att en offentlig IP-adress inte kan delas mellan dina aktiva Azure-SSIS IRs.

- De bör båda vara statiska av standardtyp. Mer information finns [i SKU:er för offentlig IP-adress.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)

- De bör båda ha ett DNS-namn. Om du inte har angett något DNS-namn när du skapar dem kan du göra det på Azure-portalen.

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- De och det virtuella nätverket bör vara under samma prenumeration och i samma region.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>Konfigurera DNS-servern 
Om du behöver använda din egen DNS-server i ett virtuellt nätverk som sammanfogas av din Azure-SSIS IR för att matcha ditt `<your storage account>.blob.core.windows.net`privata värdnamn, se till att den också kan matcha globala Azure-värdnamn (till exempel en Azure Storage-blob med namnet ). 

En rekommenderad metod är nedan: 

-   Konfigurera anpassad DNS för att vidarebefordra begäranden till Azure DNS. Du kan vidarebefordra olösta DNS-poster till IP-adressen för Azure rekursiva resolvers (168.63.129.16) på din egen DNS-server. 

Mer information finns i [Namnmatchning som använder din egen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Använd ett fullständigt kvalificerat domännamn (FQDN) för ditt privata värdnamn, `<your_private_server>`t.ex. `<your_private_server>.contoso.com`

### <a name="set-up-an-nsg"></a><a name="nsg"></a>Konfigurera en NSG
Om du behöver implementera en NSG för undernätet som används av din Azure-SSIS IR tillåter du inkommande och utgående trafik via följande portar: 

-   **Inkommande krav på Azure-SSIS IR**

| Riktning | Transportprotokoll | Källa | Källportintervall | Mål | Målportintervall | Kommentarer |
|---|---|---|---|---|---|---|
| Inkommande | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (om du ansluter till IR till ett virtuellt resurshanterarenätverk) <br/><br/>10100, 20100, 30100 (om du går med i IR till ett klassiskt virtuellt nätverk)| Tjänsten Data Factory använder dessa portar för att kommunicera med noderna i din Azure-SSIS IR i det virtuella nätverket. <br/><br/> Oavsett om du skapar en NSG-undernätsnivå eller inte konfigurerar Data Factory alltid en NSG-nivå på nivån för nätverkskorten som är anslutna till de virtuella datorer som är värdar för Azure-SSIS IR. Endast inkommande trafik från Data Factory IP-adresser på de angivna portarna tillåts av den NIC-nivån NSG. Även om du öppnar dessa portar för internettrafik på undernätsnivå blockeras trafik från IP-adresser som inte är Data Factory IP-adresser på nätverkskortets nivå. |
| Inkommande | TCP | CorpNetSaw (2000/90 | * | VirtualNetwork | 3389 | (Valfritt) Den här regeln krävs endast när Microsoft-supporter ber kunden att öppna för avancerad felsökning och kan stängas direkt efter felsökning. **CorpNetSaw-servicetag** tillåter endast säker åtkomst till arbetsstationer i Microsofts företagsnätverk för att använda fjärrskrivbord. Och det här tjänstmärket kan inte väljas från portalen och är endast tillgängligt via Azure PowerShell eller Azure CLI. <br/><br/> På NIC-nivå NSG är port 3389 öppen som standard och vi tillåter dig att styra port 3389 på undernätsnivå NSG, under tiden har Azure-SSIS IR inte tillåts port 3389 utgående som standard vid Windows-brandväggsregeln på varje IR-nod för skydd. |
||||||||

-   **Utgående krav på Azure-SSIS IR**

| Riktning | Transportprotokoll | Källa | Källportintervall | Mål | Målportintervall | Kommentarer |
|---|---|---|---|---|---|---|
| Utgående | TCP | VirtualNetwork | * | AzureCloud | 443 | Noderna i din Azure-SSIS IR i det virtuella nätverket använder den här porten för att komma åt Azure-tjänster, till exempel Azure Storage och Azure Event Hubs. |
| Utgående | TCP | VirtualNetwork | * | Internet | 80 | (Valfritt) Noderna i din Azure-SSIS IR i det virtuella nätverket använder den här porten för att hämta en lista över återkallade certifikat från Internet. Om du blockerar den här trafiken kan prestandanedgradering uppstå när du startar IR och förlorar möjligheten att kontrollera listan över återkallade certifikat för certifikatanvändning. Om du vill begränsa målet ytterligare till vissa FQDN-nätverk läser du avsnittet **Använd Azure ExpressRoute eller UDR**|
| Utgående | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | (Valfritt) Den här regeln krävs endast när noderna för din Azure-SSIS IR i det virtuella nätverket har åtkomst till en SSISDB som finns värd för SQL Database-servern. Om anslutningsprincipen för SQL Database-servern är inställd på **Proxy** i stället för **Omdirigera**behövs endast port 1433. <br/><br/> Den här utgående säkerhetsregeln gäller inte för en SSISDB som finns hos din hanterade instans i det virtuella nätverket eller Azure Database-servern som konfigurerats med privat slutpunkt. |
| Utgående | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Valfritt) Den här regeln krävs endast när noderna för din Azure-SSIS IR i det virtuella nätverket har åtkomst till en SSISDB som finns värd för din hanterade instans i det virtuella nätverket eller Azure Database-servern som konfigurerats med privat slutpunkt. Om anslutningsprincipen för SQL Database-servern är inställd på **Proxy** i stället för **Omdirigera**behövs endast port 1433. |
| Utgående | TCP | VirtualNetwork | * | Storage | 445 | (Valfritt) Den här regeln krävs bara när du vill köra SSIS-paket som lagras i Azure-filer. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Använda Azure ExpressRoute eller UDR
Om du vill inspektera utgående trafik från Azure-SSIS IR kan du dirigera trafik som initierats från Azure-SSIS IR till lokal brandväggsinstallation via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) force tunneling (annonsera en BGP-väg, 0.0.0.0/0, till det virtuella nätverket) eller till Network Virtual Appliance (NVA) som en brandvägg eller [Azure-brandvägg](https://docs.microsoft.com/azure/firewall/) via [UDRs](../virtual-network/virtual-networks-udr-overview.md). 

![NVA-scenario för Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Du måste göra under saker för att göra hela scenariot fungerar
   -   Inkommande trafik mellan Azure Batch-hanteringstjänster och Azure-SSIS IR kan inte dirigeras via brandväggsinstallationen.
   -   Brandväggsverktyget ska tillåta utgående trafik som krävs av Azure-SSIS IR.

Inkommande trafik mellan Azure Batch-hanteringstjänster och Azure-SSIS IR kan inte dirigeras till brandväggsinstallationen annars bryts trafiken på grund av asymmetrisk routningsproblem. Rutter måste definieras för inkommande trafik så att trafiken kan svara tillbaka på samma sätt som den kom in. Du kan definiera specifika UDR:er för att dirigera trafik mellan Azure Batch-hanteringstjänster och Azure-SSIS IR med nästa hoptyp som **Internet**.

Om din Azure-SSIS IR till `UK South` exempel finns på och du vill inspektera utgående trafik via Azure-brandväggen, skulle du först få en IP-serielista över tjänsttaggar `BatchNodeManagement.UKSouth` från länken för [tjänsttaggar IP-intervallhämtning](https://www.microsoft.com/download/details.aspx?id=56519) eller via [API:et för identifiering](https://aka.ms/discoveryapi)av servicetag . Använd sedan följande UDRs för relaterade IP-intervallvägar med nästa hopptyp som **Internet** tillsammans med vägen 0.0.0/0 med nästa hopptyp som **virtuell installation**.

![UDR-inställningar för Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Den här metoden medför en extra underhållskostnad. Kontrollera regelbundet IP-intervallet och lägg till nya IP-intervall i din UDR för att undvika att bryta Azure-SSIS IR. Vi rekommenderar att du kontrollerar IP-intervallet varje månad eftersom ip-adressen kommer att gälla i den nya IP-adressen varje månad. 

Om du vill göra inställningarna för UDR-regler enklare kan du köra följande Powershell-skript för att lägga till UDR-regler för Azure Batch-hanteringstjänster:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

För att brandväggsinstallationen ska tillåta utgående trafik måste du tillåta utgående till underportar som är samma som krav i NSG-utgående regler.
-   Port 443 med destination som Azure Cloud-tjänster.

    Om du använder Azure-brandväggen kan du ange nätverksregel med AzureCloud Service Tag. För brandvägg av de andra typerna kan du antingen helt enkelt tillåta mål som alla för port 443 eller tillåta under FQDN baserat på vilken typ av Din Azure-miljö:

    | Azure-miljö | Slutpunkter                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure, offentlig      | <ul><li><b>Azure Data Factory (hantering)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure-lagring (hantering)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure-behållarregister (anpassad installation)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Händelsehubb (loggning)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Microsoft Loggningstjänst (intern användning)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (hantering)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure-lagring (hantering)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure-behållarregister (anpassad installation)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Händelsehubb (loggning)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft Loggningstjänst (intern användning)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure Kina 21Vianet     | <ul><li><b>Azure Data Factory (hantering)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure-lagring (hantering)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure-behållarregister (anpassad installation)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Händelsehubb (loggning)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft Loggningstjänst (intern användning)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    När det gäller FQDN:erna för Azure Storage, Azure Container Registry and Event Hub kan du också välja att aktivera följande tjänstslutpunkter för det virtuella nätverket så att nätverkstrafiken till dessa slutpunkter går via Azure-stamnätnätverket i stället för att dirigeras till brandväggsinstallationen:
    -  Microsoft.Storage
    -  Microsoft.Container-registret
    -  Microsoft.EventHub


-   Port 80 med destination som CRL-nedladdningsplatser.

    Du ska tillåta nedladdningsplatser för certifikat för certifikat för Azure-SSIS IR-hantering under nedan.
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Om du använder certifikat med olika listal, du föreslås att inkludera dem också. Du kan läsa detta för att förstå mer på [listan över återkallade certifikat](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Om du inte tillåter den här trafiken kan prestandanedgradering uppstå när du startar Azure-SSIS IR och förlorar möjligheten att kontrollera listan över återkallade certifikat för certifikatanvändning som inte rekommenderas från säkerhetssynpunkt.

-   Port 1433, 11000-11999 med mål som Azure SQL (krävs endast när noderna i din Azure-SSIS IR i det virtuella nätverket får tillgång till en SSISDB som finns värd för SQL Database-servern).

    Om du använder Azure-brandväggen kan du ange nätverksregel med Azure SQL Service Tag, annars kan du tillåta mål som specifik azure sql url i brandväggsinstallationen.

-   Port 445 med mål som Azure Storage (krävs endast när du kör SSIS-paket som lagras i Azure-filer).

    Om du använder Azure-brandväggen kan du ange nätverksregel med Storage Service Tag, annars kan du tillåta mål som specifik azure file storage url i brandväggsinstallationen.

> [!NOTE]
> Om du konfigurerar slutpunkter för tjänsten För Azure SQL och Lagring dirigeras trafiken mellan Azure-SSIS IR och Azure SQL i samma region \ Azure Storage i samma region eller parkopplad region direkt till Microsoft Azure-stamnätsnätverket i stället för brandväggsinstallationen.

Om du inte behöver möjlighet att inspektera utgående trafik i Azure-SSIS IR kan du helt enkelt använda vägen för att tvinga all trafik till nästa hoptyp **Internet:**

-   I ett Azure ExpressRoute-scenario kan du använda en 0.0.0.0/0-väg med nästa hopptyp som **Internet** i undernätet som är värd för Azure-SSIS IR. 
-   I ett NVA-scenario kan du ändra den befintliga 0.0.0.0/0-vägen som tillämpas på undernätet som är värd för Azure-SSIS IR från nästa hopptyp som **virtuell installation** till **Internet**.

![Lägga till en rutt](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Ange rutt med nästa hop typ **Internet** betyder inte att all trafik kommer att gå över Internet. Så länge måladressen är för en av Azures tjänster dirigerar Azure trafiken direkt till tjänsten via Azures stamnät i stället för att dirigera trafiken till Internet.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>Ställ in resursgruppen

Azure-SSIS IR måste skapa vissa nätverksresurser under samma resursgrupp som det virtuella nätverket. Dessa resurser omfattar:
- En Azure belastningsutjämnare, med namnet * \<Guid>-azurebatch-cloudserviceloadbalancer*.
- En offentlig Azure-IP-adress med namnet * \<Guid>-azurebatch-cloudservicepublicip*.
- En nätverksarbetssäkerhetsgrupp med namnet * \<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Du kan nu ta med egna statiska offentliga IP-adresser för Azure-SSIS IR. I det här scenariot skapar vi endast Azure-belastningsutjämnaren och nätverkssäkerhetsgruppen under samma resursgrupp som dina statiska offentliga IP-adresser i stället för det virtuella nätverket.

Dessa resurser skapas när din Azure-SSIS IR startar. De tas bort när din Azure-SSIS IR stoppas. Om du tar med egna statiska offentliga IP-adresser för Azure-SSIS IR tas dina egna statiska offentliga IP-adresser inte bort när din Azure-SSIS IR stoppas. Använd inte dessa nätverksresurser i dina andra resurser för att undvika att blockera din Azure-SSIS IR från att stoppas.

Kontrollera att du inte har något resurslås på resursgruppen/prenumerationen som det virtuella nätverket/dina statiska offentliga IP-adresser tillhör. Om du konfigurerar ett skrivskyddat/borttagningslås misslyckas och stoppar azure-SSIS IR, eller så slutar det svara.

Se till att du inte har en Azure-princip som förhindrar att följande resurser skapas under den resursgrupp/prenumeration som det virtuella nätverket/dina statiska offentliga IP-adresser tillhör: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAdresser 

Kontrollera att resurskvoten för prenumerationen är tillräcklig för ovanstående tre nätverksresurser. För varje Azure-SSIS IR som skapas i virtuellt nätverk måste du reservera två kostnadsfria kvoter för var och en av ovanstående tre nätverksresurser. Den extra kvoten används när vi regelbundet uppgraderar din Azure-SSIS IR.

### <a name="faq"></a><a name="faq"></a>Faq

- Hur kan jag skydda den offentliga IP-adressen som exponeras på min Azure-SSIS IR för inkommande anslutning? Är det möjligt att ta bort den offentliga IP-adressen?
 
  Just nu skapas en offentlig IP-adress automatiskt när din Azure-SSIS IR ansluter till ett virtuellt nätverk. Vi har en NSG på NIC-nivå som endast tillåter Azure Batch-hanteringstjänster att inkommande anslutning till din Azure-SSIS IR. Du kan också ange en NSG på undernätsnivå för inkommande skydd.

  Om du inte vill att någon offentlig IP-adress ska exponeras kan du [konfigurera en självvärd iR som proxy för din Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) i stället för att ansluta till din Azure-SSIS IR till ett virtuellt nätverk, om detta gäller för ditt scenario.
 
- Kan jag lägga till den offentliga IP-adressen för min Azure-SSIS IR i brandväggens tillåtlista för mina datakällor?

  Du kan nu ta med egna statiska offentliga IP-adresser för Azure-SSIS IR. I det här fallet kan du lägga till dina IP-adresser i brandväggens tillåtlista för dina datakällor. Du kan också överväga andra alternativ nedan för att skydda dataåtkomst från din Azure-SSIS IR beroende på ditt scenario:

  - Om datakällan är lokal, efter att ha anslutit ett virtuellt nätverk till ditt lokala nätverk och anslutit till ditt Azure-SSIS IR till det virtuella nätverksundernätet, kan du sedan lägga till det privata IP-adressintervallet för det undernätet i brandväggens tillåtlista för din datakälla.
  - Om datakällan är en Azure-tjänst som stöder slutpunkter för virtuella nätverkstjänster kan du konfigurera en slutpunkt för en virtuell nätverkstjänst i ditt virtuella nätverksundernät och ansluta till ditt Azure-SSIS IR till det undernätet. Du kan sedan lägga till en virtuell nätverksregel med det undernätet i brandväggen för datakällan.
  - Om din datakälla är en molntjänst som inte är Azure kan du använda en UDR för att dirigera utgående trafik från din Azure-SSIS IR till en NVA/Azure-brandvägg via en statisk offentlig IP-adress. Du kan sedan lägga till den statiska offentliga IP-adressen för din NVA/Azure-brandvägg i brandväggens tillåt-lista för din datakälla.
  - Om inget av ovanstående alternativ uppfyller dina behov kan du [överväga att konfigurera en självvärd IR som proxy för din Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Du kan sedan lägga till den statiska offentliga IP-adressen för den dator som är värd för din egenvärderade IR i brandväggens tillåtlista för din datakälla.

- Varför måste jag ange två statiska offentliga adresser om jag vill ta med mina egna för Azure-SSIS IR?

  Azure-SSIS IR uppdateras automatiskt regelbundet. Nya noder skapas under uppgraderingen och gamla tas bort. Men för att undvika driftstopp tas de gamla noderna inte bort förrän de nya är klara. Därför kan din första statiska offentliga IP-adress som används av de gamla noderna inte släppas omedelbart och vi behöver din andra statiska offentliga IP-adress för att skapa de nya noderna.

- Jag har tagit med mina egna statiska offentliga IP-adresser för Azure-SSIS IR, men varför kan den fortfarande inte komma åt mina datakällor?

  - Bekräfta att de två statiska offentliga IP-adresserna båda läggs till i brandväggens tillåtlista för dina datakällor. Varje gång din Azure-SSIS IR uppgraderas, ändras dess statiska offentliga IP-adress mellan de två som du har tagit med. Om du bara lägger till en av dem i listan över tillåtna data bryts dataåtkomsten för din Azure-SSIS IR efter uppgraderingen.
  - Om din datakälla är en Azure-tjänst kontrollerar du om du har konfigurerat den med slutpunkter för virtuella nätverkstjänster. Om så är fallet växlar trafiken från Azure-SSIS IR till datakällan för att använda de privata IP-adresser som hanteras av Azure-tjänster och lägger till dina egna statiska offentliga IP-adresser i brandväggens tillåtlista för datakällan kommer inte att börja gälla.

## <a name="azure-portal-data-factory-ui"></a>Azure-portal (Data Factory UI)

I det här avsnittet visas hur du ansluter till en befintlig Azure-SSIS IR till ett virtuellt nätverk (klassiskt eller Azure Resource Manager) med hjälp av Azure-portalen och datafabrikens användargränssnitt. 

Innan du ansluter din Azure-SSIS IR till det virtuella nätverket måste du konfigurera det virtuella nätverket på rätt sätt. Följ stegen i avsnittet som gäller för din typ av virtuellt nätverk (klassiskt eller Azure Resource Manager). Följ sedan stegen i det tredje avsnittet för att ansluta till din Azure-SSIS IR till det virtuella nätverket. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurera ett virtuellt Azure Resource Manager-nätverk

Använd portalen för att konfigurera ett virtuellt Azure Resource Manager-nätverk innan du försöker ansluta till en Azure-SSIS IR till den.

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare datafabrikens användargränssnitt. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. Välj **Fler tjänster**. Filtrera efter och välj **Virtuella nätverk**. 

1. Filtrera efter och välj ditt virtuella nätverk i listan. 

1. På sidan **Virtuellt nätverk** väljer du **Egenskaper**. 

1. Välj kopieringsknappen för **RESURS-ID** om du vill kopiera resurs-ID:et för det virtuella nätverket till Urklipp. Spara ID:t från Urklipp i OneNote eller en fil. 

1. Välj **Undernät**på den vänstra menyn . Kontrollera att antalet tillgängliga adresser är större än noderna i din Azure-SSIS IR. 

1. Kontrollera att Azure Batch-providern är registrerad i Azure-prenumerationen som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory-portalen registreras Azure Batch-providern automatiskt åt dig.) 

   1. Välj **Prenumerationer**på den vänstra menyn i Azure-portalen . 

   1. Välj din prenumeration. 

   1. Till vänster väljer du **Resursleverantörer**och bekräftar att **Microsoft.Batch** är en registrerad leverantör. 

   ![Bekräftelse av statusen "Registrerad"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.Batch** i listan skapar du [ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration om du vill registrera det. Du kan ta bort den senare. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurera ett klassiskt virtuellt nätverk

Använd portalen för att konfigurera ett klassiskt virtuellt nätverk innan du försöker ansluta till en Azure-SSIS IR till den. 

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare datafabrikens användargränssnitt. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. Välj **Fler tjänster**. Filtrera efter och välj **Virtuella nätverk (klassisk)**. 

1. Filtrera efter och välj ditt virtuella nätverk i listan. 

1. På sidan **Virtuellt nätverk (klassiskt)** väljer du **Egenskaper**. 

   ![Klassiskt resurs-ID för virtuellt nätverk](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Välj kopieringsknappen för **RESURS-ID** om du vill kopiera resurs-ID:et för det klassiska nätverket till Urklipp. Spara ID:t från Urklipp i OneNote eller en fil. 

1. Välj **Undernät**på den vänstra menyn . Kontrollera att antalet tillgängliga adresser är större än noderna i din Azure-SSIS IR. 

   ![Antal tillgängliga adresser i det virtuella nätverket](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Gå med **i rollen MicrosoftAzureBatch** i rollen med deltagare i **den klassiska virtuella datorn** för det virtuella nätverket. 

   1. Välj **Åtkomstkontroll (IAM) på**den vänstra menyn och välj fliken **Rolltilldelningar.** 

       ![Knapparna "Åtkomstkontroll" och "Lägg till"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Välj **Lägg till rolltilldelning**.

   1. Välj Klassisk deltagare i den virtuella datorn för **rollen**på sidan **Lägg till rolltilldelning** . **Classic Virtual Machine Contributor** Klistra in **ddbf3205-c6bd-46ae-8127-60eb93363864**i rutan **Välj** och välj sedan **Microsoft Azure Batch** i listan med sökresultat. 

       ![Sökresultat på sidan "Lägg till rolltilldelning"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Välj **Spara** om du vill spara inställningarna och stänga sidan. 

       ![Spara åtkomstinställningar](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Bekräfta att du ser **Microsoft Azure Batch** i listan över deltagare. 

       ![Bekräfta Azure Batch-åtkomst](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Kontrollera att Azure Batch-providern är registrerad i Azure-prenumerationen som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory-portalen registreras Azure Batch-providern automatiskt åt dig.) 

   1. Välj **Prenumerationer**på den vänstra menyn i Azure-portalen . 

   1. Välj din prenumeration. 

   1. Till vänster väljer du **Resursleverantörer**och bekräftar att **Microsoft.Batch** är en registrerad leverantör. 

   ![Bekräftelse av statusen "Registrerad"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.Batch** i listan skapar du [ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration om du vill registrera det. Du kan ta bort den senare. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Ansluta till Azure-SSIS IR till ett virtuellt nätverk

När du har konfigurerat det virtuella nätverket Azure Resource Manager eller det klassiska virtuella nätverket kan du ansluta till Azure-SSIS IR till det virtuella nätverket:

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare datafabrikens användargränssnitt. 

1. Välj **Datafabriker**på den vänstra menyn i [Azure-portalen](https://portal.azure.com). Om du inte ser **Datafabriker** på menyn väljer du **Fler tjänster**och väljer sedan **Datafabriker**i avsnittet **INTELLIGENS + ANALYTICS** . 

   ![Lista över datafabriker](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Välj din datafabrik med Azure-SSIS IR i listan. Du ser hemsidan för din datafabrik. Välj panelen **Författare & Bildskärm.** Du ser datafabriksgränssnittet på en separat flik. 

   ![Datafabrikens startsida](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Växla till fliken **Redigera** i datafabriken, välj **Anslutningar**och växla till fliken **Integrationskörningar.** 

   ![Fliken "Integrationskörning"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Om din Azure-SSIS IR körs väljer du **stoppknappen** för din Azure-SSIS IR i listan **Integration runtimes** i kolumnen **Åtgärder.** Du kan inte redigera din Azure-SSIS IR förrän du stoppar den. 

   ![Stoppa IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Välj knappen **Redigera** för din Azure-SSIS IR i kolumnen **Åtgärder** i listan **Integrationkörningstider.** 

   ![Redigera integrationskörningen](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. På inställningspanelen för integreringskörning går du vidare genom avsnitten **Allmänna inställningar** och **SQL-inställningar** genom att välja knappen **Nästa.** 

1. I avsnittet **Avancerade inställningar:** 

   1. Markera kryssrutan **Välj ett virtuella nätverk för din Azure-SSIS-integrationskörning för att gå med, tillåt ADF att skapa vissa nätverksresurser och eventuellt ta med egna statiska offentliga IP-adresser.** 

   1. För **prenumeration**väljer du den Azure-prenumeration som har ditt virtuella nätverk.

   1. För **Plats**väljs samma plats för integrationskörningen.

   1. För **Typ**väljer du typ av virtuellt nätverk: klassiskt eller Azure Resource Manager. Vi rekommenderar att du väljer ett virtuellt Azure Resource Manager-nätverk, eftersom klassiska virtuella nätverk snart kommer att inaktuell.

   1. För **VNet-namn**väljer du namnet på det virtuella nätverket. Det bör vara samma som används för din Azure SQL Database-server med slutpunkter för virtuella nätverkstjänster eller hanterad instans med privat slutpunkt som värd för SSISDB. Eller så ska den vara samma som är ansluten till ditt lokala nätverk. Annars kan det vara valfritt virtuellt nätverk för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

   1. För **Undernätsnamn**väljer du namnet på undernätet för det virtuella nätverket. Det bör vara samma som används för din Azure SQL Database-server med virtuella nätverkstjänstslutpunkter som värd för SSISDB. Eller så ska det vara ett annat undernät än det som används för din hanterade instans med privat slutpunkt som värd för SSISDB. Annars kan det vara vilket undernät som helst för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

   1. Markera kryssrutan **Ta med statiska offentliga IP-adresser för din Azure-SSIS Integration Runtime** för att välja om du vill ta med egna statiska offentliga IP-adresser för Azure-SSIS IR, så att du kan tillåta dem i brandväggen för dina datakällor.

      Om du markerar kryssrutan gör du följande steg.

      1. För **Första statiska offentliga IP-adress**väljer du den första statiska offentliga IP-adressen som [uppfyller kraven](#publicIP) för din Azure-SSIS IR. Om du inte har några klickar du på **Skapa ny** länk för att skapa statiska offentliga IP-adresser på Azure-portalen och klicka sedan på uppdateringsknappen här, så att du kan välja dem.
      
      1. För **andra statiska offentliga IP-adress**väljer du den andra statiska offentliga IP-adressen som [uppfyller kraven](#publicIP) för din Azure-SSIS IR. Om du inte har några klickar du på **Skapa ny** länk för att skapa statiska offentliga IP-adresser på Azure-portalen och klicka sedan på uppdateringsknappen här, så att du kan välja dem.

   1. Välj **VNet-validering**. Om valideringen lyckas väljer du **Fortsätt**. 

   ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Granska alla inställningar för din Azure-SSIS IR i avsnittet **Sammanfattning.** Välj sedan **Uppdatera**.

1. Starta din Azure-SSIS IR genom att välja **Start-knappen** i kolumnen **Åtgärder** för din Azure-SSIS IR. Det tar cirka 20 till 30 minuter att starta Azure-SSIS IR som ansluter till ett virtuellt nätverk. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definiera variablerna

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du kan ansluta till din Azure-SSIS IR till ett virtuellt nätverk måste du konfigurera det virtuella nätverket. Om du automatiskt vill konfigurera behörigheter och inställningar för virtuella nätverk för att ansluta till det virtuella nätverket lägger du till följande skript:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Skapa en Azure-SSIS IR och ansluta den till ett virtuellt nätverk

Du kan skapa en Azure-SSIS IR och ansluta den till ett virtuellt nätverk samtidigt. Fullständiga skript och instruktioner finns i [Skapa en Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Ansluta till en befintlig Azure-SSIS IR till ett virtuellt nätverk

Artikeln [Skapa en Azure-SSIS IR](create-azure-ssis-integration-runtime.md) visar hur du skapar en Azure-SSIS IR och ansluter den till ett virtuellt nätverk i samma skript. Om du redan har en Azure-SSIS IR följer du dessa steg för att ansluta den till det virtuella nätverket: 
1. Stoppa Azure-SSIS IR. 
1. Konfigurera Azure-SSIS IR för att ansluta till det virtuella nätverket. 
1. Starta Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>Stoppa Azure-SSIS IR

Du måste stoppa Azure-SSIS IR innan du kan ansluta den till ett virtuellt nätverk. Det här kommandot släpper alla dess noder och stoppar faktureringen:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurera inställningar för virtuella nätverk för Azure-SSIS IR för att gå med

Om du vill konfigurera inställningar för det virtuella nätverk som Azure-SSIS ska ansluta till använder du det här skriptet: 

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

Om du vill ansluta till din Azure-SSIS `Set-AzDataFactoryV2IntegrationRuntime` IR till ett virtuellt nätverk kör du kommandot: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Starta Azure-SSIS IR

Starta Azure-SSIS IR: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Det här kommandot tar 20 till 30 minuter att slutföra.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure-SSIS IR finns i följande artiklar: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller allmän begreppsmässig information om IRs, inklusive Azure-SSIS IR. 
- [Självstudiekurs: Distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här självstudien innehåller steg-för-steg-instruktioner för att skapa din Azure-SSIS IR. Den använder Azure SQL Database som värd för SSIS-katalogen. 
- [Skapa en Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Den här artikeln utökar självstudien. Den innehåller instruktioner om hur du använder Azure SQL Database med slutpunkter för virtuella nätverkstjänster eller hanterad instans i ett virtuellt nätverk som värd för SSIS-katalogen. Den visar hur du ansluter din Azure-SSIS IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du får information om din Azure-SSIS IR. Den innehåller statusbeskrivningar för den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort din Azure-SSIS IR. Den visar också hur du skalar ut din Azure-SSIS IR genom att lägga till noder.