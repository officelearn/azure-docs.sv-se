---
title: Felsöka anslutningsproblem för privata slutpunkter i Azure
description: Steg-för-steg-vägledning för att diagnostisera privat slutpunktsanslutning
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538542"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Felsöka anslutningsproblem för privata slutpunkter i Azure

Den här artikeln innehåller steg-för-steg-vägledning för att validera och diagnostisera din Azure Private Endpoint-anslutningskonfiguration.

Azure Private Endpoint är ett nätverksgränssnitt som ansluter dig privat och säkert till en privat länktjänst. Den här lösningen hjälper dig att skydda dina arbetsbelastningar i Azure genom att tillhandahålla privat anslutning till dina Azure-tjänstresurser från ditt virtuella nätverk. Den här lösningen ger effektivt dessa tjänster till ditt virtuella nätverk.

Här är anslutningsscenarierna som är tillgängliga med Private Endpoint:

- Virtuellt nätverk från samma region
- Regionalt peered virtuella nätverk
- Globalt peered virtuella nätverk
- Kunder lokalt via VPN- eller Azure ExpressRoute-kretsar

## <a name="diagnose-connectivity-problems"></a>Diagnostisera anslutningsproblem 

Granska de här stegen för att se till att alla vanliga konfigurationer som förväntat löser anslutningsproblem med din privata slutpunktskonfiguration.

1. Granska privat slutpunktskonfiguration genom att bläddra i resursen.

    a. Gå till **Private Link Center**.

      ![Privat länkcenter](./media/private-endpoint-tsg/private-link-center.png)

    b. Välj **Privata slutpunkter**i den vänstra rutan .
    
      ![Privata slutpunkter](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtrera och välj den privata slutpunkt som du vill diagnostisera.

    d. Granska den virtuella nätverks- och DNS-informationen.
     - Verifiera att **anslutningstillståndet**är godkänt .
     - Kontrollera att den virtuella datorn har anslutning till det virtuella nätverket som är värd för de privata slutpunkterna.
     - Kontrollera att FQDN-informationen (kopia) och den privata IP-adressen har tilldelats.
    
       ![Virtuell nätverks- och DNS-konfiguration](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Använd [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för att se om data flödar.

    a. Välj **Övervaka**på den privata slutpunktsresursen .
     - Välj **Data in** eller data **ut**. 
     - Se om data flödar när du försöker ansluta till den privata slutpunkten. Räkna med en fördröjning på cirka 10 minuter.
    
       ![Verifiera privat slutpunktstelemetri](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Använd **felsöka för VM-anslutning** från Azure Network Watcher.

    a. Välj klient-VM.

    b. Välj **Felsöka anslutning**och välj sedan fliken **Utgående anslutningar.**
    
      ![Network Watcher - Testa utgående anslutningar](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Välj **Använd Nätverksbevakare för detaljerad anslutningsspårning**.
    
      ![Network Watcher - Felsöka anslutning](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Välj **Testa med FQDN**.
     - Klistra in FQDN från den privata slutpunktsresursen.
     - Ange en port. Använd vanligtvis 443 för Azure Storage eller Azure Cosmos DB och 1336 för SQL.

    e. Välj **Testa**och validera testresultaten.
    
      ![Network Watcher - Testresultat](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. DNS-matchning från testresultaten måste ha samma privata IP-adress tilldelad till den privata slutpunkten.

    a. Om DNS-inställningarna är felaktiga gör du så här:
     - Om du använder en privat zon: 
       - Kontrollera att klientens virtuella virtuella nätverk är associerat med den privata zonen.
       - Kontrollera att den privata DNS-zonposten finns. Om den inte finns, skapa den.
     - Om du använder anpassad DNS:
       - Granska dina anpassade DNS-inställningar och verifiera att DNS-konfigurationen är korrekt.
       Vägledning finns i [Privat slutpunktsöversikt: DNS-konfiguration](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Om anslutningen misslyckas på grund av nätverkssäkerhetsgrupper (NSG) eller användardefinierade vägar:
     - Granska NSG-utgående regler och skapa lämpliga utgående regler för att tillåta trafik.
    
       ![Regler för utgående NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Om anslutningen har validerat resultat kan anslutningsproblemet vara relaterat till andra aspekter som hemligheter, token och lösenord i programlagret.
   - I det här fallet granskar du konfigurationen av den privata länkresurs som är associerad med den privata slutpunkten. Mer information finns i [felsökningsguiden för Azure Private Link](troubleshoot-private-link-connectivity.md).

1. Kontakta [Azure-supportteamet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om problemet fortfarande är olöst och det fortfarande finns ett anslutningsproblem.

## <a name="next-steps"></a>Nästa steg

 * [Skapa en privat slutpunkt i det uppdaterade undernätet (Azure-portalen)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Felsökningsguide för Azure Private Link](troubleshoot-private-link-connectivity.md)
