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
ms.openlocfilehash: 6009953ece78eefd52fca9f12e3db80a6d2cc3eb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953216"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Felsök: Det gick inte att skapa ett kluster i Azure Data Explorer

Följ dessa steg det osannolika som det går inte att skapa kluster i Azure Data Explorer.

1. Se till att du har tillräcklig behörighet. Om du vill skapa ett kluster, måste du vara medlem i den *deltagare* eller *ägare* rollen för Azure-prenumerationen. Om nödvändigt, fungerar med din administratör för prenumerationen så att de kan lägga till dig i rätt roll.

1. Se till att det finns inga valideringsfel relaterade till klustrets namn som du angav **Skapa kluster** i Azure-portalen.

1. Kontrollera den [hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/>). Leta efter status för Azure Data Explorer i den region där du försöker att skapa klustret.

    Om statusen inte är **bra** (grön bock), försök att skapa klustret när statusen förbättrar.

1. Om du fortfarande behöver hjälp med att lösa problemet kan du öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com).