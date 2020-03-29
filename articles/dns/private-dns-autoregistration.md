---
title: Vad är autoregistrationsfunktion i Azure DNS-privata zoner
description: Översikt över autoregistrationsfunktionen i privata Azure DNS-zoner
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961203"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Vad är autoregistrationsfunktionen i Azure DNS-privata zoner

Azure DNS-funktionen för automatisk registrering av privata zoner tar bort smärtan från DNS-posthantering för virtuella datorer som distribueras i ett virtuellt nätverk. När du [länkar ett virtuellt nätverk](./private-dns-virtual-network-links.md) med en privat DNS-zon och aktiverar automatisk registrering för alla virtuella datorer skapas DNS-posterna för de virtuella datorer som distribueras i det virtuella nätverket automatiskt i den privata DNS-zonen. Förutom framåtblickande poster (A-poster) skapas även omvända uppslagsposter (PTR-poster) automatiskt för de virtuella datorerna.
Om du lägger till fler virtuella datorer i det virtuella nätverket skapas dns-poster för dessa virtuella datorer också automatiskt i den länkade privata DNS-zonen.

När du tar bort en virtuell dator tas DNS-posterna för den virtuella datorn automatiskt bort från den privata DNS-zonen.

Du kan aktivera autoregistration genom att välja alternativet Aktivera automatisk registrering när du skapar en virtuell nätverkslänk.

![Aktivera automatisk registrering](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Begränsningar

* Autoregistration fungerar endast för virtuella datorer. För alla andra resurser som interna belastningsutjämnare etc., kan du skapa DNS-poster manuellt i den privata DNS-zonen som är länkad till det virtuella nätverket.
* DNS-poster skapas automatiskt endast för det primära nätverkskortet för virtuella datorer . Om dina virtuella datorer har mer än ett nätverkskort kan du manuellt skapa DNS-posterna för andra nätverksgränssnitt.
* autoregistration för IPv6 (AAAA-poster) stöds inte.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata zoner](./private-dns-scenarios.md) som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika beteenden som du kan förvänta dig för vissa typer av åtgärder, finns i [Vanliga frågor och svar om privata DNS.](./dns-faq-private.md)
