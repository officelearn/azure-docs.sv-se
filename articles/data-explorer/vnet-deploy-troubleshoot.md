---
title: Felsöka åtkomst, inmatning och drift av Azure Data Explorer-klustret i det virtuella nätverket
description: Felsöka anslutning, inmatning, klustergenerering och drift av Azure Data Explorer-klustret i det virtuella nätverket
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241665"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Felsöka åtkomst, inmatning och drift av Azure Data Explorer-klustret i det virtuella nätverket

I det här avsnittet lär du dig hur du felsöker problem med anslutning, drift och klusterskapande för ett kluster som distribueras till det [virtuella nätverket](/azure/virtual-network/virtual-networks-overview).

## <a name="access-issues"></a>Problem med åtkomst

Om du har ett problem när du öppnar klustret med den offentliga (cluster.region.kusto.windows.net) eller privata (private-cluster.region.kusto.windows.net) slutpunkten och misstänker att den är relaterad till installation av virtuella nätverk, utför du följande steg för att felsöka problemet.

### <a name="check-tcp-connectivity"></a>Kontrollera TCP-anslutning

Det första steget är att kontrollera TCP-anslutning med Windows eller Linux OS.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Hämta [TCping](https://www.elifulkerson.com/projects/tcping.php) till datorn som ansluter till klustret.
   2. Pinga målet från källdatorn med följande kommando:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Installera *netcat* i datorn som ansluter till klustret

    ```bash
    $ apt-get install netcat
     ```

   2. Pinga målet från källdatorn med följande kommando:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Om testet inte lyckas fortsätter du med följande steg. Om testet lyckas beror problemet inte på ett TCP-anslutningsproblem. Gå till [driftproblem](#cluster-creation-and-operations-issues) för felsökning ytterligare.

### <a name="check-the-network-security-group-nsg"></a>Kontrollera nätverkssäkerhetsgruppen (NSG)

   Kontrollera att [NSG (Network Security Group)](/azure/virtual-network/security-overview) som är ansluten till klustrets undernät har en inkommande regel som tillåter åtkomst från klientdatorns IP för port 443.

### <a name="check-route-table"></a>Kontrollera rutttabell

   Om klustrets undernät har force-tunneling setup till brandvägg (undernät med en [vägtabell](/azure/virtual-network/virtual-networks-udr-overview) som innehåller standardvägen '0.0.0.0/0'), se till att datorns IP-adress har en väg med [nästa hopptyp](/azure/virtual-network/virtual-networks-udr-overview) till VirtualNetwork/Internet. Den här vägen krävs för att förhindra asymmetriska vägproblem.

## <a name="ingestion-issues"></a>Problem med förtäring

Om du har problem med inmatning och misstänker att det är relaterat till installation av virtuella nätverk gör du följande steg.

### <a name="check-ingestion-health"></a>Hälsa vid kontroll av intag

Kontrollera att [måtten för klusterintag](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indikerar ett felfritt tillstånd.

### <a name="check-security-rules-on-data-source-resources"></a>Kontrollera säkerhetsregler för datakällresurser

Om måtten visar att inga händelser har bearbetats från datakällan *(Händelser som bearbetats* för event/IoT Hubs) kontrollerar du att datakällresurserna (Event Hub eller Storage) tillåter åtkomst från klustrets undernät i brandväggsreglerna eller tjänstslutpunkterna.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Kontrollera säkerhetsregler som konfigurerats i klustrets undernät

Kontrollera att klustrets undernät har NSG-, UDR- och brandväggsregler är korrekt konfigurerade. Testa dessutom nätverksanslutningen för alla beroende slutpunkter. 

## <a name="cluster-creation-and-operations-issues"></a>Problem med skapande av kluster och åtgärder

Om du har problem med att skapa kluster eller åtgärder och misstänker att det är relaterat till installation av virtuella nätverk följer du dessa steg för att felsöka problemet.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnostisera det virtuella nätverket med REST API

[ARMClient](https://chocolatey.org/packages/ARMClient) används för att anropa REST API med PowerShell. 

1. Logga in med ARMClient

   ```powerShell
   armclient login
   ```

1. Anropa diagnosåtgärd

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Kontrollera svaret

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Vänta på att åtgärden har slutförts

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Vänta tills statusegenskapen visar *Slutförd*, då ska *egenskapsfältet* visa: *status*

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Om egenskapen *Resultat* visar ett tomt resultat innebär det att alla nätverkstester har godkänts och inga anslutningar bryts. Om följande fel visas kan det hända att *det inte är uppfyllt med det utgående beroendet {dependencyName}:{port}} (Utgående)* kan klustret inte nå de beroende tjänstslutpunkterna. Fortsätt med följande steg.

### <a name="check-network-security-group-nsg"></a>Kontrollera nätverkssäkerhetsgruppen (NSG)

Kontrollera att [nätverkssäkerhetsgruppen](/azure/virtual-network/security-overview) är korrekt konfigurerad enligt instruktionerna i [Beroenden för VNet-distribution](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

### <a name="check-route-table"></a>Kontrollera rutttabell

Om klustrets undernät har force-tunneling inställd på brandvägg (undernät med en [vägtabell](/azure/virtual-network/virtual-networks-udr-overview) som innehåller standardvägen "0.0.0.0/0") se till att [hanterings-IP-adresserna](vnet-deployment.md#azure-data-explorer-management-ip-addresses)) och [IP-adresserna](vnet-deployment.md#health-monitoring-addresses) för hälsoövervakning har en väg med [nästa hoptyp](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*och [källadressprefix](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) till *"management-ip/32"* och *"health-monitoring-ip/32".* Den här vägen krävs för att förhindra asymmetriska vägproblem.

### <a name="check-firewall-rules"></a>Kontrollera brandväggsregler

Om du tvingar tunnelundernät utgående trafik till en brandvägg, se till att alla beroenden FQDN (till exempel *.blob.core.windows.net)* är tillåtna i brandväggskonfigurationen enligt beskrivningen för [att skydda utgående trafik med brandvägg](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall).
