---
title: Hämtar information om tillstånd för ett Azure Import/Export-jobb | Microsoft Docs
description: Lär dig mer om att hämta statusinformation om för Microsoft Azure Import/Export service jobb.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.openlocfilehash: 13169716c47cf9389c8f2651393ac744441bdd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874012"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Hämtar information om tillstånd för en Import/Export-jobb
Du kan anropa den [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) åtgärd för att hämta information om både importera och exportera jobben. Informationen som returneras innehåller:

-   Aktuell status för jobbet.

-   Den ungefärliga procentandel varje jobb har slutförts.

-   Det aktuella tillståndet för varje enhet.

-   URI: er för blob som innehåller fel och utförlig loggningsinformation (om aktiverat).

I följande avsnitt beskrivs den information som returneras av den `Get Job` igen.

## <a name="job-states"></a>Status för jobb
Tabellen och tillstånd diagrammet nedan beskriver tillstånden som ett jobb övergångar via under sin livslängd. Aktuell status för jobbet kan fastställas genom att anropa den `Get Job` igen.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

I följande tabell beskrivs varje tillstånd som ett jobb kan passera.

|Jobbets status|Beskrivning|
|---------------|-----------------|
|`Creating`|När du anropar placera Job-åtgärden, skapas ett jobb och dess status är inställd på `Creating`. När jobbet är i den `Creating` tillstånd, tjänsten Import/Export förutsätter att enheterna inte har levererats till datacentret. Ett jobb kan finnas kvar i den `Creating` tillstånd för upp till två veckor, efter vilken bort automatiskt av tjänsten.<br /><br /> Om du anropar jobbegenskaper uppdateringen igen när jobbet är i den `Creating` tillstånd jobbet finns kvar i den `Creating` tillstånd och timeout-intervall återställs till två veckor.|
|`Shipping`|När du levererar paketet du bör kontakta uppdatera jobbegenskaper åtgärden Uppdatera status för jobbet `Shipping`. Tillståndet leverans kan bara anges om den `DeliveryPackage` (postnummer operatör och spårningsnummer) och `ReturnAddress` egenskaper har angetts för jobbet.<br /><br /> Jobbet kommer att finnas kvar i tillståndet leverans i upp till två veckor. Om två veckor godkänts och enheterna har inte tagits emot, meddelas Import/Export service operatörer.|
|`Received`|När alla enheter har tagits emot på datacenter, anges jobbets status till tillståndet Received.|
|`Transferring`|När enheterna har tagits emot på datacenter och minst en enhet har startat bearbetning, jobbets status anges till den `Transferring` tillstånd. Finns det `Drive States` avsnittet nedan för mer information.|
|`Packaging`|När alla enheter har bearbetat jobbet kommer att placeras i den `Packaging` tillstånd tills enheterna som har levererats till kunden.|
|`Completed`|När alla enheter har levererats till kunden, om jobbet har slutförts utan fel, kommer jobbet anges till den `Completed` tillstånd. Jobbet tas automatiskt bort efter 90 dagar i den `Completed` tillstånd.|
|`Closed`|När alla enheter har levererats till kunden, om det fanns några fel under bearbetning av jobbet, kommer jobbet anges till den `Closed` tillstånd. Jobbet tas automatiskt bort efter 90 dagar i den `Closed` tillstånd.|

Du kan avbryta ett jobb endast på vissa tillstånd. Avbrutna jobb hoppar över steget kopiera data, men annars följer samma tillståndsövergångar som ett jobb som inte har avbrutits.

I följande tabell beskrivs fel som kan uppstå för varje jobbets status samt effekten på jobbet när ett fel uppstår.

|Jobbets status|Händelse|Lösning / nästa steg|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|En eller flera enheter för ett jobb anlänt, men jobbet är inte i den `Shipping` tillstånd eller det finns ingen post för jobb i tjänsten.|Import/Export service operations-teamet försöker kontakta kunden för att skapa eller uppdatera jobbet med nödvändig information för att flytta jobbet framåt.<br /><br /> Om driftteamet inte kan kontakta kunden inom två veckor, försöker operations-teamet returnera enheterna.<br /><br /> I händelse av att enheterna inte kan returneras och kunden kan inte nås, förstöras enheterna på ett säkert sätt i 90 dagar.<br /><br /> Observera att ett jobb inte kan behandlas förrän dess tillstånd uppdateras till `Shipping`.|
|`Shipping`|Paketet för ett jobb har inte kommit över två veckor.|Driftteamet meddelar kunden om paketets saknas. Driftteamet kommer utifrån kundens svar antingen utöka intervallet ska vänta på att paketet anländer eller avbryts.<br /><br /> I händelse av att kunden inte kan kontaktas eller inte svarar inom 30 dagar, driftteamet initierar åtgärd för att flytta jobbet från den `Shipping` direkt till den `Closed` tillstånd.|
|`Completed/Closed`|Enheter aldrig uppnåtts returadressen eller har skadats i leveransen (gäller endast ett exportjobb).|Om enheterna inte når returadressen, måste kunden först anropa Get Job-åtgärden eller Kontrollera jobbstatus i portalen för att säkerställa att enheterna som har levererats. Om enheterna som har levererats, måste kunden kontakta leverans providern att enheterna.<br /><br /> Om enheterna är skadade under transport kanske kunden vill begära en annan exportjobb eller ladda ned saknas blobbar.|
|`Transferring/Packaging`|Jobbet har en felaktig eller saknas avsändaradress.|Driftteamet ska nå ut till kontaktpersonen för jobbet att erhålla rätt adress.<br /><br /> I händelse av att kunden inte kan nås, förstöras enheterna på ett säkert sätt i 90 dagar.|
|`Creating / Shipping/ Transferring`|En enhet som inte visas i listan över enheter som ska importeras finns i paketets leverans.|De extra enheterna kommer inte att bearbetas och kommer att returneras till kunden när jobbet har slutförts.|

## <a name="drive-states"></a>Tillstånd för enheten
Tabellen och diagrammet nedan beskriver livscykeln för en enskild enhet som den övergår till ett jobb som importeras eller exporteras. Du kan hämta det aktuella tillståndet för enheten genom att anropa den `Get Job` igen och kontrollera den `State` element i den `DriveList` egenskapen.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

I följande tabell beskrivs varje tillstånd som en enhet kan passera.

|Enhetsstatus|Beskrivning|
|-----------------|-----------------|
|`Specified`|Ett importjobb när jobbet skapas med Put Job-åtgärden ursprungligt tillstånd för en enhet är den `Specified` tillstånd. Ett exportjobb eftersom ingen enhet anges när jobbet skapas första enhet tillstånd är den `Received` tillstånd.|
|`Received`|Enheten går över till den `Received` tillstånd när operatorn Import/Export service har bearbetat de enheter som har tagits emot från företaget leverans för importen. För ett exportjobb tillståndet första enheten är den `Received` tillstånd.|
|`NeverReceived`|Enheten kommer att flyttas till den `NeverReceived` tillstånd när paketet för ett jobb kommer, men paketet innehåller inte enheten. En enhet kan också flytta detta tillstånd om det har två veckor eftersom tjänsten har tagit emot leveransinformation, men paketet har inte kommit på datacenter.|
|`Transferring`|En enhet ska flyttas till den `Transferring` tillstånd när tjänsten börjar att överföra data från enheten till Windows Azure Storage.|
|`Completed`|En enhet ska flyttas till den `Completed` tillstånd när tjänsten har överförts alla data utan fel.|
|`CompletedMoreInfo`|En enhet ska flyttas till den `CompletedMoreInfo` tillstånd när tjänsten har uppstått några problem vid kopiering av data från eller till enheten. Informationen kan omfatta fel, varningar och informationsmeddelanden om överskrivning blobbar.|
|`ShippedBack`|Enheten kommer att flyttas till den `ShippedBack` tillstånd när den har levererats från data center tillbaka avsändaradressen.|

I följande tabell beskrivs enhet fel tillstånd och åtgärder som vidtas för varje tillstånd.

|Enhetsstatus|Händelse|Lösning / nästa steg|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|En enhet som har markerats som `NeverReceived` (eftersom den inte skickades som en del av det jobbet leveransen) tas emot i en annan leveransen.|Driftteamet flyttas på enheten för att den `Received` tillstånd.|
|`N/A`|En enhet som inte är en del av jobb som anländer på datacenter som en del av ett annat jobb.|Enheten kommer att markeras som en extra enhet och returneras till kunden när jobbet som är associerade med det ursprungliga paketet har slutförts.|

## <a name="faulted-states"></a>Felaktig tillstånd
När ett jobb eller en enhet inte fortsätta normalt genom dess förväntade livscykel, jobbet eller enheten kommer att flyttas till en `Faulted` tillstånd. Då kontaktar driftteamet kunden via e-post eller telefon. När problemet är löst felaktig jobbet eller enheten kommer att vidtas av den `Faulted` Systemtillstånd och har flyttats till rätt läge.

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av tjänsten Import/Export REST API](storage-import-export-using-the-rest-api.md)
