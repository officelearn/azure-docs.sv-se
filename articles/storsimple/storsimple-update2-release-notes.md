---
title: "Viktig information för StorSimple 8000 Series uppdatering 2 | Microsoft Docs"
description: "Beskriver nya funktioner, problem och lösningar för StorSimple 8000 Series uppdatering 2."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 285c5abf574544737f3d30981a6c5b8f9548922a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 Series uppdatering 2 viktig information
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Följande information beskriver de nya funktionerna och identifiera kritiska öppna problem för StorSimple 8000 Series uppdatering 2. De innehåller också en lista över StorSimple-programvara, drivrutiner och uppdateringar av inbyggd disk i den här versionen. 

Uppdatering 2 kan tillämpas på alla StorSimple-enhet som kör versionen (GA) eller uppdatering 0.1 via Update 1.2. Enheten-version som är associerade med uppdatering 2 är 6.3.9600.17673.

Granska informationen som finns i viktig information innan du distribuerar uppdateringen i din StorSimple-lösning.

> [!IMPORTANT]
> * Det tar cirka 4 – 7 timmar att installera uppdateringen (inklusive Windows-uppdateringar). 
> * Uppdatering 2 har programvara, LSI drivrutiner och uppdateringar av inbyggd SSD.
> * För nya versioner, kan du inte se uppdateringar omedelbart eftersom vi gör en stegvis distribution av uppdateringar. Vänta några dagar och sedan söka efter uppdateringar igen som dessa är snart tillgängligt.
> 
> 

## <a name="whats-new-in-update-2"></a>Vad är nytt i uppdatering 2
Uppdatering 2 innehåller följande nya funktioner.

* **Lokalt fästa volymer** – i tidigare versioner av StorSimple 8000-serien datablock har nivåer till molnet baserat på användning. Det gick inte att garantera att block skulle stanna kvar på lokala. I uppdatering 2 när du skapar en volym kan du ange en volym som data lokalt Fäst och primära från volymen inte inte nivåindelas till molnet. Ögonblicksbilder av lokalt fästa volymer kopieras fortfarande till molnet för säkerhetskopiering så att molnet kan användas för data mobility och disaster recovery. Dessutom kan du ändra volymtypen (det vill säga konvertera nivåindelade volymer att lokalt fästa volymer och konvertera lokalt fästa volymer till nivåer). 
* **StorSimple virtuell enhet förbättringar** – tidigare, StorSimple 8000-serien placeras den virtuella enheten som en lösning för disaster recovery eller utveckling och testning. Det var bara en modell för virtuella enheten (model 1100). Uppdatering 2 innehåller två virtuella enhetsmodeller: 
  
  * 8010 (kallades tidigare 1100) –; ingen ändring har en kapacitet på 30 TB och använder Azure standardlagring.
  * – 8020 har 64 TB kapacitet och använder Azure Premium-lagring för bättre prestanda.
    
    Det finns en enda virtuell Hårddisk för både virtuella enhetsmodeller (8010/8020). När du startar den virtuella enheten identifierar parametrarna plattform och tillämpar rätt modellversion.
* **Förbättringar av nätverk** – uppdatering 2 innehåller följande förbättringar av nätverk:
  
  * Flera nätverkskort kan aktiveras för molnet så att växling vid fel kan inträffa om ett nätverkskort inte.
  * Routning förbättringar med fast molnet aktiverat block.
  * Online återförsök för misslyckade resurser innan en växling vid fel.
  * Nya aviseringar för tjänsten.
* **Uppdatera förbättringar** – i Uppdatera 1.2 och tidigare, StorSimple 8000-serien uppdaterades via två kanaler: Windows Update för klustring, iSCSI, och så vidare och Microsoft Update för binärfiler och inbyggd programvara.
    Uppdatering 2 använder Microsoft Update för alla uppdateringspaket. Detta ska leda till mindre tid korrigering eller göra växling vid fel. 
* **Uppdateringar av inbyggd** – följande firmware uppdateringar ingår:
  
  * LSI: lsi_sas2.sys produktversionen 2.00.72.10
  * Endast SSD (ingen Hårddisk uppdateringar): XMGG, XGEG, KZ50, F6C2 och VR08
* **Proaktiv Support** – uppdatering 2 hjälper Microsoft att dra ytterligare diagnostikinformation från enheten. När våra driftteamet identifierar enheter som har problem, är vi bättre utrustad för att samla in information från enheten och diagnostisera problem. **Acceptera uppdatering 2, kan vi har stöd för detta proaktiv**.    

## <a name="issues-fixed-in-update-2"></a>Problem som åtgärdas i uppdatering 2
Följande tabeller innehåller en översikt över problem som har lösts i uppdateringar 2.    

| Nej. | Funktion | Problem | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- |
| 1 |Nätverksgränssnitt |Efter en uppgradering till uppdatering 1 rapporterade StorSimple Manager-tjänsten att Data2 och Data3 portar misslyckades på en domänkontrollant. Det här problemet har korrigerats. |Ja |Nej |
| 2 |Uppdateringar |Efter en uppgradering till uppdatering 1 uppstod akustiskt larm varningar i den klassiska Azure-portalen på flera enheter. Det här problemet har korrigerats. |Ja |Nej |
| 3 |Openstack autentisering |När du använder Openstack som din molntjänstleverantör, kan ett felmeddelande som ditt moln autentisering sträng var för lång. Problemet har åtgärdats. |Ja |Nej |

## <a name="known-issues-in-update-2"></a>Kända problem i uppdatering 2
Följande tabell innehåller en översikt över kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer / lösning | Gäller för fysiska enheter | Gäller för den virtuella enheten |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvorum |I sällsynta fall om merparten av diskar i EBOD höljet av en 8600-enhet är frånkopplad ledde till Ingen disk i kvorum kommer lagringspoolen går offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår, kontakta Microsoft Support för nästa steg. |Ja |Nej |
| 2 |Felaktig styrenhets-ID |När en domänkontrollant ersättning utförs kan styrenhet 0 visas som kontrollant 1. Under controller ersättning, när avbildningen har lästs in från peer-noden kan styrenhets-ID visas först som peer-styrenhet-ID. I sällsynta fall kan problemet visas efter en omstart. |Ingen användaråtgärd krävs. Den här situationen ska lösas av sig självt när domänkontrollanten ersättningen är klar. |Ja |Nej |
| 3 |Lagringskonton |Använder Storage-tjänsten för att ta bort lagringskontot är ett scenario som inte stöds. Detta leder till en situation där användardata inte kan hämtas. | |Ja |Ja |
| 4 |Enheten växling vid fel |Flera redundans av en volymbehållare från samma källa enheten till olika målenheter stöds inte. Växling från en inaktiv enhet till flera enheter gör volymbehållarna på den första redundansväxlats enhet förlorar dataägarskap. Efter en växling, ska behållarna volymen visas eller fungera annorlunda när de visas i den klassiska Azure-portalen. | |Ja |Nej |
| 5 |Installation |Under StorSimple-kort för SharePoint-installation måste du ange en IP-adress för enheten för att slutföra installationen. | |Ja |Nej |
| 6 |Webbproxy |Om din webbproxykonfigurationen har det angivna protokollet HTTPS, enhet-till-tjänst-kommunikation kommer att påverkas och enheten ska försättas i offlineläge. Stöd för paket skapas också i process förbrukar betydande resurser på enheten. |Kontrollera att URL: en för web proxy har det angivna protokollet HTTP. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-configure-web-proxy.md). |Ja |Nej |
| 7 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet, måste du starta om den aktiva styrenheten på enheten. | |Ja |Nej |
| 8 |Hög molnet latens och hög i/o-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög molnet latens (ordning sekunder) och höga i/o-arbetsbelastning, enheten volymer som ingår i ett degraderat tillstånd och I/o kan misslyckas med felet ”enheten är inte klar”. |Du måste starta om styrenheterna eller utför en växling vid fel med enheten för att återställa från den här situationen manuellt. |Ja |Nej |
| 9 |Azure PowerShell |När du använder cmdleten StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - först 1 - vänta** du väljer det första objektet så att du kan skapa en ny **VolumeContainer** objekt, returnerar cmdleten alla objekt. |Omsluta cmdlet inom parentes på följande sätt: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - först 1 - vänta** |Ja |Ja |
| 10 |Migrering |När flera volymbehållare skickas för migrering är ETA för senaste säkerhetskopian exakt endast för den första volymbehållaren. Dessutom startas parallella migrering efter de första 4 säkerhetskopiorna i första volymbehållare migreras. |Vi rekommenderar att du migrerar en volymbehållare i taget. |Ja |Nej |
| 11 |Migrering |Efter återställningen läggs inte volymer till principen för säkerhetskopiering eller gruppen virtuell disk. |Du behöver lägga till dessa volymer till en princip för säkerhetskopiering för att skapa säkerhetskopior. |Ja |Ja |
| 12 |Migrering |När migreringen är klar, måste enheten 5000/7000-serien inte komma åt behållarna migrerade data. |Vi rekommenderar att du tar bort migrerade databehållare när migreringen har slutförts och bekräftats. |Ja |Nej |
| 13 |Klona och Katastrofåterställning |En StorSimple-enhet som kör uppdatering 1 kan inte klona eller utföra en återställning till en enhet med före uppdateringen 1 programvara. |Du behöver uppdatera målenheten uppdatering 1 för att tillåta dessa åtgärder |Ja |Ja |
| 14 |Migrering |Konfigurationssäkerhetskopia för migrering kan misslyckas på en serieenhet för 5000 7000-när volymen grupper med inga associerade volymer. |Ta bort alla tomma volym grupper med inga associerade volymer och försök sedan säkerhetskopian av konfigurationen. |Ja |Nej |
| 15 |Azure PowerShell-cmdlets och lokalt fästa volymer |Du kan inte skapa en lokalt Fäst volym via Azure PowerShell-cmdlets. (Alla volymer som du skapar via Azure PowerShell kommer att skikt.) |Använd alltid StorSimple Manager-tjänsten för att konfigurera lokalt fästa volymer. |Ja |Nej |
| 16 |Diskutrymme för lokalt fästa volymer |Om du tar bort en lokalt Fäst volym kan det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. StorSimple Manager-tjänsten uppdaterar lokalt tillgängligt utrymme cirka varje timme. |Vänta tills en timme innan du försöker skapa den nya volymen. |Ja |Nej |
| 17 |Lokalt fästa volymer |Din återställningsjobbet exponerar tillfälliga ögonblicksbilden säkerhetskopieringen i katalogen för säkerhetskopiering, men bara för återställningsjobbet varaktighet. Dessutom det visar att en virtuell diskgrupp med prefixet **tmpCollection** på den **Säkerhetskopieringsprinciper** sidan, men endast för återställningsjobbet varaktighet. |Detta kan inträffa om din Återställningsjobbet har bara lokalt Fäst volymer eller en blandning av lokalt Fäst och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, sker inte det här beteendet. Inga användaråtgärder krävs. |Ja |Nej |
| 18 |Lokalt fästa volymer |Om du avbryter en återställningsjobbet och det uppstår redundans controller omedelbart efteråt återställningsjobbet visas **misslyckades** i stället för **avbruten**. Om en återställningsjobbet misslyckas och det uppstår redundans controller omedelbart efteråt återställningsjobbet visas **avbruten** i stället för **misslyckades**. |Detta kan inträffa om din Återställningsjobbet har bara lokalt Fäst volymer eller en blandning av lokalt Fäst och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, sker inte det här beteendet. Inga användaråtgärder krävs. |Ja |Nej |
| 19 |Lokalt fästa volymer |Om du avbryter en återställningsjobbet eller om en återställning misslyckas och sedan en domänkontrollant växling vid fel, en ytterligare återställningsjobbet visas på den **jobb** sidan. |Detta kan inträffa om din Återställningsjobbet har bara lokalt Fäst volymer eller en blandning av lokalt Fäst och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, sker inte det här beteendet. Inga användaråtgärder krävs. |Ja |Nej |
| 20 |Lokalt fästa volymer |Om du försöker konvertera en nivåindelad volym (skapats och klonade med Update 1.2 eller tidigare) till en lokalt Fäst volym och enheten få slut på utrymme eller om ett avbrott i molnet, kan sedan clone(s) vara skadad. |Det här problemet uppstår bara med volymer som har skapats och klonade med före uppdatering 2 programvara. Detta bör vara ett ovanligt scenario. | | |
| 21 |Volymkonvertering |Uppdatera inte ACRs kopplade till en volym när en Volymkonvertering pågår (nivåer att lokalt Fäst eller vice versa). Uppdaterar ACRs kan resultera i skadade data. |Om det behövs, uppdatera ACRs innan konverteringen volym och gör inte ytterligare uppdateringar ACR medan konverteringen pågår. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Domänkontrollanten och firmware-uppdateringar i uppdatering 2
Den här versionen uppdaterar drivrutinen och disk inbyggd programvara på din enhet.

* Mer information om den inbyggda programvaran LSI uppdatera, se Microsoft Knowledge base-artikel 3121900. 
* Mer information om den inbyggda programvaran disk uppdatera, se Microsoft Knowledge base-artikel 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Uppdatering av virtuella enheter i uppdatering 2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installera uppdatering 2](storsimple-install-update-2.md) på StorSimple-enheten.

