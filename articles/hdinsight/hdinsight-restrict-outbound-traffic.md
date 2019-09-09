---
title: Konfigurera begränsning av utgående nätverks trafik för Azure HDInsight-kluster
description: Lär dig hur du konfigurerar begränsning av utgående nätverks trafik för Azure HDInsight-kluster.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 829f3e730b4993a6a7f32a9224d3c6c38bd4c06e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811954"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Konfigurera utgående nätverks trafik för Azure HDInsight-kluster med hjälp av brand vägg (för hands version)

Den här artikeln innehåller anvisningar för att skydda utgående trafik från ditt HDInsight-kluster med hjälp av Azure-brandväggen. Stegen nedan förutsätter att du konfigurerar en Azure-brandvägg för ett befintligt kluster. Om du distribuerar ett nytt kluster och bakom en brand vägg måste du först skapa ditt HDInsight-kluster och-undernät och sedan följa stegen i den här hand boken.

## <a name="background"></a>Bakgrund

Azure HDInsight-kluster distribueras vanligt vis i ditt eget virtuella nätverk. Klustret har beroenden för tjänster utanför det virtuella nätverket som kräver nätverks åtkomst för att fungera korrekt.

Det finns flera beroenden som kräver inkommande trafik. Inkommande hanterings trafik kan inte skickas via en brand Väggs enhet. Käll adresserna för den här trafiken är kända och publiceras [här](hdinsight-management-ip-addresses.md). Du kan också skapa regler för nätverks säkerhets grupper (NSG) med den här informationen för att skydda inkommande trafik till klustren.

De utgående trafik beroendena för HDInsight är nästan helt definierade med FQDN, som inte har statiska IP-adresser bakom dem. Bristen på statiska adresser innebär att nätverks säkerhets grupper (NSG: er) inte kan användas för att låsa utgående trafik från ett kluster. Adresserna ändras ofta nog för att det inte går att konfigurera regler baserat på den aktuella namn matchningen och använda den för att konfigurera NSG-regler.

Lösningen för att skydda utgående adresser är att använda en brand Väggs enhet som kan styra utgående trafik baserat på domän namn. Azure-brandväggen kan begränsa utgående HTTP-och HTTPS-trafik baserat på FQDN för mål-eller [FQDN-taggarna](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurera Azure-brandvägg med HDInsight

En sammanfattning av stegen för att låsa bort från din befintliga HDInsight med Azure Firewall är:
1. Skapa en brand vägg.
1. Lägg till program regler i brand väggen
1. Lägg till nätverks regler i brand väggen.
1. Skapa en routningstabell.

### <a name="create-a-new-firewall-for-your-cluster"></a>Skapa en ny brand vägg för klustret

1. Skapa ett undernät med namnet **AzureFirewallSubnet** i det virtuella nätverk där klustret finns. 
1. Skapa en ny brand vägg **test-FW01** med hjälp av [stegen i själv studie kursen: Distribuera och konfigurera Azure-brandväggen med hjälp](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)av Azure Portal.

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurera brand väggen med program regler

Skapa en program regel samling som gör det möjligt för klustret att skicka och ta emot viktig kommunikation.

Välj den nya brand Väggs **test-FW01** från Azure Portal. Klicka på **regler** under **Inställningar** > **program regel samling** > **Lägg till program regel samling**.

![Avdelning Lägg till programregelsamling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Utför följande steg på skärmen **Lägg till program regel samling** :

1. Ange ett **namn**, **prioritet**och klicka på **Tillåt** i list rutan med **Åtgärds** menyn och ange följande regler i **avsnittet FQDN-Taggar** :

   | **Namn** | **Käll adress** | **FQDN-tagg** | **Anteckningar** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight och WindowsUpdate | Krävs för HDI-tjänster |

1. Lägg till följande regler i **avsnittet mål-FQDN** :

   | **Namn** | **Käll adress** | **Protokoll: port** | **Mål-FQDN** | **Anteckningar** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https: 443 | login.windows.net | Tillåt Windows inloggnings aktivitet |
   | Rule_3 | * | https: 443, http: 80 | <storage_account_name.blob.core.windows.net> | Om klustret backas upp av WASB lägger du till en regel för WASB. Om du bara vill använda HTTPS-anslutningar kontrollerar du att ["säker överföring krävs"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverat på lagrings kontot. |

1. Klicka på **Lägg till**.

   ![Avdelning Ange information om samling av program regel](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurera brand väggen med nätverks regler

Skapa nätverks reglerna för att konfigurera HDInsight-klustret på rätt sätt.

1. Välj den nya brand Väggs **test-FW01** från Azure Portal.
1. Klicka på **regler** under **Inställningar** > **nätverks regel samling** > **Lägg till nätverks regel samling**.
1. På skärmen **Lägg till regel samling för nätverk** anger du ett **namn**, **prioritet**och klickar på **Tillåt** i list rutan med **Åtgärds** menyn.
1. Skapa följande regler i avsnittet **IP-adresser** :

   | **Namn** | **Protokoll** | **Käll adress** | **Mål adress** | **Målport** | **Anteckningar** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Tids tjänst |
   | Rule_2 | Any | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Om du använder Enterprise Security Package (ESP) lägger du till en nätverks regel i avsnittet IP-adresser som tillåter kommunikation med AAD-DS för ESP-kluster. Du hittar IP-adresserna för domän kontrol Lanterna i AAD-DS-avsnittet i portalen | 
   | Rule_3 | TCP | * | IP-adress för ditt Data Lake Storage konto | `*` | Om du använder Azure Data Lake Storage kan du lägga till en nätverks regel i avsnittet IP-adresser för att åtgärda ett SNI-problem med ADLS Gen1 och Gen2. Det här alternativet dirigerar trafiken till brand väggen som kan resultera i högre kostnader för stora data belastningar, men trafiken loggas och granskas i brand Väggs loggar. Identifiera IP-adressen för ditt Data Lake Storage-konto. Du kan använda ett PowerShell-kommando `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` för att matcha FQDN till en IP-adress.|
   | Rule_4 | TCP | * | * | `12000` | Valfritt Om du använder Log Analytics skapar du en nätverks regel i avsnittet IP-adresser för att aktivera kommunikation med Log Analytics-arbetsytan. |

1. Skapa följande regler i avsnittet **service Tags** :

   | **Namn** | **Protokoll** | **Käll adress** | **Service märken** | **Målport** | **Anteckningar** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | Konfigurera en nätverks regel i avsnittet service märken för SQL som gör att du kan logga och granska SQL-trafik, om du inte har konfigurerat tjänst slut punkter för SQL Server i HDInsight-undernätet som kringgår brand väggen. |

1. Klicka på **Lägg till** för att slutföra skapandet av din nätverks regel samling.

   ![Avdelning Ange program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Skapa och konfigurera en routningstabell

Skapa en routningstabell med följande poster:

1. Sex adresser från [den här listan över nödvändiga HDInsight Management IP-adresser](../hdinsight/hdinsight-management-ip-addresses.md) med nästa hopp på **Internet**:
    1. Fyra IP-adresser för alla kluster i alla regioner
    1. Två IP-adresser som är speciella för den region där klustret skapas
1. En virtuell enhets väg för IP-adressen 0.0.0.0/0 med nästa hopp som din Azure Firewall-privata IP-adress.

Om du till exempel vill konfigurera routningstabellen för ett kluster som skapats i regionen USA, centrala, använder du följande steg:

1. Logga in på Azure Portal.
1. Välj din Azure Firewall **test-FW01**. Kopiera den **privata IP-adressen** som visas på sidan **Översikt** . I det här exemplet ska vi använda en **exempel adress för 10.1.1.4**
1. Skapa en ny routningstabell.
1. Klicka på **vägar** under **Inställningar**.
1. Klicka på **Lägg till** för att skapa vägar för IP-adresserna i tabellen nedan.

| Vägnamn | Adressprefix | Nästa hopptyp | Nästa hoppadress |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Ej tillämpligt |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Ej tillämpligt |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Ej tillämpligt |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Ej tillämpligt |
| 13.67.223.215 | 13.67.223.215/32 | Internet | Ej tillämpligt |
| 40.86.83.253 | 40.86.83.253/32 | Internet | Ej tillämpligt |
| 0.0.0.0 | 0.0.0.0/0 | Virtuell installation | 10.1.1.4 |

Slutför konfigureringen av routningstabellen:

1. Tilldela routningstabellen som du har skapat till ditt HDInsight-undernät genom att klicka på **undernät** under **Inställningar** och sedan **associera**.
1. På skärmen **associera undernät** väljer du det virtuella nätverk som klustret skapades i och **HDInsight-undernätet** som du använde för ditt HDInsight-kluster.
1. Klicka på **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-Node eller anpassad program trafik

Ovanstående steg gör att klustret kan köras utan problem. Du måste fortfarande konfigurera beroenden för att anpassa dina anpassade program som körs på Edge-noderna, om tillämpligt.

Program beroenden måste identifieras och läggas till i Azure-brandväggen eller i routningstabellen.

Vägar måste skapas för att program trafiken ska undvika problem med asymmetrisk routning.

Om dina program har andra beroenden måste de läggas till i din Azure-brandvägg. Skapa program regler för att tillåta HTTP/HTTPS-trafik och nätverks regler för allt annat.

## <a name="logging"></a>Loggning

Azure-brandväggen kan skicka loggar till några olika lagrings system. Om du vill ha mer information om hur du konfigurerar loggning för brand [väggen följer du stegen i själv studie kursen: Övervaka Azure Firewall-loggar och-](../firewall/tutorial-diagnostics.md)mått.

När du har slutfört loggnings konfigurationen, om du loggar data till Log Analytics, kan du Visa blockerad trafik med en fråga, till exempel följande:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Att integrera din Azure-brandvägg med Azure Monitor loggar är användbart när du först får ett program att fungera när du inte är medveten om alla program beroenden. Du kan lära dig mer om Azure Monitor loggar från [analysera loggdata i Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="access-to-the-cluster"></a>Åtkomst till klustret
När brand väggen har kon figurer ATS kan du använda den interna slut punkten`https://<clustername>-int.azurehdinsight.net`() för att komma åt Ambari inifrån det virtuella nätverket. Om du vill använda den offentliga`https://<clustername>.azurehdinsight.net`slut punkten () eller`<clustername>-ssh.azurehdinsight.net`SSH-slutpunkten () ser du till att du har rätt vägar i NSG för flödes tabeller och regler för att undvika problemet med assymetric- [routning.](https://docs.microsoft.com/azure/firewall/integrate-lb)

## <a name="configure-another-network-virtual-appliance"></a>Konfigurera en annan virtuell nätverks installation

>[!Important]
> Följande information krävs **bara** om du vill konfigurera en annan virtuell nätverks installation (NVA) än Azure Firewall.

I föregående instruktioner kan du konfigurera Azure-brandväggen för att begränsa utgående trafik från HDInsight-klustret. Azure-brandväggen konfigureras automatiskt för att tillåta trafik för många av de vanliga viktiga scenarierna. Om du vill använda en annan virtuell nätverks installation måste du manuellt konfigurera ett antal ytterligare funktioner. Tänk på följande när du konfigurerar din virtuella nätverks installation:

* Tjänst slut punkts tjänster som stöder tjänster måste konfigureras med tjänst slut punkter.
* IP-adress beroenden är för trafik som inte är HTTP/S (både TCP-och UDP-trafik).
* FQDN HTTP/HTTPS-slutpunkter kan placeras i din NVA-enhet.
* HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera beroende på ett antal kvalificerare.
* Tilldela den routningstabell som du skapar till ditt HDInsight-undernät.

### <a name="service-endpoint-capable-dependencies"></a>Tjänst slut punkt kompatibla beroenden

| **Slutpunkt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-adress beroenden

| **Slutpunkt** | **Detaljer** |
|---|---|
| \*:123 | Kontroll av NTP-klocka. Trafiken kontrol leras på flera slut punkter på port 123 |
| IP-adresser publicerade [här](hdinsight-management-ip-addresses.md) | Detta är HDInsight-tjänsten |
| AAD – DS privata IP-adresser för ESP-kluster |
| \*: 16800 för Windows-aktivering i KMS |
| \*12000 för Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden

>[!Important]
> Listan nedan innehåller bara några av de viktigaste FQDN-namnen. Du kan hämta den fullständiga listan över FQDN: er för att konfigurera din NVA [i den här filen](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

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

* [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)
