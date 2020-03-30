---
title: Hantera enheter med StorSimple Snapshot Manager | Microsoft-dokument
description: Beskriver hur du använder MMC-snapin-modulen StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter.
services: storsimple
documentationcenter: ''
author: twooley
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: a037ddc5a43b288349b17dc7d954991a2c634aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254695"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Använda StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter
## <a name="overview"></a>Översikt
Du kan använda noder i **scopefönstret** För StorSimple Snapshot Manager för att verifiera importerade StorSimple-enhetsdata och uppdatera anslutna lagringsenheter. När du klickar på noden **Enheter** kan du dessutom se en lista över anslutna enheter och motsvarande statusinformation i **fönstret Resultat.**

![Anslutna enheter](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Bild 1: Ansluten enhet för StorSimple Snapshot Manager** 

Beroende på dina **vyval** visas följande information om varje enhet i fönstret **Resultat.** (Mer information om hur du konfigurerar en vy finns på [Visa-menyn](storsimple-use-snapshot-manager.md#view-menu).

| Kolumnen Resultat | Beskrivning |
|:--- |:--- |
| Namn |Namnet på enheten som konfigurerats i den klassiska Azure-portalen |
| Modell |Enhetens modellnummer |
| Version |Den version av programvaran som är installerad på enheten |
| Status |Om enheten är tillgänglig |
| Senast synkroniserad |Datum och tid då enheten senast synkroniserades |
| Seriellt nr |Serienumret för enheten |

Om du högerklickar på noden **Enheter** i **fönstret Scope** kan du välja mellan följande åtgärder:

* Lägga till eller ersätta en enhet
* Ansluta en enhet och verifiera import
* Uppdatera anslutna enheter

Om du klickar på noden **Enheter** och sedan högerklickar på ett enhetsnamn i **resultatfönstret** kan du välja mellan följande åtgärder:

* Autentisera en enhet
* Visa enhetsinformation
* Uppdatera en enhet
* Ta bort en enhetskonfiguration
* Ändra ett enhetslösenord

> [!NOTE]
> Alla dessa åtgärder är också tillgängliga i fönstret **Åtgärder.**


I den här självstudien beskrivs hur du använder StorSimple Snapshot Manager för att ansluta och hantera enheter och utföra följande uppgifter:

* Lägga till eller ersätta en enhet
* Ansluta en enhet och verifiera import
* Uppdatera anslutna enheter
* Autentisera en enhet
* Visa enhetsinformation
* Uppdatera en enskild enhet
* Ta bort en enhetskonfiguration
* Ändra ett lösenord för en utgången enhet
* Ersätta en misslyckad enhet

> [!NOTE]
> Allmän information om hur du använder StorSimple Snapshot Manager-gränssnittet finns i [StorSimple Snapshot Manager användargränssnitt .](storsimple-use-snapshot-manager.md)


## <a name="add-or-replace-a-device"></a>Lägga till eller ersätta en enhet
Använd följande procedur för att lägga till eller ersätta en StorSimple-enhet.

#### <a name="to-add-or-replace-a-device"></a>Så här lägger du till eller byter ut en enhet
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på noden **Enheter** i **fönstret Omfattning** och klicka sedan på Konfigurera **en enhet**. Dialogrutan **Konfigurera en enhet** visas.
   
    ![Konfigurera en StorSimple-enhet](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. I listrutan **Enhet** väljer du ENHETENS ELLER DEN virtuella enhetens IP-adress. 
4. Skriv lösenordet för StorSimple Snapshot Manager som du skapade för enheten i den klassiska Azure-portalen i textrutan **Lösenord.** Klicka på **OK**. StorSimple Snapshot Manager söker efter den enhet som du identifierade. 
   
   * Om enheten är tillgänglig lägger StorSimple Snapshot Manager till en anslutning.
   * Om enheten inte är tillgänglig av någon anledning returnerar StorSimple Snapshot Manager ett felmeddelande. Klicka på **OK** för att stänga felmeddelandet och klicka sedan på **Avbryt** för att stänga dialogrutan **Konfigurera en enhet.**

## <a name="connect-a-device-and-verify-imports"></a>Ansluta en enhet och verifiera import
Använd följande procedur för att ansluta en StorSimple-enhet och kontrollera att alla befintliga volymgrupper som har associerade säkerhetskopior importeras.

#### <a name="to-connect-a-device-and-verify-imports"></a>Så här ansluter du en enhet och verifierar import
1. Om du vill ansluta en enhet till StorSimple Snapshot Manager följer du instruktionerna i Lägg till eller ersätt en enhet. När den ansluter till en enhet svarar StorSimple Snapshot Manager på följande sätt:
   
   * Om enheten inte är tillgänglig av någon anledning returnerar StorSimple Snapshot Manager ett felmeddelande. 
   
   * Om enheten är tillgänglig lägger StorSimple Snapshot Manager till en anslutning. När du väljer enheten visas den i **fönstret Resultat** och statusfältet anger att enheten är **tillgänglig**. StorSimple Snapshot Manager importerar alla volymgrupper som konfigurerats för enheten, förutsatt att volymgrupperna har associerade säkerhetskopior. Principer för säkerhetskopiering importeras inte. Volymgrupper som inte har associerade säkerhetskopior importeras inte.
2. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
3. Högerklicka på den översta noden i **scopefönstret** och klicka sedan på **Växla Importerar display**.
   
    ![Välj Växla importvisning](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Dialogrutan **Växla importvisning** visas som visar status för importerade volymgrupper och säkerhetskopior. Klicka på **OK**.

När volymgrupperna och säkerhetskopiorna har importerats kan du använda StorSimple Snapshot Manager för att hantera dem, på samma sätt som du hanterar volymgrupper och säkerhetskopior som du har skapat och konfigurerat med StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Uppdatera anslutna enheter
Använd följande procedur för att synkronisera anslutna StorSimple-enheter med StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Så här uppdaterar du anslutna enheter
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på **Enheter**i **fönstret Scope** och klicka sedan på **Uppdatera enheter**. Detta synkroniserar de anslutna enheterna med StorSimple Snapshot Manager så att du kan visa volymgrupper och säkerhetskopior, inklusive eventuella senaste tillägg. 
   
    ![Uppdatera StorSimple-enheterna](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Åtgärden **Uppdatera enheter** hämtar alla nya volymgrupper och alla associerade säkerhetskopior från anslutna enheter. Till skillnad från åtgärden **Sök om volymer** som är tillgänglig för noden **Volymer** återställs inte registret **för säkerhetskopiering.**

## <a name="authenticate-a-device"></a>Autentisera en enhet
Använd följande procedur för att autentisera en StorSimple-enhet med StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Så här autentiserar du en enhet
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Klicka på **Enheter**i **fönstret Omfattning** .
3. Högerklicka på namnet på enheten i **resultatfönstret** och klicka sedan på **Autentisera**.
4. Dialogrutan **Autentisera** visas. Skriv enhetslösenordet och klicka sedan på **OK**.
   
    ![Dialogrutan Autentisera](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Visa enhetsinformation
Använd följande procedur för att visa information om en StorSimple-enhet och, om det behövs, synkronisera om enheten med StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Så här visar och synkroniserar du om enhetsinformation
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Klicka på **Enheter**i **fönstret Omfattning** .
3. Högerklicka på namnet på enheten i **fönstret Resultat** och klicka sedan på **Information**.

4. Dialogrutan **Enhetsinformation** visas. I den här rutan visas namn, modell, version, serienummer, status, mål-IQN (Target iCSI Qualified Name) och senaste synkroniseringsdatum och -tid.

* Klicka på **Synkronisera om** om du vill synkronisera enheten.
* Stäng dialogrutan genom att klicka på **OK** eller **Avbryt.**
  
  ![Information om enhet](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Uppdatera en enskild enhet
Använd följande procedur för att synkronisera om en enskild StorSimple-enhet med StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Så här uppdaterar du en enhet
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager. 
2. Klicka på **Enheter**i **fönstret Omfattning** . 
3. Högerklicka på namnet på enheten i **resultatfönstret** och klicka sedan på **Uppdatera enhet**. Detta synkroniserar enheten med StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Ta bort en enhetskonfiguration
Använd följande procedur för att ta bort en enskild StorSimple-enhetskonfiguration från StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Så här tar du bort en enhetskonfiguration
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Klicka på **Enheter**i **fönstret Omfattning** . 
3. Högerklicka på namnet på enheten i **fönstret Resultat** och klicka sedan på **Ta bort**. 
4. Följande meddelande visas. Klicka på **Ja** om du vill ta bort konfigurationen eller klicka på **Nej** om du vill avbryta borttagningen.
   
    ![Ta bort enhetskonfiguration](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Ändra ett lösenord för en utgången enhet
Du måste ange ett lösenord för att autentisera en StorSimple-enhet med StorSimple Snapshot Manager. Du konfigurerar det här lösenordet när du använder Windows PowerShell-gränssnittet för att konfigurera enheten. Lösenordet kan dock upphöra att gälla. Om detta händer kan du använda den klassiska Azure-portalen för att ändra lösenordet. Eftersom enheten konfigurerades i StorSimple Snapshot Manager innan lösenordet upphörde att gälla måste du sedan autentisera enheten igen i StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Så här ändrar du det utgångna lösenordet
1. Starta Tjänsten StorSimple Manager i Den klassiska Azure-portalen.
2. Klicka på **Konfigurera enheter** > **för** enheten.
3. Bläddra ned till avsnittet StorSimple Snapshot Manager. Ange ett lösenord som är 14–15 tecken. Kontrollera att lösenordet innehåller en blandning av versaler, gemener, numeriska och specialtecken.
4. Ange lösenordet igen för att bekräfta det.
5. Klicka på **Spara** längst ned på sidan.

#### <a name="to-re-authenticate-the-device"></a>Så här autentiserar du om enheten
1. Starta StorSimple Snapshot Manager.
2. Klicka på **Enheter**i **fönstret Omfattning** . En lista över konfigurerade enheter visas i **fönstret Resultat.**
3. Markera enheten, högerklicka och klicka sedan på **Autentisera**.
4. Ange det nya lösenordet i fönstret **Autentisera.**
5. Markera enheten, högerklicka och välj **Uppdatera enhet**. Detta synkroniserar enheten med StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Ersätta en misslyckad enhet
Om en StorSimple-enhet misslyckas och ersätts av en standby-enhet (redundans) använder du följande steg för att ansluta till den nya enheten och visa de associerade säkerhetskopiorna.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Så här ansluter du till en ny enhet efter redundans
1. Konfigurera om iSCSI-anslutningen till den nya enheten. Instruktioner finns i "Steg 7: Montera, initiera och formatera en volym" i [Distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Om den nya StorSimple-enheten har samma IP-adress som den gamla kanske du kan ansluta den gamla konfigurationen.


1. Stoppa Microsoft StorSimple Management Service:
   
   1. Starta Serverhanteraren.
   2. Välj **Tjänster**på **Instrumentpanelen i Serverhanteraren på Instrumentpanelen i Serverhanteraren** .
   3. Välj **Microsoft StorSimple Management Service**i fönstret **Tjänster** .
   4. Klicka på **Stoppa tjänsten**under **Microsoft StorSimple Management Service**i den högra rutan.
2. Ta bort konfigurationsinformationen relaterad till den gamla enheten:
   
   1. I Utforskaren bläddrar du till C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Ta bort filerna i BACatalog-mappen.
3. Starta om Microsoft StorSimple Management Service:
   
   1. Välj **Tjänster**på **Instrumentpanelen i Serverhanteraren på Instrumentpanelen i Serverhanteraren** .
   2. Välj **Microsoft StorSimple Management Service**i fönstret **Tjänster** .
   3. Klicka på **Starta om tjänsten**under Microsoft **StorSimple Management Service**i den högra rutan.
4. Starta StorSimple Snapshot Manager.
5. Om du vill konfigurera den nya StorSimple-enheten slutför du stegen i steg 2: Anslut en StorSimple-enhet i [Distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Högerklicka på noden på den översta nivån i **scopefönstret** (StorSimple Snapshot Manager i exemplet) och klicka sedan på **Växla Importvisning**. 
7. Ett meddelande visas när importerade volymgrupper och säkerhetskopior visas i StorSimple Snapshot Manager. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att visa och hantera volymer](storsimple-snapshot-manager-manage-volumes.md).

