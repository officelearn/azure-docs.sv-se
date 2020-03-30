---
title: Seriell konsol i Azure | Microsoft-dokument
description: Med Azure Serial Console kan du ansluta till den virtuella datorn när SSH eller RDP inte är tillgängliga.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267019"
---
# <a name="azure-serial-console"></a>Seriell konsol i Azure

Seriekonsolen i Azure-portalen ger åtkomst till en textbaserad konsol för virtuella datorer (VIRTUELLA datorer) och virtuella datorskaleuppsättningsinstanser som kör antingen Linux eller Windows. Den här seriella anslutningen ansluter till seriell port för ttyS0 eller COM1 för den virtuella datorn eller skalningsuppsättningsinstansen för virtuella datorer, vilket ger åtkomst oberoende av nätverks- eller operativsystemtillstånd. Seriell konsol kan endast nås med hjälp av Azure-portalen och är endast tillåten för de användare som har en åtkomstroll som Deltagare eller högre till den virtuella datorn eller skalan för virtuella datorer.

Seriekonsolen fungerar på samma sätt för virtuella datorer och skalningsuppsättningsinstanser för virtuella datorer och virtuella datorer. I det här dokumentet innehåller alla omnämnanden till virtuella datorer implicit infallna instanser av skalningsuppsättningar för virtuella datorer om inte annat anges.

> [!NOTE]
> Seriekonsolen är vanligtvis tillgänglig i globala Azure-regioner och i offentlig förhandsversion i Azure Government. Det är ännu inte tillgängligt i Azure China-molnet.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Förutsättningar för åtkomst till Azure Serial Console
För att komma åt seriekonsolen på din vm- eller virtuella datorskalasuppsättningsinstans behöver du följande:

- Startdiagnostik måste vara aktiverat för den virtuella datorn
- Ett användarkonto som använder lösenordsautentisering måste finnas inom den virtuella datorn. Du kan skapa en lösenordsbaserad användare med [återställningslösenordsfunktionen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) för vm-åtkomsttillägget. Välj **Återställ lösenord** i avsnittet Support + **felsökning.**
- Azure-kontot som använder Serial Console måste ha rollen Deltagare i [virtuell dator](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för både det virtuella datorn och [lagringskontot för startdiagnostik](boot-diagnostics.md)

> [!NOTE]
> Klassiska distributioner stöds inte. Den virtuella datorn eller skalningsgruppsinstansen för virtuella datorer måste använda distributionsmodellen för Azure Resource Manager.

## <a name="get-started-with-the-serial-console"></a>Komma igång med seriekonsolen
Seriekonsolen för virtuella datorer och skalningsuppsättning för virtuella datorer är endast tillgänglig via Azure-portalen:

### <a name="serial-console-for-virtual-machines"></a>Seriekonsol för virtuella datorer
Seriekonsol för virtuella datorer är lika enkelt som att klicka på **Seriell konsol** i felsökningsavsnittet **Support +** i Azure-portalen.
  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. Navigera till **alla resurser** och välj en virtuell dator. Översiktssidan för den virtuella datorn öppnas.

  1. Bläddra ned till avsnittet **Support + felsökning** och välj **Seriell konsol**. En ny ruta med seriekonsolen öppnas och startar anslutningen.

     ![Fönstret Linux Serial Console](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Seriekonsol för skalningsuppsättningar för virtuella datorer
Serial Console är tillgänglig för skalningsuppsättningar för virtuella datorer som är tillgängliga för varje instans inom skalningsuppsättningen. Du måste navigera till den enskilda instansen av en skalauppsättning för virtuella datorer innan du ser knappen **Seriell konsol.** Om skaluppsättningen för den virtuella datorn inte har aktiverat startdiagnostik, se till att du uppdaterar skaluppsättningsmodellen för virtuell dator för att aktivera startdiagnostik och uppgradera sedan alla instanser till den nya modellen för att komma åt seriell konsol.
  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. Navigera till **alla resurser** och välj en skaluppsättning för virtuella datorer. Översiktssidan för skaluppsättningen för den virtuella datorn öppnas.

  1. Navigera till **instanser**

  1. Välj en skalningsuppsättningsinstans för virtuell dator

  1. Välj **Seriell konsol**i avsnittet **Support + felsökning** . En ny ruta med seriekonsolen öppnas och startar anslutningen.

     ![Linux virtuell maskin skala som Serial Console](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Avancerade användningsområden för seriekonsol
Förutom konsolåtkomst till din virtuella dator kan du även använda Azure Serial Console för följande:
* Skicka ett [kommando för systembegäran till den virtuella datorn](./serial-console-nmi-sysrq.md)
* Skicka ett [icke-maskerbart avbrott till din virtuella dator](./serial-console-nmi-sysrq.md)
* Graciöst [starta om eller kraftfullt power-cykling din VM](./serial-console-power-options.md)


## <a name="next-steps"></a>Nästa steg
Ytterligare serial konsoldokumentation finns i sidofältet.
- Mer information finns tillgänglig för [Serial Console för virtuella Linux-datorer](./serial-console-linux.md).
- Mer information finns tillgänglig för [Seriekonsol för virtuella Windows-datorer](./serial-console-windows.md).
