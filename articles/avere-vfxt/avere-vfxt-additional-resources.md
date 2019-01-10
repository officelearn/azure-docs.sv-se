---
title: Ytterligare länkar om Avere vFXT för Azure
description: Länkar till ytterligare information om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: v-erkell
ms.openlocfilehash: 2efbe7ddc39b8bde76ee4a135f3f44af0864a374
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188580"
---
# <a name="additional-documentation"></a>Ytterligare dokumentation

Den här artikeln innehåller länkar till ytterligare dokumentation om Avere Kontrollpanelen hanteringsgränssnitt och närliggande ämnen. 

## <a name="avere-cluster-documentation"></a>Om Avere

Om ytterligare Avere finns på webbplatsen på <https://azure.github.io/Avere/>. Dessa dokument kan kanske hjälpa dig att förstå klustrets funktioner och hur du konfigurerar dess inställningar. 

* Den [FXT kluster skapas Guide](<https://azure.github.io/Avere/#fxt_cluster>) är utformad för kluster som består av noder för fysisk maskinvara, men viss information i dokumentet är relevant för vFXT-kluster. I synnerhet kan nya vFXT klusteradministratörer dra nytta av läser dessa avsnitt:
  * [Anpassa Support och inställningarna för klientövervakning](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) förklarar hur du anpassar inställningar för stöd för uppladdning och aktivera fjärrövervakning. 
  * [Konfigurera VServers och globala Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) innehåller information om hur du skapar ett namnområde för klientinriktade.
  * [Konfigurera DNS för klustret Avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) förklarar hur du konfigurerar DNS-resursallokering.
  * [Att lägga till Serverdelslagring](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) hur du lägger till filter för core-dokument.

* Den [kluster konfigurationsguide](<https://azure.github.io/Avere/#operations>) är en fullständig referens för inställningar och alternativ för ett Avere-kluster. En vFXT klustret använder en delmängd av dessa alternativ, men de flesta av samma konfigurationssidor gäller.

* Den [Dashboard Guide](<https://azure.github.io/Avere/#operations>) förklarar hur du använder klustret övervakningsfunktioner i Avere på Kontrollpanelen.

## <a name="vfxt-creation-and-management-documentation"></a>dokumentation för skapande och hantering av vFXT

En fullständig vägledning för att använda vfxt.py, moln-kluster skapas och hanteringsverktyg, finns på GitHub: [Molnbaserad klusterhantering av med vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
