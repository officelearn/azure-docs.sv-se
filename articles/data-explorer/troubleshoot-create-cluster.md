---
title: Felsöka fel i att skapa Azure Data Explorer-kluster
description: I den här artikeln beskrivs felsökningssteg för att skapa ett kluster i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562419"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Felsöka: Det gick inte att skapa Azure Data Explorer

I den osannolika händelsen att klusterskapande misslyckas i Azure Data Explorer följer du dessa steg.

1. Se till att du har tillräcklig behörighet. Om du vill skapa ett kluster måste du vara medlem i rollen *Deltagare* eller *Ägare* för Azure-prenumerationen. Om det behövs arbetar du med prenumerationsadministratören så att de kan lägga till dig i rätt roll.

1. Kontrollera att det inte finns några verifieringsfel relaterade till klusternamnet som du angav under **Skapa kluster** i Azure-portalen.

1. Titta på [instrumentpanelen för Azure-tjänsthälsa](https://azure.microsoft.com/status/). Leta efter status för Azure Data Explorer i den region där du försöker skapa klustret.

    Om statusen inte är **bra** (grön bock) kan du prova att skapa klustret när statusen har förbättras.

1. Om du fortfarande behöver hjälp med att lösa problemet öppnar du en supportbegäran i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
