---
title: Hantera enheter med StorSimple Snapshot Manager | Microsoft Docs
description: "Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att ansluta och hantera StorSimple-enheter."
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: f5e3186a4271e0be781f367fa75ada195c58c960
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Använd StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter
## <a name="overview"></a>Översikt
Du kan använda noder i StorSimple Snapshot Manager **omfång** fönstret för att verifiera importerade data för StorSimple-enheten och uppdatera anslutna lagringsenheter. Dessutom, när du klickar på den **enheter** nod, kan du visa en lista över anslutna enheter och motsvarande statusinformation i den **resultat** fönstret.

![Anslutna enheter](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Bild 1: StorSimple Snapshot Manager ansluten enhet** 

Beroende på din **visa** val av **resultat** visar följande information om varje enhet. (Mer information om hur du konfigurerar en vy, gå till [Visa-menyn](storsimple-use-snapshot-manager.md#view-menu).

| Resultaten kolumn | Beskrivning |
|:--- |:--- |
| Namn |Namnet på enheten som konfigurerats i den klassiska Azure-portalen |
| Modellen |Modellnumret för enheten |
| Version |Versionen av programvaran på enheten |
| Status |Om enheten är tillgänglig |
| Synkroniserades senast |Datum och tid när enheten senast synkroniserades |
| Serienr |Serienumret för enheten |

Om du högerklickar på den **enheter** nod i den **omfång** rutan som du kan välja mellan följande åtgärder:

* Lägga till eller ersätta en enhet
* Ansluter en enhet och kontrollera import
* Uppdatera anslutna enheter

Om du klickar på den **enheter** nod och högerklicka sedan på en enhet namn i den **resultat** rutan som du kan välja mellan följande åtgärder:

* Autentisera en enhet
* Visa information om enhet
* Uppdatera en enhet
* Ta bort en enhetskonfiguration
* Ändra lösenordet för enheten

> [!NOTE]
> Alla dessa åtgärder är också tillgängliga i den **åtgärder** fönstret.


Den här självstudiekursen beskrivs hur du använder StorSimple Snapshot Manager för att ansluta och hantera enheter och utföra följande uppgifter:

* Lägga till eller ersätta en enhet
* Ansluter en enhet och kontrollera import
* Uppdatera anslutna enheter
* Autentisera en enhet
* Visa information om enhet
* Uppdatera en enskild enhet
* Ta bort en enhetskonfiguration
* Ändra ett enhetslösenord har upphört att gälla
* Ersätta en misslyckad enhet

> [!NOTE]
> Allmän information om StorSimple Snapshot Manager-gränssnittet går du till [StorSimple Snapshot Manager användargränssnittet](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Lägga till eller ersätta en enhet
Använd följande procedur för att lägga till eller ersätta en StorSimple-enhet.

#### <a name="to-add-or-replace-a-device"></a>Lägga till eller ersätta en enhet
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på den **enheter** noden och klicka sedan på **konfigurera en enhet**. Den **konfigurera en enhet** dialogrutan visas.
   
    ![Konfigurera en StorSimple-enhet](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. I den **enhet** listrutan väljer du IP-adressen till enheten eller virtuella enheten. 
4. I den **lösenord** text Skriv lösenordet för StorSimple Snapshot Manager som du skapade för enheten i den klassiska Azure-portalen. Klicka på **OK**. StorSimple Snapshot Manager söker efter den enhet som du har identifierats. 
   
   * Om enheten inte är tillgänglig, StorSimple Snapshot Manager lägger till en anslutning.
   * Om enheten inte är tillgänglig av någon anledning, returnerar StorSimple Snapshot Manager ett felmeddelande. Klicka på **OK** Stäng felmeddelandet och klicka sedan på **Avbryt** att stänga den **konfigurera en enhet** dialogrutan.

## <a name="connect-a-device-and-verify-imports"></a>Ansluter en enhet och kontrollera import
Använd följande procedur för att ansluta en StorSimple-enhet och kontrollera att alla befintliga volymen grupper som har associerade säkerhetskopior importeras.

#### <a name="to-connect-a-device-and-verify-imports"></a>Ansluter en enhet och kontrollera import
1. Ansluter en enhet till StorSimple Snapshot Manager, följ instruktionerna i Lägg till eller ersätta en enhet. När den ansluter till en enhet, svarar StorSimple Snapshot Manager på följande sätt:
   
   * Om enheten inte är tillgänglig av någon anledning, returnerar StorSimple Snapshot Manager ett felmeddelande. 
   
   * Om enheten inte är tillgänglig, StorSimple Snapshot Manager lägger till en anslutning. När du väljer enheten visas den i den **resultat** rutan och statusfältet anger att enheten är **tillgänglig**. StorSimple Snapshot Manager importerar alla volym grupper som konfigurerats för enheten, förutsatt att grupperna volymen har associerade säkerhetskopior. Principer för säkerhetskopiering har inte importerats. Volymen grupper som inte har tillhörande säkerhetskopior har inte importerats.
2. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
3. Högerklicka på den översta noden i den **omfång** rutan och klicka sedan på **växla import visa**.
   
    ![Välj Växla import visning](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Den **växla import visa** dialogruta, visar status för den importerade volymen grupper och säkerhetskopieringar. Klicka på **OK**.

När grupperna för volymen och säkerhetskopieringar har importerats kan använda du StorSimple Snapshot Manager kan hantera dem, precis som du skulle hantera grupper för volymen och säkerhetskopieringar som du skapat och konfigurerat med StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Uppdatera anslutna enheter
Använd följande procedur om du vill synkronisera anslutna StorSimple-enheter med StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Uppdatera anslutna enheter
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **Scope** fönstret, högerklicka på **enheter**, och klicka sedan på **uppdatera enheter**. Detta synkroniserar anslutna enheter med StorSimple Snapshot Manager så att du kan visa grupper volymen och säkerhetskopieringar, inklusive eventuella nya tillägg. 
   
    ![Uppdatera StorSimple-enheter](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Den **uppdatera enheter** åtgärd hämtar alla nya grupper i volymen och alla tillhörande säkerhetskopior från anslutna enheter. Till skillnad från den **skanna volymer** åtgärd som är tillgängliga för den **volymer** noden **uppdatera enheter** inte återställa registernyckeln för säkerhetskopiering.

## <a name="authenticate-a-device"></a>Autentisera en enhet
Använd följande procedur för att autentisera en StorSimple-enhet med StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>För autentisering av en enhet
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan klickar du på **enheter**.
3. I den **resultat** fönstret, högerklicka på enheten och klicka sedan på **autentisera**.
4. Den **autentisera** dialogrutan visas. Skriv lösenordet och klicka sedan på **OK**.
   
    ![Autentisera dialogrutan](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Visa information om enhet
Använd följande procedur för att visa information om en StorSimple-enhet och synkronisera enheten med StorSimple Snapshot Manager om det behövs.

#### <a name="to-view-and-resynchronize-device-details"></a>Visa och synkronisera information om enhet
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan klickar du på **enheter**.
3. I den **resultat** fönstret, högerklicka på enheten och klicka sedan på **information**.

4 det **enhetsinformation** dialogrutan visas. Den här rutan visas namn, modell, version, serienummer, status, iSCSI-målet kvalificerade namn (IQN), och senaste Synkroniseringsdatum och tid.

* Klicka på **omsynkronisering** att synkronisera enheten.
* Klicka på **OK** eller **Avbryt** att stänga dialogrutan.
  
  ![Information om enhet](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Uppdatera en enskild enhet
Använd följande procedur för att synkronisera om en enskild StorSimple-enhet med StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Så här uppdaterar du en enhet
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** rutan klickar du på **enheter**. 
3. I den **resultat** fönstret, högerklicka på enheten och klicka sedan på **uppdatera enheten**. Detta synkroniserar enheten med StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Ta bort en enhetskonfiguration
Använd följande procedur för att ta bort en konfiguration för enskilda StorSimple-enheter från StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Ta bort en enhetskonfiguration
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan klickar du på **enheter**. 
3. I den **resultat** fönstret, högerklicka på enheten och klicka sedan på **ta bort**. 
4. Följande meddelande visas. Klicka på **Ja** att ta bort konfigurationen eller klicka på **nr** du avbryta borttagningen.
   
    ![Ta bort konfiguration för enheter](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Ändra ett enhetslösenord har upphört att gälla
Du måste ange ett lösenord för att autentisera en StorSimple-enhet med StorSimple Snapshot Manager. Du kan konfigurera det här lösenordet när du använder Windows PowerShell-gränssnittet för att ställa in enheten. Lösenordet kan löpa ut. Om det händer kan använda du den klassiska Azure-portalen för att ändra lösenordet. Eftersom enheten har konfigurerats i StorSimple Snapshot Manager innan lösenordet upphör att gälla, måste du sedan nytt autentisera enhet i StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Ändra lösenordet har upphört att gälla
1. Starta StorSimple Manager-tjänsten i den klassiska Azure-portalen.
2. Klicka på **enheter** > **konfigurera** för enheten.
3. Rulla ned till avsnittet StorSimple Snapshot Manager. Ange ett lösenord som är 14 och 15 tecken. Kontrollera att lösenordet innehåller en blandning av versaler, gemener, siffror och särskilda tecken.
4. Ange lösenordet för att bekräfta det. igen.
5. Klicka på **Spara** längst ned på sidan.

#### <a name="to-re-authenticate-the-device"></a>Återautentisera på enheten
1. Starta StorSimple Snapshot Manager.
2. I den **omfång** rutan klickar du på **enheter**. En lista över konfigurerade enheter visas i den **resultat** fönstret.
3. Välj enhet, högerklicka och klicka sedan på **autentisera**.
4. I den **autentisera** fönster, ange det nya lösenordet.
5. Välj enhet, högerklicka och välj **uppdatera enheten**. Detta synkroniserar enheten med StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Ersätta en misslyckad enhet
Om en StorSimple-enhet misslyckas och ersätts med en enhet i vänteläge (failover), Använd följande steg för att ansluta till den nya enheten och visa tillhörande säkerhetskopior.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Ansluta till en ny enhet efter växling vid fel
1. Konfigurera iSCSI-anslutning till den nya enheten. Anvisningar, finns i ”steg 7: montera, initiera och formatera en volym” i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Om den nya StorSimple-enheten har samma IP-adress som den gamla servern, kanske du kan ansluta den gamla konfigurationen.


1. Stoppa tjänsten Microsoft StorSimple Management:
   
   1. Starta Serverhanteraren.
   2. På instrumentpanelen i Serverhanteraren på den **verktyg** väljer du **Services**.
   3. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   4. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **stoppa tjänsten**.
2. Ta bort konfigurationsinformation som är relaterade till den gamla enheten:
   
   1. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog i Utforskaren.
   2. Ta bort filer i mappen BACatalog.
3. Starta om tjänsten Microsoft StorSimple Management:
   
   1. På instrumentpanelen i Serverhanteraren på den **verktyg** väljer du **Services**.
   2. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   3. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **starta om tjänsten**.
4. Starta StorSimple Snapshot Manager.
5. Om du vill konfigurera den nya virtuella StorSimple-enheten, följ instruktionerna i steg 2: Anslut en virtuell StorSimple-enhet i [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Högerklicka på den översta noden i den **omfång** fönstret (StorSimple Snapshot Manager i exemplet) och klicka sedan på **växla import visa**. 
7. Ett meddelande visas när den importerade volymen grupper och säkerhetskopieringar visas i StorSimple Snapshot Manager. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att visa och hantera volymer](storsimple-snapshot-manager-manage-volumes.md).

