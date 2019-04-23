---
title: Felsöka fel i Azure Data Explorer klustret har skapats
description: Den här artikeln beskrivs de felsökningssteg för att skapa ett kluster i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9e6b3f53f07ac86d6b648a8562be4ef45879c37e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791701"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Felsökning: Misslyckade klustret skapas i Azure Data Explorer

Följ dessa steg det osannolika som det går inte att skapa kluster i Azure Data Explorer.

1. Se till att du har tillräcklig behörighet. Om du vill skapa ett kluster, måste du vara medlem i den *deltagare* eller *ägare* rollen för Azure-prenumerationen. Om nödvändigt, fungerar med din administratör för prenumerationen så att de kan lägga till dig i rätt roll.

1. Se till att det finns inga valideringsfel relaterade till klustrets namn som du angav **Skapa kluster** i Azure-portalen.

1. Kontrollera den [hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/). Leta efter status för Azure Data Explorer i den region där du försöker att skapa klustret.

    Om statusen inte är **bra** (grön bock), försök att skapa klustret när statusen förbättrar.

1. Om du fortfarande behöver hjälp med att lösa problemet kan du öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
