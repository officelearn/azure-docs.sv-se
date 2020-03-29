---
title: Ändra DATA 0-inställningar på StorSimple 8000-seriens enhet | Microsoft-dokument
description: Lär dig hur du använder Windows PowerShell för StorSimple för att konfigurera om DATA 0-nätverksgränssnittet på din StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 3cf136c5ddec8f4998d15c597914e1f806453945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631591"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Ändra inställningarna för DATA 0-nätverksgränssnittet på storsimple 8000-serien

## <a name="overview"></a>Översikt

Din Microsoft Azure StorSimple-enhet har sex nätverksgränssnitt, från DATA 0 till DATA 5. DATA 0-gränssnittet konfigureras alltid via Windows PowerShell-gränssnittet eller seriekonsolen och är automatiskt molnaktiverat. Observera att du inte kan konfigurera DATA 0-nätverksgränssnittet via Azure-portalen.

DATA 0-gränssnittet konfigureras först via installationsguiden under den första distributionen av StorSimple-enheten. När enheten är i driftläge kan du behöva konfigurera om DATA 0-inställningar. Den här självstudien innehåller två metoder för att ändra DATA 0-nätverksinställningar, både via Windows PowerShell för StorSimple.

Efter att ha läst den här guiden kommer du att kunna:

* Ändra data 0-nätverksinställning via installationsguiden
* Ändra data 0-nätverksinställningar via `Set-HcsNetInterface` cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Ändra nätverksinställningar för DATA 0 via installationsguiden
Du kan konfigurera om DATA 0-nätverksinställningarna genom att ansluta till Windows PowerShell-gränssnittet på Din StorSimple-enhet och starta en installationsguidesession. Gör följande för att ändra INställningarna för DATA 0:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Så här ändrar du nätverksinställningar för DATA 0 via installationsguiden
1. I den seriella konsolmenyn väljer du alternativ 1, **Logga in med full åtkomst**. Ange lösenordet **för enhetsadministratören**när du uppmanas att ange. Standardlösenordet är `Password1`.
2. Skriv följande i kommandotolken:
   
    `Invoke-HcsSetupWizard`
3. En installationsguide visas för att konfigurera enhetens DATA 0-gränssnitt. Ange nya värden för IP-adressen, gatewayen och nätmasken.

> [!NOTE]
> De fasta kontrollanternas IPs måste konfigureras om via **nätverksinställningsbladet** för StorSimple-enheten i Azure-portalen. Mer information finns i [Ändra nätverksgränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Ändra DATA 0-nätverksinställningar via Set-HcsNetInterface cmdlet
Ett alternativt sätt att konfigurera om DATA 0-nätverksgränssnittet är med hjälp av cmdleten. `Set-HcsNetInterface` Cmdleten körs från Windows PowerShell-gränssnittet på Din StorSimple-enhet. När du använder den här proceduren kan styrenhetens fasta IPs också konfigureras här. Gör följande för att ändra INställningarna för DATA 0: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Så här ändrar du data 0-nätverksinställningar via cmdlet set-hcsNetInterface
1. I den seriella konsolmenyn väljer du alternativ 1, **Logga in med full åtkomst**. Ange lösenordet för enhetsadministratören när du uppmanas att ange. Standardlösenordet är `Password1`.
2. Skriv följande i kommandotolken:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Skriv följande värden för DATA 0 i de vinklade parenteserna:
   
   * IPv4-adress
   * IPv4-gateway
   * IPv4-nätmask
   * Fast IPv4-adress för Controller 0
   * Fast IPv4-adress för Controller 1
     
     Mer information om hur du använder den här cmdleten finns i [Windows PowerShell för StorSimple cmdlet-referens](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera andra nätverksgränssnitt än DATA 0 kan du använda [konfigurera nätverksinställningarna i Azure-portalen](storsimple-8000-modify-device-config.md). 
* Om du får problem när du konfigurerar nätverksgränssnitten läser du [Felsöka distributionsproblem](storsimple-troubleshoot-deployment.md).

