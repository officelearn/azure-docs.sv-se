---
title: "Ändra DATA 0-inställningar på en StorSimple-enhet | Microsoft Docs"
description: "Lär dig hur du använder Windows PowerShell för StorSimple för att konfigurera om DATA 0-nätverksgränssnittet på din StorSimple-enhet."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 58e3d509-f425-4a7f-b650-d496a7c85193
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 089988354c5e1ca2e8d5d1554084062a655e43da
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-device"></a>Ändra DATA 0-gränssnittet nätverksinställningarna på din StorSimple-enhet
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [ändra DATA 0-gränssnittet nätverksinställningarna på din StorSimple-enhet](storsimple-8000-modify-data-0.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Microsoft Azure StorSimple-enheten har sex nätverksgränssnitt från DATA 0 till DATA 5. DATA 0 gränssnittet är alltid konfigurerad via Windows PowerShell-gränssnittet eller seriekonsolen och är automatiskt moln-aktiverat. Observera att du kan konfigurera DATA 0-nätverksgränssnittet via den klassiska Azure-portalen. 

DATA 0 gränssnittet först konfigureras via guiden under installation av StorSimple-enhet. När enheten är i ett arbetsläge kan du behöva konfigurera om DATA 0 inställningar. Den här självstudiekursen innehåller två metoder för att ändra DATA 0 nätverksinställningar, både via Windows PowerShell för StorSimple.

När du har läst den här självstudiekursen kommer du att kunna:

* Ändra DATA 0 nätverk via installationsguiden
* Ändra DATA 0 nätverksinställningar via den `Set-HcsNetInterface` cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Ändra DATA 0 nätverksinställningar via installationsguiden
Du kan konfigurera om DATA 0 nätverksinställningar genom att ansluta till Windows PowerShell-gränssnittet av StorSimple-enheten och starta en session för installationsprogrammet guiden. Utför följande steg om du vill ändra DATA 0 inställningar:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Att ändra DATA 0 nätverksinställningar via installationsguiden
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas ange den **enhetens administratörslösenord**. Standardlösenordet är `Password1`.
2. Skriv följande vid kommandotolken:
   
    `Invoke-HcsSetupWizard`
3. En installationsguide kommer visas när du konfigurerar DATA 0-gränssnittet på enheten. Ange nya värden för IP-adress, gateway och nätmask.

> [!NOTE]
> Fast domänkontrollanter IP-adresser måste konfigureras via den **konfigurera** sidan i StorSimple-enheten i den klassiska Azure-portalen. Mer information finns på [ändra nätverksgränssnitt](storsimple-modify-device-config.md#modify-network-interfaces).
> 
> 

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Ändra DATA 0 nätverksinställningar via cmdlet Set-HcsNetInterface
Ett annat sätt att konfigurera om DATA 0-nätverksgränssnittet är med i `Set-HcsNetInterface` cmdlet. Cmdleten körs från Windows PowerShell-gränssnittet för din StorSimple-enhet. När du använder den här proceduren, kan de styrenhets-fästa IP-adresser även konfigureras här. Utför följande steg om du vill ändra DATA 0 inställningar: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Att ändra DATA 0 nätverksinställningar via cmdlet Set-HcsNetInterface
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas ange enhetens administratörslösenord. Standardlösenordet är `Password1`.
2. Skriv följande vid kommandotolken:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    I vinkelparenteser, skriver du följande värden för DATA 0:
   
   * IPv4-adress
   * IPv4-gateway
   * IPv4-nätmask
   * Fast IPv4-adressen för styrenhet 0
   * Fast IPv4-adressen för styrenhet 1
     
     Mer information om hur du använder denna cmdlet går du till [Windows PowerShell för StorSimple cmdlet-referens](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Nästa steg
* Du kan använda för att konfigurera nätverksgränssnitt förutom DATA 0, den [konfigurationssidan i den klassiska Azure-portalen](storsimple-modify-device-config.md). 
* Om du får problem när du konfigurerar din nätverksgränssnitt, se [felsöka distributionsproblem](storsimple-troubleshoot-deployment.md).

