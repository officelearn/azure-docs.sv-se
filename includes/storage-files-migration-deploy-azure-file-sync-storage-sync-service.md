---
title: Distribuera en lagringssynkroniseringstjänst
description: Distribuera molnresursen Azure File Sync. En lagringssynkroniseringstjänst. Ett vanligt textblock som delas mellan migreringsdokument.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124722"
---
I det här steget behöver du dina Azure-prenumerationsautentiseringsuppgifter.

Kärnresursen för att konfigurera Azure File Sync kallas "Storage Sync Service".
Vi rekommenderar att du bara distribuerar en för alla servrar i företaget som synkroniserar samma uppsättning filer nu eller i framtiden. Skapa endast flera Storage Sync Services om du har olika uppsättningar servrar som aldrig får utbyta data. (till exempel: synkronisera samma Azure-filresurs). Annars är en enda lagringssynkroniseringstjänst den bästa metoden.

Välj en Azure-region för din Storage Sync-tjänst som ligger nära din kontorsplats. Alla andra molnresurser måste distribueras i samma region.
För att förenkla hanteringen skapar du en ny resursgrupp i prenumerationen som rymmer synkroniserings- och lagringsresurser.

I följande artikel beskrivs hur du distribuerar en lagringssynkroniseringstjänst. Följ bara den här delen av doktorn. Det kommer att finnas länkar till andra underavsnitt i det här dokumentet i senare steg.

[Lär dig hur du distribuerar en lagringssynkroniseringstjänst.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
