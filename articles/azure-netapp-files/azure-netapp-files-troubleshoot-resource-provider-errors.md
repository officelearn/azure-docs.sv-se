---
title: Felsöka fel på Azure NetApp-filer Resource Provider-fel | Microsoft-dokument
description: Beskriver orsaker, lösningar och lösningar för vanliga Azure NetApp-filer Resource Provider fel.
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597199"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Felsöka fel i resursprovidern för Azure NetApp Files 

I den här artikeln beskrivs vanliga Azure NetApp-filer Resource Provider-fel, deras orsaker, lösningar och lösningar (om sådana finns).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Vanliga Azure NetApp-filresursproviderfel

***BareMetalTenantId kan inte ändras.***  

Det här felet uppstår när du försöker `BaremetalTenantId` uppdatera eller korrigera en volym och egenskapen har ett ändrat värde.

* Orsak:   
Du försöker uppdatera en volym `BaremetalTenantId` och egenskapen har ett annat värde än värdet som lagras i Azure.
* Lösning:   
Ta inte `BaremetalTenantId` med i korrigeringsfilen och uppdatera (sätta) begäran. Du kan också `BaremetalTenantId` kontrollera att det är samma i begäran.

***ServiceLevel kan inte ändras.***  

Det här felet uppstår när du försöker uppdatera eller korrigera en kapacitetspool med en annan servicenivå när kapacitetspoolen redan har volymer i den.

* Orsak:   
Du försöker uppdatera en kapacitetspooltjänstnivå när poolen innehåller volymer.
* Lösning:   
Ta bort alla volymer från kapacitetspoolen och ändra sedan servicenivån.
* Lösning:   
Skapa en annan kapacitetspool och skapa sedan volymerna igen i den nya kapacitetspoolen.

***PoolId kan inte ändras***  

Det här felet uppstår när du försöker uppdatera `PoolId` eller korrigera en kapacitetspool med en ändrad egenskap.

* Orsak:   
Du försöker uppdatera en `PoolId` kapacitetspoolegenskap. Egenskapen `PoolId` är skrivskyddad och kan inte ändras.
* Lösning:   
Ta inte `PoolId` med i korrigeringsfilen och uppdatera (sätta) begäran.  Du kan också `PoolId` kontrollera att det är samma i begäran.

***CreationToken kan inte ändras.***

Det här felet uppstår när du`CreationToken`försöker ändra filsökvägen ( ) efter att volymen har skapats. Filsökväg`CreationToken`( ) måste ställas in när volymen skapas och den kan inte ändras senare.

* Orsak:   
Du försöker ändra sökvägen`CreationToken`( ) efter att volymen har skapats, vilket inte är en åtgärd som stöds. 
* Lösning:   
Om det inte behövs att ändra sökvägen bör du överväga att ta bort parametern från begäran om att avvisa felmeddelandet.
* Lösning:   
Om du behöver ändra sökvägen (`CreationToken`) kan du skapa en ny volym med en ny filsökväg och sedan migrera data till den nya volymen.

***CreationToken måste vara minst 16 tecken långt.***

Det här felet uppstår`CreationToken`när filsökvägen ( ) inte uppfyller längdkravet. Filsökvägens längd måste vara minst ett tecken i längd.

* Orsak:   
Filsökvägen är tom.  När du skapar en volym med hjälp av API: et krävs en skapandetoken. Om du använder Azure-portalen genereras sökvägen automatiskt.
* Lösning:   
Ange minst ett tecken som`CreationToken`filsökväg ( ).

***Domännamnet kan inte ändras.***

Det här felet uppstår när du försöker ändra domännamnet i Active Directory.

* Orsak:   
Du försöker uppdatera domännamnsegenskapen.
* Lösning:    
Inga. Du kan inte ändra domännamnet.
* Lösning:   
Ta bort alla volymer med active directory-konfigurationen. Ta sedan bort Active Directory-konfigurationen och återskapa volymerna.

***Dubblettvärdesfel för objektet ExportPolicy.Rules[RuleIndex].***

Det här felet uppstår när exportprincipen inte definieras med ett unikt index. När du definierar exportprinciper måste alla exportprincipregler ha ett unikt index mellan 1 och 5.

* Orsak:   
Den definierade exportprincipen uppfyller inte kravet på exportprincipregler. Du måste ha en exportprincipregel till minst och fem exportprincipregler som högst.
* Lösning:   
Kontrollera att indexet inte redan används och att det ligger i intervallet 1 till 5.
* Lösning:   
Använd ett annat index för regeln som du försöker ange.

***Fel {åtgärd} {resourceTypeName}***

Det här felet visas när annan felhantering inte har hanterat felet när du utför en åtgärd på en resurs.   Den innehåller texten "Fel". Den `{action}` kan vara`getting`någon `creating` `updating`av `deleting`( , , , eller ).  Den `{resourceTypeName}` är `resourceTypeName` (till `netAppAccount`exempel `capacityPool` `volume`, , , och så vidare).

* Orsak:   
Det här felet är ett ohanterat undantag där orsaken inte är känd.
* Lösning:   
Kontakta Azure Support Center för att rapportera den detaljerade orsaken i loggar.
* Lösning:   
Inga.

***Filsökvägsnamnet kan bara innehålla bokstäver, siffror och bindestreck ("-").***

Det här felet uppstår när filsökvägen innehåller tecken som inte stöds, till exempel en punkt ("."), kommatecken (","), understreck ("_"), eller dollartecken ("$").

* Orsak:   
Filsökvägen innehåller tecken som inte stöds, till exempel en punkt ("."), kommatecken (","), understreck ("_"), eller dollartecken ("$").
* Lösning:   
Ta bort tecken som inte är alfabetiska bokstäver, siffror eller bindestreck ("-") från den angivna filsökvägen.
* Lösning:   
Du kan ersätta ett understreck med ett bindestreck eller använda versaler i stället för blanksteg för att ange början på nya ord.  Använd till exempel "NewVolume" i stället för "ny volym".

***FileSystemId kan inte ändras.***

Det här felet uppstår `FileSystemId`när du försöker ändra .  Att `FileSystemdId` ändra är inte en åtgärd som stöds. 

* Orsak:   
Filsystemets ID ställs in när volymen skapas. `FileSystemId`inte kan ändras senare.
* Lösning:   
Ta inte `FileSystemId` med i en korrigeringsfil och uppdatera (sätta) begäran.  Du kan också `FileSystemId` kontrollera att det är samma i begäran.

***ActiveDirectory med id: {string} finns inte.***

Delen `{string}` är det värde som `ActiveDirectoryId` du angav i egenskapen för Active Directory-anslutningen.

* Orsak:   
När du har skapat ett konto med Active Directory-konfigurationen har du angett ett värde för `ActiveDirectoryId` det ska vara tomt.
* Lösning:   
Ta inte `ActiveDirectoryId` med i create-begäran (put).

***Ogiltig api-version.***

API-versionen har antingen inte skickats eller innehåller ett ogiltigt värde.

* Orsak:   
Värdet i frågeparametern `api-version` innehåller ett ogiltigt värde.
* Lösning:   
Använd rätt API-versionsvärde.  Resursleverantören stöder många API-versioner. Värdet är i formatet yyyy-mm-dd.

***Ett ogiltigt värde {value} {1}togs emot för .***

Det här meddelandet anger ett `RuleIndex`fel `AllowedClients` `UnixReadOnly`i `UnixReadWrite` `Nfsv3`fälten `Nfsv4`för , , , , och .

* Orsak:   
Begäran om indataverifiering misslyckades för minst ett `RuleIndex` `AllowedClients`av `UnixReadOnly` `UnixReadWrite`följande `Nfsv`fält: `Nfsv4`, , , 3 och .
* Lösning:   
Se till att ställa in alla nödvändiga och icke-lökoniska parametrar på kommandoraden. Du kan till exempel `UnixReadOnly` inte `UnixReadWrite` ange både parametrar och parametrar samtidigt.
* Lösning:   
Se lösningen ovan.

***{0} IP-intervall {1} till {2} för vlan används redan***

Det här felet beror på att de interna posterna för de använda IP-intervallen har en konflikt med den nyligen tilldelade IP-adressen.

* Orsak:   
IP-adressen som tilldelats för att skapa volymen har redan registrerats.
Orsaken kan vara en tidigare misslyckad volymskapande.
* Lösning:   
Kontakta Azure Support Center.

***Värde saknas för {property}.***

Det här felet anger att en obligatorisk egenskap saknas i begäran. Strängen {egenskap} innehåller namnet på egenskapen saknas.

* Orsak:   
Begäran om indataverifiering misslyckades för minst en av egenskaperna.
* Lösning:   
Se till att ange alla nödvändiga och icke-omskolningsegenskaper i begäran, särskilt egenskapen från felmeddelandet.

***MountTargets kan inte ändras.***

Det här felet uppstår när en användare försöker uppdatera eller korrigera egenskapen MountTargets för volym.

* Orsak:   
Du försöker uppdatera egenskapen volume. `MountTargets` Det går inte att ändra den här egenskapen.
* Lösning:   
Ta inte `MountTargets` med i en korrigeringsfil och uppdatera (sätta) begäran.  Du kan också `MountTargets` kontrollera att det är samma i begäran.

***Namn som redan används.***

Det här felet anger att namnet på resursen redan används.

* Orsak:   
Du försöker skapa en resurs med ett namn som används för en befintlig resurs.
* Lösning:   
Använd ett unikt namn när du skapar resursen.

***Sökvägen som redan används.***

Det här felet anger att filsökvägen för volymen redan används.

* Orsak:   
Du försöker skapa en volym med en filsökväg som är samma som en befintlig volym.
* Lösning:   
Använd en unik filsökväg när du skapar volymen.

***Namnet är för långt.***

Det här felet anger att resursnamnet inte uppfyller kravet på maximal längd.

* Orsak:   
Resursnamnet är för långt.
* Lösning:   
Använd ett kortare namn för resursen.

***Sökvägen är för lång.***

Det här felet anger att filsökvägen för volymen inte uppfyller kravet på maximal längd.

* Orsak:   
Volymfilens sökväg är för lång.
* Lösning:   
Använd en kortare sökväg för filer.

***Namnet är för kort.***

Det här felet anger att resursnamnet inte uppfyller kravet på minsta längd.

* Orsak:   
Resursnamnet är för kort.
* Lösning:   
Använd ett längre namn för resursen.

***Sökvägen är för kort.***

Det här felet anger att volymfilens sökväg inte uppfyller kravet på minsta längd.

* Orsak:   
Volymfilens sökväg är för kort.
* Lösning:   
Öka längden på volymfilens sökväg.

***Azure NetApp-fil-API kan inte nås.***

Azure-API:et är beroende av Azure NetApp Files API för att hantera volymer. Det här felet indikerar ett problem med API-anslutningen.

* Orsak:   
Det underliggande API:et svarar inte, vilket resulterar i ett internt fel. Det här felet kommer sannolikt att vara tillfälligt.
* Lösning:   
Frågan kommer sannolikt att vara tillfällig. Begäran bör lyckas efter en tid.
* Lösning:   
Inga. Det underliggande API:et är viktigt för att hantera volymer.

***Inget åtgärdsresultat-ID{0}hittades för ' '.***

Det här felet indikerar att ett internt fel hindrar åtgärden från att slutföras.

* Orsak:   
Ett internt fel uppstod och hindrade åtgärden från att slutföras.
* Lösning:   
Det här felet kommer sannolikt att vara tillfälligt. Vänta några minuter och försök igen. Om problemet kvarstår skapar du en biljett för att få teknisk support att undersöka problemet.
* Lösning:   
Vänta några minuter och kontrollera om problemet kvarstår.

***Det är inte tillåtet att blanda protokolltyperna CIFS och NFS***

Det här felet uppstår när du försöker skapa en volym och det finns både CIFS -reglerna (SMB) och NFS-protokolltyperna i volymegenskaperna.

* Orsak:   
Både CIFS-protokolltyperna (SMB) och NFS används i volymegenskaperna.
* Lösning:   
Ta bort en av protokolltyperna.
* Lösning:   
Lämna egenskapen för protokolltypen tom eller null.

***Antal objekt: {value} för objekt: ExportPolicy.Rules[RuleIndex] ligger utanför intervallet min-max.***

Det här felet uppstår när exportprincipreglerna inte uppfyller kravet på lägsta eller högsta intervall. Om du definierar exportprincipen måste den ha en exportprincipregel till minst och fem exportprincipregler som högst.

* Orsak:   
Exportprincipen som du har definierat uppfyller inte det intervall som krävs.
* Lösning:   
Kontrollera att indexet inte redan används och att det ligger i intervallet 1 till 5.
* Lösning:   
Det är inte obligatoriskt att använda exportprincipen för volymerna. Du kan utelämna exportprincipen helt om du inte behöver använda exportprincipregler.

***Endast en active directory tillåts***

Det här felet uppstår när du försöker skapa en Active Directory-konfiguration och en finns redan för prenumerationen i regionen. Felet kan också uppstå när du försöker skapa mer än en Active Directory-konfiguration.

* Orsak:   
Du försöker skapa (inte uppdatera) en active directory, men en finns redan.
* Lösning:   
Om Active Directory-konfigurationen inte används kan du först ta bort den befintliga konfigurationen och sedan försöka återskapa åtgärden.
* Lösning:   
Inga. Endast en Active Directory är tillåten.

***Åtgärden {operation} stöds inte.***

Det här felet anger att åtgärden inte är tillgänglig för den aktiva prenumerationen eller resursen.

* Orsak:   
Åtgärden är inte tillgänglig för prenumerationen eller resursen.
* Lösning:   
Kontrollera att åtgärden har angetts korrekt och att den är tillgänglig för den resurs och prenumeration som du använder.

***OwnerId kan inte ändras***

Det här felet uppstår när du försöker ändra egenskapen OwnerId för volymen. Att ändra OwnerId är inte en åtgärd som stöds. 

* Orsak:   
Egenskapen `OwnerId` ställs in när volymen skapas. Egenskapen kan inte ändras senare.
* Lösning:   
Ta inte `OwnerId` med i en korrigeringsfil och uppdatera (sätta) begäran. Du kan också `OwnerId` kontrollera att det är samma i begäran.

***Det gick inte att hitta den överordnade poolen***

Det här felet uppstår när du försöker skapa en volym och kapacitetspoolen där du skapar volymen hittades inte.

* Orsak:   
Det gick inte att hitta kapacitetspoolen där volymen skapas.
* Lösning:   
Troligtvis har poolen inte skapats helt eller togs redan bort när volymen skapades.

***Korrigeringsåtgärd stöds inte för den här resurstypen.***

Det här felet uppstår när du försöker ändra monteringsmålet eller ögonblicksbilden.

* Orsak:   
Monteringsmålet definieras när det skapas och kan inte ändras senare.
Ögonblicksbilderna innehåller inga egenskaper som kan ändras.
* Lösning:   
Inga. Dessa resurser har inga egenskaper som kan ändras.

***Poolstorleken är för liten för den totala volymstorleken.***

Det här felet uppstår när du uppdaterar kapacitetspoolens `usedBytes` storlek och storleken är mindre än det totala värdet för alla volymer i kapacitetspoolen.  Det här felet kan också uppstå när du skapar en ny volym eller ändrar storlek på en befintlig volym, och den nya volymstorleken överskrider det lediga utrymmet i kapacitetspoolen.

* Orsak:   
Du försöker uppdatera kapacitetspoolen till en mindre storlek än usedBytes i alla volymer i kapacitetspoolen.  Du försöker också skapa en volym som är större än det lediga utrymmet i kapacitetspoolen.  Du kan också ändra storlek på en volym och den nya storleken överskrider det lediga utrymmet i kapacitetspoolen.
* Lösning:   
Ange kapacitetspoolstorleken till ett större värde eller skapa en mindre volym för en volym.
* Lösning:   
Ta bort tillräckligt med volymer så att kapacitetspoolens storlek kan uppdateras till den här storleken.

***Egenskapen: Plats för ögonblicksbild måste vara samma som Volym***

Det här felet uppstår när du skapar en ögonblicksbild med annan plats än volymen som äger ögonblicksbilden.

* Orsak:   
Ogiltigt värde i egenskapen Plats för ögonblicksbilden.
* Lösning:   
Ange giltig sträng i egenskapen Plats.

***Namnet {resourceType} måste vara samma som resursidentifieringsnamnet.***

Det här felet uppstår när du skapar en resurs och du fyller i `resourceId`namnegenskapen med annat värde än namnegenskapen för .

* Orsak:   
Ogiltigt värde i namnegenskapen när du skapar en resurs.
* Lösning:   
Lämna namnegenskapen tom eller låt den använda samma värde som namnegenskapen (mellan det `resourceId`sista omvänt snedstrecket "/" och frågetecknet "?") i .

***Protokolltypen {value} är inte känd***

Det här felet uppstår när du skapar en volym med en okänd protokolltyp.  Giltiga värden är "NFSv3", "NFSv4" och "CIFS".

* Orsak:   
Du försöker ange ett ogiltigt `protocolType` värde i egenskapen volume.
* Lösning:   
Ange en giltig `protocolType`sträng i .
* Lösning:   
Ange `protocolType` som null.

***Protokolltyper kan inte ändras***

Det här felet uppstår när `ProtocolType` du försöker uppdatera eller korrigera för en volym.  Att ändra ProtocolType är inte en åtgärd som stöds.

* Orsak:   
Egenskapen `ProtocolType` ställs in när volymen skapas.  Det går inte att uppdatera den.
* Lösning:   
Inga.
* Lösning:   
Skapa en annan volym med nya protokolltyper.

***Om du skapar resursen av typen {resourceType} överskrids kvoten för {quota}-resurser av typen {resourceType} per {parentResourceType}. Det aktuella resursantalet är {currentCount}, ta bort några resurser av den här typen innan du skapar ett nytt.***

Det här felet uppstår när du`NetAppAccount`försöker `CapacityPool` `Volume`skapa `Snapshot`en resurs ( , , eller ), men kvoten har nått sin gräns.

* Orsak:   
Du försöker skapa en resurs, men kvotgränsen `NetAppAccounts` nås (t.ex. per prenumeration eller `CapacityPools` per `NetAppAccount`).
* Lösning:   
Öka kvotgränsen.
* Lösning:   
Ta bort oanvända resurser av samma typ och skapa dem igen.

***Tog emot ett värde för skrivskyddad egenskap {propertyName}.***

Det här felet uppstår när du definierar ett värde för en egenskap som inte kan ändras. Du kan till exempel inte ändra volym-ID.

* Orsak:   
Du försöker ändra en parameter (till exempel volym-ID) som inte kan ändras.
* Lösning:   
Ändra inte ett värde för egenskapen.

***Det gick inte att hitta den begärda {resursen}.***

Det här felet uppstår när du försöker referera till en icke-existerande resurs, till exempel en volym eller ögonblicksbild. Resursen kan ha tagits bort eller har ett felstavat resursnamn.

* Orsak:   
Du försöker referera till en icke-existerande resurs (till exempel en volym eller ögonblicksbild) som redan har tagits bort eller som har ett felstavat resursnamn.
* Lösning:   
Kontrollera att stavfelen finns i begäran om stavfel för att se till att den är korrekt refererad.
* Lösning:   
Se avsnittet Lösning ovan.

***Servicenivån {volumeServiceLevel} är högre än överordnad {poolServiceLevel}***

Det här felet uppstår när du skapar eller uppdaterar en volym och du har angett servicenivån till en högre nivå än kapacitetspoolen som innehåller den.

* Orsak:   
Du försöker skapa eller uppdatera en volym med en högre rangordnad tjänstnivå än den överordnade kapacitetspoolen.
* Lösning:   
Ange servicenivån till samma eller lägre rang än den överordnade kapacitetspoolen.
* Lösning:   
Skapa volymen i en annan kapacitetspool med rätt servicenivå. Alternativt kan du ta bort alla volymer från kapacitetspoolen och ange servicenivå för kapacitetspoolen till en högre rangordning.

***SMB-servernamnet får inte vara längre än 10 tecken.***

Det här felet uppstår när du skapar eller uppdaterar en Active Directory-konfiguration för ett konto.

* Orsak:   
Längden på SMB-servernamnet överstiger 10 tecken.
* Lösning:   
Använd ett kortare servernamn. Den maximala längden är 10 tecken.
* Lösning:   
Inga.  Se lösningen ovan. 

***Det går inte att ändra undernät.***

Det här felet uppstår när `subnetId` du försöker ändra när volymen har skapats.  `SubnetId`måste ställas in när volymen skapas och inte kan ändras senare.

* Orsak:   
Du försöker ändra `subnetId` när volymen har skapats, vilket inte är en åtgärd som stöds. 
* Lösning:   
Om det `subnetId` inte behövs att ändra problemet bör du överväga att ta bort parametern från begäran om att ignorera felmeddelandet.
* Lösning:   
Om du behöver `subnetId`ändra kan du skapa en `subnetId`ny volym med en ny och sedan migrera data till den nya volymen.

***SubnetId är i ogiltigt format.***

Det här felet uppstår när du försöker `subnetId` skapa `resourceId` en ny volym, men det är inte en för ett undernät.

* Orsak:   
Det här felet uppstår när du försöker `subnetId` skapa en `resourceId` ny volym, men det är inte en för ett undernät. 
* Lösning:   
Kontrollera värdet för `subnetId` att säkerställa att `resourceId` det innehåller en för det undernät som används.
* Lösning:   
Inga. Se lösningen ovan. 

***Undernätet måste ha en Delegering av Microsoft.NetApp/volymer.***

Det här felet uppstår när du skapar en volym och `Microsoft.NetApp/volumes`det valda undernätet inte delegeras till .

* Orsak:   
Du försökte skapa volym och du har valt ett `Microsoft.NetApp/volumes`undernät som inte är delegerat till .
* Lösning:   
Markera ett annat undernät `Microsoft.NetApp/volumes`som delegeras till .
* Lösning:   
Lägg till en korrekt delegering i undernätet.

***Den angivna resurstypen är okänd/inte tillämplig.***

Det här felet uppstår när en namnkontroll har begärts antingen på en resurstyp som inte kan tillämpas eller för en okänd resurstyp.

* Orsak:   
Namnkontroll har begärts för en okänd resurstyp eller utan stöd.
* Lösning:   
Kontrollera att resursen du gör begäran om stöds eller inte innehåller några stavfel.
* Lösning:   
Se lösningen ovan.

***Okänt Azure NetApp-filfel.***

Azure-API:et är beroende av Azure NetApp Files API för att hantera volymer. Felet indikerar ett problem i kommunikationen till API:et.

* Orsak:   
Det underliggande API:et skickar ett okänt fel. Det här felet kommer sannolikt att vara tillfälligt.
* Lösning:   
Problemet kommer sannolikt att vara tillfälligt och begäran bör lyckas efter en tid. Om problemet kvarstår skapar du en supportbiljett för att få problemet undersökt.
* Lösning:   
Inga. Det underliggande API:et är viktigt för att hantera volymer.

***Värdet har tagits emot för en okänd egenskap {propertyName}.***

Det här felet uppstår när obefintliga egenskaper tillhandahålls för en resurs, till exempel volym, ögonblicksbild eller monteringsmål.

* Orsak:   
Begäran har en uppsättning egenskaper som kan användas med varje resurs. Du kan inte inkludera några obefintliga egenskaper i begäran.
* Lösning:   
Kontrollera att alla egenskapsnamn är rättstavade och att egenskaperna är tillgängliga för prenumerationen och resursen.
* Lösning:   
Minska antalet egenskaper som definierats i begäran för att eliminera egenskapen som orsakar felet.

***Uppdateringsåtgärd stöds inte för den här resurstypen.***

Endast volymer kan uppdateras. Det här felet uppstår när du försöker utföra en uppdateringsåtgärd som inte stöds, till exempel uppdatera en ögonblicksbild.

* Orsak:   
Resursen som du försöker uppdatera stöder inte uppdateringsåtgärden. Endast volymer kan få sina egenskaper ändrade.
* Lösning:   
Inga. Resursen som du försöker uppdatera stöder inte uppdateringsåtgärden. Därför kan det inte ändras.
* Lösning:   
Skapa en ny resurs med uppdateringen på plats för en volym.

***Det går inte att skapa volymen i en pool som inte är i tillstånd.***

Det här felet uppstår när du försöker skapa en volym i en pool som inte är i efterföljande tillstånd. Troligen misslyckades åtgärden för att skapa kapacitetspoolen av någon anledning.

* Orsak:   
Kapacitetspoolen som innehåller den nya volymen är i ett misslyckat tillstånd.
* Lösning:   
Kontrollera att kapacitetspoolen har skapats och att den inte är i ett misslyckat tillstånd.
* Lösning:   
Skapa en ny kapacitetspool och skapa volymen i den nya poolen.

***Volymen skapas och kan inte tas bort för tillfället.***

Det här felet uppstår när du försöker ta bort en volym som fortfarande skapas.

* Orsak:   
En volym skapas fortfarande när du försöker ta bort volymen.
* Lösning:   
Vänta tills volymen har skapats och försök sedan borttagningen igen.
* Lösning:   
Se lösningen ovan.

***Volymen tas bort och kan inte tas bort för tillfället.***

Det här felet uppstår när du försöker ta bort en volym när den redan tas bort.

* Orsak:   
En volym tas redan bort när du försöker ta bort volymen.
* Lösning:   
Vänta tills den aktuella borttagningsåtgärden är klar.
* Lösning:   
Se lösningen ovan.

***Volymen uppdateras och kan inte tas bort för tillfället.***

Det här felet uppstår när du försöker ta bort en volym som uppdateras.

* Orsak:   
En volym uppdateras när du försöker ta bort volymen.
* Lösning:   
Vänta tills uppdateringsåtgärden är klar och försök sedan borttagningen igen.
* Lösning:   
Se lösningen ovan.

***Volymen hittades inte eller skapades inte.***

Det här felet uppstår när volymskapandet har misslyckats och du försöker ändra volymen eller skapa en ögonblicksbild för volymen.

* Orsak:   
Volymen finns inte eller så misslyckades skapandet.
* Lösning:   
Kontrollera att du ändrar rätt volym och att skapandet av volymen lyckades. Du kan också kontrollera att volymen som du skapar en ögonblicksbild för finns.
* Lösning:   
Inga.  Se lösningen ovan. 

***Den angivna skapandetoken finns redan***

Det här felet uppstår när du försöker skapa en volym och du anger en skapandetoken (exportsökväg) som det redan finns en volym för.

* Orsak:   
Den skapandetoken (exportsökvägen) som du angav när volymen skapades är redan associerad med en annan volym. 
* Lösning:   
Välj en annan skapelsetoken.  Du kan också ta bort den andra volymen.

***Den angivna skapandetoken är reserverad***

Det här felet uppstår när du försöker skapa en volym och du anger "standard" eller "ingen" som filsökväg (skapandetoken).

* Orsak:    
Du försöker skapa en volym och du anger "standard" eller "ingen" som filsökväg (skapandetoken).
* Lösning:   
Välj en annan filsökväg (skapa token).
 
***Active Directory-autentiseringsuppgifter används***

Det här felet uppstår när du försöker ta bort Active Directory-konfigurationen från ett konto där det fortfarande finns minst en SMB-volym.  SMB-volymen skapades med hjälp av Active Directory-konfigurationen som du försöker ta bort.

* Orsak:   
Du försöker ta bort Active Directory-konfigurationen från ett konto, men det finns fortfarande minst en SMB-volym som ursprungligen skapades med Active Directory-konfigurationen. 
* Lösning:   
Ta först bort alla SMB-volymer som har skapats med active directory-konfigurationen.  Försök sedan med konfigurationsborttagningen igen.

***Det går inte att ändra tilldelningen av organisationsenheter om autentiseringsuppgifterna används***

Det här felet uppstår när du försöker ändra organisationsenhet för en Active Directory-konfiguration, men det finns fortfarande minst en SMB-volym.  SMB-volymen skapades med den Active Directory-konfiguration som du försöker ta bort.

* Orsak:   
Du försöker ändra organisationsenhet för en Active Directory-konfiguration.  Men det finns fortfarande minst en SMB-volym som ursprungligen skapades med active directory-konfigurationen.
* Lösning:   
 Ta först bort alla SMB-volymer som har skapats med active directory-konfigurationen.  Försök sedan med konfigurationsborttagningen igen. 

***Active Directory-uppdatering pågår redan***

Det här felet uppstår när du försöker redigera en Active Directory-konfiguration som en redigeringsåtgärd redan pågår för.

* Orsak:   
Du försöker redigera en Active Directory-konfiguration, men en annan redigeringsåtgärd pågår redan.
* Lösning:   
Vänta tills redigeringsåtgärden som körs har slutförts.

***Ta bort alla volymer med de markerade autentiseringsuppgifterna först***

Det här felet uppstår när du försöker ta bort en Active Directory-konfiguration, men det finns fortfarande minst en SMB-volym.  SMB-volymen skapades med hjälp av Active Directory-konfigurationen som du försöker ta bort.

* Orsak:   
Du försöker ta bort en Active Directory-konfiguration, men det finns fortfarande minst en SMB-volym som ursprungligen skapades med Active Directory-konfigurationen.
* Lösning:   
Ta först bort alla SMB-volymer som har skapats med active directory-konfigurationen.  Försök sedan med konfigurationsborttagningen igen. 

***Inga Active Directory-autentiseringsuppgifter hittades i regionen***

Det här felet uppstår när du försöker skapa en SMB-volym, men ingen Active Directory-konfiguration har lagts till i kontot för regionen.

* Orsak:   
Du försöker skapa en SMB-volym, men ingen Active Directory-konfiguration har lagts till i kontot. 
* Lösning:   
Lägg till en Active Directory-konfiguration i kontot innan du skapar en SMB-volym.

***Det gick inte att fråga DNS-servern. Kontrollera att nätverkskonfigurationen är korrekt och att DNS-servrar är tillgängliga.***

Det här felet uppstår när du försöker skapa en SMB-volym, men en DNS-server (som anges i Active Directory-konfigurationen) kan inte nås. 

* Orsak:   
Du försöker skapa en SMB-volym, men en DNS-server (som anges i Active Directory-konfigurationen) kan inte nås.
* Lösning:   
Granska Active Directory-konfigurationen och kontrollera att DNS-serverns IP-adresser är korrekta och kan nås.
Om det inte finns några problem med DNS-serverns IP-adresser kontrollerar du att inga brandväggar blockerar åtkomsten.

***För många samtidiga jobb***

Det här felet uppstår när du försöker skapa en ögonblicksbild när tre andra åtgärder för att skapa ögonblicksbilder redan pågår för prenumerationen.

* Orsak:   
Du försöker skapa en ögonblicksbild när tre andra åtgärder för att skapa ögonblicksbilder redan pågår för prenumerationen. 
* Lösning:   
Ögonblicksbild skapande jobb tar högst några sekunder att slutföra.  Vänta några sekunder och försök återskapa åtgärden för att skapa ögonblicksbilder.

***Det går inte att skapa fler jobb. Vänta tills de pågående jobben är klara och försök igen***

Det här felet kan uppstå när du försöker skapa eller ta bort en volym under särskilda omständigheter.

* Orsak:   
Du försöker skapa eller ta bort en volym under särskilda omständigheter.
* Lösning:   
Vänta en minut eller så och försök igen.

***Volymen övergår redan mellan lägen***

Det här felet kan uppstå när du försöker ta bort en volym som för närvarande är i ett övergångstillstånd (det vill säga för närvarande i tillståndet att skapa, uppdatera eller ta bort).

* Orsak:   
Du försöker ta bort en volym som för närvarande är i ett övergångstillstånd.
* Lösning:   
Vänta tills den tillståndsövergång som körs (tillståndsövergång) har slutförts och försök sedan igen.

***Det gick inte att dela upp ny volym från källvolymögonblicksbilden***

 Det här felet kan uppstå när du försöker skapa en volym från en ögonblicksbild.  

* Orsak:   
Du försöker skapa en volym från en ögonblicksbild och volymen slutar i ett feltillstånd.
* Lösning:   
Ta bort volymen och försök sedan återskapa åtgärden för att skapa volymen från ögonblicksbilden.

 
## <a name="next-steps"></a>Nästa steg

* [Utveckla för Azure NetApp-filer med REST API](azure-netapp-files-develop-with-rest-api.md)
