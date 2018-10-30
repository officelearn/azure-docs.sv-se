---
title: Det gick inte att skapa ett kluster i Azure Data Explorer
description: Den här artikeln beskrivs de felsökningssteg för att skapa ett kluster i Azure Data Explorer.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0edf9ebcde2df7e639666f8fe7472baacdeb8640
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212190"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Felsök: Det gick inte att skapa ett kluster i Azure Data Explorer

Följ dessa steg det osannolika som det går inte att skapa kluster i Azure Data Explorer.

1. Se till att du har tillräcklig behörighet. Om du vill skapa ett kluster, måste du vara medlem i den *deltagare* eller *ägare* rollen för Azure-prenumerationen. Om nödvändigt, fungerar med din administratör för prenumerationen så att de kan lägga till dig i rätt roll.

1. Se till att det finns inga valideringsfel relaterade till klustrets namn som du angav **Skapa kluster** i Azure-portalen.

1. Kontrollera den [hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/>). Leta efter status för Azure Data Explorer i den region där du försöker att skapa klustret.

    Om statusen inte är **bra** (grön bock), försök att skapa klustret när statusen förbättrar.

1. Om du fortfarande behöver hjälp med att lösa problemet kan du öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).