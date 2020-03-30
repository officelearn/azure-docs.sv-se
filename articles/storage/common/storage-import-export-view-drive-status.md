---
title: Visa status för Azure Import/Export-jobb | Microsoft-dokument
description: Lär dig hur du visar status för import-/exportjobb och de enheter som används.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72821438"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Visa status för Import/Export-jobb i Azure

Den här artikeln innehåller information om hur du visar enhets- och jobbstatus för Azure Import/Export-jobb. Azure Import/Export-tjänsten används för att på ett säkert sätt överföra stora mängder data till Azure Blobbar och Azure-filer. Tjänsten används också för att exportera data från Azure Blob-lagring.  

## <a name="view-job-and-drive-status"></a>Visa jobb- och enhetsstatus
Du kan spåra status för dina import- eller exportjobb från Azure-portalen. Klicka på fliken **Importera/exportera.** En lista över dina jobb visas på sidan.

![Visa jobbtillstånd](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Visa jobbstatus

Du ser en av följande jobbstatusar beroende på var enheten är i processen.

| Jobbstatus | Beskrivning |
|:--- |:--- |
| Skapar | När ett jobb har skapats anges dess tillstånd till **Skapa**. Medan jobbet är i **skapande** tillstånd förutsätter tjänsten Import/Export att enheterna inte har levererats till datacentret. Ett jobb kan finnas kvar i det här tillståndet i upp till två veckor, varefter det tas bort automatiskt av tjänsten. |
| Frakt | När du har levererat paketet bör du uppdatera spårningsinformationen i Azure-portalen.  Detta förvandlar jobbet till **leveranstillstånd.** Jobbet finns kvar i **leveransstaten** i upp till två veckor. 
| Mottaget | När alla enheter har tagits emot i datacentret anges jobbtillståndet till **Mottaget**. |
| Överföra | När minst en enhet har börjat bearbetas är jobbtillståndet inställt **på Överföring**. Mer information finns i [Drive States](#view-drive-status). |
| Paketering | När alla enheter har slutfört bearbetningen placeras jobbet i **förpackningsläge** tills enheterna levereras tillbaka till dig. |
| Slutfört | När alla enheter har skickats tillbaka till dig, om jobbet har slutförts utan fel, är jobbet inställt **på Slutförd**. Jobbet tas automatiskt bort efter 90 dagar i tillståndet **Slutförd.** |
| Stängd | När alla enheter har skickats tillbaka till dig, om det fanns några fel under bearbetningen av jobbet, är jobbet inställt på **Stängd**. Jobbet tas automatiskt bort efter 90 dagar i tillståndet **Stängt.** |

## <a name="view-drive-status"></a>Visa enhetsstatus

I tabellen nedan beskrivs livscykeln för en enskild enhet när den övergår genom ett import- eller exportjobb. Det aktuella tillståndet för varje enhet i ett jobb visas i Azure-portalen.

I följande tabell beskrivs varje tillstånd som varje enhet i ett jobb kan passera.

| Körtillstånd | Beskrivning |
|:--- |:--- |
| Angivna | För ett importjobb anges när jobbet skapas från Azure-portalen **.** För ett exportjobb, eftersom ingen enhet anges när jobbet skapas, är det ursprungliga **enhetstillståndet mottaget**. |
| Mottaget | Enheten övergår till tillståndet **Mottaget** när tjänsten Import/Export har bearbetat de enheter som togs emot från rederiet för ett importjobb. För ett exportjobb är det ursprungliga enhetstillståndet tillståndet **Mottaget** tillstånd. |
| NeverReceived (NeverReceived) | Enheten flyttas till tillståndet **NeverReceived** när paketet för ett jobb anländer men paketet inte innehåller enheten. En enhet går också in i det här tillståndet om det har gått två veckor sedan tjänsten tog emot leveransinformationen, men paketet har ännu inte anlänt till datacentret. |
| Överföra | En enhet flyttas till tillståndet **Överföring** när tjänsten börjar överföra data från enheten till Azure Storage. |
| Slutfört | En enhet flyttas till tillståndet **Slutförd** när tjänsten har överfört alla data utan fel.
| SlutfördMoreInfo | En enhet flyttas till tillståndet **CompletedMoreInfo** när tjänsten har stött på vissa problem när data kopieras antingen från eller till enheten. Informationen kan innehålla fel, varningar eller informationsmeddelanden om att skriva över blobbar.
| Levereras Tillbaka | En enhet flyttas till tillståndet **ShippedBack** när den har skickats från datacentret tillbaka till avsändaradressen. |

Den här avbildningen från Azure-portalen visar enhetstillståndet för ett exempeljobb:

![Visa enhetstillstånd](./media/storage-import-export-service/drivestate.png)

I följande tabell beskrivs tillstånden för enhetsfel och de åtgärder som vidtagits för varje tillstånd.

| Körtillstånd | Händelse | Upplösning / Nästa steg |
|:--- |:--- |:--- |
| NeverReceived (NeverReceived) | En enhet som är markerad som **NeverReceived** (eftersom den inte togs emot som en del av jobbets leverans) anländer till en annan leverans. | Operationsteamet flyttar enheten till **Mottagen**. |
| Ej tillämpligt | En enhet som inte ingår i något jobb anländer till datacentret som en del av ett annat jobb. | Enheten markeras som en extra enhet och returneras till dig när jobbet som är associerat med det ursprungliga paketet är slutfört. |

## <a name="time-to-process-job"></a>Dags att bearbeta jobbet
Hur lång tid det tar att bearbeta ett import-/exportjobb varierar beroende på ett antal faktorer, till exempel:

-  Leveranstid
-  Ladda på datacentret
-  Jobbtyp och storlek på de data som kopieras
-  Antal diskar i ett jobb. 

Import/export-tjänsten har inget serviceavtal men tjänsten strävar efter att slutföra kopian inom 7 till 10 dagar efter att diskarna har tagits emot. Förutom den status som publiceras på Azure Portal kan REST-API:er användas för att spåra jobbförloppet. Parametern procent färdigt i [API-anropet listjobb](/previous-versions/azure/dn529083(v=azure.100)) ger den procentuella kopian förlopp.


## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget WAImportExport](storage-import-export-tool-how-to.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Azure Import Export REST API-exempel](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
