---
title: Konfigurera en processerver i Azure för VMware VM och fysisk återställning efter fel med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in en processerver i Azure för återställning efter fel virtuella datorer i Azure till VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 20c3642bb9f9ad5089c37bb6da8562e570896cb4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308488"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Konfigurera en processerver i Azure för återställning efter fel

När du redundansväxlar virtuella VMware-datorer eller fysiska servrar till Azure med [Site Recovery](site-recovery-overview.md), du kan återställa dem till den lokala platsen när den är igång igen. För att kunna återställas, måste du konfigurera en tillfällig processerver i Azure, för att hantera replikering från Azure till lokalt. Du kan ta bort den här virtuella datorn när återställningen är klar.

## <a name="before-you-start"></a>Innan du börjar

Läs mer om den [återaktiveringen av skyddet](vmware-azure-reprotect.md) och [återställning efter fel](vmware-azure-failback.md) processen.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

1. I valvet > **Site Recovery-infrastruktur**> **hantera** > **Konfigurationsservrar**, Välj configuration server.
2. I server-sida klickar du på **+ processervern**
3. I **Lägg till processervern** sidan och väljer att distribuera en processerver i Azure.
4. Ange inställningarna för Azure, inklusive den prenumeration som används för växling vid fel, en resursgrupp, Azure-regionen används för växling vid fel och det virtuella nätverket som virtuella Azure-datorer finns. Om du använder flera Azure-nätverk, måste en processerver i var och en.

  ![Lägg till galleriobjektet för processervern](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. I **servernamn**, **användarnamn**, och **lösenord**, ange ett namn för processervern och autentiseringsuppgifter som ska tilldelas administratörsbehörigheter på servern.
5. Ange ett lagringskonto som ska användas för server VM-diskar och det undernät som processervern VM kommer att finnas serverns IP-adress som ska tilldelas när den virtuella datorn startar.
6. Klicka på **OK** för att starta distribuerar processerver VM.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrera processerver (körs i Azure) till en konfigurationsserver (som körs lokalt)

När processervern VM är igång, måste du registrera den med konfigurationsservern lokalt på följande sätt:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


