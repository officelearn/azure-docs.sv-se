---
title: "Konfigurera källmiljön (fysiska servrar till Azure) | Microsoft Docs"
description: "Den här artikeln beskriver hur du ställer in din lokala miljö för att starta replikering av fysiska servrar som kör Windows eller Linux till Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: d242954eb62a0d7325cc4222a54f2581967fdc19
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Konfigurera källmiljön (fysisk server till Azure)
> [!div class="op_single_selector"]
> * [VMware till Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Fysiska till Azure](./site-recovery-set-up-physical-to-azure.md)

Den här artikeln beskriver hur du ställer in din lokala miljö för att starta replikering av fysiska servrar som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Krav

Artikeln förutsätter att du redan har:
1. En Recovery Services-valvet i den [Azure-portalen](http://portal.azure.com "Azure-portalen").
3. En fysisk dator som du vill installera konfigurationsservern.

### <a name="configuration-server-minimum-requirements"></a>Minimikrav för konfiguration av servern
I följande tabell visar minimikrav för maskinvara, programvara och nätverkskraven för en konfigurationsserver.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS-baserade proxyservrar stöds inte av konfigurationsservern.

## <a name="choose-your-protection-goals"></a>Välja skyddsmål

1. I Azure-portalen går du till den **återställningstjänster** valv bladet och välj ditt valv.
2. I den **resurs** -menyn i valvet, klickar du på **komma igång** > **Site Recovery** > **steg 1: Förbered infrastrukturen** > **skyddsmål**.

    ![Välja mål](./media/site-recovery-set-up-physical-to-azure/choose-goals.png)
3. I **skyddsmål**väljer **till Azure** och **inte virtualiserade/andra**, och klicka sedan på **OK**.

    ![Välja mål](./media/site-recovery-set-up-physical-to-azure/physical-protection-goal.PNG)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

1. I **Förbered källa**, om du inte har en konfigurationsservern och klicka på **+ konfigurationsservern** att lägga till ett.

  ![Konfigurera källan](./media/site-recovery-set-up-physical-to-azure/plus-config-srv.png)
2. I den **Lägg till Server** bladet, kontrollera att **konfigurationsservern** visas i **servertyp**.
4. Hämta installationsfilen för enhetlig installationsprogram för Site Recovery.
5. Ladda ned valvregistreringsnyckeln. När du kör installationsprogrammet för enhetlig måste nyckeln för tjänstregistrering. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/site-recovery-set-up-physical-to-azure/set-source2.png)
6. På den dator som du använder som konfigurationsservern kör **Unified installationsprogram för Azure Site Recovery** att installera konfigurationsservern, processervern och huvudmålservern.

#### <a name="run-azure-site-recovery-unified-setup"></a>Kör Azure Site Recovery enhetlig installation

> [!TIP]
> Registrera för konfiguration av servern misslyckas om tiden på datorns systemklockan är mer än fem minuter från lokal tid. Synkronisera systemklockan med en [tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) innan du påbörjar installationen.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurationsservern kan installeras via kommandoraden. Mer information finns i [installera konfigurationsservern med hjälp av kommandoradsverktyg](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Vanliga problem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nästa steg

Nästa steg omfattar [konfigurera målmiljön](./site-recovery-prepare-target-physical-to-azure.md) i Azure.
