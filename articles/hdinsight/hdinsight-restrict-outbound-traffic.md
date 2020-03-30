---
title: Konfigurera begränsning av utgående nätverkstrafik – Azure HDInsight
description: Lär dig hur du konfigurerar begränsning av utgående nätverkstrafik för Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6e0c98cffef06fb6d6345fc2b23bbc22715909b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370193"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurera utgående nätverkstrafik för Azure HDInsight-kluster med brandvägg

Den här artikeln innehåller stegen för att skydda utgående trafik från ditt HDInsight-kluster med Azure-brandväggen. Stegen nedan förutsätter att du konfigurerar om en Azure-brandvägg för ett befintligt kluster. Om du distribuerar ett nytt kluster och bakom en brandvägg skapar du hdinsight-klustret och undernätet först och följer sedan stegen i den här guiden.

## <a name="background"></a>Bakgrund

Azure HDInsight-kluster distribueras normalt i ditt eget virtuella nätverk. Klustret har beroenden på tjänster utanför det virtuella nätverket som kräver nätverksåtkomst för att fungera korrekt.

Det finns flera beroenden som kräver inkommande trafik. Den inkommande hanteringstrafiken kan inte skickas via en brandväggsenhet. Källadresserna för den här trafiken är kända och publiceras [här](hdinsight-management-ip-addresses.md). Du kan också skapa NSG-regler (Network Security Group) med den här informationen för att skydda inkommande trafik till klustren.

HDInsight-utgående trafikberoenden definieras nästan helt med FQDN:er, som inte har statiska IP-adresser bakom sig. Bristen på statiska adresser innebär att NSG(Network Security Groups) inte kan användas för att låsa den utgående trafiken från ett kluster. Adresserna ändras ofta nog att man inte kan ställa in regler baserat på den aktuella namnmatchningen och använda den för att ställa in NSG-regler.

Lösningen för att skydda utgående adresser är att använda en brandväggsenhet som kan styra utgående trafik baserat på domännamn. Azure-brandväggen kan begränsa utgående HTTP- och HTTPS-trafik baserat på FQDN-taggarna för målet eller [FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurera Azure-brandväggen med HDInsight

En sammanfattning av stegen för att låsa utgående från din befintliga HDInsight med Azure-brandväggen är:

1. Skapa ett undernät.
1. Skapa en brandvägg.
1. Lägga till programregler i brandväggen
1. Lägg till nätverksregler i brandväggen.
1. Skapa en routningstabell.

### <a name="create-new-subnet"></a>Skapa nytt undernät

Skapa ett undernät med namnet **AzureFirewallSubnet** i det virtuella nätverket där klustret finns.

### <a name="create-a-new-firewall-for-your-cluster"></a>Skapa en ny brandvägg för ditt kluster

Skapa en brandvägg med namnet **Test-FW01** med hjälp av stegen i **Distribuera brandväggen** från [självstudiekurs: Distribuera och konfigurera Azure-brandväggen med Azure-portalen](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurera brandväggen med programregler

Skapa en programregelsamling som gör att klustret kan skicka och ta emot viktig kommunikation.

1. Välj den nya brandväggen **Test-FW01** från Azure-portalen.

1. Navigera till**Rules** > **Regelsamling** >  **för Inställningar** > Program **+ Lägg till programregelsamling**.

    ![Titel: Lägga till insamling av programregel](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Ange följande information på skärmen **Lägg till programregelsamling:**

    **Övre avsnittet**

    | Egenskap|  Värde|
    |---|---|
    |Namn| FwAppRule (fwAppRule)|
    |Prioritet|200|
    |Åtgärd|Tillåt|

    **Avsnittet FQDN-taggar**

    | Namn | Källa adress | FQDN-tagg | Anteckningar |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate och HDInsight | Krävs för HDI-tjänster |

    **Avsnittet Mål-FQDN**

    | Namn | Källadresser | Protokoll:Port | Mål FQDNS | Anteckningar |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Tillåter Windows-inloggningsaktivitet |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Tillåter Windows-inloggningsaktivitet |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Ersätt `storage_account_name` med ditt faktiska lagringskontonamn. Om klustret backas upp av WASB lägger du till en regel för WASB. Om du vill använda ENDAST https-anslutningar kontrollerar du att ["säker överföring krävs"](../storage/common/storage-require-secure-transfer.md) är aktiverat på lagringskontot. |

   ![Titel: Ange information om insamling av programregel](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Välj **Lägg till**.

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurera brandväggen med nätverksregler

Skapa nätverksregler för att konfigurera HDInsight-klustret på rätt sätt.

1. Fortsätt från föregående steg, navigera till **Nätverksregelsamling** > **+ Lägg till nätverksregelsamling**.

1. Ange följande information på skärmen **Lägg till samling av nätverksregel:**

    **Övre avsnittet**

    | Egenskap|  Värde|
    |---|---|
    |Namn| FwNetRule (fwNetRule)|
    |Prioritet|200|
    |Åtgärd|Tillåt|

    **Avsnittet IP-adresser**

    | Namn | Protokoll | Källadresser | Måladresser | Målportar | Anteckningar |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Tidsservice |
    | Rule_2 | Alla | * | DC_IP_Address_1, DC_IP_Address_2 | * | Om du använder ESP (Enterprise Security Package) lägger du till en nätverksregel i avsnittet IP-adresser som möjliggör kommunikation med AAD-DS för ESP-kluster. Du hittar IP-adresserna för domänkontrollanterna i AAD-DS-avsnittet i portalen |
    | Rule_3 | TCP | * | IP-adress för ditt datasjölagringskonto | * | Om du använder Azure Data Lake Storage kan du lägga till en nätverksregel i avsnittet IP-adresser för att lösa ett SNI-problem med ADLS Gen1 och Gen2. Det här alternativet dirigerar trafiken till brandväggen, vilket kan leda till högre kostnader för stora databelastningar, men trafiken loggas och granskas i brandväggsloggar. Bestäm IP-adressen för ditt Data Lake Storage-konto. Du kan använda ett powershell-kommando, till exempel `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` för att matcha FQDN till en IP-adress.|
    | Rule_4 | TCP | * | * | 12000 | (Valfritt) Om du använder Log Analytics skapar du en nätverksregel i avsnittet IP-adresser för att aktivera kommunikation med logganalysarbetsytan. |

    **Avsnittet Servicemärken**

    | Namn | Protokoll | Källadresser | Tjänsttaggar | Målportar | Anteckningar |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Konfigurera en nätverksregel i avsnittet Tjänsttaggar för SQL som gör att du kan logga och granska SQL-trafik, såvida du inte har konfigurerat tjänstslutpunkter för SQL Server i HDInsight-undernätet, vilket kommer att kringgå brandväggen. |

   ![Titel: Ange insamling av programregel](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Välj **Lägg till**.

### <a name="create-and-configure-a-route-table"></a>Skapa och konfigurera en flödestabell

Skapa en flödestabell med följande poster:

* Alla IP-adresser från [hälso- och hanteringstjänster: Alla regioner](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) med nästa hoptyp av **Internet**.

* Två IP-adresser för den region där klustret skapas från [hälso- och hanteringstjänster: Specifika regioner](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) med nästa hoptyp av **Internet**.

* En virtuell installation väg för IP-adress 0.0.0.0/0 med nästa hopp är din Azure Firewall privat IP-adress.

Om du till exempel vill konfigurera vägtabellen för ett kluster som skapats i den amerikanska regionen "Östra USA" gör du så här:

1. Välj din **Azure-brandvägg Test-FW01**. Kopiera den **privata IP-adressen** som visas på **översiktssidan.** I det här exemplet använder vi en **exempeladress på 10.0.2.4**.

1. Navigera sedan till **alla tjänster** > **Nätverksvägtabeller** **Networking** > och **Skapa rutttabell**.

1. Från den nya rutten navigerar du till **Inställningar** > **rutter** > **+ Lägg till**. Lägg till följande vägar:

| Vägnamn | Adressprefix | Nexthop-typ | Nexthop-adress |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Ej tillämpligt |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Ej tillämpligt |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Ej tillämpligt |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Ej tillämpligt |
| 13.82.225.233 | 13.82.225.233/32 | Internet | Ej tillämpligt |
| 40.71.175.99 | 40.71.175.99/32 | Internet | Ej tillämpligt |
| 0.0.0.0 | 0.0.0.0/0 | Virtuell installation | 10.0.2.4 |

Slutför konfigurationen av vägtabellen:

1. Tilldela den vägtabell som du skapade till undernätet HDInsight genom att välja **Undernät** under **Inställningar**.

1. Välj **+ Associera**.

1. På skärmen **Associera undernät** väljer du det virtuella nätverk som klustret skapades till och **det undernät** som du använde för HDInsight-klustret.

1. Välj **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-nod eller anpassad programtrafik

Ovanstående steg gör det möjligt för klustret att fungera utan problem. Du måste fortfarande konfigurera beroenden så att de passar dina anpassade program som körs på kantnoderna, om tillämpligt.

Programberoenden måste identifieras och läggas till i Azure-brandväggen eller vägtabellen.

Vägar måste skapas för programtrafiken för att undvika asymmetriska routningsproblem.

Om dina program har andra beroenden måste de läggas till i din Azure-brandvägg. Skapa programregler för att tillåta HTTP/HTTPS-trafik och nätverksregler för allt annat.

## <a name="logging-and-scale"></a>Loggning och skala

Azure-brandväggen kan skicka loggar till några olika lagringssystem. Instruktioner om hur du konfigurerar loggning för brandväggen följer du stegen i [Självstudiekursen: Övervaka Azure-brandväggsloggar och mått](../firewall/tutorial-diagnostics.md).

När du har slutfört loggningsinställningarna, om du loggar data till Log Analytics, kan du visa blockerad trafik med en fråga som följande:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Det är användbart att integrera din Azure-brandvägg med Azure Monitor-loggar när du först får ett program att fungera när du inte är medveten om alla programberoenden. Du kan läsa mer om Azure Monitor-loggar från [Analysera loggdata i Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Mer information om skalningsgränserna för Azure-brandväggen och begäransökningar finns i [det här](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) dokumentet eller refererar till vanliga frågor och [svar](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Tillgång till klustret

När brandväggen har konfigurerats kan du använda`https://CLUSTERNAME-int.azurehdinsight.net`den interna slutpunkten ( ) för att komma åt Ambari inifrån det virtuella nätverket.

Om du vill använda`https://CLUSTERNAME.azurehdinsight.net`den offentliga slutpunkten`CLUSTERNAME-ssh.azurehdinsight.net`( ) eller ssh-ändpunkten ( ) kontrollerar du att du har rätt vägar i flödestabellen och NSG-reglerna för att undvika det asymmetriska routningsproblem som förklaras [här](../firewall/integrate-lb.md). I det här fallet måste du tillåta klient-IP-adressen i reglerna för inkommande NSG och även lägga till `internet`den i den användardefinierade vägtabellen med nästa hopp som . Om detta inte är korrekt konfigurerat visas ett timeout-fel.

## <a name="configure-another-network-virtual-appliance"></a>Konfigurera en virtuell nätverksinstallation

> [!Important]
> Följande information krävs **endast** om du vill konfigurera en virtuell nätverksinstallation (NVA) förutom Azure-brandväggen.

De tidigare instruktionerna hjälper dig att konfigurera Azure-brandväggen för att begränsa utgående trafik från ditt HDInsight-kluster. Azure-brandväggen konfigureras automatiskt för att tillåta trafik för många av de vanliga viktiga scenarierna. Om du vill använda en annan virtuell nätverksinstallation måste du konfigurera ett antal ytterligare funktioner manuellt. Tänk på följande när du konfigurerar den virtuella nätverksinstallationen:

* Tjänstslutpunktskompatibla tjänster bör konfigureras med tjänstslutpunkter.
* IP-adressberoenden är för trafik som inte är HTTP/S (både TCP- och UDP-trafik).
* FQDN HTTP/HTTPS-slutpunkter kan placeras i DIN NVA-enhet.
* HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera beroenden på ett antal kvalificerare.
* Tilldela den vägtabell som du skapar till ditt HDInsight-undernät.

### <a name="service-endpoint-capable-dependencies"></a>Beroenden för tjänstslutpunktskompatibla

| **Slutpunkt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-adressberoenden

| **Slutpunkt** | **Detaljer** |
|---|---|
| \*:123 | NTP-klockkontroll. Trafiken kontrolleras vid flera slutpunkter på port 123 |
| IPs publiceras [här](hdinsight-management-ip-addresses.md) | Dessa är HDInsight-tjänsten |
| AAD-DS privata IPs för ESP-kluster |
| \*:16800 för KMS Windows-aktivering |
| \*12000 för Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden

> [!Important]
> Listan nedan ger bara några av de viktigaste FQDNs. Du kan få en fullständig lista över FQDN för att konfigurera din NVA [i den här filen](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Slutpunkt**                                                          |
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

* [Virtuell nätverksarkitektur i Azure HDInsight](hdinsight-virtual-network-architecture.md)
