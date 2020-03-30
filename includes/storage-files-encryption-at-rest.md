---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597869"
---
Alla data som lagras i Azure Files krypteras i vila med Azure storage service kryptering (SSE). Kryptering av lagringstjänster fungerar på samma sätt som BitLocker i Windows: data krypteras under filsystemnivån. Eftersom data krypteras under Azure-filresursens filsystem, eftersom det är kodat till disk, behöver du inte ha åtkomst till den underliggande nyckeln på klienten för att läsa eller skriva till Azure-filresursen.

Som standard krypteras data som lagras i Azure Files med Microsoft-hanterade nycklar. Med Microsoft-hanterade nycklar har Microsoft nycklarna för att kryptera/dekryptera data och ansvarar för att rotera dem regelbundet. Du kan också välja att hantera dina egna nycklar, vilket ger dig kontroll över rotationsprocessen. Om du väljer att kryptera dina filresurser med kundhanterade nycklar har Azure Files behörighet att komma åt dina nycklar för att uppfylla läs- och skrivförfrågningar från dina klienter. Med kundhanterade nycklar kan du återkalla den här auktoriseringen när som helst, men det innebär att din Azure-filresurs inte längre är tillgänglig via SMB eller FileREST API.

Azure Files använder samma krypteringsschema som de andra Azure-lagringstjänsterna, till exempel Azure Blob-lagring. Mer information om Azure Storage Service Encryption (SSE) finns i [Azure storage encryption för data i vila](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).