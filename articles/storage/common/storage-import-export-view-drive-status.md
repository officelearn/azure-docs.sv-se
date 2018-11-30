---
title: Visa status för Azure Import/Export-jobb | Microsoft Docs
description: Lär dig hur du visar status för Import/Export-jobb och de enheter som används.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 4a0fcb95ed34ed3d98c6b25b475c0866135897f3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584680"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Visa status för Azure Import/Export-jobb

Den här artikeln innehåller information om hur du visar status för enheten och jobb för Azure Import/Export-jobb. Azure Import/Export-tjänsten används för att på ett säkert sätt överföra stora mängder data till Azure-Blobar och Azure Files. Tjänsten används också för att exportera data från Azure Blob storage.  

## <a name="view-job-and-drive-status"></a>Visa status för jobbet och enhet
Du kan spåra statusen för din importera eller exportera jobb från Azure-portalen. Klicka på den **Import/Export** fliken. En lista över dina jobb visas på sidan.

![Visa jobbstatus](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Visa jobbstatus

Du ser något av följande jobbstatus beroende på om enheten är i processen.

| Jobbstatus | Beskrivning |
|:--- |:--- |
| Skapar | När ett jobb skapas, dess status är inställd på **skapa**. När jobbet är i den **skapa** tillstånd, Import/Export-tjänsten förutsätter att enheterna som inte har levererats till datacentret. Ett jobb kan finnas kvar i det här tillståndet för upp till två veckor, varefter den tas bort automatiskt av tjänsten. |
| Fraktas | När du levererar ditt paket ska du uppdatera spårningsinformation i Azure-portalen.  Detta aktiverar jobbet i **leverans** tillstånd. Jobbet finns kvar i den **leverans** tillstånd i upp till två veckor. 
| Mottaget | När alla enheter har tagits emot i datacentret, jobbets status är inställd på **mottagna**. |
| Överförs | När minst en enhet har startat bearbetning visas jobbets status är inställd på **överföra**. Mer information går du till [Enhetsstatus](#view-drive-status). |
| Packas | När alla enheter har bearbetat jobbet placeras i **paketering** tillstånd förrän enheterna levereras till dig. |
| Slutfört | När alla enheter har skickats till dig om jobbet har slutförts utan fel, sedan jobbet anges till **slutförd**. Jobbet tas automatiskt bort efter 90 dagar i den **slutförd** tillstånd. |
| Stängd | När alla enheter har skickats till dig, om det finns några fel under bearbetningen av jobbet, jobbet har tilldelats **stängd**. Jobbet tas automatiskt bort efter 90 dagar i den **stängd** tillstånd. |

## <a name="view-drive-status"></a>Visa enhetsstatus

Tabellen nedan beskriver livscykeln för en enskild enhet som den övergår via ett import eller export-jobb. Det aktuella tillståndet för varje enhet i ett jobb visas i Azure-portalen.

I följande tabell beskrivs varje tillstånd som varje enhet i ett jobb kan passera.

| Enhetsstatus | Beskrivning |
|:--- |:--- |
| Anges | För ett importjobb när jobbet har skapats från Azure-portalen är det ursprungliga tillståndet för en enhet **angivna**. För ett exportjobb eftersom ingen enhet anges när jobbet skapas, är den första Enhetsstatus **mottagna**. |
| Mottaget | Enheten övergår till den **mottagna** tillstånd när Import/Export-tjänsten har bearbetat de enheter som har tagits emot från transportföretaget för ett importjobb. För ett exportjobb startenhet tillståndet är den **mottagna** tillstånd. |
| NeverReceived | Enheten flyttas till den **NeverReceived** tillstånd när paketet för ett jobb tas emot, men paketet innehåller inte enheten. En enhet flyttar också i det här tillståndet om det har gått två veckor sedan tjänsten fick leveransadressen, men paketet har inte kommit på datacentret. |
| Överförs | En enhet flyttas till den **överföra** tillstånd när tjänsten börjar att överföra data från enheten till Azure Storage. |
| Slutfört | En enhet flyttas till den **slutförd** tillstånd när tjänsten har överförts alla data utan fel.
| CompletedMoreInfo | En enhet flyttas till den **CompletedMoreInfo** tillstånd när tjänsten har påträffat några problem när du kopierar data från eller till enheten. Informationen kan omfatta fel, varningar och informationsmeddelanden om skriver över BLOB-objekt.
| ShippedBack | En enhet flyttas till den **ShippedBack** tillstånd när det har skickats från datacentret till avsändaradressen. |

Den här avbildningen från Azure-portalen visar enheten tillståndet för en exempel-jobb:

![Visa Enhetsstatus](./media/storage-import-export-service/drivestate.png)

I följande tabell beskrivs fel Enhetsstatus och de åtgärder som vidtas för varje tillstånd.

| Enhetsstatus | Händelse | Lösning / nästa steg |
|:--- |:--- |:--- |
| NeverReceived | En enhet som har markerats som **NeverReceived** (eftersom den inte skickades som en del av jobbets leverans) tas emot i en annan leveransen. | Driftsteamet flyttar enheten till **mottagna**. |
| Gäller inte | En enhet som inte är en del av alla jobb som tas emot i datacentret som en del av ett annat jobb. | Enheten är markerad som en extra enhet och returneras till dig när jobbet som är associerade med det ursprungliga paketet har slutförts. |

## <a name="time-to-process-job"></a>Tid för att bearbeta jobb
Hur lång tid det tar för att bearbeta en import/export-jobbet varierar beroende på ett antal faktorer som:

-  Leveranstid
-  Inläsning i datacentret
-  Typ av utskriftsjobb och mängden data som kopieras
-  Antal diskar i ett jobb. 

Import/Export-tjänsten har inte något serviceavtal, men tjänsten strävar efter att utföra kopian i 7 till 10 dagar efter att diskarna tas emot. Utöver statusen på Azure Portal, kan REST API: er användas för att följa jobbförloppet. Procent färdigt parametern i den [lista jobb](/previous-versions/azure/dn529083(v=azure.100)) funktionsanropet API innehåller processen procent.


## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget WAImportExport](storage-import-export-tool-how-to.md)
* [Överföra data med kommandoradsverktyget azcopy](storage-use-azcopy.md)
* [Exempel på Azure Import Export REST API](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
