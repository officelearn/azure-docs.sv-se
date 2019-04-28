---
title: Felsöka Azure NetApp filer Resource Provider | Microsoft Docs
description: Beskriver orsaker och lösningar lösningar för vanliga Azure NetApp filer Resource Provider-fel.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769443"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Felsöka fel i resursprovidern för Azure NetApp Files
Den här artikeln beskriver vanliga Azure NetApp filer Resource Provider för fel, deras orsaker, lösningar och lösningar. 

<a name="error_01"></a>***Azure Key Vault inte har konfigurerats.***   
Azure Key Vault lagrar autentiseringsuppgifterna som krävs för att komma åt underliggande API. Det här felet indikerar att Azure Key Vault inte tog emot de fullständiga autentiseringsuppgifterna för åtkomst till underliggande API.

* Orsak  
Azure Key Vault tog inte emot rätt autentiseringsuppgifter eller autentiseringsuppgifterna som är ofullständig.  

* Lösning   
Azure NetApp Files-tjänsten använder Azure Key Vault. Azure Key Vault autentiserar med hjälp av en token från Azure Active Directory. Programmets ägare måste därför registrera programmet i Azure Active Directory.

* Lösning   
Ingen.  Azure Key Vault måste ställas in korrekt för att använda Azure NetApp-filer.  

<a name="error_02"></a>***Skapa en Token kan inte ändras.***   
Det här felet uppstår när du försöker ändra skapa token när volymen har skapats.
Skapa en token måste anges när volymen skapas och kan inte ändras senare.

* Orsak   
Du försöker ändra skapa token när volymen har skapats, som inte är en giltig åtgärd.

* Lösning   
Överväg att ta bort parametern från förfrågan om att Stäng felmeddelandet när volymen har skapats.

* Lösning   
Om du vill ändra skapa token kan du skapa en ny volym med en ny token skapas och migrera data till den nya volymen.


<a name="error_03"></a>***Skapa en Token måste vara minst 16 tecken.***   
Det här felet uppstår när token skapas inte uppfyller kravet på längd. Längden på Skapa token måste vara minst 16 tecken.

* Orsak   
Skapa en token uppfyller inte kravet på längd.  När du skapar en volym med hjälp av API krävs en skapa-token. Om du använder portalen kan token skapas automatiskt.

* Lösning   
Öka längden på Skapa token. Du kan till exempel lägga till ett annat ord. i början eller slutet av skapa token.

* Lösning   
Det minsta längd på Skapa token inte kan kringgås.  Du kan använda ett prefix eller suffix för att öka skapa token längd.


<a name="error_04"></a>***Fel vid borttagning av en volym som inte hittades på Azure NetApp-filer.***   
Det här felet uppstod eftersom interna registret av resurser är inte synkroniserat.

* Orsak   
Volymen kan hålla visas i portalen under en tid när den har tagits bort. Om du tar bort volymen med hjälp av API: et, är det möjligt att volymen inte har angetts korrekt. Felet kan också orsakas av inaktuella webbläsarens cache.

* Lösning   
Rensa webbläsarens cacheminne om du använder portalen. Det finns också en interna cacheminnet som uppdateras var tionde minut.  Du kan försöka att rensa cacheminnet igen.  Om problemet kvarstår efter 10 minuter kan du skapa ett supportärende.

* Lösning   
Använd en annan volym under tiden och ignorera den befintliga påverkas.


<a name="error_05"></a>***Fel vid infogar en ny volym som finns på Azure NetApp-filer.***   
Det här felet uppstår eftersom interna registret av resurser är inte synkroniserat.

* Orsak   
Volymen kan fortsätta att visas i portalen under en tid när den har tagits bort. Om du tar bort volymen med hjälp av API: et, är det möjligt att volymen inte har angetts korrekt.

* Lösning   
Om du använder portalen har volymen redan skapats.  Volymen bör visas automatiskt. Om problemet kvarstår kan du skapa ett supportärende.

* Lösning   
Du kan skapa en volym med ett annat namn och en annan skapa token.


<a name="error_06"></a>***Filens sökväg får innehålla bokstäver, siffror och bindestreck (””-””) endast.***   
Det här felet uppstår när sökvägen innehåller ogiltiga tecken, till exempel en punkt (””.), kommatecken (””,), understreck (”\_”), eller dollartecken (”$”).

* Orsak   
Sökvägen innehåller ogiltiga tecken, till exempel en punkt (””.), kommatecken (””,), understreck (”\_”), eller dollartecken (”$”).

* Lösning   
Ta bort tecken som inte är alfabetisk bokstäver, siffror eller bindestreck (”-”) från den angivna sökvägen.

* Lösning   
Du kan ersätta ett understreck med ett bindestreck eller använda versaler i stället för blanksteg som anger början av nya ord (t.ex, använda ”NewVolume” i stället för ”ny volym”).


<a name="error_07"></a>***Volym-ID kan inte ändras.***   
Det här felet uppstår vid försök att ändra volymen-ID.  Ändra volym-ID är inte en giltig åtgärd.

* Orsak   
ID för filsystemet anges när volymen har skapats. Volym-ID kan inte ändras senare.

* Lösning   
Ingen.

* Lösning   
Ingen.  Volym-ID skapas när volymen skapas och kan inte ändras senare.


<a name="error_08"></a>***Ett ogiltigt värde ”{0}' togs emot för {1}.***   
Det här meddelandet indikerar ett fel i fälten för RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 och Nfsv4.

* Orsak   
Verifiering av indata-begäran misslyckades för minst en av följande fält: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 och Nfsv4.

* Lösning   
Se till att ange alla parametrar som krävs och inte är i konflikt på kommandoraden. Exempelvis kan ange du inte både UnixReadOnly och UnixReadWrite på samma gång.

* Lösning   
Se avsnittet lösning.  


<a name="error_09"></a>***Saknade värden för ”{0}”.***   
Det här felet indikerar att ett nödvändigt attribut saknas i begäran för minst en av följande parametrar: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 och Nfsv4.

* Orsak   
Verifiering av indata-begäran misslyckades för minst en av följande fält: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 och Nfsv4.

* Lösning   
Se till att ange alla parametrar som krävs och inte är i konflikt på kommandoraden. Exempel: du kan inte ange både UnixReadOnly och UnixReadWrite på samma gång

* Lösning   
Se avsnittet lösning.  


<a name="error_10"></a> ***{0} används redan.***   
Det här felet indikerar att namnet för resursen har redan använts.

* Orsak   
Du försöker skapa en volym med ett namn som är samma som en befintlig volym.

* Lösning   
Använd ett unikt namn när du skapar en volym.

* Lösning   
Om det behövs kan du ändra namnet på den befintliga volymen så att den nya volymen kan använda rätt namn.


<a name="error_11"></a> ***{0} för kort.***   
Det här felet indikerar att volymnamnet inte uppfyller minimalt tillåten längd.

* Orsak   
Volymens namn är för kort.

* Lösning   
Öka längden på volymens namn.  

* Lösning   
Du kan lägga till ett gemensamma prefix eller suffix volymens namn.


<a name="error_12"></a>***Azure NetApp API: et Files kan inte nås.***   
Azure API förlitar sig på Azure NetApp filer API för att hantera volymer.  Det här felet indikerar ett problem med API-anslutningen.

* Orsak   
Underliggande API svarar inte, vilket resulterar i ett internt fel. Det här felet är troligt att vara tillfällig.

* Lösning   
Problemet troligen är tillfälligt.  Begäran bör fungera om en stund.

* Lösning   
Ingen. Underliggande API: et är viktiga för att hantera volymer.  


<a name="error_13"></a>***Inga autentiseringsuppgifter hittades för prenumerationen '{0}'.***   
Det här felet indikerar att de angivna autentiseringsuppgifterna är antingen ogiltig eller har inte konfigurerats korrekt i prenumerationen.

* Orsak   
Autentiseringsuppgifter som är ogiltigt eller felaktigt angiven förhindra åtkomst till tjänsten för att hantera volymer.

* Lösning   
Se till att autentiseringsuppgifterna som anges och har angett korrekt på kommandoraden.

* Lösning   
Ingen.  Ställa in autentiseringsuppgifter korrekt är nödvändigt för att använda Azure NetApp-filer.  


<a name="error_14"></a>***Inga resultat åtgärds-id hittades för ”{0}”.***   
Det här felet indikerar att ett internt fel hindrar åtgärden från att slutföras.

* Orsak   
Ett internt fel uppstod och förhindrade att åtgärden slutfördes.

* Lösning   
Det här felet är troligt att vara tillfällig.  Vänta några minuter och försök igen. Om problemet kvarstår kan du skapa ett ärende om du vill ha teknisk support undersöka problemet.

* Lösning   
Vänta några minuter och kontrollera om problemet kvarstår.


<a name="error_15"></a>***Åtgärden '{0}' stöds inte.***   
Det här felet indikerar att kommandot inte är tillgänglig för active prenumerationen eller resursen.

* Orsak   
Åtgärden är inte tillgänglig för prenumerationen eller resursen.

* Lösning   
Kontrollerar du att kommandot har angetts och tillgängliga för resursen och prenumeration som du använder.

* Lösning   
Se avsnittet lösning.  


<a name="error_16"></a>***Patch-åtgärden stöds inte för den här resurstypen.***   
Det här felet uppstår när du försöker ändra monteringsmålet eller ögonblicksbild.

* Orsak   
Monteringsmålet definieras när den har skapats och kan inte ändras senare.

* Lösning   
Ingen.  Monteringsmålet kan inte ändras när volymen har skapats.

* Lösning   
Ingen.


<a name="error_17"></a>***Ett värde för skrivskyddad egenskap '{0}'.***   
Det här felet uppstår när du definierar ett värde för en egenskap som inte kan ändras. Exempel: du kan inte ändra volym-ID.

* Orsak   
Du försökte ändra en parameter (t.ex volym-ID) som inte kan ändras.

* Lösning   
Ingen. Parametern för volym-ID kan inte ändras.

* Lösning   
Volym-ID får inte kräva ändring av.  En lösning är därför inte nödvändigt.

<a name="error_18"></a>***Den begärda {0} hittades inte.***   
Det här felet uppstår vid försök att referera till en icke-befintligt resurs, till exempel en volym eller en ögonblicksbild. Resursen kan ha tagits bort eller har ett resursnamn för kontroll.

* Orsak   
Du vill referera till en icke-befintligt resurs (till exempel en volym eller en ögonblicksbild) som redan har tagits bort eller har ett felstavade resursnamn.

* Lösning   
Kontrollera begäran för stavfel att se till att det refereras korrekt.

* Lösning   
Se avsnittet lösning.

<a name="error_19"></a>***Det gick inte att hämta autentiseringsuppgifter för prenumerationen '{0}'.***   
Det här felet indikerar att de angivna autentiseringsuppgifterna är antingen ogiltig eller ange ett felaktigt sätt i prenumerationen.

* Orsak   
Autentiseringsuppgifter som är ogiltigt eller felaktigt set i prenumerationen förhindra åtkomst till tjänsten för att hantera volymer.

* Lösning   
Se till att autentiseringsuppgifterna som anges och har angett korrekt på kommandoraden.

* Lösning   
Ingen.  Ange autentiseringsuppgifter är korrekt nödvändigt för att använda Azure NetApp-filer.

<a name="error_20"></a>***Okänd Azure NetApp filer fel.***   
Azure API förlitar sig på Azure NetApp filer API för att hantera volymer. Felet anger ett problem i kommunikationen till API: et.

* Orsak   
Underliggande API: et skickar ett okänt fel.  Det här felet är troligt att vara tillfällig.

* Lösning   
Problemet troligen är tillfälligt och begäran bör lyckas efter en stund. Om problemet kvarstår kan du skapa ett supportärende om du vill att problemet undersöks.

* Lösning   
Ingen.  Underliggande API: et är viktiga för att hantera volymer.

<a name="error_21"></a>***Värdet som togs emot för en okänd egenskap '{0}'.***   
Det här felet uppstår när icke-befintligt egenskaper har angetts för en resurs som volym, ögonblicksbild eller monteringsmål.

* Orsak   
Begäran har en uppsättning egenskaper som kan användas med varje resurs.  Du kan inte innehålla några icke-befintligt egenskaper i begäran.

* Lösning   
Se till att alla egenskapsnamn är rättstavade och egenskaperna som är tillgängliga för prenumeration och resursgrupp.

* Lösning   
Minska antalet egenskaper som definierats i förfrågan om att eliminera den egenskap som orsakar felet.


<a name="error_22"></a>***Uppdateringsåtgärden stöds inte för den här resurstypen.***   
Det är bara volymer kan uppdateras. Det här felet uppstår när du försöker utföra en uppdateringsåtgärd som inte stöds, till exempel uppdaterar en ögonblicksbild.

* Orsak   
Du försöker att uppdatera resursen stöder inte uppdateringen.  Det är bara volymer kan ha sina egenskaper ändras.

* Lösning   
Ingen.  Den resurs som du försöker uppdatera stöder inte uppdateringen. Därför kan inte ändras.

* Lösning   
Skapa en ny resurs med uppdatering på plats och migrera data för en volym.


<a name="error_23"></a>***Antal objekt: {0} för objektet: {1} är utanför intervallet för min – max.***   
Det här felet uppstår när en export principregler inte uppfyller kravet på lägsta eller högsta intervall.  Om du definierar export-principen, måste den ha en export principregel minst och fem export principregler på högsta.

* Orsak   
Export-principen som du har definierat uppfyller inte intervallet.  

* Lösning   
Se till att indexet inte redan används och som är i intervallet från 1 till 5.

* Lösning   
Det är inte obligatoriskt att använda export på volymerna. Du kan därför utelämna exportera principen helt och hållet om du inte behöver ha principregler för export.


<a name="error_24"></a>***Duplicera Värdefel för objektet {0}.***   
Det här felet uppstår när export-principen inte har definierats med ett unikt index.  När du definierar principer för export måste alla export principregler ha ett unikt index mellan 1 och 5.

* Orsak   
Definierade exportera principen uppfyller inte kravet på principregler för export. Du måste ha en export principregel minst och fem export principregler på högsta.  

* Lösning   
Se till att indexet inte redan används och att den är i intervallet från 1 till 5.

* Lösning   
Använd ett annat index för den regel som du vill ange.


