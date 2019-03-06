---
title: Hämta statusinformation för ett Azure Import/Export-jobb | Microsoft Docs
description: Lär dig mer om att hämta statusinformation för Microsoft Azure Import/Export service jobb.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 1a878b5a9f0502ff9acd411359895d7431fb76f4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437250"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Hämta statusinformation för ett Import/Export-jobb
Du kan anropa den [Get Job](/rest/api/storageimportexport/jobs) åtgärd för att hämta information om både importera och exportera jobb. Den information som returneras innehåller:

-   Den aktuella statusen för jobbet.

-   Ungefär hur mycket att varje jobb har slutförts.

-   Det aktuella tillståndet för varje enhet.

-   URI: er för BLOB-objekt som innehåller felloggar och utförlig loggningsinformation (om aktiverat).

I följande avsnitt beskrivs den information som returneras av den `Get Job` igen.

## <a name="job-states"></a>Status för jobb
Tabellen och tillstånd diagrammet nedan beskriver tillstånden som ett jobb som försätter via under dess livscykel. Det aktuella tillståndet för jobbet kan fastställas genom att anropa den `Get Job` igen.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

I följande tabell beskrivs varje tillstånd som ett jobb kan passera.

|Jobbstatus|Beskrivning|
|---------------|-----------------|
|`Creating`|När du anropar åtgärden Placera jobbet, skapas ett jobb och dess status är inställd på `Creating`. När jobbet är i den `Creating` tillstånd, Import/Export-tjänsten förutsätter att enheterna som inte har levererats till datacentret. Ett jobb kan finnas kvar i den `Creating` tillstånd i upp till två veckor, varefter den tas bort automatiskt av tjänsten.<br /><br /> Om du behöver anropa Update jobbegenskaper igen när jobbet är i den `Creating` tillstånd, jobbet finns kvar i den `Creating` tillstånd och timeout-intervall återställs till två veckor.|
|`Shipping`|När du levererar ditt paket du anropa uppdatera jobbegenskaper åtgärden Uppdatera tillståndet för jobbet är `Shipping`. Tillståndet leverans kan endast anges om den `DeliveryPackage` (postnummer operatör och spårningsnummer) och `ReturnAddress` har säkerhetegenskaperna angetts för jobbet.<br /><br /> Jobbet finns kvar i tillståndet leverans i upp till två veckor. Om två veckor godkänts och enheterna som inte har tagits emot, kommer operatörer för Import/Export-tjänsten att meddelas.|
|`Received`|När alla enheter har tagits emot i datacentret, anges jobbets status till mottagna tillstånd.|
|`Transferring`|När enheterna har tagits emot i datacentret och minst en enhet har startat bearbetning, jobbets status anges till den `Transferring` tillstånd. Se den `Drive States` mer detaljerad information.|
|`Packaging`|När alla enheter har bearbetat jobbet kommer att placeras i den `Packaging` tillstånd förrän enheterna levereras till kunden.|
|`Completed`|När alla enheter har levererats till kunden, om jobbet har slutförts utan fel, kommer jobbet anges till den `Completed` tillstånd. Jobbet tas automatiskt bort efter 90 dagar i den `Completed` tillstånd.|
|`Closed`|När alla enheter har levererats till kunden, om det har gjorts några fel under bearbetningen av jobbet, kommer jobbet anges till den `Closed` tillstånd. Jobbet tas automatiskt bort efter 90 dagar i den `Closed` tillstånd.|

Du kan avbryta ett jobb endast på vissa tillstånd. Ett avbrutna jobb hoppar över steget för kopia av data, men annars det följer samma tillståndsövergångar som ett jobb som inte har avbrutits.

I följande tabell beskrivs fel som kan uppstå för varje jobbets status samt effekten på jobbet när ett fel uppstår.

|Jobbstatus|Händelse|Lösning / nästa steg|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|En eller flera enheter för ett jobb anlänt, men jobbet är inte i den `Shipping` tillstånd eller det finns ingen post för jobbet i tjänsten.|Import/Export service operations-teamet försöker kontakta kunden för att skapa eller uppdatera jobbet med nödvändig information för att gå vidare jobbet.<br /><br /> Om driftsteamet inte kan kontakta kunden inom två veckor, försöker driftsteamet returnera enheterna.<br /><br /> I händelse av att enheterna inte kan returneras och kunden inte kan nås, förstörs enheterna på ett säkert sätt i 90 dagar.<br /><br /> Observera att ett jobb inte kan behandlas förrän statusen har uppdaterats till `Shipping`.|
|`Shipping`|Paketet för ett jobb har inte kommit över två veckor.|Driftsteamet meddelar kund saknas paketet. Driftsteamet kommer baserat på kundens svar, antingen utöka intervallet som ska vänta tills paketet tas emot eller avbryta jobbet.<br /><br /> I händelse av att kunden inte kan kontaktas eller inte svarar inom 30 dagar, driftsteamet initierar åtgärd för att flytta jobbet från den `Shipping` direkt till den `Closed` tillstånd.|
|`Completed/Closed`|Enheterna som aldrig har nåtts avsändaradressen eller var skadad i leverans (gäller endast för ett exportjobb).|Om enheterna inte når avsändaradressen, bör kunden först anropa Get Job-åtgärden eller Kontrollera jobbstatus i portalen för att se till att enheterna har levererats. Om enheterna som har levererats, bör kunden kontakta leverans-providern för att försöka hitta enheterna.<br /><br /> Om enheterna är skadad under leverans kan kanske kunden vill begära en annan export-jobbet eller ladda ned blobbarna saknas.|
|`Transferring/Packaging`|Jobbet har en felaktig eller saknas avsändaradress.|Driftsteamet kontaktar kontaktperson för jobbet att hämta rätt adress.<br /><br /> I händelse av att kunden inte kan nås, förstörs enheterna på ett säkert sätt i 90 dagar.|
|`Creating / Shipping/ Transferring`|En enhet som inte visas i listan över enheter som ska importeras som ingår i paketet levereras.|Extra enheter kommer inte att bearbetas och återgår till kunden när jobbet har slutförts.|

## <a name="drive-states"></a>Enhetsstatus
Tabellen och diagrammet nedan beskriver livscykeln för en enskild enhet som den övergår via ett import eller export-jobb. Du kan hämta det aktuella tillståndet för enheten genom att anropa den `Get Job` åtgärden, kontrollera den `State` elementet i den `DriveList` egenskapen.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

I följande tabell beskrivs varje tillstånd som en enhet kan passera.

|Enhetsstatus|Beskrivning|
|-----------------|-----------------|
|`Specified`|För ett importjobb när jobbet har skapats med åtgärden Placera jobbet är det ursprungliga tillståndet för en enhet i `Specified` tillstånd. För ett exportjobb eftersom ingen enhet anges när jobbet skapas, är den första Enhetsstatus den `Received` tillstånd.|
|`Received`|Enheten övergår till den `Received` tillstånd när operatorn Import/Export-tjänsten har bearbetat de enheter som har tagits emot från transportföretaget för ett importjobb. För ett exportjobb startenhet tillståndet är den `Received` tillstånd.|
|`NeverReceived`|Enheten kommer att flytta till den `NeverReceived` tillstånd när paketet för ett jobb tas emot, men paketet innehåller inte enheten. En enhet kan också flytta i det här tillståndet om det har gått två veckor sedan tjänsten fick leveransadressen, men paketet har inte kommit på datacentret.|
|`Transferring`|En enhet kommer att flytta till den `Transferring` tillstånd när tjänsten börjar att överföra data från enheten till Windows Azure Storage.|
|`Completed`|En enhet kommer att flytta till den `Completed` tillstånd när tjänsten har överförts alla data utan fel.|
|`CompletedMoreInfo`|En enhet kommer att flytta till den `CompletedMoreInfo` tillstånd när tjänsten har påträffat några problem när du kopierar data från eller till enheten. Informationen kan omfatta fel, varningar och informationsmeddelanden om skriver över BLOB-objekt.|
|`ShippedBack`|Enheten kommer att flytta till den `ShippedBack` tillstånd när det har skickats från data center tillbaka till avsändaradressen.|

I följande tabell beskrivs fel Enhetsstatus och de åtgärder som vidtas för varje tillstånd.

|Enhetsstatus|Händelse|Lösning / nästa steg|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|En enhet som har markerats som `NeverReceived` (eftersom den inte skickades som en del av jobbets leverans) tas emot i en annan leveransen.|Driftsteamet flyttas enheten till den `Received` tillstånd.|
|`N/A`|En enhet som inte är en del av alla jobb har nått datacentralen som en del av ett annat jobb.|Enheten kommer att markeras som en extra enhet och återgår till kunden när jobbet som är associerade med det ursprungliga paketet har slutförts.|

## <a name="faulted-states"></a>Felaktig tillstånd
När ett jobb eller en enhet inte kan normalt fortsätter genom dess förväntade livscykel, jobbet eller enheten kommer att flyttas till en `Faulted` tillstånd. I det här läget driftsteamet kommer att kontakta kunden via e-post eller telefon. När problemet är löst, felaktig jobbet eller enheten kommer att vidtas av den `Faulted` tillstånd och flyttade i rätt läge.

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
