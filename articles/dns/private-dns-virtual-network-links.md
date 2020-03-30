---
title: Vad är en delkälla för en virtuell nätverkslänk i Azure DNS-privata zoner
description: Översikt över underresurs för virtuell nätverkslänk en privat Azure DNS-zon
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646769"
---
# <a name="what-is-a-virtual-network-link"></a>Vad är en virtuell nätverkslänk?

När du har skapat en privat DNS-zon i Azure är den inte omedelbart tillgänglig från något virtuellt nätverk. Du måste länka den till ett virtuellt nätverk innan en virtuell dator som finns i nätverket kan komma åt den privata DNS-zonen.
Om du vill länka en privat DNS-zon till ett virtuellt nätverk måste du skapa en virtuell nätverkslänk under den privata DNS-zonen. Varje privat DNS-zon har en samling underordnade resurser för virtuella nätverkslänkar. Var och en av dessa resurser representerar en anslutning till ett virtuellt nätverk.

Du kan länka ett virtuellt nätverk till en privat DNS-zon som ett virtuellt nätverk för registrering eller som ett virtuellt nätverk för upplösning.

## <a name="registration-virtual-network"></a>Registrering virtuellt nätverk

När du [skapar en länk](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) mellan en privat DNS-zon och ett virtuellt nätverk har du möjlighet att aktivera automatisk automatisk [återställning](./private-dns-autoregistration.md) av DNS-poster för virtuella datorer. Om du väljer det här alternativet blir det virtuella nätverket ett virtuellt nätverk för registrering för den privata DNS-zonen. En DNS-post skapas automatiskt för de virtuella datorer som du distribuerar i nätverket. DNS-poster skapas för de virtuella datorer som du redan har distribuerat i det virtuella nätverket. Ur det virtuella nätverksperspektivet blir den privata DNS-zonen registreringszonen för det virtuella nätverket.
En privat DNS-zon kan ha flera virtuella registreringsnätverk, men varje virtuellt nätverk kan ha exakt en registreringszon associerad med den.

## <a name="resolution-virtual-network"></a>Upplösning virtuellt nätverk

När du skapar en virtuell nätverkslänk under en privat DNS-zon och väljer att inte aktivera automatisk registrering av DNS-post, behandlas det virtuella nätverket som ett endast virtuellt lösningsnätverk. DNS-poster för virtuella datorer som distribueras i sådana nätverk skapas inte automatiskt i den länkade privata DNS-zonen. De virtuella datorer som distribueras i ett sådant nätverk kan dock fråga DNS-posterna från den privata DNS-zonen. Dessa poster kan skapas manuellt av dig eller fyllas i från andra virtuella nätverk som har länkats som registreringsnätverk med den privata DNS-zonen.
En privat DNS-zon kan ha virtuella nätverk med flera upplösningar och ett virtuellt nätverk kan ha flera lösningszoner associerade till sig.

## <a name="limits"></a>Begränsningar

För att förstå hur många registrerings- och lösningsnätverk som du kan länka till privata DNS-zoner se [Azure DNS-gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Andra överväganden

* Virtuella nätverk som distribueras med klassisk distributionsmodell stöds inte.

* Du kan bara skapa en länk mellan en privat DNS-zon och ett virtuellt nätverk.

* Varje virtuell nätverkslänk under en privat DNS-zon måste ha ett unikt namn inom ramen för den privata DNS-zonen. Du kan ha länkar med samma namn i olika privata DNS-zoner.

* När du har skapat en virtuell nätverkslänk kontrollerar du fältet "Länkstatus" för den virtuella nätverkslänkresursen. Beroende på storleken på det virtuella nätverket kan det ta några minuter innan länken fungerar och länkstatusen ändras till *Slutförd*.

* När du tar bort ett virtuellt nätverk tas alla virtuella nätverkslänkar och automatiskt registrerade DNS-poster som är associerade med det i olika privata DNS-zoner automatiskt bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du länkar ett virtuellt nätverk till en privat DNS-zon med [Azure-portalen](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Lär dig hur du skapar en privat zon i Azure DNS med hjälp av [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata zoner](./private-dns-scenarios.md) som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika beteenden som du kan förvänta dig för vissa typer av åtgärder, finns i [Vanliga frågor och svar om privata DNS.](./dns-faq-private.md)
