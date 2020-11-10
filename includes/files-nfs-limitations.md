---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 10177dd949ac531027e13cf633b11c16674fd4ab
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386552"
---
I för hands versionen har NFS följande begränsningar:

- NFS 4,1 stöder för närvarande bara de obligatoriska funktionerna från [protokoll specifikationen](https://tools.ietf.org/html/rfc5661). Valfria funktioner som delegering och motringning av alla typer, Lås uppgraderingar och degradering och Kerberos-autentisering och kryptering stöds inte.
- Om de flesta av dina begär Anden är metadata-koncentriska kommer svars tiden att bli sämre vid jämförelse med Läs-/skriv-/uppdaterings åtgärder.
- Du måste skapa ett nytt lagrings konto för att kunna skapa en NFS-resurs.
- Endast hanterings planens REST-API: er stöds. REST-API: er för data plan är inte tillgängliga, vilket innebär att verktyg som Storage Explorer inte fungerar med NFS-resurser eller att du inte kan bläddra i NFS dela data i Azure Portal.
- Endast tillgängligt för Premium-nivån.
- För närvarande endast tillgängligt med lokalt redundant lagring (LRS).

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funktioner som ännu inte stöds

Dessutom är följande Azure Files funktioner inte tillgängliga med NFS-resurser:

- Identitets baserad autentisering
- Azure Backup support
- Ögonblicksbilder
- Mjuk borttagning
- Fullständig stöd för kryptering under överföring (mer information finns i [NFS-säkerhet](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (endast tillgängligt för Windows-klienter som NFS 4,1 inte stöder)
