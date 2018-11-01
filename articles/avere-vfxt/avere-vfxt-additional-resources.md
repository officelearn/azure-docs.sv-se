---
title: Ytterligare länkar om Avere vFXT för Azure
description: Länkar till ytterligare information om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: afba39d1af700650cfbf7226dff36729a76a0bda
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634460"
---
# <a name="additional-documentation"></a>Ytterligare dokumentation

Den här artikeln innehåller länkar till ytterligare dokumentation om Avere Kontrollpanelen hanteringsgränssnitt och närliggande ämnen. 

## <a name="avere-cluster-documentation"></a>Om Avere

Om ytterligare Avere finns på webbplatsen på <http://library.averesystems.com/>. Dessa dokument kan kanske hjälpa dig att förstå klustrets funktioner och hur du konfigurerar dess inställningar. 

* Den [FXT kluster skapas Guide](<http://library.averesystems.com/#fxt_cluster>) är utformad för kluster som består av noder för fysisk maskinvara, men viss information i dokumentet är relevant för vFXT-kluster. I synnerhet kan nya vFXT klusteradministratörer dra nytta av läser dessa avsnitt:
  * [Anpassa Support och inställningarna för klientövervakning](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>) förklarar hur du anpassar inställningar för stöd för uppladdning och aktivera fjärrövervakning. 
  * [Konfigurera VServers och globala Namespace](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>) innehåller information om hur du skapar ett namnområde för klientinriktade.
  * [Konfigurera DNS för klustret Avere](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) förklarar hur du konfigurerar DNS-resursallokering.
  * [Att lägga till Serverdelslagring](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) hur du lägger till filter för core-dokument.

* Den [kluster konfigurationsguide](<http://library.averesystems.com/#operations>) är en fullständig referens för inställningar och alternativ för ett Avere-kluster. En vFXT klustret använder en delmängd av dessa alternativ, men de flesta av samma konfigurationssidor gäller.

* Den [Dashboard Guide](<http://library.averesystems.com/#operations>) förklarar hur du använder klustret övervakningsfunktioner i Avere på Kontrollpanelen.

## <a name="vfxt-creation-and-management-documentation"></a>dokumentation för skapande och hantering av vFXT

En fullständig vägledning för att använda vfxt.py, moln-kluster skapas och hanteringsverktyg, ges <https://github.com/AvereSystems/vFXT.py/blob/master/docs/README.md>.  
