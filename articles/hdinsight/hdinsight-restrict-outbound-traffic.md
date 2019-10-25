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
ms.openlocfilehash: 56e745a4f4e4bfbe82da00b46b7a5c0a58e3785e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789796"
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
1. Skapa en ny brand vägg **test-FW01** med hjälp av stegen i [Självstudier: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurera brand väggen med program regler

Skapa en program regel samling som gör det möjligt för klustret att skicka och ta emot viktig kommunikation.

Välj den nya brand Väggs **test-FW01** från Azure Portal. Klicka på **regler** under **Inställningar**  > **program regel samling**  > **Lägg till program regel samling**.

![Rubrik: Lägg till program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Utför följande steg på skärmen **Lägg till program regel samling** :

1. Ange ett **namn**, **prioritet**och klicka på **Tillåt** i list rutan med **Åtgärds** menyn och ange följande regler i **avsnittet FQDN-Taggar** :

   | **Namn** | **Käll adress** | **FQDN-tagg** | **Anteckningar** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight och WindowsUpdate | Krävs för HDI-tjänster |

1. Lägg till följande regler i **avsnittet mål-FQDN** :

   | **Namn** | **Käll adress** | **Protokoll: port** | **Mål-FQDN** | **Anteckningar** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https: 443 | login.windows.net | Tillåt Windows inloggnings aktivitet |
   | Rule_3 | * | https: 443 | login.microsoftonline.com | Tillåt Windows inloggnings aktivitet |
   | Rule_4 | * | https: 443, http: 80 | < storage_account_name. blob. Core. Windows. net > | Om klustret backas upp av WASB lägger du till en regel för WASB. Om du bara vill använda HTTPS-anslutningar kontrollerar du att ["säker överföring krävs"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverat på lagrings kontot. |

1. Klicka på **Lägg till**.

   ![Rubrik: Ange information om program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurera brand väggen med nätverks regler

Skapa nätverks reglerna för att konfigurera HDInsight-klustret på rätt sätt.

1. Välj den nya brand Väggs **test-FW01** från Azure Portal.
1. Klicka på **regler** under **Inställningar**  > **nätverks regel samling**  > **Lägg till nätverks regel samling**.
1. På skärmen **Lägg till regel samling för nätverk** anger du ett **namn**, **prioritet**och klickar på **Tillåt** i list rutan med **Åtgärds** menyn.
1. Skapa följande regler i avsnittet **IP-adresser** :

   | **Namn** | **Protokoll** | **Käll adress** | **Mål adress** | **Målport** | **Anteckningar** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | - | * | * | `123` | Tids tjänst |
   | Rule_2 | Alla | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Om du använder Enterprise Security Package (ESP) lägger du till en nätverks regel i avsnittet IP-adresser som tillåter kommunikation med AAD-DS för ESP-kluster. Du hittar IP-adresserna för domän kontrol Lanterna i AAD-DS-avsnittet i portalen | 
   | Rule_3 | TCP | * | IP-adress för ditt Data Lake Storage konto | `*` | Om du använder Azure Data Lake Storage kan du lägga till en nätverks regel i avsnittet IP-adresser för att åtgärda ett SNI-problem med ADLS Gen1 och Gen2. Det här alternativet dirigerar trafiken till brand väggen som kan resultera i högre kostnader för stora data belastningar, men trafiken loggas och granskas i brand Väggs loggar. Identifiera IP-adressen för ditt Data Lake Storage-konto. Du kan använda ett PowerShell-kommando som `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` för att matcha FQDN till en IP-adress.|
   | Rule_4 | TCP | * | * | `12000` | Valfritt Om du använder Log Analytics skapar du en nätverks regel i avsnittet IP-adresser för att aktivera kommunikation med Log Analytics-arbetsytan. |

1. Skapa följande regler i avsnittet **service Tags** :

   | **Namn** | **Protokoll** | **Käll adress** | **Service märken** | **Målport** | **Anteckningar** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | Konfigurera en nätverks regel i avsnittet service märken för SQL som gör att du kan logga och granska SQL-trafik, om du inte har konfigurerat tjänst slut punkter för SQL Server i HDInsight-undernätet som kringgår brand väggen. |

1. Klicka på **Lägg till** för att slutföra skapandet av din nätverks regel samling.

   ![Rubrik: Ange program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

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

| Vägnamn | Adressprefix | Nexthop-typ | Nexthop-adress |
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
1. Klicka på **OK**

## <a name="edge-node-or-custom-application-traffic"></a>Edge-Node eller anpassad program trafik

Ovanstående steg gör att klustret kan köras utan problem. Du måste fortfarande konfigurera beroenden för att anpassa dina anpassade program som körs på Edge-noderna, om tillämpligt.

Program beroenden måste identifieras och läggas till i Azure-brandväggen eller i routningstabellen.

Vägar måste skapas för att program trafiken ska undvika problem med asymmetrisk routning.

Om dina program har andra beroenden måste de läggas till i din Azure-brandvägg. Skapa program regler för att tillåta HTTP/HTTPS-trafik och nätverks regler för allt annat.

## <a name="logging-and-scale"></a>Loggning och skalning

Azure-brandväggen kan skicka loggar till några olika lagrings system. Anvisningar om hur du konfigurerar loggning för brand väggen finns i [Självstudier: övervaka Azure Firewall-loggar och-mått](../firewall/tutorial-diagnostics.md).

När du har slutfört loggnings konfigurationen, om du loggar data till Log Analytics, kan du Visa blockerad trafik med en fråga, till exempel följande:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Att integrera din Azure-brandvägg med Azure Monitor loggar är användbart när du först får ett program att fungera när du inte är medveten om alla program beroenden. Du kan lära dig mer om Azure Monitor loggar från [analysera loggdata i Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Mer information om skalnings gränserna för Azure-brandväggen och begär Anden ökar finns i [det här](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#azure-firewall-limits) dokumentet eller [vanliga frågor och svar](https://docs.microsoft.com/en-us/azure/firewall/firewall-faq). 

## <a name="access-to-the-cluster"></a>Åtkomst till klustret
När brand väggen har kon figurer ATS kan du använda den interna slut punkten (`https://<clustername>-int.azurehdinsight.net`) för att komma åt Ambari inifrån VNET. 

Om du vill använda den offentliga slut punkten (`https://<clustername>.azurehdinsight.net`) eller SSH-slutpunkten (`<clustername>-ssh.azurehdinsight.net`) kontrollerar du att du har rätt vägar i routningstabellen och NSG-regler för att undvika det assymetric som beskrivs [här](https://docs.microsoft.com/azure/firewall/integrate-lb). I det här fallet måste du tillåta klientens IP-adress i reglerna för inkommande NSG och även lägga till den i den användardefinierade routningstabellen med nästa hopp uppsättning som `internet`. Om detta inte konfigureras korrekt visas ett timeout-fel.

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

| **Endpoint** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-adress beroenden

| **Endpoint** | **Detaljer** |
|---|---|
| \*:123 | Kontroll av NTP-klocka. Trafiken kontrol leras på flera slut punkter på port 123 |
| IP-adresser publicerade [här](hdinsight-management-ip-addresses.md) | Detta är HDInsight-tjänsten |
| AAD – DS privata IP-adresser för ESP-kluster |
| \*:16800 för aktivering av KMS-Windows |
| \*12000 för Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden

>[!Important]
> Listan nedan innehåller bara några av de viktigaste FQDN-namnen. Du kan hämta den fullständiga listan över FQDN: er för att konfigurera din NVA [i den här filen](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

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
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Nästa steg

* [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)
