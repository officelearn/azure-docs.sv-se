---
title: Felsök anslutnings problem med Azures privata slut punkt
description: Steg-för-steg-anvisningar för hur du diagnostiserar privat slut punkts anslutning
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
ms.openlocfilehash: df4ec6ddbba029eb29d2440717697968f8c79302
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191072"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Felsöka anslutningsproblem för privata slutpunkter

Den här guiden innehåller steg-för-steg-anvisningar om hur du verifierar och diagnostiserar konfigurationen för den privata slut punkten. 

Den privata Azure-slutpunkten är ett nätverks gränssnitt som ansluter privat och säkert till en privat länk tjänst. Den här lösningen hjälper dig att skydda dina arbets belastningar i Azure genom att tillhandahålla privat anslutning till dina Azure-tjänsteresurser från det virtuella nätverket. Detta innebär att dessa tjänster faktiskt tas med i ditt virtuella nätverk. 

Här är de anslutnings scenarier som är tillgängliga med privata slut punkter 
- virtuellt nätverk från samma region 
- regionalt peer-peerade virtuella nätverk
- globalt peer-kopplat virtuella nätverk
- kund lokalt via VPN eller Express Route-kretsar

## <a name="diagnosing-connectivity-problems"></a>Diagnostisera anslutnings problem 
Gå igenom stegen nedan för att se till att alla vanliga konfigurationer är de som förväntas lösa anslutnings problem med din privata slut punkts konfiguration.

1. Granska privat slut punkts konfiguration genom att bläddra i resursen 

    a) gå till **Private Link Center**

      ![Privat länk Center](./media/private-endpoint-tsg/private-link-center.png)

    b) Välj privata slut punkter i det vänstra navigerings fönstret
    
      ![Privata slut punkter](./media/private-endpoint-tsg/private-endpoints.png)

    c) filtrera och välj den privata slut punkt som du vill diagnostisera

    d) granska det virtuella nätverket och DNS-informationen
    
     - Verifiera anslutnings tillstånd har **godkänts**
     - Kontrol lera att den virtuella datorn har anslutning till det VNet som är värd för de privata slut punkterna
     - FQDN-information (kopia) och tilldelad privat IP-adress
    
       ![Konfiguration av virtuellt nätverk och DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. Använd [**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) för att granska data flödar

    a) på privat slut punkts resurs väljer du **övervaka**
     - Välj data in eller data ut och granska om data flödar vid försök att ansluta till den privata slut punkten. Vänta en stund med ca. 10 minuter.
    
       ![Verifiera privat slut punkt för telemetri](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Använd fel sökning av VM-anslutning från **Network Watcher**

    a) Välj den virtuella klient datorn

    b) Välj avsnittet **anslutnings fel sökning** , fliken **utgående anslutning**
    
      ![Network Watcher-testa utgående anslutningar](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) Välj **använd Network Watcher för spårning av detaljerade anslutningar**
    
      ![Network Watcher fel sökning av anslutning](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) Välj **test efter FQDN**
     - Klistra in det fullständiga domän namnet från resursen för den privata slut punkten
     - Ange en port (*vanligt vis 443 för Azure Storage eller COSMOS 1336 för SQL...* )

    e) Klicka på **testa** och verifiera test resultaten
    
      ![Network Watcher-test resultat](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. DNS-matchning från test resultaten måste ha samma privata IP-adress som tilldelats till den privata slut punkten

    a) om DNS-inställningarna är felaktiga gör du följande
     - Använda privat zon: 
       - Kontrol lera att det virtuella datorns virtuella nätverk är associerat med den privata zonen
       - Granska Privat DNS zon posten finns, skapa om den inte är befintlig
    
     - Använda anpassad DNS:
       - Granska kundens DNS-inställningar och verifiera att DNS-konfigurationen är korrekt.
       Se [Översikt över privat slut punkt – DNS-konfiguration](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) .

    b) om anslutningen inte fungerar på grund av NSG/UDR
     - Granska NSG utgående regler och skapa lämpliga utgående regler för att tillåta trafik
    
       ![NSG utgående regler](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Om anslutningen har verifierats kan anslutnings problemet vara relaterat till andra aspekter som hemligheter, tokens och lösen ord på program nivån.
   - I det här fallet granskar du konfigurationen av den privata länk resurs som är associerad med den privata slut punkten. Se [fel söknings guide för privata länkar](troubleshoot-private-link-connectivity.md). 

6. Kontakta [support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) teamet för Azure om problemet fortfarande är olöst och det fortfarande finns anslutnings problem. 

## <a name="next-steps"></a>Nästa steg

 * [Skapa en privat slut punkt på det uppdaterade under nätet (Azure Portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Fel söknings guide för privat länk](troubleshoot-private-link-connectivity.md)
