---
title: Ytterligare länkar om Avere vFXT för Azure
description: Länkar till ytterligare information om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153776"
---
# <a name="additional-documentation"></a>Ytterligare dokumentation

Den här artikeln innehåller länkar till ytterligare dokumentation om Hanteringsgränssnittet för Avere Kontrollpanelen och relaterade ämnen.

## <a name="avere-cluster-documentation"></a>Avere klusterdokumentation

Ytterligare Avere kluster dokumentation finns på <https://azure.github.io/Avere/>webbplatsen på . Dessa dokument kan hjälpa dig att förstå klustrets funktioner och hur du konfigurerar dess inställningar.

* [FXT Cluster Creation Guide](<https://azure.github.io/Avere/#fxt_cluster>) är utformad för kluster som består av fysiska maskinvarunoder, men viss information i dokumentet är också relevant för vFXT-kluster. I synnerhet kan nya vFXT-klusteradministratörer dra nytta av att läsa dessa avsnitt:
  * [Anpassa support- och övervakningsinställningar](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) förklarar hur du anpassar supportuppladdningsinställningar och aktiverar fjärrövervakning.
  * [Konfigurera VServers och Global Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) har information om hur du skapar ett klientvänt namnområde.
  * [Om du konfigurerar DNS för Avere-klustret](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) beskrivs hur du konfigurerar round-robin DNS.
  * [Lägga till Back-end Storage-dokument](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) hur du lägger till kärnfilers.

* [Klusterkonfigurationsguiden](<https://azure.github.io/Avere/#operations>) är en fullständig referens till inställningar och alternativ för ett Avere-kluster. Ett vFXT-kluster använder en delmängd av dessa alternativ, men de flesta konfigurationssidor gäller.

* [Instrumentpanelsguiden](<https://azure.github.io/Avere/#operations>) förklarar hur du använder klusterövervakningsfunktionerna på Avere-kontrollpanelen.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT skapande och hantering dokumentation

En fullständig guide för hur du använder vfxt.py, ett skriptbaserat verktyg för att skapa och hantera molnkluster, finns på GitHub: [Molnklusterhantering med vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
