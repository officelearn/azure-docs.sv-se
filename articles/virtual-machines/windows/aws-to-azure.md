---
title: Flytta en AWS Windows-datorer till Azure | Microsoft Docs
description: Flytta en Amazon Web Services (AWS) EC2 Windows-instans till en Azure virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f66101d9847c57c5e078c3484a243e7b38823f53
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001782"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Flytta en virtuell Windows-dator från Amazon Web Services (AWS) till en Azure-dator

Om du utvärderar Azure-datorer för att hantera dina arbetsbelastningar kan du exportera en befintlig Amazon Web Services (AWS) EC2 Windows VM-instans sedan överför den virtuella hårddisken (VHD) till Azure. När den virtuella Hårddisken har överförts, kan du skapa en ny virtuell dator i Azure från den virtuella Hårddisken. 

Den här artikeln beskriver flytta en enskild virtuell dator från AWS till Azure. Om du vill flytta virtuella datorer från AWS till Azure i skala, se [migrera virtuella datorer i Amazon Web Services (AWS) till Azure med Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn 
 
Du kan ladda upp både generaliserade och specialiserade virtuella hårddiskar till Azure. Varje typ måste du förbereda den virtuella datorn innan du exporterar från AWS. 

- **Generaliserad virtuell Hårddisk** – en generaliserad virtuell Hårddisk har haft all personlig information tas bort med hjälp av Sysprep. Om du tänker använda den virtuella Hårddisken som en bild för att skapa nya virtuella datorer från bör du: 
 
    * [Förbereda Windows VM](prepare-for-upload-vhd-image.md).  
    * Generalisera en virtuell dator med Sysprep.  

 
- **Specialiserad VHD** – en specialiserad virtuell Hårddisk som innehåller användarkonton, program och andra systemtillstånd från den ursprungliga virtuella datorn. Om du planerar att använda den virtuella Hårddisken som – är att skapa en ny virtuell dator och se till att följande steg har slutförts.  
    * [Förbereda en Windows virtuell Hårddisk för överföring till Azure](prepare-for-upload-vhd-image.md). **Inte** generalisera den virtuella datorn med hjälp av Sysprep. 
    * Ta bort alla gäst virtualization-verktyg och agenter som installerats på den virtuella datorn (d.v.s. VMware-verktyg). 
    * Se till att den virtuella datorn är konfigurerad för att hämta dess IP-adress och DNS-inställningarna via DHCP. Detta säkerställer att servern får en IP-adress inom det virtuella nätverket när den startas.  


## <a name="export-and-download-the-vhd"></a>Exportera och ladda ned den virtuella Hårddisken 

Exportera EC2-instans på en virtuell Hårddisk i en Amazon S3-bucket. Följ stegen i dokumentationsartikel Amazon [och exportera en instans som en virtuell dator med hjälp av VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) och kör den [skapa-instans-export-aktivitet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) kommando för att exportera EC2-instans till en VHD-fil. 

Den exporterade VHD-filen sparas i en Amazon S3-bucket som du anger. Grundläggande syntax för export av den virtuella Hårddisken är nedan, Ersätt bara platshållartexten i <brackets> med din information.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

När den virtuella Hårddisken har exporterats, följer du anvisningarna i [hur hämtar jag ett objekt från en S3-Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) att ladda ned VHD-filen från S3-bucket. 

> [!IMPORTANT]
> AWS-avgifter för dataöverföring avgifter för att ladda ned den virtuella Hårddisken. Se [Amazon S3 priser](https://aws.amazon.com/s3/pricing/) för mer information.


## <a name="next-steps"></a>Nästa steg

Nu kan du ladda upp den virtuella Hårddisken till Azure och skapa en ny virtuell dator. 

- Om du har kört Sysprep på källan till **generalisera** den innan du exporterar, se [överföra en generaliserad virtuell Hårddisk och använda den för att skapa en nya virtuella datorer i Azure](upload-generalized-managed.md)
- Om du inte har kört Sysprep innan du exporterar den virtuella Hårddisken anses **specialiserade**, se [ladda upp en specialiserad virtuell Hårddisk till Azure och skapa en ny virtuell dator](create-vm-specialized.md)

 
