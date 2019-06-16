---
title: Konfigurera utgående trafik nätverksbegränsning för Azure HDInsight-kluster
description: Lär dig hur du konfigurerar utgående trafik nätverksbegränsning för Azure HDInsight-kluster.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/30/2019
ms.openlocfilehash: 542813e0f82a1a52142a2b82bea3fdb101fdec28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077163"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Konfigurera utgående nätverkstrafik för Azure HDInsight-kluster med brandvägg (förhandsversion)

Den här artikeln innehåller anvisningar för att skydda dina utgående trafik från ditt HDInsight-kluster med hjälp av Azure-brandvägg. Stegen nedan förutsätter att du konfigurerar ett Azure-brandväggen för ett befintligt kluster. Om du distribuerar ett nytt kluster och bakom en brandvägg, skapa ditt HDInsight-kluster och undernätet först och följ sedan stegen i den här guiden.

## <a name="background"></a>Bakgrund

Azure HDInsight-kluster distribueras normalt i ditt eget virtuella nätverk. Klustret har beroenden på tjänster utanför det virtuella nätverket som kräver nätverksåtkomst för att fungera korrekt.

Det finns flera beroenden som kräver inkommande trafik. Inkommande hanteringstrafik kan inte skickas via en brandväggsenhet. Källadresser för den här trafiken är kända och publiceras [här](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Du kan också skapa regler för Nätverkssäkerhetsgrupp (NSG) med den här informationen för att skydda inkommande trafik till klustren.

HDInsight utgående trafik beroenden definieras nästan helt och hållet med FQDN: er som inte har den statiska IP-adresser bakom dem. Bristen på statiska adresser innebär att Nätverkssäkerhetsgrupper (NSG) inte kan användas för att låsa den utgående trafiken från ett kluster. Adresserna ändras tillräckligt ofta att det går inte att ställa in regler baserat på den aktuella namnmatchningen och använda den för att konfigurera NSG-regler.

Lösning för att skydda utgående adresser är att använda en brandväggsenhet som kan styra utgående trafik baserat på domännamn. Azure-brandväggen kan begränsa utgående HTTP och HTTPS-trafik baserat på det fullständiga Domännamnet för målet eller [FQDN taggar](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurera Brandvägg för Azure med HDInsight

Det finns en sammanfattning av stegen för att låsa utgående trafiken från dina befintliga HDInsight med Azure-brandvägg:
1. Skapa en brandvägg.
1. Lägga till regler för program i brandväggen
1. Lägga till regler i brandväggen.
1. Skapa en routningstabell.

### <a name="create-a-new-firewall-for-your-cluster"></a>Skapa en ny Brandvägg för ditt kluster

1. Skapa ett undernät med namnet **AzureFirewallSubnet** i det virtuella nätverket där klustret finns. 
1. Skapa en ny brandväggsregel **Test-FW01** med hjälp av stegen i [självstudien: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurera brandväggen med regler för program

Skapa en regelsamling för program som gör att klustret för att skicka och ta emot viktiga kommunikation.

Välj ny brandvägg **Test FW01** från Azure-portalen. Klicka på **regler** under **inställningar** > **regelsamling för programmet** > **lägga till programmet regelsamlingen**.

![Rubrik: Lägg till regel programsamling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

På den **lägga till programmet regelsamlingen** skärmen, gör du följande:

1. Ange en **namn**, **prioritet**, och klicka på **Tillåt** från den **åtgärd** listrutan och ange följande regler i **FQDN taggar avsnittet** :

   | **Namn** | **Källadress** | **FQDN Tag** | **Anteckningar** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight och WindowsUpdate | Krävs för HDI-tjänster |

1. Lägg till följande regler för att den **Target FQDN avsnittet** :

   | **Namn** | **Källadress** | **Protokoll: Port** | **Målets fullständiga domännamn** | **Anteckningar** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https:443 | login.windows.net | Tillåter Windows inloggningsaktivitet |
   | Rule_3 | * | https:443,http:80 | <storage_account_name.blob.core.windows.net> | Om ditt kluster backas upp av WASB, sedan lägga till en regel för WASB. För att använda endast https-anslutningar kan du till att [”säker överföring krävs”](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverat för lagringskontot. |

1. Klicka på **Lägg till**.

   ![Rubrik: Ange programinformation regeln samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurera brandväggen med Nätverksregler

Skapa regler för network för att korrekt konfigurera ditt HDInsight-kluster.

1. Välj ny brandvägg **Test FW01** från Azure-portalen.
1. Klicka på **regler** under **inställningar** > **Network regelsamlingen** > **Lägg till nätverk regelsamlingen**.
1. På den **Lägg till nätverk regelsamlingen** anger en **namn**, **prioritet**, och klicka på **Tillåt** från den **åtgärd** nedrullningsbara menyn.
1. Skapa följande regler i den **IP-adresser** avsnittet:

   | **Namn** | **Protokoll** | **Källadress** | **Måladress** | **Målport** | **Anteckningar** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Tidstjänst |
   | Rule_2 | Alla | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Om du använder Enterprise Security Package (ESP), sedan lägga till en regel i avsnittet IP-adresser som tillåter kommunikation med AAD-DS för ESP-kluster. Du hittar IP-adresserna för domänkontrollanterna i AAD-DS-avsnittet i portalen | 
   | Rule_3 | TCP | * | IP-adressen för ditt Data Lake Storage-konto | `*` | Om du använder Azure Data Lake Storage kan du lägga till en regel i avsnittet IP-adresser för att åtgärda ett SNI-problem med ADLS Gen1 och Gen2. Det här alternativet dirigerar trafiken till brandväggen som kan leda till högre kostnader för stora databelastningar men trafiken kommer att loggas och granskningsbar i loggar från brandväggen. Kontrollera IP-adressen för ditt Data Lake Storage-konto. Du kan använda ett powershell-kommando som `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` att matcha detta FQDN till en IP-adress.|
   | Rule_4 | TCP | * | * | `12000` | (Valfritt) Om du använder Log Analytics kan skapa du en regel i avsnittet IP-adresser om du vill aktivera kommunikation med Log Analytics-arbetsytan. |

1. Skapa följande regler i den **Tjänsttaggar** avsnittet:

   | **Namn** | **Protokoll** | **Källadress** | **Tjänsttaggar** | **Målport** | **Anteckningar** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | * | `1433,11000-11999,14000-14999` | Konfigurera en regel i avsnittet Tjänsttaggar för SQL som gör att du kan logga in och granska SQL-trafik, såvida inte du har konfigurerat Tjänsteslutpunkter för SQL Server på HDInsight-undernät som ska passera brandväggen. |

1. Klicka på **Lägg till** för att skapa regelsamlingen nätverk.

   ![Rubrik: Ange programinformation regeln samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Skapa och konfigurera en routningstabell

Skapa en routningstabell med följande poster:

1. Sex adresser från [den här listan över obligatoriska HDInsight hantering av IP-adresserna](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) med ett nexthop för **Internet**:
    1. Fyra IP-adresser för alla kluster i alla regioner
    1. Två IP-adresser som är specifika för den region där klustret har skapats
1. En virtuell installation väg för IP-adress 0.0.0.0/0 med nästa hopp som din privata IP-adress för Brandvägg för Azure.

Till exempel för att konfigurera routningstabellen för ett kluster som skapats i regionen USA för ”USA, centrala”, använder du följande steg:

1. Logga in på Azure Portal.
1. Välj din Azure-brandväggen **Test-FW01**. Kopiera den **privat IP-adress** visas på den **översikt** sidan. I det här exemplet ska vi använda en **exempel 10.1.1.4-adress**
1. Skapa en ny routningstabell.
1. Klicka på **vägar** under **inställningar**.
1. Klicka på **Lägg till** skapa vägar för IP-adresser i tabellen nedan.

| Vägnamn | Adressprefix | Nexthop-typ | Nexthop-adress |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Saknas |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Saknas |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Saknas |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Saknas |
| 13.67.223.215 | 13.67.223.215/32 | Internet | Saknas |
| 40.86.83.253 | 40.86.83.253/32 | Internet | Saknas |
| 0.0.0.0 | 0.0.0.0/0 | Virtuell installation | 10.1.1.4 |

Slutför routningskonfigurationen för tabellen:

1. Tilldela routningstabellen som du skapade till ditt HDInsight-undernät genom att klicka på **undernät** under **inställningar** och sedan **associera**.
1. På den **Associera undernät** , väljer det virtuella nätverket som klustret har skapats i och **HDInsight undernät** du använde för ditt HDInsight-kluster.
1. Klicka på **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-nod eller anpassade programtrafik

Stegen ovan kan klustret för att fungera utan problem. Du måste fortfarande konfigurera beroenden för att hantera dina anpassade program som körs på edge-noder, om tillämpligt.

Programberoenden måste identifieras och läggas till i Azure-brandväggen eller routningstabellen.

Vägar måste skapas för programtrafik att undvika problem med asymmetrisk routning.

Om ditt program har andra beroenden, måste läggas till din Azure-brandvägg. Skapa regler för att tillåta HTTP/HTTPS-trafik och nätverk regler för allt annat.

## <a name="logging"></a>Loggning

Azure-brandväggen kan skicka loggar till ett par olika lagringssystem. För instruktioner om hur du konfigurerar loggning för brandväggen, Följ stegen i [självstudien: Övervaka Azure-brandväggen loggar och mått](../firewall/tutorial-diagnostics.md).

När du har slutfört installationen loggning om du logga data till Log Analytics kan visa du blockerad trafik med en fråga till exempel följande:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrera din Azure-brandvägg med Azure Monitor-loggar är användbart när du först hämtar ett program som fungerar när du inte är medvetna om alla beroenden för programmet. Du kan läsa mer om Azure Monitor-loggar från [analysera loggdata i Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="access-to-the-cluster"></a>Åtkomst till klustret
Du kan använda den interna slutpunkten efter att ha konfigurationen av brandvägg har (`https://<clustername>-int.azurehdinsight.net`) att komma åt Ambari från det virtuella nätverket. Du använder den offentliga slutpunkten (`https://<clustername>.azurehdinsight.net`) eller ssh slutpunkt (`<clustername>-ssh.azurehdinsight.net`), kontrollera att du har rätt vägar i routningstabellen och NSG-regler inställningar för att undvika assymetric routningsproblem som förklaras [här](https://docs.microsoft.com/azure/firewall/integrate-lb).

## <a name="configure-another-network-virtual-appliance"></a>Konfigurera en annan virtuell nätverksinstallation

>[!Important]
> Följande information är **endast** krävs om du vill konfigurera en virtuell nätverksinstallation (NVA) än Azure-brandväggen.

De föregående anvisningarna hjälper dig att konfigurera Azure-brandväggen för att begränsa utgående trafik från ditt HDInsight-kluster. Azure-brandväggen konfigureras automatiskt för att tillåta trafik för många vanliga viktiga scenarier. Om du vill använda en annan virtuell nätverksinstallation behöver du manuellt konfigurera ett antal ytterligare funktioner. Tänk på följande som din konfigurera ditt nätverks virtuella installation:

* Tjänsteslutpunkt kan tjänster konfigureras med Tjänsteslutpunkter.
* IP-adress beroenden är för icke-HTTP/S-trafik (både TCP och UDP-trafik).
* FQDN HTTP/HTTPS-slutpunkter kan placeras i NVA-enheten.
* Jokertecken HTTP/HTTPS-slutpunkterna är beroenden som kan variera baserat på ett antal kvalificerare.
* Tilldela routningstabellen som du skapar HDInsight-undernätet.

### <a name="service-endpoint-capable-dependencies"></a>Tjänsteslutpunkt kan beroenden

| **Slutpunkt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-adress beroenden

| **Slutpunkt** | **Detaljer** |
|---|---|
| \*:123 | NTP-klockkontrollen. Trafiken är markerad på flera slutpunkter på porten 123 |
| IP-adresser som publicerats [här](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | Det här är HDInsight-tjänst |
| AAD-DS privata IP-adresser för ESP-kluster |
| \*: 16800 för KMS-aktivering för Windows |
| \*12000 för Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden

>[!Important]
> I listan nedan ger endast några av de viktigaste fullständigt bestämda domännamnen. Du kan få en fullständig lista över FQDN: er för att konfigurera din NVA [i den här filen](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

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
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Nästa steg

* [Azure HDInsight virtuella nätverksarkitekturen](hdinsight-virtual-network-architecture.md)
