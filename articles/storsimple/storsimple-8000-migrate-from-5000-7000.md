---
title: "Migrera data på StorSimple 5000 7000-serien 8000-serien enheten | Microsoft Docs"
description: "Ger en översikt och förutsättningar för migrering-funktion."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/11/2017
ms.author: alkohli
ms.openlocfilehash: 36df62c4b01c623702707d39c6af59f4752ee6e0
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrera data från StorSimple 5000 7000-serien 8000-serien enheten

> [!IMPORTANT]
> - Migreringen är för närvarande en assisterad åtgärd. Om du planerar att migrera data från enheten StorSimple 5000 7000-serien till en enhet för 8000-serien, måste du schemalägga migrering med Microsoft-supporten. Microsoft Support kommer sedan att aktivera din prenumeration för migrering. Mer information finns i så här [skapar ett supportärende](storsimple-8000-contact-microsoft-support.md).
> - När du filen tjänstbegäran kan det ta några veckor att genomföra planen för migreringen och att starta migreringen.
> - Innan du kontaktar Microsoft Support kan vara noga med att reivew och fullständig den [migrering krav](#migration-prerequisites) anges i artikeln.

## <a name="overview"></a>Översikt

Den här artikeln introducerar migrering-funktion som gör att StorSimple 5000 7000-kunder att migrera sina data till StorSimple 8000-serien fysisk enhet eller ett program som 8010/8020 moln. Den här artikeln innehåller också länkar till en nedladdningsbar stegvis genomgång av de steg som krävs för att migrera data från en 5000 7000 serien äldre enhet till en 8000-serien fysiska eller molnet installation.

Den här artikeln gäller för både lokala 8000-serien enheten samt StorSimple moln-enhet.


## <a name="migration-feature-versus-host-side-migration"></a>Funktionen för hårdlänksmigrering jämfört med värd-sida-migrering

Du kan flytta dina data med hjälp av funktionen migrering eller genom att utföra en migrering sida värden. Det här avsnittet beskriver egenskaperna för varje metod inklusive- och nackdelar. Använd informationen för att ta reda på vilken metod du vill använda för att migrera dina data.

Funktionen för hårdlänksmigrering simulerar (DR) återställningsprocessen från 7000/5000-serien 8000-serien. Den här funktionen kan du migrera data från format för 5000/7000-serien 8000-serien format på Azure. Migreringsprocessen initieras med hjälp av verktyget StorSimple-migrering. Verktyget startar hämtningen och konvertering av metadata för säkerhetskopian på enheten för 8000-serien och använder sedan den senaste säkerhetskopian för att exponera volymer på enheten.

|      | Tekniker                                                                                                                                     |Nackdelar                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migreringsprocessen bevarar historiken för säkerhetskopior som gjorts på 5000/7000-serien.                                               | När användare försöker komma åt data hämtar migreringen data från Azure vilket medför kostnader för hämtning av data.                                     |
| 2.   | Inga data migreras på värddatorns sida.                                                                                                     | Processen måste driftstopp mellan starten av den och senaste säkerhetskopieringen som angetts i 8000-serien (kan beräknas med hjälp av migreringsverktyget). |
| 3.   | Den här processen bevarar alla principer, bandbredd mallar, kryptering och andra inställningar på enheter i 8000-serien.                      | Användaråtkomst kommer att få tillbaka endast data som nås av användare och kommer inte att rehydrate hela datamängden.                                                  |
| 4.   | Den här processen kräver mer tid att konvertera äldre säkerhetskopieringar i Azure som görs asynkront utan att påverka tillverkningen | Migreringen kan bara utföras på en nivå för konfiguration av molnet.  Enskilda volymer i konfigurationen för molnet kan inte migreras separat                       |

En värd-sida-migrering kan ställa in oberoende av 8000-serien och kopiera data från enheten 5000/7000-serien 8000-serien enheten. Detta motsvarar att migrera data från en lagringsenhet till en annan. En mängd olika verktyg, till exempel Diskboss robocopy används för att kopiera data.

|      | Tekniker                                                                                                                      |Nackdelar                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migreringen kan vara närmat sig stegvist volymen av volym.                                               | Tidigare säkerhetskopior (tas på 5000/7000-serien) är inte tillgänglig på enheten 8000-serien.                                                                                                       |
| 2.   | Gör det möjligt för konsolidering av data i ett lagringskonto på Azure.                                                       | Första säkerhetskopiering till molnet i 8000-serien tar längre tid som alla data på 8000-serien behöver säkerhetskopieras till Azure.                                                                     |
| 3.   | Alla data är lokala på anordningen efter en lyckad migrering. Det finns inga fördröjningar när åtkomst till data. | Azure storage-förbrukning kommer att öka tills data tas bort från enheten 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Om enheten 7000/5000-serien har en stor mängd data, under migreringen måste dessa data hämtas från azure som påförs kostnader och svarstider som rör hämtar data från Azure |

Den här artikeln fokuserar enbart på funktionen migrering från 5000/7000 till 8000-serieenhet. Mer information om värden sida-migrering, gå till [migrering från andra lagringsenheter](http://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating Data to StorSimple Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Förutsättningar för migrering

Här är migrering förutsättningarna för äldre 5000 och 7000 serien enheten och 8000-serien StorSimple-enhet.

> [!IMPORTANT]
> Granska och slutför migreringen krav innan du arkiverar en tjänstbegäran med Microsoft-supporten.

### <a name="for-the-50007000-series-device-source"></a>För 5000/7000-serieenhet (källa)

Innan du påbörjar migreringen kan du se till att:

* Du har din 5000 eller 7000-serien datakällan enhet. enheten kan vara live eller ned.

    > [!IMPORTANT]
    > Vi rekommenderar att du har seriell åtkomst till den här enheten under hela migreringsprocessen. Det bör finnas några enhetsproblem kan seriell åtkomst underlätta felsökningen.

* 5000 och 7000 serien källenheten kör programvara version v2.1.1.518 eller senare. Tidigare versioner stöds inte.
* Kontrollera versionen som körs serierna 5000 och 7000, se i det övre högra hörnet på din Webbgränssnittet. Detta ska visa programvaruversionen med din enhet. För migrering, bör serierna 5000 och 7000 köra v2.1.1.518.

    ![Kontrollera programvaruversionen på äldre enhet](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Om enheten live inte körs v2.1.1.518 eller senare och uppgradera datorn till den minimala versionen som krävs. Detaljerade anvisningar finns i [uppgradera datorn till v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Om du kör v2.1.1.518, gå till webbgränssnittet om det finns inga meddelanden för registret återställning. Om registret återställningen misslyckades, kör du registret återställning. Mer information finns på hur du [köra registret återställning](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Om du har en inaktiv enhet som inte har körts v2.1.1.518 utför en växling till en ersättning enhet som kör v2.1.1.518. Detaljerade anvisningar finns i DR av StorSimple-enheten 5000/7000-serien.
    * Säkerhetskopiera data för din enhet genom att ta en ögonblicksbild i molnet.
    * Sök efter alla andra aktiva säkerhetskopieringsjobb som körs på källan. Detta inkluderar jobb på StorSimple Data Protection konsolen värden. Vänta tills de aktuella jobben ska slutföras.


### <a name="for-the-8000-series-physical-device-target"></a>För den fysiska enheten för 8000-serien (mål)

Innan du påbörjar migreringen kan du se till att:

* Mål 8000-serien enheten är registrerad och körs. Mer information finns i så här [distribuera din StorSimple-enhet med StorSimple Manager-tjänsten](storsimple-8000-deployment-walkthrough-u2.md).
* Enheten 8000-serien har den senaste StorSimple 8000 Series uppdatering 5 är installerat och körs 6.3.9600.17845 eller senare version. Om enheten inte har de senaste uppdateringarna installerade måste du installera de senaste uppdateringarna innan du kan fortsätta med migreringen. Mer information finns i så här [installera den senaste uppdateringen på enheten 8000-serien](storsimple-8000-install-update-5.md).
* Azure-prenumerationen har aktiverats för migrering. Om din prenumeration inte är aktiverad, kan du kontakta Microsoft Support om du vill aktivera prenumerationen för migrering.

### <a name="for-the-80108020-cloud-appliance-target"></a>För 8010/8020 moln-enhet (målet)

Se till innan du påbörjar migreringen:

* Dina mål moln installation är registrerad och körs. Mer information finns i så här [distribuera och hantera StorSimple moln installation](storsimple-8000-cloud-appliance-u2.md).
* Moln-enheten kör den senaste StorSimple 8000 Series uppdatering 5 programvaruversion 6.3.9600.17845. Om moln-installation inte körs uppdatering 5, skapa en ny uppdatering 5 molnet installation innan du går vidare med migreringen. Mer information finns i så här [skapa en 8010/8020 moln installation](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>För datorer som kör StorSimple-Migreringsverktyget

StorSimple-Migreringsverktyget är ett gränssnittsbaserade verktyg som gör att du kan migrera data från en StorSimple 5000 7000-serien till en enhet för 8000-serien. Om du vill installera verktyget StorSimple-migrering, använder du en dator som uppfyller följande krav.

Datorn är ansluten till Internet och:

* Kör följande operativsystem
    * Windows 10.
    * Windows Server 2012 R2 (eller senare) att installera Migreringsverktyg för StorSimple.
* Har installerats på .NET 4.5.2.
* Har minst 5 GB ledigt utrymme för att installera och använda verktyget.

> [!TIP]
> Om din StorSimple-enhet är ansluten till en Windows Server-värd, kan du installera Migreringsverktyget på värddatorn för Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Installera Migreringsverktyg för StorSimple

Utför följande steg för att installera StorSimple Migreringsverktyget på datorn.

1. Kopiera mappen _StorSimple8000SeriesMigrationTool_ till Windows-datorn. Kontrollera att enheten där programmet kopieras har tillräckligt med utrymme.

    Öppna verktyget konfigurationsfilen _StorSimple8000SeriesMigrationTool.exe.config_ i mappen. Här är fragment av filen.
    
    ```
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Redigera värden för nycklarna och ersätta med:

    * `UserName`– Användarnamn för att logga in på Azure-portalen.
    * `SubscriptionName and SubscriptionId`– Namn och ID för din Azure-prenumeration. I Enhetshanteraren för StorSimple-tjänsten landningssida, under **allmänna**, klickar du på **egenskaper**. Kopiera prenumerationsnamn och prenumerations-ID som är associerade med din tjänst.
    * `ResourceName`– Namnet på din StorSimple Enhetshanteraren tjänst i Azure-portalen. Dessutom visas under tjänstegenskaper.
    * `ResourceGroup`– Namnet på resursgruppen som är associerade med din StorSimple Enhetshanteraren tjänst i Azure-portalen. Dessutom visas under tjänstegenskaper.
    ![Kontrollera egenskaper för målenhet](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– Azure Active Directory klient-ID i Azure-portalen. Logga in på Microsoft Azure som en administratör. I Microsoft Azure-portalen klickar du på **Azure Active Directory**. Under **hantera**, klickar du på **egenskaper**. Klient ID visas i den **katalog-ID** rutan.
    ![Kontrollera klient-ID för Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Spara ändringarna i konfigurationsfilen.
4.  Kör den _StorSimple8000SeriesMigrationTool.exe_ att starta verktyget. När du tillfrågas om autentiseringsuppgifter, ange de autentiseringsuppgifter som hör till din prenumeration på Azure-portalen. 
5.  StorSimple Migreringsverktyget Användargränssnittet visas.
  

## <a name="next-steps"></a>Nästa steg
Hämta stegvisa instruktioner om hur du [migrera data från en StorSimple 5000 7000-serien till en serieenhet i 8000-](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
