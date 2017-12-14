---
title: "Gruppera virtuella datorer med VMware vCenter-märkning | Microsoft Docs"
description: "Beskriver hur du skapar en grupp innan du kör en bedömning med tjänsten Azure migrera."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 967c2a1fc0e5144c6c7d5c8c4a81cec3d77ffdb5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>Grupp virtuella datorer med vCenter-märkning

Den här artikeln beskriver hur du skapar en grupp datorer för [Azure migrera](migrate-overview.md) bedömning med taggar i VMware vCenter. Du kan ange den Taggkategori som du vill använda när du skapar en grupp. 

## <a name="set-up-tagging"></a>Ställ in taggning

Under distributionen av Azure migrera identifierar lokala Azure migrera Virtuella datorer som körs på ESXi-värdar som hanteras av en vCenter-server. Du måste ställa in vCenter taggning innan identifieringen.

1. Navigera till vCenter-servern i VMware vSphere-webbklienten.
2. Klicka på **taggar**, för att granska alla aktuella taggar.
3. För att tagga en virtuell dator, Välj **relaterade objekt** > **virtuella datorer**, och välj sedan den virtuella datorn som du vill använda taggar.
4. I **sammanfattning** > **taggar**, klickar du på **tilldela**. 
5. Klicka på **ny tagg**, och ange en taggnamn och beskrivning.
6. Om du vill crate en kategori för taggen, Välj **ny kategori** i den nedrullningsbara listan.
7. Ange ett namn, beskrivning och kardinalitet. Klicka sedan på **OK**.

    ![VM-taggar](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>Använd taggar för att skapa grupper

1. Konfigurera identifiering av lokala datorer, enligt beskrivningen i den [VMware assessment kursen](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms).
2. I **Taggkategori för att gruppera**, Välj kategorin vCenter-tagg som gruppen assessment ska baseras. Azure migrera skapar automatiskt en grupp för den valda kategorin.

    

## <a name="next-steps"></a>Nästa steg

[Ställ in VMware VM assessment](tutorial-assessment-vmware.md).
