---
title: Ytterligare länkar om Avere vFXT för Azure
description: Länkar till ytterligare information om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 65e764d843f9e87adee4cf94c1d22b02db80eda0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958849"
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

En fullständig vägledning för att använda vfxt.py, moln-kluster skapas och hanteringsverktyg, finns på GitHub: [Molnhantering kluster med vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
