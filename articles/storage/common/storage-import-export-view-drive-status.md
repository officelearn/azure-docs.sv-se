---
title: Visa status för jobb på Azure Import/Export | Microsoft Docs
description: Lär dig hur du visar status för Import/Export-jobb och de enheter som används.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 176cbf190b6442682a222eb4f24b6583fb87a46b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661019"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Visa status för jobb på Azure Import/Export

Den här artikeln innehåller information om hur du visar status för enheten och jobb för Azure Import/Export-jobb. Azure Import/Export-tjänsten används för att säkert överföra stora mängder data till Azure-BLOB och Azure-filer. Tjänsten används också för att exportera data från Azure Blob storage.  

## <a name="view-job-and-drive-status"></a>Visa status för jobbet och enhet
Du kan spåra statusen för din importera eller exportera jobben från Azure-portalen. Klicka på den **Import/Export** fliken. En lista över jobb visas på sidan.

![Visa jobbets status](./media/storage-import-export-service/jobstate.png)


## <a name="view-job-status"></a>Visa jobbstatus

Du ser något av följande jobbstatus beroende på om enheten är i processen.

| Jobbstatus | Beskrivning |
|:--- |:--- |
| Skapar | När ett jobb skapas, dess status är inställd på **skapa**. När jobbet är i den **skapa** tillstånd, tjänsten Import/Export förutsätter att enheterna inte har levererats till datacentret. Ett jobb kan finnas kvar i det här tillståndet för upp till två veckor, efter vilken bort automatiskt av tjänsten. |
| Fraktas | När du levererar paketet ska du uppdatera spårningsinformation i Azure-portalen.  Detta aktiverar jobbet i **leverans** tillstånd. Jobbet finns kvar i den **leverans** tillstånd i upp till två veckor. 
| Mottagning | När alla enheter tas emot på Datacenter jobbets status är inställd på **mottagna**. |
| Överförs | När minst en enhet har startat bearbetning visas jobbets status är inställd på **överföra**. Mer information finns på [Enhetsstatus](#view-drive-status). |
| Packas | När alla enheter har bearbetat jobbet placeras i **paketering** tillstånd tills enheterna som har levererats till dig. |
| Slutfört | När alla enheter levereras till dig om jobbet har slutförts utan fel, sedan jobbet har angetts till **slutförd**. Jobbet tas automatiskt bort efter 90 dagar i den **slutförd** tillstånd. |
| Stängd | När alla enheter levereras till dig om några fel uppstod under bearbetning av jobbet, jobbet har angetts till **stängd**. Jobbet tas automatiskt bort efter 90 dagar i den **stängd** tillstånd. |

## <a name="view-drive-status"></a>Visa status för enhet

I tabellen nedan beskrivs livscykeln för en enskild enhet som den övergår till ett jobb som importeras eller exporteras. Det aktuella tillståndet för varje enhet i ett jobb visas i Azure-portalen.

I följande tabell beskrivs varje tillstånd varje enhet i ett jobb kan passera.

| Enhetsstatus | Beskrivning |
|:--- |:--- |
| Angivna | Ett importjobb när jobbet har skapats från Azure-portalen ursprungligt tillstånd för en enhet är **angivna**. För ett exportjobb eftersom ingen enhet anges när jobbet skapas, är tillståndet inledande enhet **mottagna**. |
| Mottagning | Enheten går över till den **mottagna** tillstånd när tjänsten Import/Export har bearbetat de enheter som har tagits emot från företaget leverans för importen. För ett exportjobb tillståndet första enheten är den **mottagna** tillstånd. |
| NeverReceived | Enheten flyttas till den **NeverReceived** tillstånd när paketet för ett jobb kommer, men paketet innehåller inte enheten. En enhet flyttas till det här tillståndet även om det har två veckor eftersom tjänsten har tagit emot leveransinformation, men paketet har inte kommit på datacentret. |
| Överförs | En enhet flyttas till den **överföra** tillstånd när tjänsten börjar att överföra data från enheten till Azure Storage. |
| Slutfört | En enhet flyttas till den **slutförd** tillstånd när tjänsten har överförts alla data utan fel.
| CompletedMoreInfo | En enhet flyttas till den **CompletedMoreInfo** tillstånd när tjänsten har uppstått några problem vid kopiering av data från eller till enheten. Informationen kan omfatta fel, varningar och informationsmeddelanden om överskrivning blobbar.
| ShippedBack | En enhet flyttas till den **ShippedBack** tillstånd när den har levererats från datacentret till avsändaradressen. |

Den här avbildningen från Azure-portalen visar tillståndet för enheten för en exempel-jobb:

![Visa status för enhet](./media/storage-import-export-service/drivestate.png)

I följande tabell beskrivs enhet fel tillstånd och åtgärder som vidtas för varje tillstånd.

| Enhetsstatus | Händelse | Lösning / nästa steg |
|:--- |:--- |:--- |
| NeverReceived | En enhet som har markerats som **NeverReceived** (eftersom den inte skickades som en del av det jobbet leveransen) tas emot i en annan leveransen. | Driftteamet flyttar enheten och **mottagna**. |
| Gäller inte | En enhet som inte är en del av jobb som når datacenter som en del av ett annat jobb. | Enheten har markerats som en extra enhet och returneras till dig när jobbet som är associerade med det ursprungliga paketet har slutförts. |

## <a name="time-to-process-job"></a>Tid för att bearbeta jobb
Hur lång tid det tar för att bearbeta en import-/ exportjobb varierar beroende på ett antal faktorer som:

-  Leveranstid
-  Läsa in på datacentret
-  Jobbtyp och storleken på data som kopieras
-  Antal diskar i ett jobb. 

Import/Export-tjänsten har inte ett SLA men tjänsten strävar efter att slutföra kopian i 7 till 10 dagar efter diskarna har tagits emot. Utöver statusen som publiceras på Azure Portal kan REST API: er användas för att följa jobbförloppet. Procent färdigt parametern i den [lista jobb]() funktionsanrop API ger procentandel kopiera pågår.


## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget WAImportExport](storage-import-export-tool-how-to.md)
* [Överföra data med kommandoradsverktyget azcopy](storage-use-azcopy.md)
* [Azure Import exportera REST API-exemplet](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

