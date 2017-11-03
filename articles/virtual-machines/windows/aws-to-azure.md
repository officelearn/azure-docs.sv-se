---
title: Flytta en Windows AWS virtuella datorer till Azure | Microsoft Docs
description: "Flytta en Amazon Web Services (AWS) EC2 Windows-instans till virtuella datorer i Azure med hjälp av Azure PowerShell."
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
ms.date: 06/01/2017
ms.author: cynthn
ms.openlocfilehash: 7d2b498d3f84c4fd6cccf97c6d7781f293f5b395
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>Flytta en virtuell Windows-dator från Amazon Web Services (AWS) till Azure med hjälp av PowerShell

Om du utvärderar virtuella datorer i Azure som värd för din arbetsbelastning, kan du exportera en befintlig Amazon Web Services (AWS) EC2 Windows VM-instans sedan överför den virtuella hårddisken (VHD) till Azure. När den virtuella Hårddisken överförs kan du skapa en ny virtuell dator i Azure från den virtuella Hårddisken. 

Det här avsnittet beskriver flyttar en enda virtuell dator från AWS till Azure. Om du vill flytta virtuella datorer från AWS till Azure i skala, se [migrera virtuella datorer i Amazon Web Services (AWS) till Azure med Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn 
 
Du kan ladda upp både generaliserad och specialiserade VHD till Azure. Varje typ måste du förbereda den virtuella datorn innan du exporterar från AWS. 

- **Generaliserad virtuell Hårddisk** -en generaliserad virtuell Hårddisk har haft all personlig information bort med hjälp av Sysprep. Om du tänker använda den virtuella Hårddisken som en bild för att skapa nya virtuella datorer från bör du: 
 
    * [Förbereda Windows VM](prepare-for-upload-vhd-image.md).  
    * Generalisera den virtuella datorn med hjälp av Sysprep.  

 
- **Specialiserad VHD** – en särskild virtuell Hårddisk underhåller användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. Om du tänker använda den virtuella Hårddisken som – är att skapa en ny virtuell dator och se till att följande åtgärder har slutförts.  
    * [Förbereda en Windows-VHD att överföra till Azure](prepare-for-upload-vhd-image.md). **Inte** generalisera den virtuella datorn med hjälp av Sysprep. 
    * Ta bort gäst virtualiseringsverktyg och agenter som installerats på den virtuella datorn (d.v.s. VMware-verktyg). 
    * Se till att den virtuella datorn är konfigurerad för att hämta dess IP-adress och DNS-inställningarna via DHCP. Detta säkerställer att servern erhåller en IP-adress inom VNet när den startas.  


## <a name="export-and-download-the-vhd"></a>Exportera och ladda ned den virtuella Hårddisken 

Exportera EC2-instansen till en virtuell Hårddisk i ett Amazon S3-bucket. Följ stegen som beskrivs i avsnittet Amazon dokumentationen [och exportera en instans som en virtuell dator med hjälp av VM Import/Export](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) och kör den [skapa-instans-export-aktivitet](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) kommando för att exportera EC2-instansen till en VHD-fil. 

Exporterade VHD-filen sparas i en Amazon S3-bucket som du anger. Den grundläggande syntaxen för export av den virtuella Hårddisken visas nedan, bara ersätta platshållartexten i <brackets> med din information.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

När den virtuella Hårddisken har exporterats, följ instruktionerna i [hur hämtar jag ett objekt från ett S3-Bucket?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) hämta VHD-filen från S3-bucket. 

> [!IMPORTANT]
> AWS avgifter dataöverföring avgifter för att ladda ned den virtuella Hårddisken. Se [Amazon S3 priser](https://aws.amazon.com/s3/pricing/) för mer information.


## <a name="next-steps"></a>Nästa steg

Nu kan du överföra den virtuella Hårddisken till Azure och skapa en ny virtuell dator. 

- Om du har kört Sysprep på din datakälla till **generalisera** den innan du exporterar, se [överför en generaliserad virtuell Hårddisk och använda den för att skapa en ny virtuella datorer i Azure](upload-generalized-managed.md)
- Om du inte har kört Sysprep innan du exporterar den virtuella Hårddisken anses **specialiserade**, se [överför en särskild VHD till Azure och skapa en ny virtuell dator](create-vm-specialized.md)

 
