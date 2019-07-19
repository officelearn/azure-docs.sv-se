---
title: Felsöka Azure NetApp Files Resource Provider-fel | Microsoft Docs
description: Beskriver orsaker, lösningar och lösningar för vanliga Azure NetApp Files Resource Provider-fel.
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
ms.date: 07/10/2019
ms.author: b-juche
ms.openlocfilehash: f417d83a67f2f3afa33a83a56a72d0d82c64ab0d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850009"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Felsöka fel i resursprovidern för Azure NetApp Files 

I den här artikeln beskrivs vanliga Azure NetApp Files Resource Provider-fel, deras orsaker, lösningar och lösningar (om det är tillgängligt).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Vanliga Azure NetApp Files Resource Provider-fel

***BareMetalTenantId kan inte ändras.***  

Felet uppstår när du försöker uppdatera eller korrigera en volym och `BaremetalTenantId` egenskapen har ett ändrat värde.

* Orsak:   
Du försöker uppdatera en volym och `BaremetalTenantId` egenskapen har ett annat värde än värdet som lagras i Azure.
* Lösa   
Ta `BaremetalTenantId` inte med i begäran om korrigering och uppdatering (begäran). Du kan också se `BaremetalTenantId` till att är detsamma i begäran.

***ServiceLevel kan inte ändras.***  

Det här felet uppstår när du försöker uppdatera eller korrigera en kapacitets uppsättning med en annan service nivå när poolen redan har volymer.

* Orsak:   
Du försöker uppdatera en service nivå för kapacitets bassäng när poolen innehåller volymer.
* Lösa   
Ta bort alla volymer från kapacitets gruppen och ändra sedan service nivån.
* Korrigera   
Skapa en annan pool för kapacitet och skapa sedan volymerna igen i den nya kapacitets poolen.

***PoolId kan inte ändras***  

Det här felet uppstår när du försöker uppdatera eller korrigera en kapacitets uppsättning med en `PoolId` ändrad egenskap.

* Orsak:   
Du försöker uppdatera en egenskap för kapacitets `PoolId` gruppen. `PoolId` Egenskapen är en skrivskyddad egenskap och kan inte ändras.
* Lösa   
Ta `PoolId` inte med i begäran om korrigering och uppdatering (begäran).  Du kan också se `PoolId` till att är detsamma i begäran.

***CreationToken kan inte ändras.***

Felet uppstår när du försöker ändra sökvägen till filen (`CreationToken`) när volymen har skapats. Fil Sök väg`CreationToken`() måste anges när volymen skapas och kan inte ändras senare.

* Orsak:   
Du försöker ändra sökvägen till filen (`CreationToken`) när volymen har skapats, vilket inte är en åtgärd som stöds. 
* Lösa   
Om du inte behöver ändra sökvägen till filen kan du ta bort parametern från begäran för att ignorera fel meddelandet.
* Korrigera   
Om du behöver ändra sökvägen till filen (`CreationToken`) kan du skapa en ny volym med en ny fil Sök väg och sedan migrera data till den nya volymen.

***CreationToken måste vara minst 16 tecken långt.***

Felet uppstår när fil Sök vägen (`CreationToken`) inte uppfyller längd kraven. Fil Sök vägens längd måste vara minst ett tecken långt.

* Orsak:   
Fil Sök vägen är tom.  När du skapar en volym med hjälp av API: et krävs en token för skapande. Om du använder Azure Portal genereras fil Sök vägen automatiskt.
* Lösa   
Ange minst ett Character som fil Sök väg (`CreationToken`).

***Det går inte att ändra domän namnet.***

Felet uppstår när du försöker ändra domän namnet i Active Directory.

* Orsak:   
Du försöker uppdatera domän namns egenskapen.
* Lösa    
Ingen. Du kan inte ändra domän namnet.
* Korrigera   
Ta bort alla volymer med Active Directory-konfigurationen. Ta sedan bort Active Directory-konfigurationen och återskapa volymerna.

***Duplicerat värde fel för objektet ExportPolicy. rules [RuleIndex].***

Felet uppstår när export principen inte har definierats med ett unikt index. När du definierar export principer måste alla export policy regler ha ett unikt index mellan 1 och 5.

* Orsak:   
Den definierade export principen uppfyller inte kraven för export policy regler. Du måste ha en export policy regel enligt minimi-och fem export princip reglerna maximalt.
* Lösa   
Kontrol lera att indexet inte redan används och att det är i intervallet från 1 till 5.
* Korrigera   
Använd ett annat index för regeln som du försöker ange.

***Fel {Action} {resourceTypeName}***

Det här felet visas när andra fel hanteringen misslyckades med att hantera felet när en åtgärd utfördes på en resurs.   Den innehåller texten ' error '. `updating``getting` `creating` `deleting`Kan vara vilken som helst av (,, eller). `{action}`  `{resourceTypeName}` Är`capacityPool`(till exempel`netAppAccount`, ,,och`volume`så vidare). `resourceTypeName`

* Orsak:   
Det här felet är ett ohanterat undantag där orsaken inte är känd.
* Lösa   
Kontakta Azure Support Center för att rapportera den detaljerade orsaken i loggarna.
* Korrigera   
Ingen.

***Sökvägen till filen får bara innehålla bokstäver, siffror och bindestreck ("" ").***

Felet uppstår när fil Sök vägen innehåller tecken som inte stöds, till exempel en punkt ("."), komma (","), under streck ("_") eller dollar tecken ("$").

* Orsak:   
Fil Sök vägen innehåller tecken som inte stöds, till exempel en punkt ("."), kommatecken (","), under streck (_) eller dollar tecken ($).
* Lösa   
Ta bort tecken som inte är alfabetiska bokstäver, siffror eller bindestreck ("-") från den fil Sök väg som du har angett.
* Korrigera   
Du kan ersätta ett under streck med ett bindestreck eller använda versaler i stället för blank steg för att ange början på nya ord.  Använd till exempel "NewVolume" i stället för "ny volym".

***FileSystemId kan inte ändras.***

Felet uppstår när du försöker ändra `FileSystemId`.  Det `FileSystemdId` finns inte stöd för att ändra. 

* Orsak:   
ID: t för fil systemet anges när volymen skapas. `FileSystemId`kan inte ändras senare.
* Lösa   
Ta `FileSystemId` inte med i en korrigerings-eller uppdaterings förfrågan.  Du kan också se `FileSystemId` till att är detsamma i begäran.

***ActiveDirectory med ID: {String} finns inte.***

Delen är det värde som du angav `ActiveDirectoryId` i egenskapen för Active Directory anslutningen. `{string}`

* Orsak:   
När du skapade ett konto med Active Directory-konfigurationen har du angett ett värde `ActiveDirectoryId` som ska vara tomt.
* Lösa   
Ta `ActiveDirectoryId` inte med i Create (parkera)-begäran.

***Ogiltig API-version.***

API-versionen har antingen inte skickats eller innehåller ett ogiltigt värde.

* Orsak:   
Värdet i Frågeparametern `api-version` innehåller ett ogiltigt värde.
* Lösa   
Använd rätt värde för API-version.  Resurs leverantören har stöd för många API-versioner. Värdet är i formatet åååå-mm-dd.

***Ett ogiltigt värde {Value} togs emot för {1}.***

Det här meddelandet anger ett fel i fälten för `RuleIndex` `UnixReadOnly`, `AllowedClients` `UnixReadWrite` `Nfsv3`,,, och `Nfsv4`.

* Orsak:   
Begäran om verifiering av begäran misslyckades för minst ett av följande fält: `RuleIndex`, `UnixReadOnly` `UnixReadWrite` `AllowedClients`,,, `Nfsv`3 och `Nfsv4`.
* Lösa   
Se till att ange alla parametrar som krävs och som inte är i konflikt på kommando raden. Du kan till exempel inte ange både `UnixReadOnly` parametrarna och `UnixReadWrite` samtidigt.
* Korrigera   
Se lösningen ovan.

***IP- {0} intervall {1} till för {2} VLAN används redan***

Felet beror på att de interna posterna i de använda IP-intervallen har en konflikt med den nyligen tilldelade IP-adressen.

* Orsak:   
Den IP-adress som tilldelats skapandet av volymen har redan registrerats.
Orsaken kan vara att det inte gick att skapa en tidigare volym.
* Lösa   
Kontakta Azure Support Center.

***Värde saknas för {Property}.***

Det här felet anger att en obligatorisk egenskap saknas i begäran. Strängen {Property} innehåller namnet på den saknade egenskapen.

* Orsak:   
Begäran om verifiering av begäran misslyckades för minst en av egenskaperna.
* Lösa   
Se till att ange alla egenskaper som krävs och som inte är i konflikt i begäran, särskilt egenskapen i fel meddelandet.

***MountTargets kan inte ändras.***

Det här felet uppstår när en användare försöker uppdatera eller korrigera egenskapen Volume MountTargets.

* Orsak:   
Du försöker uppdatera volym `MountTargets` egenskapen. Det finns inte stöd för att ändra den här egenskapen.
* Lösa   
Ta `MountTargets` inte med i en korrigerings-eller uppdaterings förfrågan.  Du kan också se till `MountTargets` att är detsamma i begäran.

***Namnet används redan.***

Det här felet anger att namnet på resursen redan används.

* Orsak:   
Du försöker skapa en resurs med ett namn som används för en befintlig resurs.
* Lösa   
Använd ett unikt namn när du skapar resursen.

***Fil Sök vägen används redan.***

Det här felet anger att fil Sök vägen för volymen redan används.

* Orsak:   
Du försöker skapa en volym med en fil Sök väg som är samma som en befintlig volym.
* Lösa   
Använd en unik fil Sök väg när du skapar volymen.

***Namnet är för långt.***

Det här felet indikerar att resurs namnet inte uppfyller det maximala längd kravet.

* Orsak:   
Resurs namnet är för långt.
* Lösa   
Använd ett kortare namn för resursen.

***Fil Sök vägen är för lång.***

Det här felet anger att fil Sök vägen för volymen inte uppfyller det maximala längd kravet.

* Orsak:   
Volym filens sökväg är för lång.
* Lösa   
Använd en kortare fil Sök väg.

***Namnet är för kort.***

Det här felet indikerar att resurs namnet inte uppfyller minimi kraven för längd.

* Orsak:   
Resurs namnet är för kort.
* Lösa   
Använd ett längre namn för resursen.

***Fil Sök vägen är för kort.***

Det här felet indikerar att volym fil Sök vägen inte uppfyller minimi kraven för längd.

* Orsak:   
Volym filens sökväg är för kort.
* Lösa   
Öka längden på volymens fil Sök väg.

***Det gick inte att komma åt Azure NetApp Files API.***

Azure API är beroende av Azure NetApp Files API för att hantera volymer. Det här felet indikerar ett problem med API-anslutningen.

* Orsak:   
Det underliggande API: et svarar inte, vilket resulterar i ett internt fel. Det här felet är troligt vis tillfälligt.
* Lösa   
Problemet är troligt vis tillfälligt. Begäran ska lyckas efter en stund.
* Korrigera   
Ingen. Det underliggande API: t är viktigt för att hantera volymer.

***Det gick inte att hitta något resultat{0}-ID för åtgärden.***

Det här felet indikerar att det inte går att slutföra åtgärden på grund av ett internt fel.

* Orsak:   
Ett internt fel uppstod och åtgärden kunde inte slutföras.
* Lösa   
Det här felet är troligt vis tillfälligt. Vänta några minuter och försök igen. Om problemet kvarstår kan du skapa en biljett för att få teknisk support att undersöka problemet.
* Korrigera   
Vänta några minuter och kontrol lera om problemet kvarstår.

***Tillåts inte att blanda protokoll typer CIFS och NFS***

Felet uppstår när du försöker skapa en volym och det finns både CIFS-och NFS-protokoll typerna i volym egenskaperna.

* Orsak:   
Både CIFS-och NFS-protokoll typerna används i volym egenskaperna.
* Lösa   
Ta bort en av protokoll typerna.
* Korrigera   
Lämna protokoll typ egenskapen tom eller null.

***Antal objekt: {Value} för objekt: ExportPolicy. rules [RuleIndex] ligger utanför det högsta tillåtna intervallet.***

Det här felet uppstår när export policy reglerna inte uppfyller minimi kraven eller det högsta tillåtna intervallet. Om du definierar export principen, måste den ha en export policy regel med minst och fem regler för export regler maximalt.

* Orsak:   
Den export princip som du har definierat uppfyller inte det intervall som krävs.
* Lösa   
Kontrol lera att indexet inte redan används och att det är i intervallet från 1 till 5.
* Korrigera   
Det är inte obligatoriskt att använda export policy på volymerna. Du kan utelämna export principen helt om du inte behöver använda exportera princip regler.

***Endast en Active Directory tillåts***

Det här felet uppstår när du försöker skapa en Active Directory-konfiguration och en redan finns för prenumerationen i regionen. Felet kan också uppstå när du försöker skapa fler än en Active Directory-konfiguration.

* Orsak:   
Du försöker skapa (inte uppdatera) en Active Directory, men den finns redan.
* Lösa   
Om Active Directory-konfigurationen inte används kan du först ta bort den befintliga konfigurationen och sedan försöka skapa igen.
* Korrigera   
Ingen. Endast ett Active Directory tillåts.

***Åtgärden {operation} stöds inte.***

Det här felet anger att åtgärden inte är tillgänglig för den aktiva prenumerationen eller resursen.

* Orsak:   
Åtgärden är inte tillgänglig för prenumerationen eller resursen.
* Lösa   
Kontrol lera att åtgärden har angetts korrekt och att den är tillgänglig för den resurs och prenumeration som du använder.

***Det går inte att ändra OwnerId***

Det här felet uppstår när du försöker ändra egenskapen OwnerId för volymen. Det finns inte stöd för att ändra OwnerId. 

* Orsak:   
`OwnerId` Egenskapen anges när volymen skapas. Det går inte att ändra egenskapen senare.
* Lösa   
Ta `OwnerId` inte med i en korrigerings-eller uppdaterings förfrågan. Du kan också se till `OwnerId` att är detsamma i begäran.

***Det gick inte att hitta den överordnade poolen***

Felet uppstår när du försöker skapa en volym och den pool där du skapar volymen inte hittas.

* Orsak:   
Det gick inte att hitta den kapacitets grupp där volymen skapas.
* Lösa   
Förmodligen skapades inte poolen helt, eller så har den redan tagits bort när volymen skapades.

***Korrigerings åtgärden stöds inte för den här resurs typen.***

Felet uppstår när du försöker ändra monterings mål eller ögonblicks bild.

* Orsak:   
Monterings målet definieras när det skapas och kan inte ändras senare.
Ögonblicks bilderna innehåller inte några egenskaper som kan ändras.
* Lösa   
Ingen. Dessa resurser har inga egenskaper som kan ändras.

***Storleken på poolen är för liten för total volym storlek.***

Det här felet uppstår när du uppdaterar storleken på kapacitets gruppen och storleken är mindre än det totala `usedBytes` värdet för alla volymer i den kapacitets gruppen.  Felet kan också uppstå när du skapar en ny volym eller ändrar storlek på en befintlig volym, och den nya volym storleken överskrider det lediga utrymmet i kapacitets gruppen.

* Orsak:   
Du försöker uppdatera kapacitets poolen till en mindre storlek än usedBytes i alla volymer i kapacitets gruppen.  Eller så försöker du skapa en volym som är större än det lediga utrymmet i kapacitets gruppen.  Alternativt försöker du ändra storlek på en volym och den nya storleken överskrider det lediga utrymmet i kapacitets gruppen.
* Lösa   
Ange ett större värde för kapacitets gruppen eller skapa en mindre volym för en volym.
* Korrigera   
Ta bort tillräckligt många volymer så att kapacitets bassängens storlek kan uppdateras till den här storleken.

***Egenskapen: Platsen för ögonblicks bild måste vara samma som volymen***

Det här felet uppstår när du skapar en ögonblicks bild med en annan plats än den volym som äger ögonblicks bilden.

* Orsak:   
Ogiltigt värde i egenskapen location för ögonblicks bilden.
* Lösa   
Ange en giltig sträng i egenskapen location.

***Namnet på {resourceType} måste vara identiskt med namnet på resurs identifieraren.***

Felet uppstår när du skapar en resurs och du fyller i egenskapen namn med ett annat värde än egenskapen namn för `resourceId`.

* Orsak:   
Ogiltigt värde i egenskapen name när du skapar en resurs.
* Lösa   
Lämna egenskapen name tom eller Tillåt att den använder samma värde som egenskapen Name (mellan det sista omvänt snedstrecket/och frågetecknet "?") i `resourceId`.

***Protokoll typen {Value} är inte känd***

Felet uppstår när du skapar en volym med en okänd protokoll typ.  Giltiga värden är "NFSv3" och "CIFS".

* Orsak:   
Du försöker ange ett ogiltigt värde i egenskapen Volume `protocolType` .
* Lösa   
Ange en giltig sträng i `protocolType`.
* Korrigera   
Ange `protocolType` som null.

***Det går inte att ändra protokoll typer***

Felet uppstår när du försöker uppdatera eller korrigera `ProtocolType` en volym.  Det finns inte stöd för att ändra ProtocolType.

* Orsak:   
`ProtocolType` Egenskapen anges när volymen skapas.  Det går inte att uppdatera.
* Lösa   
Ingen.
* Korrigera   
Skapa en annan volym med nya protokoll typer.

***Om du skapar resursen av typen {resourceType} överskrids kvoten för {quota} resurser av typen {resourceType} per {parentResourceType}. Det aktuella resurs antalet är {currentCount}. ta bort några resurser av den här typen innan du skapar en ny.***

Det här felet uppstår när du försöker skapa en`NetAppAccount`resurs ( `Volume`, `CapacityPool`, eller `Snapshot`), men kvoten har nått sin gräns.

* Orsak:   
Du försöker skapa en resurs, men kvot gränsen nås (till exempel: `NetAppAccounts` per prenumeration eller `CapacityPools` per `NetAppAccount`).
* Lösa   
Öka kvot gränsen.
* Korrigera   
Ta bort oanvända resurser av samma typ och skapa dem igen.

***Tog emot ett värde för den skrivskyddade egenskapen {propertyName}.***

Det här felet uppstår när du definierar ett värde för en egenskap som inte kan ändras. Du kan till exempel inte ändra volym-ID.

* Orsak:   
Du försöker ändra en parameter (till exempel volym-ID) som inte kan ändras.
* Lösa   
Ändra inte ett värde för egenskapen.

***Det gick inte att hitta den begärda {Resource}.***

Felet uppstår när du försöker referera till en resurs som inte finns, till exempel en volym eller en ögonblicks bild. Resursen kan ha tagits bort eller ha ett felstavat resurs namn.

* Orsak:   
Du försöker referera till en resurs som inte finns (till exempel en volym eller ögonblicks bild) som redan har tagits bort eller som har ett felstavat resurs namn.
* Lösa   
Kontrol lera om det finns stavfel i begäran för att kontrol lera att den är korrekt refererad.
* Korrigera   
Se avsnittet lösning ovan.

***Service nivån {volumeServiceLevel} är högre än överordnad {poolServiceLevel}***

Felet uppstår när du skapar eller uppdaterar en volym och du har angett Service nivån till en högre nivå än den kapacitets uppsättning som innehåller den.

* Orsak:   
Du försöker skapa eller uppdatera en volym med en högre rangordnings tjänst nivå än den överordnade kapacitets gruppen.
* Lösa   
Ange Service nivån till samma eller en lägre rangordning än den överordnade kapacitets uppsättningen.
* Korrigera   
Skapa volymen i en annan kapacitets pool med rätt service nivå. Du kan också ta bort alla volymer från kapacitets gruppen och ange service nivå för kapacitetsutnyttjandet till en högre rangordning.

***SMB-servernamn får inte vara längre än 10 tecken.***

Felet uppstår när du skapar eller uppdaterar en Active Directory-konfiguration för ett konto.

* Orsak:   
Längden på SMB-servernamnet är längre än 10 tecken.
* Lösa   
Använd ett kortare Server namn. Den maximala längden är 10 tecken.
* Korrigera   
Ingen.  Se lösningen ovan. 

***SubnetId kan inte ändras.***

Felet uppstår när du försöker ändra `subnetId` när volymen har skapats.  `SubnetId`måste anges när volymen skapas och kan inte ändras senare.

* Orsak:   
Du försöker ändra `subnetId` när volymen har skapats, vilket inte är en åtgärd som stöds. 
* Lösa   
Om du inte `subnetId` behöver ändra det kan du ta bort parametern från begäran för att ignorera fel meddelandet.
* Korrigera   
Om du behöver ändra `subnetId`kan du skapa en ny volym med en ny `subnetId`och sedan migrera data till den nya volymen.

***SubnetId har ett ogiltigt format.***

Felet uppstår när du försöker skapa en ny volym men `subnetId` inte är en `resourceId` för ett undernät.

* Orsak:   
Felet uppstår när du försöker skapa en ny volym, men `subnetId` är inte en `resourceId` för ett undernät. 
* Lösa   
Kontrol lera värdet för `subnetId` för att se till att det innehåller ett `resourceId` för under nätet som används.
* Korrigera   
Ingen. Se lösningen ovan. 

***Under nätet måste ha delegeringen "Microsoft. NetApp/Volumes".***

Felet uppstår när du skapar en volym och det valda under nätet inte delegeras till `Microsoft.NetApp/volumes`.

* Orsak:   
Du försökte skapa volymen och du har valt ett undernät som inte är delegerat till `Microsoft.NetApp/volumes`.
* Lösa   
Välj ett annat undernät som är delegerat `Microsoft.NetApp/volumes`till.
* Korrigera   
Lägg till en korrekt delegering till under nätet.

***Den angivna resurs typen är okänd/inte tillämplig.***

Felet uppstår när en namn kontroll har begärts antingen på en resurs typ som inte är tillämplig eller för en okänd resurs typ.

* Orsak:   
Namn kontroll har begärts för en resurs typ som är okänd eller inte stöds.
* Lösa   
Kontrol lera att resursen som du gör förfrågan om stöds eller innehåller inga stavfel.
* Korrigera   
Se lösningen ovan.

***Okänt Azure NetApp Files fel.***

Azure API är beroende av Azure NetApp Files API för att hantera volymer. Felet indikerar ett problem i kommunikationen till API: et.

* Orsak:   
Det underliggande API: et skickar ett okänt fel. Det här felet är troligt vis tillfälligt.
* Lösa   
Problemet är troligt vis tillfälligt och begäran bör lyckas efter en stund. Om problemet kvarstår kan du skapa ett support ärende för att undersöka problemet.
* Korrigera   
Ingen. Det underliggande API: t är viktigt för att hantera volymer.

***Ett värde togs emot för den okända egenskapen {propertyName}.***

Felet uppstår när egenskaper som inte finns anges för en resurs, till exempel volym, ögonblicks bild eller monterings mål.

* Orsak:   
Begäran har en uppsättning egenskaper som kan användas med varje resurs. Du kan inte ta med egenskaper som inte finns i begäran.
* Lösa   
Kontrol lera att alla egenskaps namn är rättstavade och att egenskaperna är tillgängliga för prenumerationen och resursen.
* Korrigera   
Minska antalet egenskaper som definierats i begäran för att eliminera den egenskap som orsakar felet.

***Uppdaterings åtgärden stöds inte för den här resurs typen.***

Det går bara att uppdatera volymer. Felet uppstår när du försöker utföra en uppdaterings åtgärd som inte stöds, till exempel uppdatera en ögonblicks bild.

* Orsak:   
Resursen som du försöker uppdatera har inte stöd för uppdaterings åtgärden. Endast volymer kan ha sina egenskaper ändrade.
* Lösa   
Ingen. Den resurs som du försöker uppdatera har inte stöd för uppdaterings åtgärden. Det går därför inte att ändra.
* Korrigera   
För en volym skapar du en ny resurs med uppdateringen på plats och migrerar data.

***Det går inte att skapa volymen i en pool som inte har statusen slutförd.***

Felet uppstår när du försöker skapa en volym i en pool som inte har statusen lyckades. Förmodligen misslyckades åtgärden Skapa för kapacitets poolen av någon anledning.

* Orsak:   
Den kapacitets uppsättning som innehåller den nya volymen är i ett felaktigt tillstånd.
* Lösa   
Kontrol lera att mediepoolen har skapats och att den inte är i ett felaktigt tillstånd.
* Korrigera   
Skapa en ny kapacitets pool och skapa volymen i den nya poolen.

***Volymen skapas och kan inte tas bort just nu.***

Felet uppstår när du försöker ta bort en volym som fortfarande skapas.

* Orsak:   
En volym skapas fortfarande när du försöker ta bort volymen.
* Lösa   
Vänta tills volymen har skapats och försök sedan att ta bort den igen.
* Korrigera   
Se lösningen ovan.

***Volymen tas bort och kan inte tas bort just nu.***

Felet uppstår när du försöker ta bort en volym när den redan har tagits bort.

* Orsak:   
En volym tas redan bort när du försöker ta bort volymen.
* Lösa   
Vänta tills den aktuella borttagnings åtgärden har slutförts.
* Korrigera   
Se lösningen ovan.

***Volymen håller på att uppdateras och kan inte tas bort just nu.***

Felet uppstår när du försöker ta bort en volym som uppdateras.

* Orsak:   
En volym uppdateras när du försöker ta bort volymen.
* Lösa   
Vänta tills uppdateringen är slutförd och försök sedan att ta bort åtgärden igen.
* Korrigera   
Se lösningen ovan.

***Det gick inte att hitta volymen eller så har den inte skapats.***

Felet uppstår när det inte gick att skapa volymen och du försöker ändra volymen eller skapa en ögonblicks bild för volymen.

* Orsak:   
Volymen finns inte, eller så gick det inte att skapa.
* Lösa   
Kontrol lera att du ändrar rätt volym och att volym skapandet har slutförts. Eller kontrol lera att volymen som du skapar en ögonblicks bild för finns.
* Korrigera   
Ingen.  Se lösningen ovan. 

***Angiven token för skapande finns redan***

Felet uppstår när du försöker skapa en volym och du anger en token för skapande (export Sök väg) som det redan finns en volym för.

* Orsak:   
Den skapande-token (export Sök väg) som du angav när volymen skapades har redan kopplats till en annan volym. 
* Lösa   
Välj en annan token för skapande.  Du kan också ta bort den andra volymen.

***Angiven token för skapande är reserverad***

Det här felet uppstår när du försöker skapa en volym och anger "default" eller "none" som fil Sök väg (skapa token).

* Orsak:    
Du försöker skapa en volym och ange "default" eller "none" som fil Sök väg (skapande-token).
* Lösa   
Välj en annan fil Sök väg (token för skapande).
 
***Active Directory autentiseringsuppgifter används***

Felet uppstår när du försöker ta bort Active Directory-konfigurationen från ett konto där det fortfarande finns minst en SMB-volym.  SMB-volymen har skapats med den Active Directory konfiguration som du försöker ta bort.

* Orsak:   
Du försöker ta bort Active Directorys konfigurationen från ett konto, men minst en SMB-volym finns fortfarande som ursprungligen skapades med hjälp av Active Directory-konfigurationen. 
* Lösa   
Ta först bort alla SMB-volymer som har skapats med hjälp av Active Directory-konfigurationen.  Försök sedan att ta bort konfigurationen igen.

***Det går inte att ändra organisatoriska enhets tilldelning om autentiseringsuppgifterna används***

Det här felet uppstår när du försöker ändra organisationsenheten för en Active Directory-konfiguration, men minst en SMB-volym finns kvar.  SMB-volymen har skapats med den Active Directory konfiguration som du försöker ta bort.

* Orsak:   
Du försöker ändra organisationsenheten för en Active Directory-konfiguration.  Men minst en SMB-volym finns fortfarande som ursprungligen skapades med hjälp av Active Directory-konfigurationen.
* Lösa   
 Ta först bort alla SMB-volymer som har skapats med hjälp av Active Directory-konfigurationen.  Försök sedan att ta bort konfigurationen igen. 

***Active Directory uppdateringen pågår redan***

Felet uppstår när du försöker redigera en Active Directory-konfiguration för vilken en redigerings åtgärd redan pågår.

* Orsak:   
Du försöker redigera en Active Directory-konfiguration, men en annan redigerings åtgärd pågår redan.
* Lösa   
Vänta tills den pågående redigerings åtgärden har slutförts.

***Ta bort alla volymer med de valda autentiseringsuppgifterna först***

Felet uppstår när du försöker ta bort en Active Directory-konfiguration, men minst en SMB-volym finns kvar.  SMB-volymen har skapats med den Active Directory konfiguration som du försöker ta bort.

* Orsak:   
Du försöker ta bort en Active Directory konfiguration, men det finns minst en SMB-volym som ursprungligen skapades med hjälp av Active Directory-konfigurationen.
* Lösa   
Ta först bort alla SMB-volymer som har skapats med hjälp av Active Directory-konfigurationen.  Försök sedan att ta bort konfigurationen igen. 

***Inga Active Directory autentiseringsuppgifter hittades i regionen***

Det här felet uppstår när du försöker skapa en SMB-volym, men ingen Active Directory-konfiguration har lagts till i kontot för regionen.

* Orsak:   
Du försöker skapa en SMB-volym, men ingen Active Directory konfiguration har lagts till i kontot. 
* Lösa   
Lägg till en Active Directory-konfiguration till kontot innan du skapar en SMB-volym.

***Det gick inte att fråga DNS-servern. Kontrol lera att nätverks konfigurationen är korrekt och att DNS-servrarna är tillgängliga.***

Det här felet uppstår när du försöker skapa en SMB-volym, men en DNS-server (anges i Active Directory konfiguration) kan inte kontaktas. 

* Orsak:   
Du försöker skapa en SMB-volym, men en DNS-server (anges i Active Directory konfigurationen) kan inte kontaktas.
* Lösa   
Granska Active Directory-konfigurationen och se till att DNS-serverns IP-adresser är korrekta och kan kontaktas.
Om det inte finns några problem med DNS-serverns IP-adresser kontrollerar du att inga brand väggar blockerar åtkomsten.

***För många samtidiga jobb***

Det här felet uppstår när du försöker skapa en ögonblicks bild när tre andra ögonblicks bild skapande åtgärder redan pågår för prenumerationen.

* Orsak:   
Du försöker skapa en ögonblicks bild när tre andra ögonblicks bild skapande åtgärder redan pågår för prenumerationen. 
* Lösa   
Jobb för att skapa ögonblicks bilder tar några sekunder att slutföra.  Vänta några sekunder och försök att skapa en ögonblicks bild igen.

***Det går inte att skapa ytterligare jobb. Vänta tills de pågående jobben har slutförts och försök igen.***

Det här felet kan inträffa när du försöker skapa eller ta bort en volym under vissa omständigheter.

* Orsak:   
Du försöker skapa eller ta bort en volym under vissa omständigheter.
* Lösa   
Vänta en stund och försök sedan igen.

***Volymen är redan i gång mellan tillstånd***

Det här felet kan inträffa när du försöker ta bort en volym som för närvarande är i ett över gångs tillstånd (det vill säga för närvarande i status för att skapa, uppdatera eller ta bort).

* Orsak:   
Du försöker ta bort en volym som för närvarande är i ett över gångs tillstånd.
* Lösa   
Vänta tills den pågående åtgärden (tillstånds över gång) har slutförts och försök sedan igen.

***Det gick inte att dela den nya volymen från ögonblicks bilden av käll volymen***

 Det här felet kan inträffa när du försöker skapa en volym från en ögonblicks bild.  

* Orsak:   
Du försöker skapa en volym från en ögonblicks bild och volym slutar i ett fel tillstånd.
* Lösa   
Ta bort volymen och gör sedan om åtgärden för att skapa volymen från ögonblicks bilden.

 
## <a name="next-steps"></a>Nästa steg

* [Utveckla för Azure NetApp Files med REST API](azure-netapp-files-develop-with-rest-api.md)
