---
title: Migrera data på StorSimple 5000-7000-serien till 8000-seriens enhet| Microsoft-dokument
description: Ger en översikt och förutsättningarna för migreringsfunktionen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631687"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrera data från StorSimple 5000-7000-serien till 8000-seriens enhet

> [!IMPORTANT]
> - Den 31 juli 2019 kommer StorSimple 5000/7000-serien att nå slutet av supportstatusen (EOS). Vi rekommenderar att StorSimple 5000/7000-seriens kunder migrerar till något av alternativen som beskrivs i dokumentet.
> - Migreringen är för närvarande en assisterad åtgärd. Om du tänker migrera data från storsimple 5000-7000-seriens enhet till en enhet i 8000-serien måste du schemalägga migreringen med Microsoft Support. Microsoft Support aktiverar sedan din prenumeration för migrering. Mer information finns i hur du [öppnar en supportbiljett](storsimple-8000-contact-microsoft-support.md).
> - När du har arkiverat tjänstbegäran kan det ta några veckor att köra migreringsplanen och faktiskt starta migreringen.
> - Innan du kontaktar Microsoft Support måste du granska och slutföra de [migreringsförutsättningskrav](#migration-prerequisites) som anges i artikeln.

## <a name="overview"></a>Översikt

Den här artikeln introducerar migreringsfunktionen som gör att StorSimple 5000-7000-seriens kunder kan migrera sina data till Den fysiska enheten i StorSimple 8000-serien eller en molninstallation 8010/8020. Den här artikeln länkar också till en nedladdningsbar steg-för-steg-genomgång av de steg som krävs för att migrera data från en äldre enhet i 5000-7000-serien till en fysisk eller molnbaserad 8000-serie.

Den här artikeln gäller både den lokala 8000-serien och StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Migreringsfunktion jämfört med migrering på värdsidan

Du kan flytta dina data med migreringsfunktionen eller genom att utföra en migrering på värdsidan. I det här avsnittet beskrivs detaljerna för varje metod, inklusive för- och nackdelar. Använd den här informationen för att ta reda på vilken metod du vill använda för att migrera dina data.

Migreringsfunktionen simulerar en DR-process (Disaster Recovery) från 7000/5000-serien till 8000-serien. Med den här funktionen kan du migrera data från 5000/7000-serieformat till 8000-serieformat på Azure. Migreringsprocessen initieras med hjälp av storsimileringsverktyget. Verktyget startar hämtningen och konverteringen av metadata för säkerhetskopiering på 8000-seriens enhet och använder sedan den senaste säkerhetskopian för att exponera volymerna på enheten.

|      | Fördelar                                                                                                                                     |Nackdelar                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migreringsprocessen bevarar historiken för säkerhetskopior som togs på 5000/7000-serien.                                               | När användare försöker komma åt data hämtar den här migreringen data från Azure och medför därmed datahämtningskostnader.                                     |
| 2.   | Inga data migreras på värdsidan.                                                                                                     | Processen behöver driftstopp mellan starten av säkerhetskopian och den senaste säkerhetskopian som visas i 8000-serien (kan beräknas med migreringsverktyget). |
| 3.   | Den här processen bevarar alla principer, bandbreddsmallar, kryptering och andra inställningar på enheter i 8000-serien.                      | Användaråtkomst kommer att föra tillbaka endast de data som nås av användarna och kommer inte att rehydrera hela datauppsättningen.                                                  |
| 4.   | Den här processen kräver ytterligare tid för att konvertera alla äldre säkerhetskopior i Azure som görs asynkront utan att påverka produktionen | Migrering kan endast göras på molnkonfigurationsnivå.  Enskilda volymer i en molnkonfiguration kan inte migreras separat                       |

En migrering på värdsidan gör det möjligt att konfigurera 8000-serien oberoende av dem och kopiera data från enheten i 5000/7000-serien till 8000-serien. Detta motsvarar att migrera data från en lagringsenhet till en annan. En mängd olika verktyg som Diskboss, robocopy används för att kopiera data.

|      | Fördelar                                                                                                                      |Nackdelar                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migration kan hanteras stegvis volym för volym.                                               | Tidigare säkerhetskopior (tagna på 5000/7000-serien) kommer inte att vara tillgängliga på 8000-serien enheten.                                                                                                       |
| 2.   | Möjliggör konsolidering av data till ett lagringskonto på Azure.                                                       | Första säkerhetskopieringen till molnet i 8000-serien tar längre tid eftersom alla data i 8000-serien måste säkerhetskopieras till Azure.                                                                     |
| 3.   | Efter en lyckad migrering är alla data lokala på apparaten. Det finns inga fördröjningar när du öppnar data. | Azure-lagringsförbrukningen ökar tills data tas bort från 5000/7000-enheten.                                                                                                        |
| 4.   |                                                                                                                           | Om enheten i 7000/5000-serien har en stor mängd data måste dessa data hämtas från Azure under migreringen, vilket medför kostnader och svarstider relaterade till nedladdning av data från Azure |

Den här artikeln fokuserar bara på migreringsfunktionen från enheten i 5000/7000 till 8000-serien. Mer information om migrering på värdsidan finns i [Migrering från andra lagringsenheter](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Förutsättningar för migrering

Här är migreringsförutsättningarna för din äldre enhet i 5 000- eller 7000-serien och StorSimple-enheten i 8000-serien.

> [!IMPORTANT]
> Granska och fyll i migreringsförutsättningarna innan du lämnar in en tjänstbegäran till Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>För 5000/7000-seriens enhet (källa)

Innan du påbörjar migreringen bör du se till att:

* Du har din källa enhet i 5000 eller 7000-serien. enheten kan vara strömförande eller ned.

    > [!IMPORTANT]
    > Vi rekommenderar att du har seriell åtkomst till den här enheten under hela migreringen. Om det finns några enhetsproblem kan seriell åtkomst hjälpa till med felsökning.

* Källenheten i 5 000 eller 7000-serien kör programversion v2.1.1.518 eller senare. Tidigare versioner stöds inte.
* Om du vill kontrollera vilken version som 5 000- eller 7000-serien körs tittar du i det övre högra hörnet i webbgränssnittet. Detta bör visa den programvaruversion som enheten kör. För migrering bör din 5000 eller 7000-serien köra v2.1.1.518.

    ![Kontrollera programvaruversionen på äldre enhet](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Om din aktiva enhet inte kör v2.1.1.518 eller senare uppgraderar du systemet till den minimala version som krävs. Detaljerade instruktioner finns i [Uppgradera systemet till v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Om du kör v2.1.1.518 går du till webbgränssnittet för att se om det finns några meddelanden om fel vid återställning av registret. Om registeråterställning hade misslyckats kör du registeråterställning. Mer information finns i hur du [kör registeråterställning](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Om du har en nedenhet som inte körde v2.1.1.518 utför du en redundansenhet till en ersättningsenhet som kör v2.1.1.518. Detaljerade instruktioner finns i DR på din StorSimple-enhet i 5000/7000-serien.
    * Säkerhetskopiera data för din enhet genom att ta en ögonblicksbild av molnet.
    * Sök efter andra aktiva säkerhetskopieringsjobb som körs på källenheten. Detta inkluderar jobben på StorSimple Data Protection Console-värden. Vänta tills de aktuella jobben har slutförts.


### <a name="for-the-8000-series-physical-device-target"></a>För den fysiska enheten i 8000-serien (mål)

Innan du påbörjar migreringen bör du se till att:

* Din målenhet i 8000-serien är registrerad och igång. Mer information finns i hur [du distribuerar din StorSimple-enhet med StorSimple Manager-tjänsten](storsimple-8000-deployment-walkthrough-u2.md).
* Enheten i 8000-serien har den senaste Uppdateringen av StorSimple 8000 Series 5 installerad och kör 6.3.9600.17845 eller senare. Om enheten inte har de senaste uppdateringarna installerade måste du installera de senaste uppdateringarna innan du kan fortsätta med migreringen. Mer information finns i hur du [installerar den senaste uppdateringen på enheten i 8000-serien](storsimple-8000-install-update-5.md).
* Din Azure-prenumeration är aktiverad för migrering. Om din prenumeration inte är aktiverad kontaktar du Microsoft Support för att aktivera din prenumeration för migrering.

### <a name="for-the-80108020-cloud-appliance-target"></a>För molninstallationen 8010/8020 (mål)

Innan du påbörjar migreringen bör du se till att:

* Din målmolninstallation är registrerad och igång. Mer information finns i hur [du distribuerar och hanterar StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* Molninstallationen kör den senaste Versionen av StorSimple 8000 Series Update 5 6.3.9600.17845. Om molninstallationen inte kör Uppdatering 5 skapar du en ny molninstallation för Uppdatering 5 innan du fortsätter med migreringen. Mer information finns i hur du [skapar en molninstallation 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>För datorn som kör StorSimple Migration tool

StorSimple Migration tool är ett gränssnittsbaserat verktyg som gör att du kan migrera data från en StorSimple 5000-7000-serie till en enhet i 8000-serien. Om du vill installera verktyget StorSimple Migration använder du en dator som uppfyller följande krav.

Datorn har Internet-anslutning och:

* Kör följande operativsystem
    * Windows 10.
    * Windows Server 2012 R2 (eller senare) för att installera StorSimple Migration-verktyget.
* Har .NET 4.5.2 installerat.
* Har minst 5 GB ledigt utrymme för att installera och använda verktyget.

> [!TIP]
> Om StorSimple-enheten är ansluten till en Windows Server-värd kan du installera migreringsverktyget på värddatorn för Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Så här installerar du verktyget StorSimple Migration

Gör följande för att installera StorSimple Migration-verktyget på datorn.

1. Kopiera mappen _StorSimple8000SeriesMigrationTool_ till din Windows-dator. Kontrollera att enheten där programvaran kopieras har tillräckligt med utrymme.

    Öppna verktyget config filen _StorSimple8000SeriesMigrationTool.exe.config_ i mappen. Här är utdrag av filen.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Redigera de värden som motsvarar tangenterna och ersätt med:

    * `UserName`– Användarnamn för att logga in på Azure-portalen.
    * `SubscriptionName and SubscriptionId`– Namn och ID för din Azure-prenumeration. Klicka på Egenskaper under **Allmänt**på målsidan för Tjänsten StorSimple.Klicka på **Egenskaper**. Kopiera prenumerationsnamnet och prenumerations-ID:t som är kopplat till din tjänst.
    * `ResourceName`– Namn på din StorSimple Device Manager-tjänst i Azure-portalen. Visas även under serviceegenskaper.
    * `ResourceGroup`– Namnet på resursgruppen som är associerad med tjänsten StorSimple Device Manager i Azure-portalen. Visas även under serviceegenskaper.
    ![Kontrollera tjänstegenskaper för målenhet](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– Azure Active Directory-klient-ID i Azure-portalen. Logga in på Microsoft Azure som administratör. Klicka på **Azure Active Directory**i Microsoft Azure-portalen . Klicka på **Egenskaper** under **Hantera**. Klient-ID visas i rutan **Katalog-ID.**
    ![Kontrollera klient-ID för Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Spara ändringarna i konfigurationsfilen.
4.  Kör _StorSimple8000SeriesMigrationTool.exe_ för att starta verktyget. När du uppmanas att ange autentiseringsuppgifterna som är associerade med din prenumeration i Azure-portalen. 
5.  Användargränssnittet för storsimplemigrering visas.
  

## <a name="next-steps"></a>Nästa steg
Ladda ned steg-för-steg-instruktioner om hur [du migrerar data från en StorSimple 5000-7000-serie till en enhet i 8000-serien](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
