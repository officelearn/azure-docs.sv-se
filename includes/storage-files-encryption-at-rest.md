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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597869"
---
Alla data som lagras i Azure Files krypteras i vila med hjälp av Azure Storage Service Encryption (SSE). Kryptering av lagrings tjänst fungerar på samma sätt som BitLocker i Windows: data krypteras under fil system nivå. Eftersom data krypteras under fil systemet i Azure-filresursen, eftersom de är kodade till disk, behöver du inte ha åtkomst till den underliggande nyckeln på klienten för att läsa eller skriva till Azure-filresursen.

Som standard krypteras data som lagras i Azure Files med Microsoft-hanterade nycklar. Med Microsoft-hanterade nycklar innehåller Microsoft nycklar för att kryptera/dekryptera data och ansvarar för att rotera dem regelbundet. Du kan också välja att hantera dina egna nycklar, vilket ger dig kontroll över rotations processen. Om du väljer att kryptera dina fil resurser med Kundhanterade nycklar har Azure Files behörighet att få åtkomst till dina nycklar för att uppfylla Läs-och skriv förfrågningar från dina klienter. Med Kundhanterade nycklar kan du återkalla den här behörigheten när som helst, men det innebär att Azure-filresursen inte längre kan nås via SMB eller det fileraste API: et.

Azure Files använder samma krypterings schema som de andra Azure Storage-tjänsterna, till exempel Azure Blob Storage. Om du vill veta mer om Azure Storage Service Encryption (SSE) kan du läsa mer i [Azure Storage-kryptering för data i vila](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).