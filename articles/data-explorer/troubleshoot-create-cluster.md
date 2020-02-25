---
title: Felsöka fel i skapande av Azure Datautforskaren-kluster
description: I den här artikeln beskrivs fel söknings steg för att skapa ett kluster i Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562419"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Felsöka: det gick inte att skapa Azure-Datautforskaren

Om det förmodas att det inte går att skapa kluster i Azure Datautforskaren följer du dessa steg.

1. Se till att du har tillräcklig behörighet. Om du vill skapa ett kluster måste du vara medlem i rollen *deltagare* eller *ägare* för Azure-prenumerationen. Om det behövs kan du arbeta med prenumerations administratören så att de kan lägga till dig i rätt roll.

1. Se till att det inte finns några verifierings fel relaterade till kluster namnet som du angav under **skapa kluster** i Azure Portal.

1. Titta på [instrumentpanelen för Azure-tjänsthälsa](https://azure.microsoft.com/status/). Sök efter status för Azure Datautforskaren i den region där du försöker skapa klustret.

    Om statusen inte är **felfri** (grön bock markering) kan du försöka skapa klustret när statusen har förbättrats.

1. Om du fortfarande behöver hjälp med att lösa problemet, måste du öppna en supportbegäran i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
