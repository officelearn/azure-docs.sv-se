---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8901f54c4802766eb3f12830d98d29fb47337ae5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705222"
---
I för hands versionen har NFS följande begränsningar:

- NFS 4,1 stöder för närvarande bara de obligatoriska funktionerna från [protokoll specifikationen](https://tools.ietf.org/html/rfc5661). Valfria funktioner som delegering och motringning av alla typer, Lås uppgraderingar och degradering och Kerberos-autentisering och kryptering stöds inte.
- Om de flesta av dina begär Anden är metadata-koncentriska kommer svars tiden att bli sämre vid jämförelse med Läs-/skriv-/uppdaterings åtgärder.
- Du måste skapa ett nytt lagrings konto för att kunna skapa en NFS-resurs.
- Endast hanterings planens REST-API: er stöds. REST-API: er för data plan är inte tillgängliga, vilket innebär att verktyg som Storage Explorer inte fungerar med NFS-resurser, eller att du inte kan bläddra i NFS-dela data i Azure Portal.
- Endast tillgängligt för Premium-nivån.
- För närvarande endast tillgängligt med lokalt redundant lagring.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funktioner som ännu inte stöds

Dessutom är följande Azure Files funktioner inte tillgängliga med NFS-resurser:

- Identitets baserad autentisering
- Azure Backup support
- Ögonblicksbilder
- Mjuk borttagning
- Fullständig stöd för kryptering under överföring (mer information finns i [NFS-säkerhet](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (endast tillgängligt för Windows-klienter som NFS 4,1 inte stöder)