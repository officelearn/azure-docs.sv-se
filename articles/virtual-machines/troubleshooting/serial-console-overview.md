---
title: Azures serie konsol | Microsoft Docs
description: Med Azures serie konsol kan du ansluta till din virtuella dator när SSH eller RDP inte är tillgängliga.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153867"
---
# <a name="azure-serial-console"></a>Azures serie konsol

Serie konsolen i Azure Portal ger till gång till en textbaserad konsol för virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer som kör Linux eller Windows. Den här seriella anslutningen ansluter till ttyS0 eller COM1 seriell port för den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans, vilket ger åtkomst oberoende av nätverket eller operativ systemets tillstånd. Det går bara att komma åt serie konsolen med hjälp av Azure Portal och tillåts bara för de användare som har åtkomst rollen deltagare eller högre till den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

Serie konsolen fungerar på samma sätt för virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer. I det här dokumentet kommer alla omnämnanden för virtuella datorer att implicit inkludera skalnings uppsättnings instanser för virtuella datorer om inget annat anges.

> [!NOTE]
> Serie konsolen är allmänt tillgänglig i globala Azure-regioner och i offentlig för hands version i Azure Government. Den är ännu inte tillgänglig i Azure Kina-molnet.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Krav för att få åtkomst till Azures serie konsol
Du behöver följande för att få åtkomst till serie konsolen på den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans:

- Startdiagnostik måste vara aktiverat för den virtuella datorn
- Ett användar konto som använder lösenordsautentisering måste finnas i den virtuella datorn. Du kan skapa en lösenordsbaserad användare med funktionen [Återställ lösen ord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) för VM Access-tillägget. Välj **Återställ lösen ord** i avsnittet **support och fel sökning** .
- Azure-kontot som använder serie konsolen måste ha [rollen virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för både den virtuella datorn och lagrings kontot för [startdiagnostik](boot-diagnostics.md)

> [!NOTE]
> Klassiska distributioner stöds inte. Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste använda Azure Resource Manager distributions modell.

## <a name="get-started-with-the-serial-console"></a>Kom igång med serie konsolen
Serie konsolen för virtuella datorer och skalnings uppsättningen för virtuella datorer kan bara nås via Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Serie konsol för Virtual Machines
Serie konsolen för virtuella datorer är lika enkel som att klicka på **seriell konsol** i avsnittet **support och fel sökning** i Azure Portal.
  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. Gå till **alla resurser** och välj en virtuell dator. Översikts sidan för den virtuella datorn öppnas.

  1. Rulla ned till avsnittet **support och fel sökning** och välj **seriell konsol**. Ett nytt fönster med seriell konsol öppnas och börjar anslutningen.

     ![Fönstret för seriella Linux-konsol](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Serie konsol för Virtual Machine Scale Sets
Serie konsolen är tillgänglig för skalnings uppsättningar för virtuella datorer, tillgängliga på varje instans i skalnings uppsättningen. Du måste gå till den enskilda instansen av en skalnings uppsättning för virtuella datorer innan du ser knappen **seriell konsol** . Om den virtuella datorns skalnings uppsättning inte har startdiagnostik aktive rad, se till att du uppdaterar modell för skalnings uppsättning för virtuella datorer för att aktivera startdiagnostik, och uppgradera sedan alla instanser till den nya modellen för att få åtkomst till serie konsolen.
  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. Gå till **alla resurser** och välj en skalnings uppsättning för virtuell dator. Översikts sidan för skalnings uppsättningen för den virtuella datorn öppnas.

  1. Navigera till **instanser**

  1. Välj en instans för skalnings uppsättning för virtuell dator

  1. I avsnittet **support och fel sökning** väljer du **seriell konsol**. Ett nytt fönster med seriell konsol öppnas och börjar anslutningen.

     ![Serie konsol för skalnings uppsättning för virtuella Linux-datorer](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Avancerade användnings områden för serie konsolen
Förutom konsol åtkomst till den virtuella datorn kan du också använda Azures serie konsol för följande:
* Skicka ett [kommando för system begär Ande till din virtuella dator](./serial-console-nmi-sysrq.md)
* Skickar ett [icke-maskbart avbrott till din virtuella dator](./serial-console-nmi-sysrq.md)
* [Starta om datorn eller framtvinga en omstart av den virtuella](./serial-console-power-options.md) datorn


## <a name="next-steps"></a>Nästa steg
Ytterligare dokumentation om serie konsolen finns på sid panelen.
- Mer information finns i [serie konsolen för virtuella Linux-datorer](./serial-console-linux.md).
- Mer information finns i [serie konsolen för virtuella Windows-datorer](./serial-console-windows.md).
