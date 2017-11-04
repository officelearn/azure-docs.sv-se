---
title: "Ändra lösenord via StorSimple Enhetshanteraren | Microsoft Docs"
description: "Beskriver hur du använder StorSimple Manager-tjänsten för att ändra administratörslösenordet StorSimple Snapshot Manager och enhet."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f178509c-f4e1-48a8-90b2-d4ad050eeb30
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 843d0a4bcc1807a6b1995d067e0f9e572307775d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Använda StorSimple Manager-tjänsten för att ändra din StorSimple-lösenord
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [använda StorSimple Manager-tjänsten för att ändra din StorSimple-lösenord](storsimple-8000-change-passwords.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Den klassiska Azure-portalen **konfigurera** innehåller alla enheter-parametrar som du kan konfigurera om på en StorSimple-enhet som hanteras av en StorSimple Manager-tjänsten. Den här självstudiekursen beskrivs hur du kan använda den **konfigurera** sidan för att ändra enhetsadministratören eller lösenordet för StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Ändra enhetens administratörslösenord
När du använder Windows PowerShell-gränssnittet för att komma åt StorSimple-enhet, måste du ange ett administratörslösenord för enheten. När den första StorSimple-enheten har registrerats med en tjänst standardlösenordet för det här gränssnittet är *Password1*. För att skydda dina data måste du ändra lösenordet i slutet av registreringsprocessen. Du kan inte avsluta registreringen utan att ändra lösenordet. Mer information finns i [steg3: konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Lösenordet som angavs först via Windows PowerShell-gränssnittet under registreringen kan sedan ändras via den klassiska Azure-portalen. Utför följande steg om du vill ändra enhetens administratörslösenord.

#### <a name="to-change-the-device-administrator-password"></a>Ändra enhetens administratörslösenord
1. I den klassiska portalen klickar du på **enheter** > **konfigurera** för din enhet.
2. Rulla ned till den **administratörslösenordet för enheten** avsnitt. Ange ett administratörslösenord som innehåller mellan 8 och 15 tecken. Lösenordet måste vara en kombination av 3 eller flera av versaler, gemener, siffror och särskilda tecken.
3. Bekräfta lösenordet.
4. Klicka på **Spara** längst ned på sidan.

Administratörslösenord för enheten ska nu uppdateras. Du kan använda den här ändrade lösenord för att komma åt Windows PowerShell-gränssnittet.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Ändra lösenordet för StorSimple Snapshot Manager
Programvaran StorSimple Manager finns på din Windows-värd och ger administratörer möjlighet att hantera säkerhetskopiering av din StorSimple-enhet i form av lokala och molnbaserade ögonblicksbilder.

När du konfigurerar en enhet i StorSimple Snapshot Manager, uppmanas du att ange enhetens IP-adress och lösenord för att autentisera lagringsenheten. Det här lösenordet först konfigureras via Windows PowerShell-gränssnittet. Mer information finns i [steg3: konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Lösenordet som angavs först via Windows PowerShell-gränssnittet under registreringen kan sedan ändras via den klassiska portalen. Utför följande steg om du vill ändra lösenordet för StorSimple Snapshot Manager.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Så här ändrar du lösenordet för StorSimple Snapshot Manager
1. I den klassiska portalen klickar du på **enheter** > **konfigurera** för din enhet.
2. Rulla ned till den **StorSimple Snapshot Manager** avsnitt. Ange ett lösenord som är 14 eller 15 tecken. Kontrollera att lösenordet innehåller en kombination av 3 eller flera av versaler, gemener, siffror och särskilda tecken.
3. Bekräfta lösenordet.
4. Klicka på **Spara** längst ned på sidan.

Lösenordet för StorSimple Snapshot Manager ska nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [StorSimple-säkerhet](storsimple-security.md).
* Lär dig mer om [ändra din enhetskonfiguration](storsimple-modify-device-config.md).
* Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

