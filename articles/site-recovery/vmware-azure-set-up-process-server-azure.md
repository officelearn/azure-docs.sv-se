---
title: Konfigurera en processserver VMware/fysisk återställning av återställning av återställning av återställning av återställning av webbplatser i Azure Site Recovery
description: I den här artikeln beskrivs hur du konfigurerar en processserver i Azure, till återställning av Virtuella Azure-datorer till VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083959"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Konfigurera en processerver i Azure för återställning efter fel

När du har växlat över virtuella datorer med VMware eller fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md)kan du återställa dem till den lokala platsen när den är igång igen. För att misslyckas tillbaka måste du konfigurera en tillfällig processserver i Azure för att hantera replikering från Azure till lokalt. Du kan ta bort den här virtuella datorn när återställningen efter återställning efter återställningen är klar.

## <a name="before-you-start"></a>Innan du börjar

Läs mer om [processen för återskydd](vmware-azure-reprotect.md) och återställning av [återställning.](vmware-azure-failback.md)

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processserver i Azure

1. Välj konfigurationsservern i valvet > **platsåterställningsinfrastruktur**> **Manage** > hantera**konfigurationsservrar.**
2. Klicka på + **Bearbeta server** på serversidan
3. I **Lägg till processserversida** och välj att distribuera processservern i Azure.
4. Ange Azure-inställningar, inklusive prenumerationen som används för redundans, en resursgrupp, Azure-regionen som används för redundans och det virtuella nätverket där virtuella Azure-datorer finns. Om du använde flera Azure-nätverk behöver du en processserver i var och en.

   ![Lägg till processservergalleriobjekt](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Ange ett namn för processservern i **Servernamn,** **Användarnamn**och **Lösenord**och autentiseringsuppgifter som ska tilldelas administratörsbehörigheter på servern.
5. Ange ett lagringskonto som ska användas för serverns VM-diskar, undernätet där processserverns virtuella dator ska placeras och serverns IP-adress som ska tilldelas när den virtuella datorn startar.
6. Klicka på **OK** för att börja distribuera processservern VM. Processservern kommer att distribueras på Standard_A8_v2 SKU. Kontrollera att den här VM-SKU:n är tillgänglig för din prenumeration.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrera processservern (som körs i Azure) till en konfigurationsserver (som körs lokalt)

När den virtuella datorn för processservern är igång måste du registrera den hos den lokala konfigurationsservern enligt följande:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


