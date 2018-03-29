---
title: Översikt över belastningen belastningsutjämnare mot Internet | Microsoft Docs
description: Översikt för Internet facing belastningsutjämnare och dess funktioner. Hur fungerar en belastningsutjämnare för Azure med hjälp av virtuella datorer och molntjänster.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 33a63538e3b96c5a37ddda6be31a0d972884b9b7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="internet-facing-load-balancer-overview"></a>Internet Internetriktade belastningen översikt över belastningsutjämnare


Azure belastningsutjämnare mappar offentliga IP-adress och port antalet inkommande trafik till privata IP-adress och port numret för den virtuella datorn och vice versa för svarstrafik från den virtuella datorn. Belastningsutjämningsregler kan du distribuera specifika typer av trafik mellan flera virtuella datorer eller tjänster. Sprida belastningen på begäran Internet-trafik över flera webbservrar eller webbroller.

Du kan definiera en offentlig slutpunkt för en molntjänst som innehåller instanser av webbroller eller arbetsroller i tjänstdefinitionsfilen (.csdef).

Den *servicedefinition.csdef* filen innehåller slutpunktskonfigurationen och när du har flera rollinstanser för distribution av en webb- eller arbetarroll rollen belastningsutjämnaren konfigureras för den. Sätt att lägga till instanser för din molndistribution ändrar instansantalet på tjänstkonfigurationsfilen (.csfg).

## <a name="example-of-an-internet-facing-load-balancer"></a>Exempel på en Internetuppkopplad belastningsutjämnare

Följande bild visar en belastningsutjämnad slutpunkt för webbtrafik som delas mellan tre virtuella datorer för den offentliga och privata TCP-porten 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

![exempel på offentliga belastningsutjämnare](./media/load-balancer-internet-overview/IC727496.png)

Bild 1 - belastningsutjämnade slutpunkt för webbtrafik

När Internet-klienter skickar webbsida begäranden till den offentliga IP-adressen för Molntjänsten på TCP-port 80, distribuerar Azure belastningsutjämnare begäranden mellan tre virtuella datorer i den belastningsutjämnade uppsättningen. Mer information om belastningen belastningsutjämnaren algoritmer finns i [grundläggande belastningen belastningsutjämnaren översiktssidan](load-balancer-overview.md#fundamental-load-balancer-features).

Som standard distribuerar Azure belastningsutjämnare nätverkstrafik jämnt mellan flera virtuella datorer. Du kan också konfigurera sessionen tillhörighet mer information finns i [belastningsdistributionsläget nätverksbelastningsutjämning](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [intern belastningsutjämnare](load-balancer-internal-overview.md) att bättre förstå vilka belastningsutjämning är en passar bättre för din molndistribution.

Du kan också [komma igång med en Internetuppkopplad belastningsutjämnaren](load-balancer-get-started-internet-arm-ps.md) och konfigurera vilken typ av [distribution läge](load-balancer-distribution-mode.md) för en särskild belastningsutjämnare trafik nätverksproblem.

Om ditt program kräver aktiva anslutningar för servrar bakom en belastningsutjämnare rekommenderar vi att du läser mer om [timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md). Den här artikeln innehåller information om beteendet vid inaktiva anslutningar när du använder Azure Load Balancer.
