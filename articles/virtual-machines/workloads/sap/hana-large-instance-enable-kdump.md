---
title: Skript för att aktivera kdump i SAP HANA (stora instanser) | Microsoft Docs
description: Skript för att aktivera kdump i SAP HANA (stora instanser) HLI typ I, HLI typ II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204941"
---
# <a name="enable-kdump-service"></a>Aktivera kdump-tjänsten

Det här dokumentet beskriver information om hur du aktiverar kdump-tjänsten på den stora Azure HANA-instansen (**typ I och typ II**)

## <a name="supported-skus"></a>SKU: er som stöds

|  Hana-stor instans typ   |  OS-leverantör   |  OS-paketets version   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>Krav

- Kdump-tjänsten `/var/crash` använder katalogen för att skriva dum par, se till att partitionen motsvarar den här katalogen har tillräckligt med utrymme för att ta emot dumpar.

## <a name="setup-details"></a>Installations information

- Skript för att aktivera kdump hittar du [här](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)

- Kör det här skriptet på HANA-stor instans med kommandot nedan

    > [!NOTE]
    > sudo privilegium som krävs för att köra det här kommandot.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Om kommandot utdata kdump har Aktiver ATS, startar du om systemet för att tillämpa ändringen, sedan har kdump Aktiver ATS. Starta om systemet för att tillämpa ändringarna.

- Om kommandots utdata inte kunde utföra en viss åtgärd, avslutar!!!!, kdump-tjänsten inte är aktive rad. Se avsnittet [support problem](#support-issue).

## <a name="test-kdump"></a>Testa kdump

> [!NOTE]
>  Under åtgärden utlöses en kernel-krasch och en omstart av systemet.

- Utlösa en kernel-krasch

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- När systemet har startats om kontrollerar du `/var/crash` katalogen för kernel krasch-loggar.

- Om katalogen `/var/crash` innehåller med aktuellt datum har kdump Aktiver ATS.

## <a name="support-issue"></a>Support ärende

Om skriptet Miss lyckas med ett fel eller om kdump inte är aktiverat, kan du generera en tjänstbegäran med Microsoft Support-teamet med följande information.

* Prenumerations-ID för HLI

* servernamn

* OS-leverantör

* OS-version

* Kernelversion
