---
title: Avgöra när du behöver använda Azure BLOB, Azure-filer eller Azure-diskar
description: Läs mer om olika sätt att lagra och komma åt data i Azure för att du bestämmer dig för vilken teknik som ska användas.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/28/2018
ms.author: tamram
ms.openlocfilehash: ded0884ff83cc214d78f65fed8cefa646f11d952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Avgöra när du behöver använda Azure BLOB, Azure-filer eller Azure-diskar

Microsoft Azure tillhandahåller flera funktioner i Azure Storage för att lagra och komma åt dina data i molnet. Den här artikeln täcker Azure-filer, Blobbar och diskar och är utformad för att hjälpa dig att välja mellan dessa funktioner.

## <a name="scenarios"></a>Scenarier

I följande tabell jämförs filer, Blobbar och diskar och visar exempelscenarier som är lämpliga för varje.

| Funktion | Beskrivning | När du ska använda detta |
|--------------|-------------|-------------|
| **Azure Files** | Tillhandahåller ett gränssnitt för SMB, klientbiblioteken, och en [REST-gränssnittet](/rest/api/storageservices/file-service-rest-api) som ger åtkomst från valfri plats till lagrade filer. | Du vill ”lyfta och flytta” ett program till molnet som redan använder filsystem API: er för att dela data mellan den och andra program som körs i Azure.<br/><br/>Du vill lagra utvecklings- och felsökningsverktyg som behöver komma åt från många virtuella datorer. |
| **Azure Blobs** | Ger klientbibliotek och en [REST-gränssnittet](/rest/api/storageservices/blob-service-rest-api) som tillåter att Ostrukturerade data lagras och komma åt i massiv skala i blockblobbar. | Du vill att program med stöd för direktuppspelning och direktåtkomst scenarier.<br/><br/>Du vill komma åt programdata från var som helst. |
| **Azure-diskar** | Ger klientbibliotek och en [REST-gränssnittet](/rest/api/compute/manageddisks/disks/disks-rest-api) som gör att data lagras beständigt och nås från en ansluten virtuell hårddisk. | Du vill lyfta och flytta program som använder filsystem API: er för att läsa och skriva data till beständiga diskar.<br/><br/>Du vill lagra data som inte krävs kan nås från utanför den virtuella datorn där disken är ansluten. |

## <a name="comparison-files-and-blobs"></a>Jämförelse: Filer och Blobbar

I följande tabell jämförs Azure-filer med Azure BLOB.  
  
||||  
|-|-|-|  
|**Attributet**|**Azure Blobs**|**Azure Files**|  
|Alternativ för hållbarhet|LRS-, ZRS-GRS, RA-GRS|LRS, ZRS, GRS|  
|Hjälpmedel|REST API:er|REST API:er<br /><br /> SMB 2.1 och SMB 3.0 (standard filsystem API: er)|  
|Anslutning|REST-API: er – globalt|REST API: er – globalt<br /><br /> SMB 2.1 – inom region<br /><br /> SMB 3.0 – globalt|  
|Slutpunkter|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Kataloger|Flat namnområde|True katalogobjekt|  
|Skiftlägeskänslighet namn|Skiftlägeskänsligt|Skilftlägeskänsliga, men case bevarar|  
|Kapacitet|Upp till 500 TiB-behållare|5 TiB-filresurser|  
|Dataflöde|Upp till 60 MiB/s per blockblob|Upp till 60 MiB/s per resurs|  
|Objektstorlek|Upp till ungefär 4,75 TiB per blockblob|Upp till 1 TiB per fil|  
|Fakturerade kapacitet|Baserat på skrivna byte|Baserat på filstorlek|  
|Klientbibliotek|Flera språk|Flera språk|  
  
## <a name="comparison-files-and-disks"></a>Jämförelse: Filer och diskar

Azure Files kompletterar Azure-diskar. En disk kan endast kopplas till en virtuell Azure-dator i taget. Diskar har fast format virtuella hårddiskar lagrade som sidblobbar i Azure Storage och används av den virtuella datorn för att lagra beständiga data. Filresurser i Azure-filer kan nås på samma sätt som den lokala disken används (med hjälp av filsystem API: er) och kan delas mellan flera virtuella datorer.  
 
I följande tabell jämförs Azure-filer med Azure-diskar.  
 
||||  
|-|-|-|  
|**Attributet**|**Azure-diskar**|**Azure Files**|  
|Omfång|Exklusivt för en enskild virtuell dator|Delad åtkomst över flera virtuella datorer|  
|Ögonblicksbilder och kopiera|Ja|Nej|  
|Konfiguration|Ansluten vid start av den virtuella datorn|Ansluten när den virtuella datorn har startats|  
|Autentisering|Inbyggd|Ställ in med net use|  
|Rensa|Automatisk|Manuell|  
|Åtkomst med hjälp av REST|Filerna i den virtuella Hårddisken kan inte nås|Filer som lagras på en resurs kan nås|  
|Max storlek|4 TiB-disk|5 TiB-filresurs och 1 TiB-fil i resursen|  
|Maximalt antal 8KB IOps|500 IOps|1000 IOps|  
|Dataflöde|Upp till 60 MiB/s per Disk|Upp till 60 MiB/s per resurs|  

## <a name="next-steps"></a>Nästa steg

När de fattar beslut om hur data lagras och komma åt bör du också beakta kostnaderna. Mer information finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Vissa SMB-funktioner kan inte användas till molnet. Mer information finns i [funktioner som inte stöds av tjänsten Azure File](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Mer information om diskar finns [hantera diskar och bilder](../../virtual-machines/windows/about-disks-and-vhds.md) och [hur du ansluter en datadisk till en virtuell Windows-dator](../../virtual-machines/windows/attach-managed-disk-portal.md).
