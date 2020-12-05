---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 372342611265640a2a64100f003880a430d61ca0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620959"
---
I för hands versionen har NFS följande begränsningar:

- NFS 4,1 stöder för närvarande bara de flesta funktioner från [protokoll specifikationen](https://tools.ietf.org/html/rfc5661). Vissa funktioner som delegering och motringning av alla typer, Lås uppgraderingar och nedgradering, Kerberos-autentisering och kryptering stöds inte.
- Om de flesta av dina begär Anden är metadata-koncentriska kommer svars tiden att bli sämre vid jämförelse med Läs-/skriv-/uppdaterings åtgärder.
- Du måste skapa ett nytt lagrings konto för att kunna skapa en NFS-resurs.
- Endast hanterings planens REST-API: er stöds. REST-API: er för data plan är inte tillgängliga, vilket innebär att verktyg som Storage Explorer inte fungerar med NFS-resurser eller att du inte kan bläddra i NFS dela data i Azure Portal.
- AzCopy stöds inte för närvarande.
- Endast tillgängligt för Premium-nivån.
- NFS-resurser accepterar bara numeriskt UID/GID. För att undvika att klienter skickar alfanumeriska UID/GID bör du inaktivera ID-mappning.
- Resurser kan bara monteras från ett lagrings konto på en enskild virtuell dator, när privata länkar används. Försök att montera resurser från andra lagrings konton kommer att Miss lyckas.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funktioner som ännu inte stöds

Dessutom är följande Azure Files funktioner inte tillgängliga med NFS-resurser:

- Identitets baserad autentisering
- Azure Backup support
- Ögonblicksbilder
- Mjuk borttagning
- Fullständig stöd för kryptering under överföring (mer information finns i [NFS-säkerhet](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (endast tillgängligt för Windows-klienter som NFS 4,1 inte stöder)
