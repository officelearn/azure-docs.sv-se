---
title: Data placering för Azure Network Watcher | Microsoft Docs
description: Förstå data placering för tjänsten Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117843"
---
# <a name="data-residency-for-azure-network-watcher"></a>Data placering för Azure Network Watcher
Azure Network Watcher lagrar inte kund information förutom tjänsten anslutnings övervakare (för hands version).


## <a name="connection-monitor-preview-data-residency"></a>Anslutnings övervakare (för hands version) data placering
Tjänsten *anslutning övervakare (för hands version)* lagrar kund information. Dessa data lagras automatiskt av Network Watcher i en enda region. *Anslutnings övervakaren (för hands version)* uppfyller automatiskt placering krav för data, inklusive de som anges i [säkerhets Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Placering för Singapore-data

I Azure är funktionen för att aktivera lagring av kunddata i en enda region för närvarande endast tillgänglig i Sydostasien region (Singapore) för Asien och stillahavsområdet geo. För alla andra regioner lagras kund information på Geo. Mer information finns i [säkerhets Center](https://azuredatacentermap.azurewebsites.net/).

> [!Note]
> **Tidigare replikering** Kunderna har möjlighet att lagra IP-adresser för slutanvändare med sina Network Watcher-instanser så att Network Watcher kan övervaka tillgänglighets-, fördröjnings-och nätverkstopologi ändringar som rör slut användar-IP-adresserna. Dessa IP-adresser för slutanvändare kan klassificeras som kund information. Från och med den 15 juli 2020 har Network Watcher lagrat dessa data i en enda region. (Kund information replikeras inte längre till HK.) Dessa data replikeras inte längre till HK. Den här kund informationen är och krypterades i vila.
> 
> Om den här kund informationen skulle bli tillgänglig för en tredje part, så skulle den tredje parten kunna känna till IP-adressen, men inte bevilja åtkomst till den dator eller enhet som är associerad med IP-adressen. Network Watcher tror att inga tredje parter har åtkomst till dessa kunddata på HK.

## <a name="next-steps"></a>Nästa steg

* Läs en översikt över [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
