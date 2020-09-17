---
title: Data placering för Azure Network Watcher | Microsoft Docs
description: Den här artikeln hjälper dig att förstå data placering för Azure Network Watcher-tjänsten.
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
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706834"
---
# <a name="data-residency-for-azure-network-watcher"></a>Data placering för Azure Network Watcher
Med undantag för tjänsten anslutnings övervakare (för hands version), lagrar inte Azure Network Watcher kunddata.


## <a name="connection-monitor-preview-data-residency"></a>Anslutnings övervakare (för hands version) data placering
Tjänsten anslutning övervakare (för hands version) lagrar kund information. Dessa data lagras automatiskt av Network Watcher i en enda region. Anslutnings övervakaren (för hands version) uppfyller automatiskt placering krav för data, inklusive krav som anges i [säkerhets Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Placering för Singapore-data

I Azure är funktionen som gör det möjligt att lagra kunddata i en enda region för närvarande endast tillgänglig i Sydostasien region (Singapore) för Asien och stillahavsområdet geo. För alla andra regioner lagras kund information på Geo. Mer information finns i [säkerhets Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Nästa steg

* Läs en översikt över [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
