---
title: Konfigurera källmiljön (fysiska servrar till Azure) | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in din lokala miljö att börja replikera fysiska servrar som kör Windows eller Linux till Azure.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: anoopkv
ms.openlocfilehash: 00b09db97e597521de5c73eeefab77b0dfa1304d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38671030"
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Konfigurera källmiljön (fysisk till Azure)

Den här artikeln beskriver hur du ställer in din lokala miljö att börja replikera fysiska servrar som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter att du redan har:
1. En Recovery Services-valv i den [Azure-portalen](http://portal.azure.com "Azure-portalen").
3. En fysisk dator som du vill installera konfigurationsservern.

### <a name="configuration-server-minimum-requirements"></a>Minsta konfigurationskrav för server
I följande tabell visas de minimikraven på maskinvara, programvara och nätverkskrav för en konfigurationsserver.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS-baserade proxyservrar stöds inte av konfigurationsservern.

## <a name="choose-your-protection-goals"></a>Välja skyddsmål

1. I Azure-portalen går du till den **återställningstjänster** valv bladet och välj ditt valv.
2. I den **Resource** menyn för valvet klickar du på **komma igång** > **Site Recovery** > **steg 1: Förbered infrastrukturen**   >  **Skyddsmål**.

    ![Välja mål](./media/physical-azure-set-up-source/choose-goals.png)
3. I **skyddsmål**väljer **till Azure** och **inte virtualiserad/övrigt**, och klicka sedan på **OK**.

    ![Välja mål](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

1. I **Förbered källa**, om du inte har en konfigurationsserver, klickar du på **+ konfigurationsserver** att lägga till ett.

  ![Konfigurera källan](./media/physical-azure-set-up-source/plus-config-srv.png)
2. I den **Lägg till Server** bladet kontrollerar du att **konfigurationsservern** visas i **servertyp**.
4. Ladda ned installationsfilen enhetliga installationsprogrammet för Site Recovery.
5. Ladda ned valvregistreringsnyckeln. Du behöver Registreringsnyckeln när du kör enhetliga installationsprogrammet. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/physical-azure-set-up-source/set-source2.png)
6. Kör på den dator som du använder som konfigurationsserver **enhetliga installationsprogrammet för Azure Site Recovery** att installera konfigurationsservern, processervern och huvudmålservern.

#### <a name="run-azure-site-recovery-unified-setup"></a>Kör Azure Site Recovery ett enhetligt installationsprogram

> [!TIP]
> Configuration server registreringen misslyckas om tiden på systemklockan för datorns är mer än fem minuter från lokal tid. Synkronisera systemklockan med en [tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) innan du påbörjar installationen.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurationsservern kan installeras via en kommandorad. Mer information finns i [installera konfigurationsservern med hjälp av kommandoradsverktyg](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Vanliga problem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nästa steg

Nästa steg omfattar [konfigurera målmiljön](physical-azure-set-up-target.md) i Azure.
