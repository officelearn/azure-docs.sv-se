---
title: Flytta virtuella Windows AWS-datorer till Azure
description: Flytta en AMAZON Web Services (AWS) EC2 Windows-instans till en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 9bd01f24ac2cada02f51089d238519cd6c7e0248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74039271"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Flytta en Virtuell Windows-dator från Amazon Web Services (AWS) till en virtuell Azure-dator

Om du utvärderar virtuella Azure-datorer för att vara värd för dina arbetsbelastningar kan du exportera en befintlig AMAZON Web Services (AWS) EC2 Windows VM-instans och sedan ladda upp den virtuella hårddisken (VHD) till Azure. När den virtuella hårddisken har överförts kan du skapa en ny virtuell dator i Azure från den virtuella hårddisken. 

Den här artikeln handlar om att flytta en enda virtuell dator från AWS till Azure. Om du vill flytta virtuella datorer från AWS till Azure i stor skala läser du [Migrera virtuella datorer i Amazon Web Services (AWS) till Azure med Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn 
 
Du kan ladda upp både generaliserade och specialiserade virtuella hårddiskar till Azure. Varje typ kräver att du förbereder den virtuella datorn innan du exporterar från AWS. 

- **Generaliserad virtuell hårddisk** - en generaliserad virtuell hårddisk har fått all din personliga kontoinformation borttagen med Sysprep. Om du tänker använda den virtuella hårddisken som en avbildning för att skapa nya virtuella datorer från bör du: 
 
    * [Förbered en Virtuell Windows-dator](prepare-for-upload-vhd-image.md).  
    * Generalisera den virtuella datorn med Sysprep.  

 
- **Specialiserad virtuell hårddisk** – en specialiserad virtuell hårddisk underhåller användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. Om du tänker använda den virtuella hårddisken som den är för att skapa en ny virtuell dator, se till att följande steg har slutförts.  
    * [Förbered en Virtuell Windows-hårddisk för att ladda upp till Azure](prepare-for-upload-vhd-image.md). **Generalisera inte** den virtuella datorn med Sysprep. 
    * Ta bort alla gästvirtualiseringsverktyg och agenter som är installerade på den virtuella datorn (dvs. VMware-verktyg). 
    * Kontrollera att den virtuella datorn är konfigurerad för att hämta sina IP-adress- och DNS-inställningar via DHCP. Detta säkerställer att servern skaffar en IP-adress i det virtuella nätverket när den startar.  


## <a name="export-and-download-the-vhd"></a>Exportera och ladda ned den virtuella hårddisken 

Exportera EC2-instansen till en virtuell hårddisk i en Amazon S3-hink. Följ stegen i Amazon-dokumentationsartikeln [Exportera en instans som en virtuell dator med hjälp av vm Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) och kör kommandot [create-instance-export-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) för att exportera EC2-instansen till en VHD-fil. 

Den exporterade VHD-filen sparas i Amazon S3-bucketen som du anger. Den grundläggande syntaxen för att exportera den virtuella hårddisken \<är nedan, bara ersätta platshållartexten inom parentes> med din information.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

När den virtuella hårddisken har exporterats följer du instruktionerna i [How Do I Download an Object from an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) för att hämta VHD-filen från S3-bucketen. 

> [!IMPORTANT]
> AWS tar ut dataöverföringsavgifter för nedladdning av den virtuella hårddisken. Se [Amazon S3 Prissättning](https://aws.amazon.com/s3/pricing/) för mer information.


## <a name="next-steps"></a>Nästa steg

Nu kan du ladda upp den virtuella hårddisken till Azure och skapa en ny virtuell dator. 

- Om du körde Sysprep på din källa för att **generalisera** den innan du exporterar läser [du Ladda upp en generaliserad virtuell hårddisk och använda den för att skapa nya virtuella datorer i Azure](upload-generalized-managed.md)
- Om du inte körde Sysprep innan du exporterade, anses den virtuella hårddisken vara **specialiserad**, se [Ladda upp en specialiserad virtuell hårddisk till Azure och skapa en ny virtuell dator](create-vm-specialized.md)

 
