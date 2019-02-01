---
title: Migrera data på StorSimple 5000-7000-serien 8000-serien enheten | Microsoft Docs
description: Innehåller en översikt och förutsättningar av funktionen för migrering.
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
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495861"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrera data från StorSimple 5000-7000-serien till enheten i 8000-serien

> [!IMPORTANT]
> - Den 31 juli 2019 når StorSimple 5000/7000-serien slutet av supportstatus (EOS). Vi rekommenderar att StorSimple 5000/7000-kunder som migrerar till en av de alternativ som beskrivs i dokumentet.
> - Migreringen är för närvarande en assisterad åtgärd. Om du planerar att migrera data från enheten StorSimple 5000-7000-serien till en enhet för 8000-serien, måste du schemalägga migrering med Microsoft Support. Microsoft Support kan sedan din prenumeration för migrering. Läs mer om hur du [öppna ett supportärende](storsimple-8000-contact-microsoft-support.md).
> - När du registrerat tjänstbegäran kan ta det par veckor att genomföra migreringsplanen och startar migreringen.
> - Innan du kontaktar Microsoft Support, måste du granska och slutför den [migrering krav](#migration-prerequisites) anges i artikeln.

## <a name="overview"></a>Översikt

Den här artikeln introducerar den migrering-funktionen som tillåter StorSimple 5000-7000-kunder att migrera sina data till StorSimple 8000-serien fysisk enhet eller en 8010/8020-molninstallationer. Den här artikeln innehåller också länkar till en nedladdningsbar stegvis genomgång av de steg som krävs för att migrera data från en 5000 – 7000 serien äldre enhet till en 8000-serien fysiska eller molninstallationen.

Den här artikeln gäller för både lokala 8000-serieenhet samt StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Migrering funktionen jämfört med värd-sida-migrering

Du kan flytta dina data med hjälp av funktionen för migrering eller genom att utföra en värd-sida-migrering. Det här avsnittet beskrivs specifika för varje metod, inklusive för- och nackdelar. Använd den här informationen för att ta reda på vilken metod som du vill använda för att migrera dina data.

Funktionen migrering simulerar (DR) återställningsprocessen 7000/5000 seriens till 8000-serien. Den här funktionen kan du migrera data från 5000/7000-serien format till 8000-serien format på Azure. Migreringsprocessen initieras med hjälp av Migreringsverktyget för StorSimple. Verktyget startar hämtningen och konverteringen av säkerhetskopierade metadata på enhet för 8000-serien och använder sedan den senaste säkerhetskopian för att exponera volymer på enheten.

|      | Experter                                                                                                                                     |Nackdelar                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migreringsprocessen bevarar historiken för säkerhetskopior som gjorts på 5000/7000-serien.                                               | När användare försöker komma åt data, hämtar migreringen data från Azure därmed dra på dig kostnader för hämtning av data.                                     |
| 2.   | Inga data migreras på värd-sida.                                                                                                     | Processen måste avbrott mellan början av den och senaste säkerhetskopieringen som visas på 8000-serien (kan beräknas med hjälp av migreringsverktyget). |
| 3.   | Den här processen bevarar alla principer, bandbreddsmallar, kryptering och andra inställningar på enheter i 8000-serien.                      | Användaråtkomst ska ta med endast de data som nås av användarna och kommer inte att rehydrate hela datamängden.                                                  |
| 4.   | Den här processen kräver mer tid att konvertera alla äldre säkerhetskopieringar i Azure som utförs asynkront utan att påverka produktionsmiljön | Migrering kan bara utföras på en nivå för konfiguration av molnet.  Enskilda volymer i en molnkonfiguration kan inte migreras separat                       |

En värd-sida-migrering kan konfigurera för 8000-serien oberoende av varandra och kopiera data från 5000/7000-serieenhet till enheten i 8000-serien. Det här motsvarar att migrera data från en lagringsenhet till en annan. En mängd olika verktyg, till exempel Diskboss robocopy används för att kopiera data.

|      | Experter                                                                                                                      |Nackdelar                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migrering kan vara närmat sig stegvist på basis av-volym.                                               | Tidigare säkerhetskopior (vidtas på 5000/7000-serien) är inte tillgängliga på enheten 8000-serien.                                                                                                       |
| 2.   | Tillåter för konsolidering av data i ett lagringskonto på Azure.                                                       | Första säkerhetskopiering till molnet på 8000-serien tar längre tid som alla data på 8000-serien behov som ska säkerhetskopieras till Azure.                                                                     |
| 3.   | Alla data är lokala i installationen efter en lyckad migrering. Det finns ingen fördröjningar vid åtkomst till data. | Azure-lagring samband ökar tills data tas bort från 5000/7000-enhet.                                                                                                        |
| 4.   |                                                                                                                           | Om den 7000/5000-serien har en stor mängd data, under migreringen måste dessa data hämtas från azure som tillkommer kostnader och svarstider som rör laddades ned från Azure |

Den här artikeln handlar bara om funktionen för migrering från 5000/7000 att enhet 8000-serien. Mer information om värd-sida-migrering, går du till [migrering från andra lagringsenheter](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Förutsättningar för migrering

Här följer migrering kraven för en äldre 5000 eller 7000 serieenhet och StorSimple-enheten 8000-serien.

> [!IMPORTANT]
> Granska och slutför migreringen förutsättningarna innan du skicka en serviceförfrågan med Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>För enheten 5000/7000-serien (källa)

Innan du börjar migreringen måste du se till att:

* Du har din 5000 eller 7000-serien käll-enhet. enheten kan vara live eller ned.

    > [!IMPORTANT]
    > Vi rekommenderar att du har seriell åtkomst till den här enheten under migreringsprocessen. Det bör finnas några enhetsproblem kan seriell åtkomst underlätta felsökningen.

* 5000 eller 7000 serien källenheten kör programvara version v2.1.1.518 eller senare. Tidigare versioner stöds inte.
* Kontrollera den version som din 5000 och 7000-serien körs, leta i det övre högra hörnet i Web-Gränssnittet. Programvaruversion som din enhet körs bör visas. För migrering, bör din 5000 och 7000-serien med v2.1.1.518.

    ![Kontrollera programvaruversionen på äldre enhet](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Om dina realtidsappar för enheter som inte kör v2.1.1.518 eller senare, uppgradera datorn till den minsta versionen som krävs. Detaljerade anvisningar finns i [uppgradera datorn till v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Om du kör v2.1.1.518, går du till webbgränssnittet för att se om det finns några meddelanden för registret Återställ fel. Om registret återställning misslyckades, köra återställning av registret. Mer information finns i så här [köra återställning av registret](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Om du har en inaktiv enhet som inte har körts v2.1.1.518 kan du utföra en redundansväxling till en ersättningsenhet som kör v2.1.1.518. Detaljerade anvisningar finns DR för StorSimple-enheten 5000/7000-serien.
    * Säkerhetskopiera data för din enhet genom att ta en ögonblicksbild i molnet.
    * Sök efter eventuella andra aktiva säkerhetskopieringsjobb som körs på källenheten. Detta inkluderar jobb på StorSimple-konsolvärden Data Protection. Vänta tills de aktuella jobb ska slutföras.


### <a name="for-the-8000-series-physical-device-target"></a>Den fysiska enheten för 8000-serien (mål)

Innan du börjar migreringen måste du se till att:

* Enheten target 8000-serien är registrerade och som körs. Läs mer om hur du [distribuera StorSimple-enheten med StorSimple Manager-tjänsten](storsimple-8000-deployment-walkthrough-u2.md).
* Enheten 8000-serien har den senaste StorSimple 8000-serien uppdatering 5 är installerat och körs 6.3.9600.17845 eller senare version. Om enheten inte har de senaste uppdateringarna installerade måste du installera de senaste uppdateringarna innan du kan fortsätta med migreringen. Läs mer om hur du [installera den senaste uppdateringen på enheten 8000-serien](storsimple-8000-install-update-5.md).
* Din Azure-prenumeration har aktiverats för migrering. Om prenumerationen inte är aktiverad, kan du kontakta Microsoft Support om du vill aktivera prenumerationen för migrering.

### <a name="for-the-80108020-cloud-appliance-target"></a>För 8010/8020 molninstallationen (målet)

Kontrollera innan du påbörjar migreringen:

* Molninstallationen mål är registrerade och körs. Läs mer om hur du [distribuera och hantera StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* Molninstallationen körs den senaste StorSimple 8000-serien uppdatering 5 programvaruversion 6.3.9600.17845. Om din molninstallation inte körs uppdatering 5, skapa en ny uppdatering 5-molninstallation innan du fortsätter med migreringen. Läs mer om hur du [skapa 8010/8020-molninstallationer](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>För den dator som kör StorSimple-Migreringsverktyget

StorSimple-Migreringsverktyget är ett gränssnittsbaserade verktyg som hjälper dig att migrera data från en StorSimple 5000-7000-serien till en enhet för 8000-serien. Använd en dator som uppfyller följande krav för att installera Migreringsverktyg för StorSimple.

Datorn är ansluten till Internet och:

* Kör följande operativsystem
    * Windows 10.
    * Windows Server 2012 R2 (eller senare) att installera Migreringsverktyg för StorSimple.
* Har installerats på .NET 4.5.2.
* Har minst 5 GB ledigt utrymme för att installera och använda verktyget.

> [!TIP]
> Om din StorSimple-enhet är ansluten till en Windows Server-värd, kan du installera Migreringsverktyget på Windows Server-värddatorn.

#### <a name="to-install-storsimple-migration-tool"></a>Installera Migreringsverktyg för StorSimple

Utför följande steg för att installera Migreringsverktyg för StorSimple på datorn.

1. Kopiera mappen _StorSimple8000SeriesMigrationTool_ till din Windows-dator. Se till att den enhet där programvaran kopieras har tillräckligt med utrymme.

    Öppna konfigurationsfilen verktyget _StorSimple8000SeriesMigrationTool.exe.config_ i mappen. Här är ett utdrag från filen.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Redigera de värden som motsvarar nycklarna och ersätta med:

    * `UserName` – Användarnamnet att logga in på Azure-portalen.
    * `SubscriptionName and SubscriptionId` – Namn och ID: T för din Azure-prenumeration. I StorSimple Device Manager-tjänsten startsidan under **Allmänt**, klickar du på **egenskaper**. Kopiera prenumerationens namn och prenumerations-ID som är associerade med din tjänst.
    * `ResourceName` – Namnet på din StorSimple Device Manager-tjänsten i Azure-portalen. Filterläget visas under Egenskaper.
    * `ResourceGroup` – Namnet på resursgruppen som är associerade med din StorSimple Device Manager-tjänsten i Azure-portalen. Filterläget visas under Egenskaper.
    ![Kontrollera egenskaper för målenhet](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – Azure Active Directory klient-ID i Azure-portalen. Logga in på Microsoft Azure som en administratör. I Microsoft Azure-portalen klickar du på **Azure Active Directory**. Under **hantera**, klickar du på **egenskaper**. Klient ID visas i den **katalog-ID** box.
    ![Kontrollera klient-ID för Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Spara ändringarna i konfigurationsfilen.
4.  Kör den _StorSimple8000SeriesMigrationTool.exe_ att starta verktyget. När du tillfrågas om autentiseringsuppgifter, ange de autentiseringsuppgifter som hör till din prenumeration på Azure-portalen. 
5.  StorSimple Migreringsverktyget Användargränssnittet visas.
  

## <a name="next-steps"></a>Nästa steg
Ladda ned de stegvisa anvisningarna om hur du [migrera data från en StorSimple 5000-7000-serien till en enhet för 8000-serien](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
