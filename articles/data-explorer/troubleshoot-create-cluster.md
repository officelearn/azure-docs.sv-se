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
ms.openlocfilehash: 9b8bfe2a4b9b7a8432f14fb53b3e7a4cae49a3b4
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189979"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Felsökning: Det gick inte att skapa ett kluster i Azure Data Explorer

Följ dessa steg det osannolika som det går inte att skapa kluster i Azure Data Explorer.

1. Se till att du har tillräcklig behörighet. Om du vill skapa ett kluster, måste du vara medlem i den *deltagare* eller *ägare* rollen för Azure-prenumerationen. Om nödvändigt, fungerar med din administratör för prenumerationen så att de kan lägga till dig i rätt roll.

1. Se till att det finns inga valideringsfel relaterade till klustrets namn som du angav **Skapa kluster** i Azure-portalen.

1. Kontrollera den [hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/). Leta efter status för Azure Data Explorer i den region där du försöker att skapa klustret.

    Om statusen inte är **bra** (grön bock), försök att skapa klustret när statusen förbättrar.

1. Om du fortfarande behöver hjälp med att lösa problemet kan du öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).