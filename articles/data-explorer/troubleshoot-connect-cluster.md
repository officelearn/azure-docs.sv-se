---
title: Felsöka klusteranslutningsfel i Azure Data Explorer
description: I den här artikeln beskrivs felsökningssteg för anslutning till ett kluster i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827044"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Felsöka: Det gick inte att ansluta till ett kluster i Azure Data Explorer

Om du inte kan ansluta till ett kluster i Azure Data Explorer följer du dessa steg.

1. Se till att anslutningssträngen är korrekt. Det bör vara i `https://<ClusterName>.<Region>.kusto.windows.net`form: , till `https://docscluster.westus.kusto.windows.net`exempel följande exempel: .

1. Se till att du har tillräcklig behörighet. Om du inte har det får du svaret *obehörig*.

    Mer information om behörigheter finns i [Hantera databasbehörigheter](manage-database-permissions.md). Vid behov kan du ta hjälp av klusteradministratören, som kan lägga till dig i rätt roll.

1. Kontrollera att klustret inte har tagits bort: läs aktivitetsloggen i din prenumeration.

1. Titta på [instrumentpanelen för Azure-tjänsthälsa](https://azure.microsoft.com/status/). Leta upp statusen för Azure Data Explorer i den region där du försöker ansluta till ett kluster.

    Om statusen inte är **bra** (grön bock) kan du försöka ansluta till klustret när statusen har förbättras.

1. Om du fortfarande behöver hjälp med att lösa problemet öppnar du en supportbegäran i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).