---
title: Felsöka anslutnings problem med Azures privata länkar
description: Steg-för-steg-anvisningar för att diagnostisera anslutning till privata länkar
services: private-link
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
ms.openlocfilehash: 0d26ad6802e551523875dcad13066fdbdbf39ada
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191040"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Felsöka anslutningsproblem för Private Link-tjänsten

Den här guiden innehåller steg-för-steg-anvisningar om hur du verifierar och diagnostiserar anslutning för konfiguration av privata länkar. 

Med Azures privata länk kan du få åtkomst till Azure PaaS-tjänster (t. ex. Azure Storage, Azure Cosmos DB och SQL Database) och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan också skapa en egen privat länk-tjänst i ditt virtuella nätverk (VNet) och leverera den privat för kunderna. 

Du kan aktivera tjänsten som körs bakom Azure Standard Load Balancer för åtkomst till privat länk. Konsumenter av din tjänst kan skapa en privat slut punkt i sitt virtuella nätverk och mappa den till den här tjänsten för att få åtkomst till den privat.

Här är de anslutnings scenarier som är tillgängliga med tjänsten Private Link
- virtuellt nätverk från samma region 
- regionalt peer-peerade virtuella nätverk
- globalt peer-kopplat virtuella nätverk
- kund lokalt via VPN eller Express Route-kretsar

## <a name="deployment-troubleshooting"></a>Distributions fel sökning

Läs informationen om hur du [inaktiverar nätverks principer i tjänsten för privata länkar](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) för fel söknings fall där du inte kan välja käll-IP-adress från det undernät du väljer för din privata länk tjänst.

Kontrol lera att inställningen **privateLinkServiceNetworkPolicies** är inaktive rad för under nätet som du väljer käll-IP-adressen från.

## <a name="diagnosing-connectivity-problems"></a>Diagnostisera anslutnings problem

Om du har problem med anslutningen till konfiguration av privat länk går du igenom stegen nedan för att se till att alla vanliga konfigurationer är som förväntat.

1. Granska konfiguration av privat länk tjänst genom att bläddra i resursen 

    a) gå till **Private Link Center**

      ![Privat länk Center](./media/private-link-tsg/private-link-center.png)

    b) Välj **privata länk tjänster** i det vänstra navigerings fönstret

      ![Privata länk tjänster](./media/private-link-tsg/private-link-service.png)

    c) filtrera och välj den privata länk tjänst som du vill diagnostisera

    d) granska de privata slut punkts anslutningarna
     - Se till att den privata slut punkten som du söker efter anslutning från visas i listan med **godkänt** anslutnings tillstånd. 
     - Om det **väntar**väljer du det och godkänner. 

       ![Anslutningar för privata slut punkter](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Navigera till privat slut punkt som du ansluter från genom att klicka på namnet. Kontrol lera att anslutnings status visas som **godkänd**.

       ![Översikt över anslutning till privat slut punkt](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - När båda sidorna har godkänts kan du försöka ansluta igen.

    e) granska **alias** från fliken Översikt och **resurs-ID** från fliken Egenskaper 
     - Kontrol lera att **alias/resurs-ID** matchar det **alias/resurs-ID** som du använder för att skapa en privat slut punkt för den här tjänsten. 

       ![Verifiera alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verifiera resurs-ID](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) granska synlighet (avsnittet Översikt) information
     - Se till att din prenumeration ligger under **Synlighets** omfånget

       ![Kontrol lera synlighet](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) granska Load Balancer (översikt) information
     - Du kan navigera till belastningsutjämnare genom att klicka på länken belastnings utjämning

       ![Verifiera Load Balancer](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Kontrol lera att Load Balancer inställningar har kon figurer ATS enligt dina förväntningar
       - Granska klient delens IP-konfiguration
       - Granska backend-pooler
       - Granska regler för belastnings utjämning

       ![Verifiera Load Balancer egenskaper](./media/private-link-tsg/pls-ilb-properties.png)

     - Se till att Load Balancer arbetar enligt inställningarna ovan
       - Välj en virtuell dator i något annat undernät än under nätet där Load Balancer backend-poolen är tillgänglig
       - Försök att få åtkomst till belastningsutjämnaren på klient sidan från den virtuella datorn ovan
       - Om anslutningen görs till backend-poolen enligt reglerna för belastnings utjämning, fungerar belastningsutjämnaren
       - Du kan också granska Load Balancer mått via Azure Monitor för att se om data flödar genom belastningsutjämnare

2. Använd [**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) för att granska data flödar

    a) i resurs för privata länk tjänster väljer du **mått**
     - Välj **byte in** eller **byte ut**
     - Granska data flödar vid försök att ansluta till den privata länk tjänsten. Vänta en stund med ca. 10 minuter.

       ![Verifiera mått för privata länk tjänster](./media/private-link-tsg/pls-metrics.png)

3. Kontakta [support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) teamet för Azure om problemet fortfarande är olöst och det fortfarande finns anslutnings problem. 

## <a name="next-steps"></a>Nästa steg

 * [Skapa en privat länk tjänst (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Fel söknings guide för privat slut punkt](troubleshoot-private-endpoint-connectivity.md)
