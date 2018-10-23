---
title: Nätverkssäkerhet i Azure Data Lake Storage Gen1 | Microsoft Docs
description: Förstå hur IP-brandväggen och nätverksintegration fungerar i Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168194"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Virtual Network-integration för Azure Data Lake Storage Gen1 – förhandsversion

Introduktion till Virtual Network-integration för Azure Data Lake Storage Gen1 (förhandsversion). Med VNet-integration kan du förhindra obehörig att få åtkomst till dina Azure Data Lake Storage Gen1-konton genom att låsa dessa konton till dina specifika virtuella nätverk och undernät. Du kan nu konfigurera ditt ADLS Gen1-konto att bara acceptera trafik från angivna virtuella nätverk och undernät och blockera åtkomst från alla andra platser. Det här hjälper till att skydda ditt ADLS-konto från externa hot.

VNet-integration för ADLS Gen1 utnyttjar slutpunktssäkerhet för virtuellt nätverk-tjänsten mellan ditt virtuella nätverk och Azure Active Directory-tjänsten för att generera ytterligare säkerhetsanspråk i åtkomsttoken. Dessa anspråk används sedan för att autentisera ditt virtuella nätverk för ditt ADLS Gen1-konto och tillåta åtkomst.

> [!NOTE]
> Det här är en förhandsversionsteknik och vi rekommenderar inte användning i produktionsmiljöer.
>
> Det finns ingen extra kostnad med att använda dessa funktioner. Ditt konto debiteras enligt standardavgifterna för ADLS Gen1 ([priser](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) och alla Azure-tjänster du använder ([priser](https://azure.microsoft.com/pricing/#product-picker)).

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>Scenarier för VNET-integration för ADLS Gen1

Med VNet-integration för ADLS Gen1 kan du begränsa åtkomsten till ditt ADLS Gen1-konto från angivna virtuella nätverk och undernät.  Andra virtuella nätverk/virtuella datorer i Azure får inte åtkomst till ditt konto när den har låsts till det angiva VNet-undernätet.  Funktionellt möjliggör VNet-integration för ADLS Gen1 samma scenario som [slutpunkter för virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview).  Det finns några viktiga skillnader som beskrivs i avsnitten nedan. 

![Scenariodiagram för VNet-integration för ADLS Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> De befintliga IP-brandväggsreglerna kan användas utöver VNet-regler för att även tillåta åtkomst från lokala nätverk. 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>Optimal routning med VNet-integration för ADLS Gen1

En viktig fördel med tjänstslutpunkter för virtuellt nätverk är [optimal routning](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) från ditt virtuella nätverk.  Om du vill utföra samma routningsoptimering till ADLS Gen1-konton använder du följande [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) från ditt virtuella nätverk till ditt ADLS Gen1-konto:

- **Offentligt IP-adress för ADLS** – Använd den offentliga IP-adressen för dina ADLS Gen1-målkonton.  Du kan identifiera IP-adresserna för ditt ADLS Gen1-konto genom att [lösa DNS-namnen](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) för dina konton.  Skapa en separat post för varje adress.

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>Dataexfiltrering från kundens virtuella nätverk

Utöver att skydda ADLS-konton för åtkomst från virtuellt nätverk kan du också vara intresserad av att se till att det inte finns någon exfiltrering till ett obehörigt konto.

Vår rekommendation är att använda en brandväggslösning i ditt virtuella nätverk för att filtrera den utgående trafiken baserat på målkonto-URL:en och tillåta åtkomst till endast behöriga ADLS Gen1-konton.

Några tillgängliga alternativ är:
- [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) : Du kan [distribuera och Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) för det virtuella nätverket och skydda den utgående ADLS-trafiken och låsa den till det kända och behöriga kontots URL.
- [Network Virtual Appliance](https://azure.microsoft.com/solutions/network-appliances/)-brandvägg: Om administratören bara godkänner användning av vissa kommersiella brandväggsleverantörer kan du använda en NVA-brandväggslösning på Azure Marketplace för att utföra samma funktion.

> [!NOTE]
> Att använda brandväggar i datasökvägen introducerar ett ytterligare hopp i datasökvägen och kan påverka nätverkets prestanda för datautbyte slutpunkt till slutpunkt, inklusive tillgängligt dataflöde och svarstid för anslutningen. 

## <a name="limitations"></a>Begränsningar
1.  HDInsight-kluster måste skapas på nytt när de har lagts till i förhandsversionen.  Kluster som skapats innan det fanns stöd för VNet-integration för ADLS Gen1 måste återskapas för att stödja den här nya funktionen. 
2.  När du skapar ett nytt HDInsight-kluster och väljer ett ADLS Gen1-konto med VNet-integration så misslyckas processen. Du måste först inaktivera VNet-regeln eller så kan du **tillåta åtkomst från alla nätverk och tjänster** via bladet **Brandvägg och virtuella nätverk** för ADLS-kontot.  Se avsnittet [Undantag](##Exceptions) om du vill ha mer information.
3.  Förhandsversionen av VNet-integration för ADLS Gen1 fungerar inte med [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
4.  Fil-/mappdata på ditt VNet-aktiverade ADLS Gen1-konto är inte åtkomliga via portalen.  Det här omfattar åtkomst från en virtuell dator som finns i det virtuella nätverket och aktiviteter som att använda Datautforskaren.  Kontohanteringsaktiviteter fortsätter att fungera.  Fil-/mappdata på ditt VNET-aktiverade ADLS-konto är åtkomliga via alla icke-portalresurser: SDK-åtkomst, PowerShell-skript, andra Azure-tjänster (när de inte kommer från portalen) osv... 

## <a name="configuration"></a>Konfiguration

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>Steg 1: Konfigurera det virtuella nätverket att använda AAD-slutpunkt
1.  Gå till Azure-portalen och logga in på ditt konto. 
2.  [Skapa ett nytt virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) i din prenumeration eller gå till ett befintligt virtuellt nätverk.  Det virtuella nätverket måste för närvarande finnas i samma region som ADLS Gen 1-kontot. 
3.  På bladet Virtuellt nätverk väljer du **Tjänstslutpunkter**. 
4.  Klicka på **Lägg till** för att lägga till en ny tjänstslutpunkt.
![Lägga till en tjänstslutpunkt för virtuellt nätverk](media/data-lake-store-network-security/config-vnet-1.png)
5.  Välj **Microsoft.AzureActiveDirectory** som tjänst för slutpunkten.
![Välja Microsoft.AzureActiveDirectory-tjänstslutpunkt](media/data-lake-store-network-security/config-vnet-2.png)
6.  Välj de undernät som du vill tillåta anslutningsmöjligheter för, klicka på **Lägg till**.
![Välj undernätet](media/data-lake-store-network-security/config-vnet-3.png)
7.  Det kan ta upp till 15 minuter för tjänstslutpunkten att läggas till. När den har lagts till visas den i listan. Kontrollera att den visas och all information är enligt konfigurationen. 
![Lägga till tjänstslutpunkten](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>Steg 2: Konfigurera det tillåtna virtuella nätverket/undernätet för ditt ADLS Gen1-konto
1.  När du har konfigurerat ditt virtuella nätverk [skapar du ett nytt Azure Data Lake Storage Gen1-konto](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) i din prenumeration eller gå till ett befintligt ADLS Gen1-konto. Det ADLS Gen1-konto måste för närvarande finnas i samma region som det virtuella nätverket. 
2.  Välj **Brandvägg och virtuella nätverk**.

  > [!NOTE]
  > Om du inte ser **Brandvägg och virtuellt nätverk** i inställningarna loggar du ut från portalen. Stäng webbläsaren. Rensa webbläsarcachen. Starta om datorn och försök igen.

  ![Lägga till en VNet-regeln på ADLS-kontot](media/data-lake-store-network-security/config-adls-1.png)
3.  Välj **Valda nätverk**. 
4.  Klicka på **Lägg till befintligt virtuellt nätverk**.
  ![Lägga till befintligt virtuellt nätverk](media/data-lake-store-network-security/config-adls-2.png)
5.  Välj virtuella nätverk och undernät för att tillåta för anslutning och klicka sedan på **Lägg till**.
  ![Välja virtuellt nätverk och undernät](media/data-lake-store-network-security/config-adls-3.png)
6.  Se till att de virtuella nätverken och undernäten visas korrekt i listan och **spara**.
  ![Spara den nya regeln](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > Det kan ta upp till 5 minuter innan inställningarna börjar gälla när du har sparat.

7.  [Valfritt] Om du, utöver virtuella nätverk och undernät, vill tillåta anslutningsmöjligheter från specifika IP-adresser kan du göra det i avsnittet **Brandvägg** på samma sida. 

## <a name="exceptions"></a>Undantag
Det finns två kryssrutor i området Undantag på bladet **Brandvägg och virtuella nätverk** som möjliggör anslutning från ett antal olika tjänster och virtuella datorer på Azure.
![Undantag för brandvägg och virtuellt nätverk](media/data-lake-store-network-security/firewall-exceptions.png)
- **Tillåt åtkomst för alla Azure-tjänster till det här Data Lake Storage Gen1-kontot** tillåter alla Azure-tjänster som Azure Data Factory, Event Hubs, alla virtuella Azure-datorer osv... att kommunicera med ditt ADLS-konto.

- **Tillåt åtkomst för Azure Data Lake Analytics till det här Data Lake Storage Gen1-kontot** tillåter Azure Data Lake Analytics-tjänsten att ansluta till det här ADLS-kontot. 

Vi rekommenderar att du har de här undantagen inaktiverade och bara aktiverar dem om du behöver anslutning från dessa andra tjänster utanför ditt virtuella nätverk.
