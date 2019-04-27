---
title: Ändra DATA 0-inställningar på enheten StorSimple 8000 | Microsoft Docs
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631591"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Ändra DATA 0-gränssnitt nätverksinställningarna på din enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple-enheten har sex nätverksgränssnitt från DATA 0 till DATA 5. DATA 0 gränssnittet är alltid konfigurerad via Windows PowerShell-gränssnittet eller seriekonsolen och är automatiskt moln-aktiverat. Observera att du inte kan konfigurera DATA 0-nätverksgränssnittet via Azure portal.

DATA 0 gränssnittet först konfigureras via installationsguiden under inledande distribution av StorSimple-enheten. När enheten är i ett arbetsläge kan du behöva konfigurera om DATA 0 inställningar. Den här självstudien innehåller två metoder för att ändra DATA 0 nätverksinställningar, både via Windows PowerShell för StorSimple.

När du har läst den här självstudiekursen kommer du att kunna:

* Ändra DATA 0-nätverksinställning via installationsguiden
* Ändra DATA 0 nätverksinställningar via den `Set-HcsNetInterface` cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Ändra DATA 0 nätverksinställningar via installationsguiden
Du kan konfigurera om DATA 0 nätverksinställningar genom att ansluta till Windows PowerShell-gränssnittet för StorSimple-enheten och starta en session för installationen-guiden. Utför följande steg om du vill ändra DATA 0 inställningar:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Ändra DATA 0 nätverksinställningar via installationsguiden
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas ange den **enhetens administratörslösenord**. Standardlösenordet är `Password1`.
2. Skriv följande i kommandotolken:
   
    `Invoke-HcsSetupWizard`
3. En installationsguide visas för att konfigurera DATA 0-gränssnittet på enheten. Ange nya värden för IP-adressen, gatewayen och nätmask.

> [!NOTE]
> Fast styrenheterna IP-adresser måste konfigureras via den **nätverksinställningar** bladet för StorSimple-enheten i Azure-portalen. Mer information går du till [ändra nätverksgränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Ändra DATA 0 nätverksinställningar via cmdlet Set-HcsNetInterface
Ett annat sätt att konfigurera om DATA 0-nätverksgränssnittet är med i `Set-HcsNetInterface` cmdlet. Cmdleten körs från Windows PowerShell-gränssnittet för StorSimple-enheten. När du använder den här proceduren kan kontrollenheternas fasta IP-adresser även konfigureras här. Utför följande steg om du vill ändra DATA 0 inställningar: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Ändra DATA 0 nätverksinställningar via cmdlet Set-HcsNetInterface
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas ange enhetens administratörslösenord. Standardlösenordet är `Password1`.
2. Skriv följande i kommandotolken:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    I vinkelparenteser, skriver du följande värden för DATA 0:
   
   * IPv4 address
   * IPv4-gateway
   * IPv4-nätmask
   * Fast IPv4-adress för kontrollenhet 0
   * Fast IPv4-adress för kontrollenhet 1
     
     Mer information om användning av den här cmdleten, gå till [Windows PowerShell för StorSimple cmdlet-referens](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Nästa steg
* Du kan använda för att konfigurera nätverksgränssnitt än DATA 0, den [Konfigurera nätverk i Azure-portalen](storsimple-8000-modify-device-config.md). 
* Om du får problem när du konfigurerar dina nätverksgränssnitt, se [felsöka distributionsproblem](storsimple-troubleshoot-deployment.md).

