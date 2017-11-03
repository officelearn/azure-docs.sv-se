---
title: "Migrera från andra plattformar och AWS till hanterade diskar i Azure | Microsoft Docs"
description: "Skapa virtuella datorer i Azure med hjälp av virtuella hårddiskar som överförts från andra moln som AWS eller andra virtualiseringsplattformar och dra nytta av Azure hanterade diskar."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3b427556c589c7cc5205bfda16edc8d891814326
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrera från Amazon Web Services (AWS) och andra plattformar till hanterade diskar i Azure

Du kan överföra VHD-filer från AWS eller lokalt virtualiseringslösningar till Azure för att skapa virtuella datorer som utnyttjar hanterade diskar. Azure-hanterade diskar eliminerar behovet av att hantera lagringskonton för Azure IaaS-VM. Du behöver bara ange typ (Premium eller Standard) och storlek på disk och Azure skapar och hanterar disken du. 

Du kan överföra generaliserad och särskilda virtuella hårddiskar. 
- **Generaliserad virtuell Hårddisk** -har haft all personlig information bort med hjälp av Sysprep. 
- **Specialiserad VHD** -underhåller användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. 

> [!IMPORTANT]
> Du bör följa innan du laddar upp alla VHD till Azure [förbereda en Windows-VHD eller VHDX för att överföra till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenario                                                                                                                         | Dokumentation                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Du har befintliga AWS EC2 instanser som du vill migrera till Azure virtuella datorer med hjälp av hanterade diskar                              | [Flytta en virtuell dator från Amazon Web Services (AWS) till Azure](aws-to-azure.md)                           |
| Du har en virtuell dator från en annan virtualiseringsplattform som du vill använda som en bild för att skapa flera virtuella Azure-datorer. | [Överför en generaliserad virtuell Hårddisk och använda den för att skapa en ny virtuell dator i Azure](upload-generalized-managed.md) |
| Du har en unik anpassade virtuell dator som du vill återskapa i Azure.                                                      | [Överför en särskild VHD till Azure och skapa en ny virtuell dator](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Översikt över hanterade diskar

Azure-hanterade diskar förenklar VM management genom att ta bort behovet av att hantera storage-konton. Hanterade diskar också förmånen från bättre tillförlitlighet för virtuella datorer i en Tillgänglighetsuppsättning. Det garanterar att diskar för olika virtuella datorer i en Tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika att en enskild felpunkt. Den placeras automatiskt diskar på olika virtuella datorer i en Tillgänglighetsuppsättning i olika skalningsenheter (stämplar) som begränsar effekten av enskild skala enhet lagringsfel orsakade på grund av maskinvara och programvarufel. Utifrån dina behov kan välja du mellan två typer av lagringsalternativ: 
 
- [Hanterade Premiumdiskar](premium-storage.md) är fylld tillstånd enhet (SSD) baserade lagringsmedia som ger hög prestanda, låg latens diskstöd för virtuella datorer som körs I/O-intensiva arbetsbelastningar. Du kan dra nytta av hastighet och prestanda för dessa diskar genom att migrera till Premium hanterade diskar.  

- [Hanterade standarddiskar](standard-storage.md) använda hårddisken (HDD) baserade lagringsmedia och passar bäst för utveckling och testning och andra regelbunden åtkomst arbetsbelastningar som är mindre känsliga för prestanda variationen.  

## <a name="plan-for-the-migration-to-managed-disks"></a>Planera för migrering till hanterade diskar

Det här avsnittet hjälper dig att göra det bästa på VM- och diskresurser typer.


### <a name="location"></a>Plats

Välj en plats där Azure hanterade diskar är tillgängliga. Om du migrerar till hanterade Premiumdiskar också se till att Premium-lagring är tillgänglig i den region där du planerar att migrera till. Se [Azure-tjänster efter Region](https://azure.microsoft.com/regions/#services) uppdaterad information om tillgängliga platser.

### <a name="vm-sizes"></a>VM-storlekar

Om du migrerar till Premium hanterade diskar som du behöver uppdatera storleken på den virtuella datorn till Premium-lagring kan storlek i den region där VM finns. Granska storlek på Virtuella datorer som är Premium-lagring som är kompatibla. Specifikationer för Azure VM-storleken anges i [storlekar för virtuella datorer](sizes.md).
Granska prestandaegenskaper för virtuella datorer som fungerar med Premium-lagring och välja den lämpligaste VM-storlek som bäst passar din arbetsbelastning. Kontrollera att det finns tillräckligt med bandbredd på den virtuella datorn för att ge disk-trafik.

### <a name="disk-sizes"></a>Diskstorlekar

**Premium hanterade diskar**

Det finns tre typer av Premium hanterade diskar som kan användas med den virtuella datorn och var och en har särskilda IOPs och genomströmning gränser. Överväg att dessa begränsningar när du väljer Premium disktyp för den virtuella datorn baserat på dina behov av ditt program vad gäller kapacitet, prestanda, skalbarhet och belastning läses in.

| Premium diskar typ  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Diskstorlek           | 128 GB            | 512 GB            | 1 024 GB (1 TB)    |
| IOPS per disk       | 500               | 2 300              | 5000              |
| Dataflöde per disk | 100 MB per sekund | 150 MB per sekund | 200 MB per sekund |

**Hanterade standarddiskar**

Det finns fem typer av Standard hanterade diskar som kan användas med den virtuella datorn. Var och en av dem har olika kapacitet men samma IOPS och genomströmning gränser. Välj typ av Standard hanterade diskar baserat på kapacitetsbehov för programmet.

| Disk av standardtyp  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Diskstorlek           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1 024 GB (1 TB)   |
| IOPS per disk       | 500              | 500              | 500              | 500              | 500              |
| Dataflöde per disk | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund |

### <a name="disk-caching-policy"></a>Princip för cachelagring av disk 

**Premium hanterade diskar**

Princip för cachelagring av disk är som standard *skrivskyddad* för alla Premium datadiskar, och *Read-Write* för Premium operativsystemets disk som är kopplade till den virtuella datorn. Den här inställningen rekommenderas för att uppnå optimal prestanda för ditt program IOs. Inaktivera cachelagring på disk så att du kan få bättre prestanda för programmet för skrivintensiv eller lässkyddad datadiskar (till exempel loggfiler för SQL Server).

### <a name="pricing"></a>Prissättning

Granska de [priser för hanterade diskar](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Priser för hanterade Premiumdiskar är samma som ohanterad Premiumdiskar. Men priser för hanterade standarddiskar skiljer sig från ohanterade standarddiskar.


## <a name="next-steps"></a>Nästa steg

- Du bör följa innan du laddar upp alla VHD till Azure [förbereda en Windows-VHD eller VHDX för att överföra till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
