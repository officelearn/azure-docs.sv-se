---
title: Hantera enheter med StorSimple Snapshot Manager | Microsoft Docs
description: Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att ansluta och hantera StorSimple-enheter.
services: storsimple
documentationcenter: ''
author: SharS
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 51632b8b68640814fc113a94925b6d6deaca4c5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482582"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Använd StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter
## <a name="overview"></a>Översikt
Du kan använda noder i StorSimple Snapshot Manager **omfång** fönstret för att verifiera importerade data för StorSimple-enheten och uppdatera anslutna enheter. Dessutom när du klickar på den **enheter** noden kan du se en lista över anslutna enheter och motsvarande statusinformation i den **resultat** fönstret.

![Anslutna enheter](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Bild 1: Ansluten enhet i StorSimple Snapshot Manager** 

Beroende på din **visa** val, den **resultat** visar följande information om varje enhet. (Mer information om hur du konfigurerar en vy, går du till [Visa-menyn](storsimple-use-snapshot-manager.md#view-menu).

| Resultaten kolumn | Beskrivning |
|:--- |:--- |
| Namn |Namnet på enheten som konfigurerats i den klassiska Azure-portalen |
| Modell |Modellnumret för enheten |
| Version |Versionen av programvaran är installerad på enheten |
| Status |Om enheten är tillgänglig |
| Senaste synkronisering |Datum och tid när enheten senast synkroniserades |
| Serienr |Serienumret för enheten |

Om du högerklickar på den **enheter** nod i den **omfång** fönstret som du kan välja mellan följande åtgärder:

* Lägga till eller ersätta en enhet
* Anslut en enhet och kontrollera import
* Uppdatera anslutna enheter

Om du klickar på den **enheter** noden och högerklicka sedan på en enhet namn i den **resultat** fönstret som du kan välja mellan följande åtgärder:

* Autentisera en enhet
* Visa enhetsinformation
* Uppdatera en enhet
* Ta bort en enhetskonfiguration
* Ändra enhetens lösenord

> [!NOTE]
> Alla dessa åtgärder är även tillgängliga i den **åtgärder** fönstret.


Den här självstudien beskrivs hur du använder StorSimple Snapshot Manager för att ansluta och hantera enheter och utföra följande uppgifter:

* Lägga till eller ersätta en enhet
* Anslut en enhet och kontrollera import
* Uppdatera anslutna enheter
* Autentisera en enhet
* Visa enhetsinformation
* Uppdatera en enskild enhet
* Ta bort en enhetskonfiguration
* Ändra en har upphört att gälla enhetslösenord
* Ersätt en misslyckad enhet

> [!NOTE]
> Allmän information om hur du använder StorSimple Snapshot Manager-gränssnittet går du till [StorSimple Snapshot Manager-användargränssnittet](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Lägga till eller ersätta en enhet
Använd följande procedur för att lägga till eller ersätta en StorSimple-enhet.

#### <a name="to-add-or-replace-a-device"></a>Lägga till eller ersätta en enhet
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på den **enheter** noden och klicka sedan på **konfigurera en enhet**. Den **konfigurera en enhet** dialogrutan visas.
   
    ![Konfigurera en StorSimple-enhet](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. I den **enhet** listrutan väljer du IP-adressen för enheten eller virtuell enhet. 
4. I den **lösenord** text skriver du lösenordet för StorSimple Snapshot Manager som du skapade för enheten i den klassiska Azure-portalen. Klicka på **OK**. StorSimple Snapshot Manager söker efter den enhet som du har identifierat. 
   
   * Om enheten inte är tillgänglig, StorSimple Snapshot Manager lägger till en anslutning.
   * Om enheten inte är tillgänglig av någon anledning, returnerar StorSimple Snapshot Manager ett felmeddelande. Klicka på **OK** att Stäng felmeddelandet och klicka sedan på **Avbryt** att Stäng den **konfigurera en enhet** dialogrutan.

## <a name="connect-a-device-and-verify-imports"></a>Anslut en enhet och kontrollera import
Använd följande procedur för att ansluta en StorSimple-enhet och kontrollera att alla befintliga volymgrupper som har kopplat säkerhetskopior importeras.

#### <a name="to-connect-a-device-and-verify-imports"></a>Anslut en enhet och verifiera import
1. För att ansluta till StorSimple Snapshot Manager för en enhet, följer du anvisningarna i Lägg till eller ersätta en enhet. När den ansluter till en enhet, svarar StorSimple Snapshot Manager på följande sätt:
   
   * Om enheten inte är tillgänglig av någon anledning, returnerar StorSimple Snapshot Manager ett felmeddelande. 
   
   * Om enheten inte är tillgänglig, StorSimple Snapshot Manager lägger till en anslutning. När du väljer enheten, visas den i den **resultat** fönstret och statusfältet anger att enheten är **tillgänglig**. StorSimple Snapshot Manager importerar alla volymgrupper som konfigurerats för den aktuella enheten, förutsatt att volymgrupper har associerat säkerhetskopieringar. Principer för säkerhetskopiering har inte importerats. Volymgrupper som saknar tillhörande säkerhetskopior importeras inte.
2. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
3. Högerklicka på den översta noden i den **omfång** fönstret och klicka sedan på **växla import visa**.
   
    ![Välj Växla import visning](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Den **växla import visa** dialogruta, som visar status för den importerade volymgrupper och säkerhetskopieringar. Klicka på **OK**.

När den volymgrupper och säkerhetskopieringar har importerats kan använda du StorSimple Snapshot Manager kan hantera dem, precis som du skulle hantera volymgrupper och säkerhetskopieringar som du skapat och konfigurerat med StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Uppdatera anslutna enheter
Använd följande procedur för att synkronisera anslutna StorSimple-enheter med StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Uppdatera anslutna enheter
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på **enheter**, och klicka sedan på **uppdatera enheter**. Detta synkroniserar anslutna enheter med StorSimple Snapshot Manager så att du kan visa volymgrupper och säkerhetskopior, inklusive eventuella nya tillägg. 
   
    ![Uppdatera StorSimple-enheter](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Den **uppdatera enheter** åtgärden hämtar alla nya volymgrupper och alla tillhörande säkerhetskopior från anslutna enheter. Till skillnad från den **Genomsök igen volymer** åtgärd som är tillgängliga för den **volymer** noden **uppdatera enheter** inte återställa säkerhetskopierade registret.

## <a name="authenticate-a-device"></a>Autentisera en enhet
Använd följande procedur för att autentisera en StorSimple-enhet med StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>För autentisering av en enhet
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret klickar du på **enheter**.
3. I den **resultat** , högerklicka på namnet på enheten och klicka sedan på **autentisera**.
4. Den **autentisera** dialogrutan visas. Skriv lösenordet för enheten och klicka på **OK**.
   
    ![Autentisera dialogrutan](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Visa enhetsinformation
Använd följande procedur för att visa information om en StorSimple-enhet och, om det behövs kan du synkronisera om enheten med StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Visa och synkronisera om enhetsinformation
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret klickar du på **enheter**.
3. I den **resultat** , högerklicka på namnet på enheten och klicka sedan på **information**.

4. Ju **enhetsinformation** dialogrutan visas. Den här rutan visar namn, modell, version, serienummer, status, iSCSI-målet kvalificerade namn (IQN), och senaste Synkroniseringsdatum och tid.

* Klicka på **synkronisera om** att synkronisera enheten.
* Klicka på **OK** eller **Avbryt** att stänga dialogrutan.
  
  ![Enhetsinformation](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Uppdatera en enskild enhet
Använd följande procedur för att synkronisera om en enskild StorSimple-enhet med StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Uppdatera en enhet
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** fönstret klickar du på **enheter**. 
3. I den **resultat** , högerklicka på namnet på enheten och klicka sedan på **uppdatera enheten**. Detta synkroniserar enheten med StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Ta bort en enhetskonfiguration
Använd följande procedur för att ta bort konfiguration för en enskild StorSimple-enhet från StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Att ta bort en enhetskonfiguration
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret klickar du på **enheter**. 
3. I den **resultat** , högerklicka på namnet på enheten och klicka sedan på **ta bort**. 
4. Följande meddelande visas. Klicka på **Ja** att ta bort konfigurationen eller klicka på **nr** du avbryta borttagningen.
   
    ![Ta bort enhetskonfiguration](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Ändra en har upphört att gälla enhetslösenord
Du måste ange ett lösenord för att autentisera en StorSimple-enhet med StorSimple Snapshot Manager. Du kan konfigurera det här lösenordet när du använder Windows PowerShell-gränssnittet för att ställa in enheten. Lösenordet kan går ut. Om det händer kan använda du klassiska Azure-portalen för att ändra lösenordet. Sedan, eftersom enheten har konfigurerats i StorSimple Snapshot Manager innan lösenordet upphör att gälla, måste du autentisera enhet i StorSimple Snapshot Manager igen.

#### <a name="to-change-the-expired-password"></a>Ändra lösenordet har upphört att gälla
1. Starta StorSimple Manager-tjänsten i den klassiska Azure-portalen.
2. Klicka på **enheter** > **konfigurera** för enheten.
3. Rulla ned till avsnittet StorSimple Snapshot Manager. Ange ett lösenord som är 14 och 15 tecken. Se till att lösenordet innehåller en blandning av versaler, gemener, siffror och specialtecken.
4. Ange lösenord för att bekräfta det. på nytt.
5. Klicka på **Spara** längst ned på sidan.

#### <a name="to-re-authenticate-the-device"></a>Återautentisera på enheten
1. Starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret klickar du på **enheter**. En lista över konfigurerade enheter visas i den **resultat** fönstret.
3. Välj enheten, högerklicka och klicka sedan på **autentisera**.
4. I den **autentisera** fönstret, ange det nya lösenordet.
5. Välj enheten, högerklicka och välj **uppdatering enheten**. Detta synkroniserar enheten med StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Ersätt en misslyckad enhet
Om en StorSimple-enhet misslyckas och ersätts med en enhet i vänteläge (redundans), Använd följande steg för att ansluta till den nya enheten och visa tillhörande säkerhetskopior.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Ansluta till en ny enhet efter redundans
1. Konfigurera iSCSI-anslutning till den nya enheten. Anvisningar, finns i ”steg 7: Montera, initiera och formatera en volym ”i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Om den nya StorSimple-enheten har samma IP-adress som den gamla servern, kanske du kan ansluta gammal konfiguration.


1. Stoppa Microsoft StorSimple Management-tjänsten:
   
   1. Starta Serverhanteraren.
   2. På instrumentpanelen i Serverhanteraren på den **verktyg** menyn och välj **Services**.
   3. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   4. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **stoppa tjänsten**.
2. Ta bort den konfigurationsinformation som rör gamla enheten:
   
   1. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog i Utforskaren.
   2. Ta bort filer i mappen BACatalog.
3. Starta om hanteringstjänsten för Microsoft StorSimple:
   
   1. På instrumentpanelen i Serverhanteraren på den **verktyg** menyn och välj **Services**.
   2. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   3. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **starta om tjänsten**.
4. Starta StorSimple Snapshot Manager.
5. Om du vill konfigurera den nya StorSimple-enheten, slutför du stegen i steg 2: Ansluta en StorSimple-enhet i [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Högerklicka på översta noden i den **omfång** fönstret (StorSimple Snapshot Manager i det här exemplet) och klicka sedan på **växla import visa**. 
7. Ett meddelande visas när den importerade volymgrupper och säkerhetskopieringar som är synliga i StorSimple Snapshot Manager. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att visa och hantera volymer](storsimple-snapshot-manager-manage-volumes.md).

