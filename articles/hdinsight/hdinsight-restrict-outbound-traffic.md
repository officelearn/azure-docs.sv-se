---
title: Konfigurera begränsning av utgående nätverks trafik – Azure HDInsight
description: Lär dig hur du konfigurerar begränsning av utgående nätverks trafik för Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: c65e3ad7ed02ddd4e6ed1d60628a738d333e9a9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189389"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurera utgående nätverks trafik för Azure HDInsight-kluster med hjälp av brand vägg

Den här artikeln innehåller anvisningar för att skydda utgående trafik från ditt HDInsight-kluster med hjälp av Azure-brandväggen. I stegen nedan förutsätter vi att du konfigurerar en Azure-brandvägg för ett befintligt kluster. Om du distribuerar ett nytt kluster bakom en brand vägg måste du först skapa ditt HDInsight-kluster och undernät. Följ sedan stegen i den här guiden.

## <a name="background"></a>Bakgrund

HDInsight-kluster distribueras vanligt vis i ett virtuellt nätverk. Klustret har beroenden för tjänster utanför det virtuella nätverket.

Det finns flera beroenden som kräver inkommande trafik. Inkommande hanterings trafik kan inte skickas via en brand Väggs enhet. Käll adresserna för den här trafiken är kända och publiceras [här](hdinsight-management-ip-addresses.md). Du kan också skapa regler för nätverks säkerhets grupper (NSG) med den här informationen för att skydda inkommande trafik till klustren.

De utgående trafik beroendena för HDInsight är nästan helt definierade med FQDN. Som inte har statiska IP-adresser bakom dem. Bristen på statiska adresser innebär att nätverks säkerhets grupper (NSG: er) inte kan låsa utgående trafik från ett kluster. Adresserna ändras ofta så att det inte går att skapa regler baserat på den aktuella namn matchningen och Använd.

Säkra utgående adresser med en brand vägg som kan styra utgående trafik baserat på domän namn. Azure-brandväggen begränsar utgående trafik baserat på FQDN för mål-eller FQDN- [taggarna](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurera Azure-brandvägg med HDInsight

En sammanfattning av stegen för att låsa bort från din befintliga HDInsight med Azure Firewall är:

1. Skapa ett undernät.
1. Skapa en brand vägg.
1. Lägg till program regler i brand väggen
1. Lägg till nätverks regler i brand väggen.
1. Skapa en routningstabell.

### <a name="create-new-subnet"></a>Skapa nytt undernät

Skapa ett undernät med namnet **AzureFirewallSubnet** i det virtuella nätverk där klustret finns.

### <a name="create-a-new-firewall-for-your-cluster"></a>Skapa en ny brand vägg för klustret

Skapa en brand vägg med namnet **test-FW01** med hjälp av stegen i **distribuera brand väggen** från [självstudie: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurera brand väggen med program regler

Skapa en program regel samling som gör det möjligt för klustret att skicka och ta emot viktig kommunikation.

1. Välj den nya brand Väggs **test-FW01** från Azure Portal.

1. Gå till **Inställningar** > **regler** > **program regel samling** > **+ Lägg till program regel samling**.

    ![Rubrik: Lägg till program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Ange följande information på skärmen **Lägg till program regel samling** :

    **Övre delen**

    | Egenskap|  Värde|
    |---|---|
    |Namn| FwAppRule|
    |Prioritet|200|
    |Åtgärd|Tillåt|

    **Avsnittet FQDN-Taggar**

    | Name | Käll adress | FQDN-tagg | Anteckningar |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate och HDInsight | Krävs för HDI-tjänster |

    **Avsnittet mål-FQDN**

    | Name | Käll adresser | `Protocol:Port` | Mål-FQDN | Anteckningar |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https: 443 | login.windows.net | Tillåt Windows inloggnings aktivitet |
    | Rule_3 | * | https: 443 | login.microsoftonline.com | Tillåt Windows inloggnings aktivitet |
    | Rule_4 | * | https: 443, http: 80 | storage_account_name. blob. Core. Windows. net | Ersätt `storage_account_name` med det faktiska lagrings konto namnet. Om klustret backas upp av WASB lägger du till en regel för WASB. Om du bara vill använda HTTPS-anslutningar kontrollerar du att ["säker överföring krävs"](../storage/common/storage-require-secure-transfer.md) är aktiverat på lagrings kontot. |

   ![Rubrik: Ange information om program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Välj **Lägg till**.

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurera brand väggen med nätverks regler

Skapa nätverks reglerna för att konfigurera HDInsight-klustret på rätt sätt.

1. Fortsätt från föregående steg, gå till **regel samling** > för nätverk **+ Lägg till nätverks regel samling**.

1. Ange följande information på skärmen **Lägg till regel samling för nätverk** :

    **Övre delen**

    | Egenskap|  Värde|
    |---|---|
    |Namn| FwNetRule|
    |Prioritet|200|
    |Åtgärd|Tillåt|

    **Avsnittet IP-adresser**

    | Name | Protokoll | Käll adresser | Mål adresser | Målportar | Anteckningar |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Tids tjänst |
    | Rule_2 | Alla | * | DC_IP_Address_1 DC_IP_Address_2 | * | Om du använder Enterprise Security Package (ESP) lägger du till en nätverks regel i avsnittet IP-adresser som tillåter kommunikation med AAD-DS för ESP-kluster. Du hittar IP-adresserna för domän kontrol Lanterna i AAD-DS-avsnittet i portalen |
    | Rule_3 | TCP | * | IP-adress för ditt Data Lake Storage konto | * | Om du använder Azure Data Lake Storage kan du lägga till en nätverks regel i avsnittet IP-adresser för att åtgärda ett SNI-problem med ADLS Gen1 och Gen2. Det här alternativet dirigerar trafiken till brand väggen. Detta kan resultera i högre kostnader för stora data belastningar, men trafiken loggas och granskas i brand Väggs loggar. Identifiera IP-adressen för ditt Data Lake Storage-konto. Du kan använda ett PowerShell-kommando `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` för att matcha FQDN till en IP-adress.|
    | Rule_4 | TCP | * | * | 12000 | Valfritt Om du använder Log Analytics skapar du en nätverks regel i avsnittet IP-adresser för att aktivera kommunikation med arbets ytan Log Analytics. |

    **Avsnittet service Tags**

    | Name | Protokoll | Källadresser | Tjänsttaggar | Mål portar | Anteckningar |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Konfigurera en nätverks regel i avsnittet service märken för SQL som gör att du kan logga och granska SQL-trafik. Om du inte har konfigurerat tjänst slut punkter för SQL Server i HDInsight-undernätet, vilket kringgår brand väggen. |

   ![Rubrik: Ange program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Välj **Lägg till**.

### <a name="create-and-configure-a-route-table"></a>Skapa och konfigurera en routningstabell

Skapa en routningstabell med följande poster:

* Alla IP-adresser från [hälso-och hanterings tjänster: alla regioner](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) med en nästa hopp typ av **Internet**.

* Två IP-adresser för den region där klustret skapas från [hälso-och hanterings tjänster: vissa regioner](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) med nästa hopp typ av **Internet**.

* En virtuell enhets väg för IP-adressen 0.0.0.0/0 med nästa hopp som din Azure Firewall-privata IP-adress.

Om du till exempel vill konfigurera routningstabellen för ett kluster som skapats i regionen USA, östra, använder du följande steg:

1. Välj din Azure Firewall **test-FW01**. Kopiera den **privata IP-adressen** som visas på sidan **Översikt** . I det här exemplet ska vi använda en **exempel adress för 10.0.2.4**.

1. Navigera sedan till **alla tjänster** > **nätverks** > **flödes tabeller** och **skapa routningstabell**.

1. Från din nya väg går du till **Inställningar** > **vägar** > **+ Lägg till**. Lägg till följande vägar:

| Vägnamn | Adressprefix | Nexthop-typ | Nexthop-adress |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Ej tillämpligt |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Ej tillämpligt |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Ej tillämpligt |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Ej tillämpligt |
| 13.82.225.233 | 13.82.225.233/32 | Internet | Ej tillämpligt |
| 40.71.175.99 | 40.71.175.99/32 | Internet | Ej tillämpligt |
| 0.0.0.0 | 0.0.0.0/0 | Virtuell installation | 10.0.2.4 |

Slutför konfigureringen av routningstabellen:

1. Tilldela routningstabellen som du skapade i ditt HDInsight-undernät genom att välja **undernät** under **Inställningar**.

1. Välj **+ associera**.

1. På skärmen **associera undernät** väljer du det virtuella nätverk som klustret skapades i. Och **under nätet** som du använde för ditt HDInsight-kluster.

1. Välj **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-Node eller anpassad program trafik

Ovanstående steg gör att klustret kan köras utan problem. Du måste fortfarande konfigurera beroenden för att anpassa dina anpassade program som körs på Edge-noderna, om tillämpligt.

Program beroenden måste identifieras och läggas till i Azure-brandväggen eller i routningstabellen.

Vägar måste skapas för att program trafiken ska undvika problem med asymmetrisk routning.

Om dina program har andra beroenden måste de läggas till i din Azure-brandvägg. Skapa program regler för att tillåta HTTP/HTTPS-trafik och nätverks regler för allt annat.

## <a name="logging-and-scale"></a>Loggning och skalning

Azure-brandväggen kan skicka loggar till några olika lagrings system. Anvisningar om hur du konfigurerar loggning för brand väggen finns i [Självstudier: övervaka Azure Firewall-loggar och-mått](../firewall/tutorial-diagnostics.md).

När du har slutfört loggnings konfigurationen, om du använder Log Analytics, kan du Visa blockerad trafik med en fråga som:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Att integrera Azure-brandväggen med Azure Monitor loggar är användbart när du först får ett program att fungera. Särskilt när du inte känner till alla program beroenden. Du kan lära dig mer om Azure Monitor loggar från [analysera loggdata i Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Mer information om skalnings gränserna för Azure-brandväggen och begär Anden ökar finns i [det här](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) dokumentet eller [vanliga frågor och svar](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Åtkomst till klustret

När brand väggen har kon figurer ATS kan du använda den interna slut punkten (`https://CLUSTERNAME-int.azurehdinsight.net`) för att komma åt Ambari inifrån det virtuella nätverket.

Om du vill använda den offentliga`https://CLUSTERNAME.azurehdinsight.net`slut punkten () eller`CLUSTERNAME-ssh.azurehdinsight.net`SSH-slutpunkten () kontrollerar du att du har rätt vägar i routningstabellen och NSG-regler för att undvika problemet med asymmetrisk routning som beskrivs [här](../firewall/integrate-lb.md). I detta fall måste du tillåta klientens IP-adress i reglerna för inkommande NSG och även lägga till den i den användardefinierade routningstabellen med nästa hopp uppsättning som `internet`. Om routningen inte är korrekt konfigurerad visas ett tids gräns fel.

## <a name="configure-another-network-virtual-appliance"></a>Konfigurera en annan virtuell nätverks installation

> [!Important]
> Följande information krävs **bara** om du vill konfigurera en annan virtuell nätverks installation (NVA) än Azure Firewall.

I föregående instruktioner kan du konfigurera Azure-brandväggen för att begränsa utgående trafik från HDInsight-klustret. Azure-brandväggen konfigureras automatiskt för att tillåta trafik för många av de vanliga viktiga scenarierna. Om du använder en annan virtuell nätverks installation måste du konfigurera ett antal ytterligare funktioner. Tänk på följande faktorer när du konfigurerar din virtuella nätverks installation:

* Tjänst slut punkts tjänster som stöder tjänster måste konfigureras med tjänst slut punkter.
* IP-adress beroenden är för trafik som inte är HTTP/S (både TCP-och UDP-trafik).
* FQDN HTTP/HTTPS-slutpunkter kan placeras i din NVA-enhet.
* HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera beroende på ett antal kvalificerare.
* Tilldela den routningstabell som du skapar till ditt HDInsight-undernät.

### <a name="service-endpoint-capable-dependencies"></a>Tjänst slut punkt kompatibla beroenden

| **Endpoint** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-adress beroenden

| **Endpoint** | **Information** |
|---|---|
| \*: 123 | Kontroll av NTP-klocka. Trafiken kontrol leras på flera slut punkter på port 123 |
| IP-adresser publicerade [här](hdinsight-management-ip-addresses.md) | De här IP-adresserna är HDInsight-tjänsten |
| AAD – DS privata IP-adresser för ESP-kluster |
| \*: 16800 för Windows-aktivering i KMS |
| \*12000 för Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden

> [!Important]
> Listan nedan innehåller bara några av de viktigaste FQDN-namnen. Du kan hämta ytterligare FQDN (Azure Storage och Azure Service Bus) för att konfigurera din NVA [i den här filen](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Endpoint**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Nästa steg

* [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)
