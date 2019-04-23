---
title: Felsök anslutningsfel i Datautforskaren i Azure-kluster
description: Den här artikeln beskrivs de felsökningssteg för att ansluta till ett kluster i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795264"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Felsökning: Det gick inte att ansluta till ett kluster i Azure Data Explorer

Följ dessa steg om du inte kan ansluta till ett kluster i Azure Data Explorer.

1. Se till att anslutningssträngen är korrekt. Det bör vara i formatet: `https://<ClusterName>.<Region>.kusto.windows.net`, till exempel i följande exempel: `https://docscluster.westus.kusto.windows.net`.

1. Se till att du har tillräcklig behörighet. Om du inte får du svar på *obehörig*.

    Mer information om behörigheter finns i [hantera databasbehörigheter](manage-database-permissions.md). Om nödvändigt, fungerar med din Klusteradministratör så att de kan lägga till dig i rätt roll.

1. Kontrollera att klustret inte har tagits bort: granska aktivitetsloggen i din prenumeration.

1. Kontrollera den [hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/). Leta efter status för Azure Data Explorer i den region där du försöker ansluta till ett kluster.

    Om statusen inte är **bra** (grön bock), försök att ansluta till klustret efter status förbättrar.

1. Om du fortfarande behöver hjälp med att lösa problemet kan du öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).