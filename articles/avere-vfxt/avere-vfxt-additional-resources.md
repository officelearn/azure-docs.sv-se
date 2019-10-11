---
title: Ytterligare länkar om AVERT vFXT för Azure
description: Länkar till ytterligare information om AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: rohogue
ms.openlocfilehash: c8ac08e3d03e8eb525cad7d73bece40c515e31a1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256288"
---
# <a name="additional-documentation"></a>Ytterligare dokumentation

Den här artikeln innehåller länkar till ytterligare dokumentation om hanterings gränssnittet AVERT på kontroll panelen och relaterade ämnen. 

## <a name="avere-cluster-documentation"></a>AVERT kluster dokumentation

Ytterligare kluster dokumentation för AVERT finns på webbplatsen <https://azure.github.io/Avere/>. Dessa dokument kan hjälpa dig att förstå klustrets funktioner och hur du konfigurerar dess inställningar. 

* [Guiden skapa kluster för FXT](<https://azure.github.io/Avere/#fxt_cluster>) är utformad för kluster som består av fysiska maskinvarukonfigurationer, men viss information i dokumentet är också relevant för vFXT-kluster. I synnerhet kan nya vFXT-kluster administratörer ha nytta av att läsa följande avsnitt:
  * Genom att [Anpassa inställningarna för support och övervakning](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) förklaras hur du anpassar inställningarna för överförings överföring och aktiverar fjärr styrning. 
  * [Konfiguration av VServers och globalt namn område](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) innehåller information om hur du skapar ett namn område för klienter.
  * När du [konfigurerar DNS för ett AVERT-kluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) förklaras hur du konfigurerar resursallokering (Round-Robin).
  * [Lägga till lagrings dokument för Server delen](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) Lägg till Core-.

* [Kluster konfigurations guiden](<https://azure.github.io/Avere/#operations>) är en fullständig referens till Inställningar och alternativ för ett AVERT-kluster. Ett vFXT-kluster använder en delmängd av dessa alternativ, men de flesta av samma konfigurations sidor gäller.

* I [instrument panels guiden](<https://azure.github.io/Avere/#operations>) förklaras hur du använder funktionerna för kluster övervakning på fliken aver i kontroll panelen.

## <a name="vfxt-creation-and-management-documentation"></a>dokumentation om skapande och hantering av vFXT

En fullständig guide för att använda vfxt.py, moln kluster skapande-och hanterings verktyget finns på GitHub: [moln kluster hantering med vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
