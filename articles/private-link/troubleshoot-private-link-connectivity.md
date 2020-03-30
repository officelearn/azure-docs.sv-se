---
title: Felsöka anslutningsproblem för Azure Private Link
description: Steg-för-steg-vägledning för att diagnostisera privat länkanslutning
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
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539475"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Felsöka anslutningsproblem för Azure Private Link

Den här artikeln innehåller steg-för-steg-vägledning för att validera och diagnostisera anslutning för din Azure Private Link-konfiguration.

Med Azure Private Link kan du komma åt Azure-plattformen som en tjänst (PaaS)-tjänster, till exempel Azure Storage, Azure Cosmos DB och Azure SQL Database och Azure som är värd för kund- eller partnertjänster via en privat slutpunkt i ditt virtuella nätverk. Trafiken mellan det virtuella nätverket och tjänsten passerar över Microsofts stamnätsnätverk, vilket eliminerar exponering från det offentliga internet. Du kan också skapa din egen privata länktjänst i ditt virtuella nätverk och leverera den privat till dina kunder.

Du kan aktivera din tjänst som körs bakom standardnivån för Azure Load Balancer för privat länk-åtkomst. Konsumenter av din tjänst kan skapa en privat slutpunkt i deras virtuella nätverk och mappa den till den här tjänsten för att komma åt den privat.

Här är anslutningsscenarierna som är tillgängliga med Private Link:

- Virtuellt nätverk från samma region
- Regionalt peered virtuella nätverk
- Globalt peered virtuella nätverk
- Kunder lokalt via VPN- eller Azure ExpressRoute-kretsar

## <a name="deployment-troubleshooting"></a>Felsökning av distribution

Granska informationen om [Inaktivera nätverksprinciper på den privata länktjänsten](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) för felsökningsärenden där du inte kan välja käll-IP-adressen från det undernät du väljer för din privata länktjänst.

Kontrollera att inställningen **privateLinkServiceNetworkPolicies** är inaktiverad för undernätet som du väljer käll-IP-adressen från.

## <a name="diagnose-connectivity-problems"></a>Diagnostisera anslutningsproblem

Om du får anslutningsproblem med din privata länkkonfiguration läser du de här stegen för att se till att alla vanliga konfigurationer är som förväntat.

1. Granska privat länkkonfiguration genom att bläddra i resursen.

    a. Gå till **Private Link Center**.

      ![Privat länkcenter](./media/private-link-tsg/private-link-center.png)

    b. Välj **Privata länktjänster**i den vänstra rutan .

      ![Privata länktjänster](./media/private-link-tsg/private-link-service.png)

    c. Filtrera och välj den privata länktjänst som du vill diagnostisera.

    d. Granska de privata slutpunktsanslutningarna.
     - Kontrollera att den privata slutpunkt som du söker anslutning från visas med ett **godkänt anslutningstillstånd.**
     - Om tillståndet **väntar**markerar du det och godkänner det.

       ![Privata slutpunktsanslutningar](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Gå till den privata slutpunkt som du ansluter från genom att välja namnet. Kontrollera att anslutningsstatusen visas som **Godkänd**.

       ![Översikt över privat slutpunktsanslutning](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - När båda sidor har godkänts provar du anslutningen igen.

    e. Granska **Alias** på fliken **Översikt** och **Resurs-ID** på fliken **Egenskaper.**
     - Kontrollera att **alias-** och **resurs-ID-informationen** matchar det **alias-** och **resurs-ID** som du använder för att skapa en privat slutpunkt till den här tjänsten.

       ![Verifiera aliasinformation](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verifiera resurs-ID-information](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Granska **synlighetsinformation** på fliken **Översikt.**
     - Kontrollera att din prenumeration **Visibility** omfattas av synlighetsomfånget.

       ![Verifiera synlighetsinformation](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Granska information om **belastningsutjämnare** på fliken **Översikt.**
     - Du kan gå till belastningsutjämnaren genom att välja länken för belastningsutjämnare.

       ![Verifiera information om belastningsutjämnare](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Se till att belastningsutjämnarens inställningar är konfigurerade enligt dina förväntningar.
       - Granska **Frontend IP-konfiguration**.
       - Granska **backend-pooler**.
       - Granska **belastningsutjämningsregler**.

       ![Verifiera egenskaper för belastningsutjämnare](./media/private-link-tsg/pls-ilb-properties.png)

     - Kontrollera att belastningsutjämnaren fungerar enligt föregående inställningar.
       - Välj en virtuell dator i ett annat undernät än undernätet där belastningsutjämnarens backend-pool är tillgänglig.
       - Prova att komma åt belastningsutjämnarens klientdel från den tidigare virtuella datorn.
       - Om anslutningen gör den till backend-poolen enligt belastningsutjämningsregler fungerar belastningsutjämnaren.
       - Du kan också granska belastningsutjämnarmåttet via Azure Monitor för att se om data flödar genom belastningsutjämnaren.

1. Använd [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för att se om data flödar.

    a. Välj **Mått**på den privata länktjänstresursen .
     - Välj **Byte in** eller Byte **Out**.
     - Se om data flödar när du försöker ansluta till den privata länktjänsten. Räkna med en fördröjning på cirka 10 minuter.

       ![Verifiera mått för privata länktjänster](./media/private-link-tsg/pls-metrics.png)

1. Kontakta [Azure-supportteamet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om problemet fortfarande är olöst och det fortfarande finns ett anslutningsproblem.

## <a name="next-steps"></a>Nästa steg

 * [Skapa en privat länktjänst (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Felsökningsguide för Azure Private Endpoint](troubleshoot-private-endpoint-connectivity.md)
