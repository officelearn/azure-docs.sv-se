---
title: Anslut Azure SSIS-integrering runtime till ett virtuellt nätverk | Microsoft Docs
description: Lär dig hur du ansluter till Azure-SSIS-integrering runtime till ett Azure virtual network.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: aca67ceff2650a5470b1c08b20c21d71f00bae62
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751538"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Ansluta till en Azure-SSIS-integrering körning till ett virtuellt nätverk
Anslut din Azure-SSIS-integrering runtime (IR) till Azure-nätverk i följande scenarier: 

- Du vill ansluta till lokala datalager från SSIS-paket som körs på Azure-SSIS Integration Runtime. 

- Du är värd för SQL Server Integration Services (SSIS) katalogdatabasen i Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsversion). 

 Azure Data Factory version 2 (förhandsversion) kan du ansluta din Azure-SSIS-integrering runtime till ett virtuellt nätverk som skapats via den klassiska distributionsmodellen och Azure Resource Manager-distributionsmodellen. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som finns i allmänhet tillgänglighet (GA), finns det [Data Factory version 1 dokumentationen](v1/data-factory-introduction.md). 

## <a name="access-to-on-premises-data-stores"></a>Åtkomst till lokala datalager
Om SSIS-paket åtkomst till endast de offentliga moln datalager, behöver du inte ansluta till Azure-SSIS-IR till ett virtuellt nätverk. Om SSIS-paket åtkomst till lokala datalager, måste du ansluta till Azure-SSIS-IR till ett virtuellt nätverk som är anslutet till det lokala nätverket. 

Här följer några viktiga saker att Observera: 

- Om det ingen finns virtuella nätverk är anslutna till ditt lokala nätverk, först skapa en [virtuellt nätverk i Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) eller en [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering Runtime att ansluta till. Konfigurera sedan en plats-till-plats [VPN-gatewayanslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) eller [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) anslutning från det virtuella nätverket till ditt lokala nätverk. 

- Om det finns en befintlig Azure Resource Manager eller klassiska virtuella nätverk som är anslutna till ditt lokala nätverk på samma plats som din Azure-SSIS-IR, kan du ansluta IR till det virtuella nätverket. 

- Om det finns ett befintligt klassiska virtuella nätverk som anslutna till ditt lokala nätverk på en annan plats från Azure-SSIS-IR måste du först skapa en [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-IR att ansluta till. Konfigurera sedan en [klassisk till klassiskt virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) anslutning. Eller så kan du skapa en [virtuellt nätverk i Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) för din Azure-SSIS-integrering runtime att ansluta till. Konfigurera en [klassisk till Azure Resource Manager virtuellt nätverk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning. 
 
- Om det finns en befintlig Azure Resource Manager virtuella nätverk är anslutna till ditt lokala nätverk på en annan plats från Azure-SSIS-IR måste du först skapa en [virtuellt nätverk i Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) för din Azure-SSIS IR att ansluta till. Konfigurera en virtuell nätverksanslutning för Azure Resource Manager till Azure Resource Manager. Du kan skapa en [klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-IR att ansluta till. Konfigurera sedan en [klassisk till Azure Resource Manager virtuellt nätverk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning. 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance-preview"></a>Värd för databasen SSIS-katalog i Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsgranskning)
SSIS-katalogen finns i Azure SQL Database med slutpunkter för virtuellt nätverk eller hanteras instansen (förhandsgranskning), du kan ansluta till din Azure-SSIS-IR till: 

- Samma virtuella nätverk 
- Ett annat virtuellt nätverk som har en anslutning för nätverk till nätverk med det som används för Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsgranskning) 

Om du ansluter din Azure-SSIS-IR till samma virtuella nätverk som hanteras-instans, se till att Azure-SSIS-IR är i ett annat undernät än hanteras-instans. Om du ansluter Azure SSIS-IR till ett annat virtuellt nätverk än hanteras-instans, rekommenderar vi virtuellt nätverk peering (som är begränsad till samma region) eller ett virtuellt nätverk till virtuellt nätverk. Se [ansluta programmet till Azure SQL-hanterade databasinstans](../sql-database/sql-database-managed-instance-connect-app.md).

Det virtuella nätverket kan distribueras via den klassiska distributionsmodellen eller Azure Resource Manager-distributionsmodellen.

Följande avsnitt innehåller mer information. 

## <a name="requirements-for-virtual-network-configuration"></a>Kraven för konfiguration av virtuellt nätverk
-   Se till att `Microsoft.Batch` är en registrerad provider för den här prenumerationen för din virtuella undernät som är värd för Azure-SSIS-IR. Om du använder klassiskt virtuellt nätverk kan också ansluta `MicrosoftAzureBatch` till klassiska virtuella deltagarrollen för det virtuella nätverket. 

-   Välj rätt undernät som värd för Azure-SSIS-IR. Se [markerar du undernätet](#subnet). 

-   Om du använder egna tjänster DNS (Domain Name)-server på det virtuella nätverket finns [Domain Name Services server](#dns_server). 

-   Om du använder en Nätverkssäkerhetsgrupp (NSG) på undernät, se [nätverkssäkerhetsgrupp](#nsg) 

-   Om du använder Azure Express Route eller konfigurera användaren definierat väg (UDR), se [Använd Azure ExpressRoute eller användaren definierade flödet](#route). 

-   Kontrollera att resursgruppen för det virtuella nätverket kan skapa och ta bort vissa Azure nätverksresurser. Se [krav för resursgruppen](#resource-group). 

### <a name="subnet"></a> Välj undernätet
-   Välj inte GatewaySubnet för att distribuera en Azure-SSIS-integrering körning eftersom den dedikerade virtuella nätverksgatewayerna. 

-   Kontrollera att det undernät som du väljer har tillräckligt med tillgängliga adressutrymmen för Azure-SSIS-IR ska användas. Lämna minst 2 * IR nod antalet tillgängliga IP-adresser. Azure reserverar vissa IP-adresser inom varje undernät, och dessa adresser kan inte användas. De första och sista IP-adresserna undernät är reserverade för överensstämmelse med protokollet, tillsammans med tre flera adresser som används för Azure-tjänster. Mer information finns i [finns det några begränsningar med hjälp av IP-adresser inom dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Om du inte använda ett undernät som uteslutande är upptagen med andra Azure-tjänster (till exempel SQL-hanterade databasinstans (förhandsgranskning), Apptjänst osv.). 

### <a name="dns_server"></a> Domain Name Services-server 
Om du behöver använda din egen Services DNS (Domain Name)-server i ett virtuellt nätverk som är ansluten genom ditt Azure-SSIS-integrering runtime kontrollerar du att den kan lösa offentliga Azure värdnamn (exempelvis ett Azure Storage blob namn, `<your storage account>.blob.core.windows.net`). 

Du bör följande steg: 

-   Konfigurera anpassade DNS för att vidarebefordra begäran till Azure DNS. Du kan vidarebefordra olösta DNS-posterna IP-adressen för Azures rekursiv matchare (168.63.129.16) på DNS-servern. 

-   Ställ in anpassad DNS som primär och Azure DNS som sekundär för det virtuella nätverket. Registrera IP-adressen för Azures rekursiv matchare (168.63.129.16) som en sekundär DNS-server om DNS-servern är inte tillgänglig. 

Mer information finns i [namnmatchning som använder DNS-servern](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Nätverkssäkerhetsgrupp
Om du behöver implementera en nätverkssäkerhetsgrupp (NSG) i ett virtuellt nätverk som är ansluten genom ditt Azure-SSIS-integrering runtime, Tillåt inkommande/utgående trafik via följande portar: 

| Riktning | Transportprotokoll | Källa | Portintervall för källa | Mål | Målport intervall | Kommentarer |
|---|---|---|---|---|---|---|
| Inkommande | TCP | Internet | * | VirtualNetwork | 29876, 29877 (om du ansluter IR till ett virtuellt nätverk med Azure Resource Manager) <br/><br/>10100, 20100, 30100 (om du ansluter IR till ett klassiskt virtuellt nätverk)| Data Factory-tjänsten använder dessa portar för att kommunicera med noderna i Azure-SSIS-integrering-körningsmiljön i det virtuella nätverket. <br/><br/> Om du anger en NSG eller inte konfigurerar Data Factory alltid en NSG på nivån av nätverkskort (NIC) kopplade till virtuella datorer som är värdar för Azure-SSIS-IR. Bara inkommande trafik från Data Factory IP-adresser tillåts. Även om du öppnar portarna för Internet-trafiken blockeras trafik från IP-adresser som inte är Data Factory IP-adresser på NIC-nivå. |
| Utgående | TCP | VirtualNetwork | * | Internet | 443 | Noderna i Azure-SSIS-integrering-körningsmiljön i det virtuella nätverket använda den här porten för att komma åt Azure-tjänster, till exempel Azure Storage och Händelsehubbar i Azure. |
| Utgående | TCP | VirtualNetwork | * | Internet- eller Sql | 1433, 11000 11999, 14000 14999 | Noderna i din Azure-SSIS-integrering körning i virtuella nätverk använder portarna för att komma åt SSISDB hos dina Azure SQL Database-server - detta gäller inte för SSISDB hos hanteras instansen (förhandsversion). |
||||||||

### <a name="route"></a> Använd Azure ExpressRoute eller användardefinierad väg
Du kan ansluta en [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kretsen i den virtuella nätverksinfrastrukturen för att utöka ditt lokala nätverk till Azure. 

En vanlig konfiguration är att använda Tvingad tunneling (annonsera en BGP-väg 0.0.0.0/0 till det virtuella nätverket) som styr utgående Internet-trafik från det virtuella nätverk flödet till lokala nätverksenhet kontroll och loggning. Den här trafikflöde bryts anslutning mellan Azure SSIS-IR i det virtuella nätverket med beroende Azure Data Factory-tjänster. Lösningen är att definiera en (eller flera) [användardefinierade vägar (udr: er)](../virtual-network/virtual-networks-udr-overview.md) på det undernät som innehåller Azure SSIS-IR. En UDR definierar undernät-specifika vägar som hanteras i stället för BGP-väg. 

Eller så kan du definiera användardefinierade vägar (udr: er) för att tvinga utgående Internet-trafik från det undernät som är värd för Azure-SSIS-IR till ett annat undernät som är värd för en virtuell nätverksenhet som en brandvägg eller en DMZ värd för kontroll och loggning. 

I båda fallen kan tillämpa en 0.0.0.0/0 väg med nästa hopptyp som **Internet** i undernät som är värd för Azure-SSIS-IR så att kommunikationen mellan Data Factory-tjänsten och Azure-SSIS är IR kan genomföras. 

![Lägga till en väg](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Om du är orolig för att förlora möjligheten att inspektera utgående Internet-trafik från det undernätet du kan också lägga till en regel för NSG på subnätet för att begränsa utgående mål att [Azure Datacenter IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653). 

Se [detta PowerShell-skript](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) ett exempel. Du måste köra skriptet weekly för att hålla Azure data center IP-adresslistan uppdaterade. 

### <a name="resource-group"></a> Krav för resursgrupp
Azure-SSIS-IR måste skapa vissa nätverksresurser under samma resursgrupp som det virtuella nätverket, inklusive en Azure belastningsutjämnare, en Azure offentliga IP-adress och en nätverkssäkerhetsgrupp för arbetet. 

-   Kontrollera att du inte har någon resurslås på den resursgrupp eller prenumeration som tillhör det virtuella nätverket. Om du konfigurerar ett skrivskyddat Lås eller ta bort lås, kan starta och stoppa IR misslyckas eller Lägg. 

-   Kontrollera att du inte har en Azure-princip som förhindrar att skapas under den resursgrupp eller prenumeration som tillhör det virtuella nätverket i följande resurser: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Azure-portalen (Data Factory UI)
Det här avsnittet visar hur du ansluter till en befintlig Azure-SSIS-körning till ett virtuellt nätverk (klassiska eller Azure Resource Manager) med hjälp av Azure-portalen och Data Factory-Användargränssnittet. Du måste först konfigurera det virtuella nätverket på lämpligt sätt innan du ansluter din Azure-SSIS-IR till den. Gå igenom följande två avsnitt baserat på vilken typ av ditt virtuella nätverk (klassiska eller Azure Resource Manager). Fortsätt sedan med det tredje avsnittet för att ansluta till Azure-SSIS-IR till det virtuella nätverket. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Använda portalen för att konfigurera ett virtuellt nätverk med Azure Resource Manager
Du behöver konfigurera ett virtuellt nätverk innan du kan ansluta till en Azure-SSIS-IR till den. 

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöds Data Factory Användargränssnittet bara i dessa webbläsare. 

2. Logga in på [Azure Portal](https://portal.azure.com). 

3. Välj **fler tjänster**. Filtrera efter och välj **virtuella nätverken**. 

4. Filtrera efter och välj det virtuella nätverket i listan. 

5. På den **för virtuella nätverk** väljer **egenskaper**. 

6. Klicka på kopieringsknappen för **resurs-ID** Kopiera resurs-ID för det virtuella nätverket till Urklipp. Spara ID från Urklipp i OneNote eller en fil. 

7. Välj **undernät** på den vänstra menyn. Se till att antalet **tillgängliga adresser** är större än noderna i din Azure-SSIS-integrering runtime. 

8. Kontrollera att Azure Batch-provider är registrerad i Azure-prenumeration som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration är prenumerationen registrerad för Azure Batch. (Om du skapar Azure SSIS-IR Data Factory-portalen, Azure Batch-providern registreras automatiskt åt dig.) 

   a. Välj i Azure-portalen **prenumerationer** på den vänstra menyn. 

   b. Välj din prenumeration. 

   c. Välj **resursproviders** till vänster och bekräfta att **Microsoft.Batch** är en registrerad provider. 

   ![Bekräftelse av ”registrerad” status](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.Batch** i listan, för att registrera den, [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Använda portalen för att konfigurera ett klassiskt virtuellt nätverk
Du behöver konfigurera ett virtuellt nätverk innan du kan ansluta till en Azure-SSIS-IR till den. 

1. Starta Microsoft Edge eller Google Chrome. Data Factory Användargränssnittet stöds för närvarande bara i dessa webbläsare. 

2. Logga in på [Azure Portal](https://portal.azure.com). 

3. Välj **fler tjänster**. Filtrera efter och välj **virtuella nätverk (klassiskt)**. 

4. Filtrera efter och välj det virtuella nätverket i listan. 

5. På den **virtuella nätverk (klassiska)** väljer **egenskaper**. 

   ![Resurs-ID för klassiska virtuella nätverk](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

6. Klicka på kopieringsknappen för **resurs-ID** Kopiera resurs-ID för det klassiska nätverket till Urklipp. Spara ID från Urklipp i OneNote eller en fil. 

7. Välj **undernät** på den vänstra menyn. Se till att antalet **tillgängliga adresser** är större än noderna i din Azure-SSIS-integrering runtime. 

   ![Antal tillgängliga adresser i det virtuella nätverket](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

8. Anslut **MicrosoftAzureBatch** till den **klassiska Virtual Machine-deltagare** roll för det virtuella nätverket. 

    a. Välj **åtkomstkontroll (IAM)** på den vänstra menyn och välj **Lägg till** i verktygsfältet. 

    ![”Åtkomstkontroll” och ”Lägg till” knappar](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. På den **lägga till behörigheter** väljer **klassiska Virtual Machine-deltagare** för **rollen**. Klistra in **ddbf3205-c6bd-46ae-8127-60eb93363864** i den **Välj** och välj sedan **Microsoft Azure Batch** från listan över sökresultat. 

    ![Sökresultat på sidan ”Lägg till behörigheter”](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Välj **spara** att spara inställningarna och stänga sidan. 

    ![Spara inställningar](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Bekräfta att du ser **Microsoft Azure Batch** i listan över deltagare. 

    ![Bekräfta Azure Batch-åtkomst](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

9. Kontrollera att Azure Batch-provider är registrerad i Azure-prenumeration som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration är prenumerationen registrerad för Azure Batch. (Om du skapar Azure SSIS-IR Data Factory-portalen, Azure Batch-providern registreras automatiskt åt dig.) 

   a. Välj i Azure-portalen **prenumerationer** på den vänstra menyn. 

   b. Välj din prenumeration. 

   c. Välj **resursproviders** till vänster och bekräfta att **Microsoft.Batch** är en registrerad provider. 

   ![Bekräftelse av ”registrerad” status](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.Batch** i listan, för att registrera den, [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Anslut Azure SSIS-IR till ett virtuellt nätverk
1. Starta Microsoft Edge eller Google Chrome. För närvarande stöds Data Factory Användargränssnittet bara i dessa webbläsare. 

2. I den [Azure-portalen](https://portal.azure.com)väljer **datafabriker** på den vänstra menyn. Om du inte ser **datafabriker** på menyn, Välj **fler tjänster**, och välj **datafabriker** i den **INTELLIGENCE + analys**avsnitt. 

   ![Lista över datafabriker](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

3. Välj din data factory med Azure-SSIS-integrering körning i listan. Du kan se startsidan för din data factory. Välj den **författare & distribuera** panelen. Du kan se Data Factory-Användargränssnittet på en separat flik. 

   ![Datafabrikens startsida](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

4. I Användargränssnittet för Data Factory växla till den **redigera** väljer **anslutningar**, och växla till den **integrering körningar** fliken. 

   ![Fliken ”integration körningar”](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

5. Om din Azure-SSIS-IR körs i listan integration runtime väljer du den **stoppa** knappen i den **åtgärder** för Azure-SSIS-IR. Du kan inte redigera en IR tills du avbryter den. 

   ![Stoppa IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

6. Välj i listan integration runtime den **redigera** knappen i den **åtgärder** för Azure-SSIS-IR. 

   ![Redigera integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

7. På den **allmänna inställningar** sida av den **integrering Runtime installationsprogrammet** väljer **nästa**. 

   ![Allmänna inställningar för IR-installationen](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)

8. På den **SQL-inställningar** , anger du administratörslösenordet och väljer **nästa**. 

   ![SQL Server-inställningar för IR-installationen](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)

9. På den **avancerade inställningar** gör du följande åtgärder: 

   a. Markera kryssrutan för **väljer ett virtuellt nätverk för din Azure-SSIS-integrering Runtime att ansluta och ge Azure-tjänster så här konfigurerar du VNet behörighetsinställningar**. 

   b. För **typen**väljer du om det virtuella nätverket är ett klassiskt virtuellt nätverk eller ett virtuellt nätverk med Azure Resource Manager. 

   c. För **VNet namn**, Välj det virtuella nätverket. 

   d. För **Undernätsnamn**, Välj ditt undernät i det virtuella nätverket. 

   e. Klicka på **VNet validering** och om det lyckas, klickar du på **uppdatering**. 

   ![Avancerade inställningar för IR-installationen](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

10. Nu, kan du börja IR med hjälp av den **starta** knappen i den **åtgärder** för Azure-SSIS-IR. Det tar cirka 20 till 30 minuter att starta en Azure-SSIS-IR. 

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk
Du behöver konfigurera ett virtuellt nätverk innan du kan ansluta till Azure-SSIS-IR till den. För att automatiskt konfigurera virtuella nätverk/behörighetsinställningar för din Azure-SSIS-integrering runtime att ansluta till virtuella nätverk, lägger du till följande skript:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Skapa en Azure-SSIS-IR och ansluta den till ett virtuellt nätverk
Du kan skapa en Azure-SSIS-IR och ansluta den till ett virtuellt nätverk på samma gång. Fullständig skript och instruktioner finns i [skapa en Azure-SSIS-integrering körning](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Anslut en befintlig Azure-SSIS-IR till ett virtuellt nätverk
Skriptet i den [skapa en Azure-SSIS-integrering körning](create-azure-ssis-integration-runtime.md) artikeln visar hur du skapar en Azure-SSIS-IR och ansluta den till ett virtuellt nätverk i samma skript. Om du har en befintlig Azure-SSIS-IR, utför följande steg för att ansluta till det virtuella nätverket: 
1. Stoppa Azure SSIS-IR. 
2. Konfigurera Azure SSIS-IR för att ansluta det virtuella nätverket. 
3. Starta Azure-SSIS-IR. 

### <a name="define-the-variables"></a>Definiera variabler
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Stoppa Azure SSIS-IR
Stoppa Azure SSIS-integrering runtime innan du kan ansluta till ett virtuellt nätverk. Det här kommandot släpper alla dess noder och stoppar fakturering:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurera inställningar för virtuella nätverk att ansluta till Azure-SSIS IR
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

### <a name="configure-the-azure-ssis-ir"></a>Konfigurera Azure SSIS-IR
Konfigurera Azure SSIS-integrering runtime för att ansluta det virtuella nätverket genom att köra den `Set-AzureRmDataFactoryV2IntegrationRuntime` kommando: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Starta Azure-SSIS-IR
Kör följande kommando för att starta Azure-SSIS-integrering körning: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Det här kommandot tar 20 till 30 minuter att slutföra.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS-körningsmiljön finns i följande avsnitt: 
- [Azure-SSIS-integrering runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integration körningar generellt, inklusive Azure SSIS-IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS-IR. Azure SQL Database används som värd för SSIS-katalogen. 
- [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln kan utökas med kursen och innehåller instruktioner om med Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsversion) som värd för SSIS-katalogen och koppla IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den visar också hur du skalar upp Azure SSIS-IR genom att lägga till noder. 
