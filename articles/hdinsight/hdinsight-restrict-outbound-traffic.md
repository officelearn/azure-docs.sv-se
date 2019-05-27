---
title: Konfigurera utgående trafik nätverksbegränsning för Azure HDInsight-kluster
description: Lär dig hur du konfigurerar utgående trafik nätverksbegränsning för Azure HDInsight-kluster.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/13/2019
ms.openlocfilehash: 44b6f099b5b17329976b9fec3c0ac38b5e394221
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978005"
---
# <a name="configure-outbound-network-traffic-restriction-for-azure-hdinsight-clusters-preview"></a>Konfigurera utgående trafik nätverksbegränsning för Azure HDInsight-kluster (förhandsversion)

Den här artikeln innehåller anvisningar för att skydda dina utgående trafik från ditt HDInsight-kluster med hjälp av Azure-brandvägg. Stegen nedan förutsätter att du konfigurerar ett Azure-brandväggen för ett befintligt kluster. Om du distribuerar ett nytt kluster och bakom en brandvägg, skapa ditt HDInsight-kluster och undernätet först och följ sedan stegen i den här guiden.

## <a name="background"></a>Bakgrund

Azure HDInsight-kluster distribueras normalt i ditt eget virtuella nätverk. Klustret har beroenden på tjänster utanför det virtuella nätverket som kräver nätverksåtkomst för att fungera korrekt.

Det finns flera beroenden som kräver inkommande trafik. Inkommande hanteringstrafik kan inte skickas via en brandväggsenhet. Källadresser för den här trafiken är kända och publiceras [här](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Du kan också skapa regler för Nätverkssäkerhetsgrupp (NSG) med den här informationen för att skydda inkommande trafik till klustren.

HDInsight utgående trafik beroenden definieras nästan helt och hållet med FQDN: er som inte har den statiska IP-adresser bakom dem. Bristen på statiska adresser innebär att Nätverkssäkerhetsgrupper (NSG) inte kan användas för att låsa den utgående trafiken från ett kluster. Adresserna ändras tillräckligt ofta att det går inte att ställa in regler baserat på den aktuella namnmatchningen och använda den för att konfigurera NSG-regler.

Lösning för att skydda utgående adresser är att använda en brandväggsenhet som kan styra utgående trafik baserat på domännamn. Azure-brandväggen kan begränsa utgående HTTP och HTTPS-trafik baserat på det fullständiga Domännamnet för målet eller [FQDN taggar](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurera Brandvägg för Azure med HDInsight

Det finns en sammanfattning av stegen för att låsa utgående trafiken från dina befintliga HDInsight med Azure-brandvägg:
1. Aktivera Tjänsteslutpunkter.
1. Skapa en brandvägg.
1. Lägga till regler för program i brandväggen
1. Lägga till regler i brandväggen.
1. Skapa en routningstabell.

### <a name="enable-service-endpoints"></a>Aktivera Tjänsteslutpunkter

Om du vill kringgå brandväggen (t.ex. att spara kostnader på dataöverföring) kan du aktivera tjänstslutpunkter för SQL och storage på ditt HDInsight-undernät. Eventuella Azure SQL-beroenden som klustret har måste konfigureras med Tjänsteslutpunkter även när du har aktiverat till Azure SQL Tjänsteslutpunkter.

Om du vill aktivera rätt Tjänsteslutpunkter, gör du följande:

1. Logga in på Azure Portal och välj det virtuella nätverket som HDInsight-kluster distribueras i.
1. Välj **undernät** under **inställningar**.
1. Välj det undernät där klustret distribueras.
1. På skärmen för att redigera inställningar för undernät, klickar du på **Microsoft.SQL** och/eller **Microsoft.Storage** från den **tjänstslutpunkter**  >   **Tjänster** listrutan.
1. Om du använder ett ESP-kluster så du måste också välja den **Microsoft.AzureActiveDirectory** tjänsteslutpunkt.
1. Klicka på **Spara**.

### <a name="create-a-new-firewall-for-your-cluster"></a>Skapa en ny Brandvägg för ditt kluster

1. Skapa ett undernät med namnet **AzureFirewallSubnet** i det virtuella nätverket där klustret finns. 
1. Skapa en ny brandväggsregel **Test-FW01** med hjälp av stegen i [självstudien: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).
1. Välj ny brandvägg från Azure-portalen. Klicka på **regler** under **inställningar** > **regelsamling för programmet** > **lägga till programmet regelsamlingen**.

    ![Rubrik: Lägg till programregelsamling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurera brandväggen med regler för program

Skapa en regelsamling för program som gör att klustret för att skicka och ta emot viktiga kommunikation.

Välj ny brandvägg **Test FW01** från Azure-portalen. Klicka på **regler** under **inställningar** > **regelsamling för programmet** > **lägga till programmet regelsamlingen**.

På den **lägga till programmet regelsamlingen** skärmen, gör du följande:

1. Ange en **namn**, **prioritet**, och klicka på **Tillåt** från den **åtgärd** nedrullningsbara menyn.
1. Lägg till följande regler:
    1. En regel som tillåter trafik för HDInsight och Windows Update:
        1. I den **FQDN taggar** avsnittet tillhandahåller en **namn**, och Ställ in **Source adresser** till `*`.
        1. Välj **HDInsight** och **WindowsUpdate** från den **FQDN taggar** nedrullningsbara menyn.
    1. En regel som tillåter inloggningsaktivitet för Windows:
        1. I den **Target FQDN** avsnittet tillhandahåller en **namn**, och Ställ in **Source adresser** till `*`.
        1. Ange `https:443` under **protokoll: Port** och `login.windows.net` under **rikta FQDN**.
    1. En regel som tillåter SQM-telemetri:
        1. I den **Target FQDN** avsnittet tillhandahåller en **namn**, och Ställ in **Source adresser** till `*`.
        1. Ange `https:443` under **protokoll: Port** och `sqm.telemetry.microsoft.com` under **rikta FQDN**.
    1. Om ditt kluster backas upp av WASB och du inte använder Tjänsteslutpunkter ovan, lägger du till en regel för WASB:
        1. I den **Target FQDN** avsnittet tillhandahåller en **namn**, och Ställ in **Source adresser** till `*`.
        1. Ange `http` eller [https] beroende på om du använder wasb: / / eller wasbs: / / under **protokoll: Port** och URL: en för storage-konto under **Target FQDN**.
1. Klicka på **Lägg till**.

![Rubrik: Ange programinformation regeln samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurera brandväggen med Nätverksregler

Skapa regler för network för att korrekt konfigurera ditt HDInsight-kluster.

> [!Important]
> Du kan välja mellan att använda SQL-tjänsttaggar i brandväggen använder regler network enligt beskrivningen i det här avsnittet eller en SQL-tjänsten endpoint en beskrivs i [i avsnittet om tjänstslutpunkter](#enable-service-endpoints). Om du väljer att använda SQL-taggar i Nätverksregler kan du logga och granska SQL-trafik. Med hjälp av en tjänstslutpunkt har SQL-trafiken kringgår brandväggen.

1. Välj ny brandvägg **Test FW01** från Azure-portalen.
1. Klicka på **regler** under **inställningar** > **Network regelsamlingen** > **Lägg till nätverk regelsamlingen**.
1. På den **Lägg till nätverk regelsamlingen** anger en **namn**, **prioritet**, och klicka på **Tillåt** från den **åtgärd** nedrullningsbara menyn.
1. Skapa följande regler:
    1. En regel som innebär att klustret kan utföra klockan sync via NTP.
        1. I den **regler** avsnittet tillhandahåller en **namn** och välj **alla** från den **protokollet** listrutan.
        1. Ange **källadresser** och **måladresser** till `*`.
        1. Ange **målportar** till 123.
    1. Om du använder Enterprise Security Package (ESP), och Lägg sedan till en regel som tillåter kommunikation med AAD-DS för ESP-kluster.
        1. Fastställa två IP-adresserna för domänkontrollanterna.
        1. I nästa rad i den **regler** avsnittet tillhandahåller en **namn** och välj **alla** från den **protokollet** listrutan.
        1. Ange **Source adresser** `*`.
        1. Ange alla IP-adresser för domänkontrollanter i **måladresser** avgränsade med kommatecken.
        1. Ange **målportar** till `*`.
    1. Om du använder Azure Data Lake Storage kan du lägga till en regel för att åtgärda ett SNI-problem med ADLS Gen1 och Gen2. Det här alternativet dirigerar trafiken till brandväggen som kan leda till högre kostnader för stora databelastningar men trafiken kommer att loggas och granskningsbar.
        1. Kontrollera IP-adressen för ditt Data Lake Storage-konto. Du kan använda ett powershell-kommando som `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` att matcha detta FQDN till en IP-adress.
        1. I nästa rad i den **regler** avsnittet tillhandahåller en **namn** och välj **alla** från den **protokollet** listrutan.
        1. Ange **Source adresser** `*`.
        1. Ange IP-adressen för ditt lagringskonto i **måladresser**.
        1. Ange **målportar** till `*`.
    1. En regel för att möjliggöra kommunikation med Key Management-tjänsten för Windows aktivering.
        1. I nästa rad i den **regler** avsnittet tillhandahåller en **namn** och välj **alla** från den **protokollet** listrutan.
        1. Ange **Source adresser** `*`.
        1. Ange **måladresser** till `*`.
        1. Ange **målportar** till `1688`.
    1. Om du använder Log Analytics kan du sedan skapa en regel för att aktivera kommunikation med Log Analytics-arbetsytan.
        1. I nästa rad i den **regler** avsnittet tillhandahåller en **namn** och välj **alla** från den **protokollet** listrutan.
        1. Ange **Source adresser** `*`.
        1. Ange **måladresser** till `*`.
        1. Ange **målportar** till `12000`.
    1. Konfigurera en tjänsttagg för SQL som gör att du kan logga in och granska SQL-trafiken.
        1. I nästa rad i den **regler** avsnittet tillhandahåller en **namn** och välj **alla** från den **protokollet** listrutan.
        1. Ange **Source adresser** `*`.
        1. Ange **måladresser** till `*`.
        1. Välj **Sql** från den **Tjänsttaggar** listrutan.
        1. Ange **målportar** till `1433,11000-11999,14000-14999`.
1. Klicka på **Lägg till** för att skapa regelsamlingen nätverk.

![Rubrik: Ange programinformation regeln samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Skapa och konfigurera en routningstabell

Skapa en routningstabell med följande poster:

1. Sju adresser från [den här listan över obligatoriska HDInsight hantering av IP-adresserna](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) med ett nexthop för **Internet**:
    1. Fyra IP-adresser för alla kluster i alla regioner
    1. Två IP-adresser som är specifika för den region där klustret har skapats
    1. En IP-adress för Azures rekursiva matchare
1. En virtuell installation väg för IP-adress 0.0.0.0/0 med nästa hopp som din privata IP-adress för Brandvägg för Azure.

Till exempel för att konfigurera routningstabellen för ett kluster som skapats i regionen USA för ”USA, centrala”, använder du följande steg:

1. Logga in på Azure Portal.
1. Välj din Azure-brandväggen **Test-FW01**. Kopiera den **privat IP-adress** visas på den **översikt** sidan. I det här exemplet ska vi använda en **exempel 10.1.1.4-adress**
1. Skapa en ny routningstabell.
1. Klicka på **vägar** under **inställningar**.
1. Klicka på **Lägg till** skapa vägar för IP-adresser i tabellen nedan.

| Vägnamn | Adressprefix | Nästa hopptyp | Nästa hoppadress |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Saknas |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Saknas |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Saknas |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Saknas |
| 13.67.223.215 | 13.67.223.215/32 | Internet | Saknas |
| 40.86.83.253 | 40.86.83.253/32 | Internet | Saknas |
| 168.63.129.16 | 168.63.129.16/32 | Internet | Saknas |
| 0.0.0.0 | 0.0.0.0/0 | Virtuell installation | 10.1.1.4 |

![Rubrik: Skapa en routningstabell](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-route-table.png)

Slutför routningskonfigurationen för tabellen:

1. Tilldela routningstabellen som du skapade till ditt HDInsight-undernät genom att klicka på **undernät** under **inställningar** och sedan **associera**.
1. På den **Associera undernät** , väljer det virtuella nätverket som klustret har skapats i och **AzureFirewallSubnet** du skapade för användning med brandväggen.
1. Klicka på **OK**.

![Rubrik: Skapa en routningstabell](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-route-table-associate-subnet.png)

## <a name="edge-node-application-traffic"></a>Kantnod trafik

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
