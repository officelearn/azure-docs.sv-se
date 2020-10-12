---
title: Azure Service Fabric värd för aktivering och inaktive ring av livs cykeln
description: Förklarar program-och ServicePacknas livs cykel på en nod
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: a39aecf16d1c3303c0a590b389ba2aa69d4472f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87405134"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric är värd för livs cykeln
Den här artikeln innehåller en översikt över händelser som inträffar när ett program aktive ras på en nod och olika kluster konfigurationer som används för att styra beteendet.

Innan du fortsätter bör du se till att du förstår de olika begreppen och förhållandena som beskrivs i [modellera ett program i Service Fabric][a1]. 

> [!NOTE]
> I den här artikeln, om inget annat anges som betyder något annat:
>
> - *Repliken* refererar både till en replik av en tillstånds känslig tjänst och en instans av en tillstånds lös tjänst.
> - *CodePackage* behandlas som likvärdigt med en *ServiceHost* -process som registrerar en *ServiceType*och är värd för repliker av tjänster för denna *ServiceType*.
>

## <a name="activation-of-applicationservicepackage"></a>Aktivering av program/ServicePack

Pipelinen för aktivering är följande:

1. Ladda ned ApplicationPackage. Exempel: ApplicationManifest.xml osv.
2. Konfigurera miljö för program för t. ex.: skapa användare osv.
3. Starta spårning av program för inaktive ring.
4. Hämta ServicePack. Exempel: ServiceManifest.xml, kod, konfiguration, data paket osv.
5. Konfigurera en miljö för tjänst paket för ex: Konfigurera brand väggen, allokera portar för slut punkter osv.
6. Starta spårning av ServicePack för inaktive ring.
7. Starta SetupEntryPoint för CodePackages och vänta på slut för ande.
8. Starta MainEntryPoint av CodePackages.

### <a name="servicetype-blocklisting"></a>ServiceType-Blocklisting
**ServiceTypeDisableFailureThreshold** avgör antalet haverier (aktivering, hämtning, CodePackage-haverier) efter vilken ServiceType har schemalagts för Blocklisting. Det första aktiveringen/nedladdnings problemet eller CodePackage-kraschen bör utlösa schemat för ServiceType Blocklisting. **ServiceTypeDisableGraceInterval** -konfigurationen fastställer Grace-intervallet efter vilken ServiceType har marker ATS som blocklisted på noden. Observera att för att allt detta ska ske vid aktivering/hämtning/CodePackage bör omstart fortfarande göras i ett nytt försök med ett internt och spåras av under systemet. Försöker igen, innebär till exempel att CodePackage kommer att schemaläggas att starta igen efter kraschen eller på att Service Fabric försöka hämta paket igen.
När du har blocklisted bör du se felet "" system. hosting "som rapporterade felet för egenskapen ' ServiceTypeRegistration: ServiceType '. ServiceType inaktiverades på noden. "

ServiceType kommer att aktive ras igen på noden 
- Om aktiverings åtgärden lyckas eller når **ActivationMaxFailureCount** -försök vid ett haveri.
- Om hämtnings åtgärden lyckas eller når **DeploymentMaxFailureCount** -försök vid ett haveri.
- Om en CodePackage som har kraschat börjar säkerhetskopiera och registrerar ServiceType.

Anledningen till att aktivera ServiceType igen när **ActivationMaxFailureCount** / **DeploymentMaxFailureCount** -försök är det högsta antalet försök som Service Fabric utföras för att aktivera/Ladda ned ett program på en nod. Om det inte lyckas görs ett nytt försök att utföra den aktuella åtgärden och eftersom Service Fabric vill ge tjänsten en annan möjlighet att aktivera, vilket kan lyckas, vilket leder till att problemet automatiskt reservas är det knutet till livs cykeln för aktiverings-/nedladdnings åtgärden. En ny aktiverings-/nedladdnings åtgärd som utlöses genom placering av en replik kan utlösa ServiceType-Blocklisting igen eller lyckas.

> [!NOTE]
> Om din CodePackage som inte registrerar en ServiceType kraschar, påverkar den inte ServiceType. Endast den CodePackage som är värd för en replik krasch påverkar ServiceType.
>

### <a name="codepackage-crash"></a>CodePackage-krasch
När en CodePackage kraschar använder Service Fabric en säkerhets kopiering för att starta den igen och avstängningen är oberoende av om kod paketet har registrerat en typ med oss eller inte.

Värdet för säkerhets kopiering är alltid min (RetryTime, **ActivationMaxRetryInterval**) och det här värdet kan vara konstant, linjärt eller exponentiell baserat på **ActivationRetryBackoffExponentiationBase** -konfigurationen.

- Konstant: om **ActivationRetryBackoffExponentiationBase** = = 0 Then RetryTime = **ActivationRetryBackoffInterval**;
- Linjär: IF  **ActivationRetryBackoffExponentiationBase** = = 0 Then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** där ContinousFailureCount är antalet gånger som en CodePackage kraschar eller inte kan aktive ras.
- Exponentiell: RetryTime = (**ActivationRetryBackoffInterval** i sekunder) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
Du kan kontrol lera beteendet som du vill, t. ex. snabb omstarter. Låt oss prata om linjärt. Det innebär att om en CodePackage kraschar kommer start intervallet att vara efter 10, 20, 30 40 SEK tills CodePackage har inaktiverats. 
    
Maximal tids period Service Fabric säkerhets kopieringar (väntar) efter ett haveri styrs av **ActivationMaxRetryInterval**
    
Om din CodePackage kraschar och kommer att säkerhets kopie ras måste den vara igång för **CodePackageContinuousExitFailureResetInterval** för att Service Fabric ta hänsyn till den som felfri vid den tidpunkt då den kommer att skriva över hälso rapporten som OK och återställa ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage registrerar inte ServiceType
Om en CodePackage är aktiv och förväntas registrera en ServiceType med oss, men aldrig gör, kommer Service Fabric att generera en varnings-HealthReport efter **ServiceTypeRegistrationTimeout** som säger att ServiceType inte har kon figurer ATS inom tids gränsen.

### <a name="activation-failure"></a>Aktiverings problem
Service Fabric använder alltid en linjär avstängning (samma som CodePackage krasch) när fel påträffas under aktiveringen. Det innebär att aktiverings åtgärden får upp efter (0 + 10 + 20 + 30 + 40) = 100 SEK (första försöket är omedelbart). När aktiveringen inte har gjorts om.
    
Maximal aktiverings backoff kan vara **ActivationMaxRetryInterval** och försöka **ActivationMaxFailureCount**igen.

### <a name="download-failure"></a>Nedladdnings problem
Service Fabric använder alltid en linjär säkerhets kopiering när det påträffar ett fel under nedladdningen. Det innebär att aktiverings åtgärden får upp efter (0 + 10 + 20 + 30 + 40) = 100 SEK (första försöket är omedelbart). Det går inte att göra om den här nedladdningen. Den linjära avstängningen för nedladdning är lika med ContinuousFailureCount ***DeploymentRetryBackoffInterval** och kan återställas till **DeploymentMaxRetryInterval**. Precis som aktiveringar kan nedladdnings åtgärden försöka igen för **ActivationMaxFailureCount**.

> [!NOTE]
> Innan du ändrar konfigurationerna finns här några exempel som du bör ha i åtanke.

* Om CodePackage håller på att krascha och säkerhets kopie ras kommer ServiceType att inaktive ras. Men om aktiverings konfigurationen är sådan att den har en snabb omstart kan CodePackage komma upp några gånger innan den kan se inaktive ringen av ServiceType. För att: anta att CodePackage kommer igång registrerar du ServiceType med Service Fabric och sedan kraschar. I så fall, när en värd tar emot en typ registrering, annulleras **ServiceTypeDisableGraceInterval** -perioden. Detta kan upprepas tills CodePackage tillbaka till ett värde som är större än  **ServiceTypeDisableGraceInterval** och sedan är ServiceType inaktiverat på noden. Det kan därför bero på att din ServiceType är inaktive rad på noden.

* När Service Fabric systemet behöver placera en replik på en nod, ställer RA (ReconfigurationAgent) under system för att aktivera programmet och försöker aktivera begäran var 15: a 15 SEK (**RAPMessageRetryInterval**). För att Service Fabric systemet för att veta att ServiceType har inaktiverats, måste aktiverings åtgärden i vara aktiv under en längre period än återförsöksintervall och **ServiceTypeDisableGraceInterval**. Exempel: låt klustret ha inställningen configs **ActivationMaxFailureCount** inställd på 5 och **ActivationRetryBackoffInterval** inställd på 1 SEK. Det innebär att aktiverings åtgärden kommer att bli upp efter (0 + 1 + 2 + 3 + 4) = 10 SEK (första omförsöket sker omedelbart) och när den är värd för att försöka igen. I det här fallet kommer aktiverings åtgärden att slutföras och kommer inte att försöka igen efter 15 sekunder. Det hände eftersom Service Fabric uttömda alla omförsök inom 15 sekunder. Det innebär att varje nytt försök från ReconfigurationAgent skapar en ny aktiverings åtgärd i värd under systemet och mönstret fortsätter upprepas och ServiceType inaktive ras aldrig på noden. Eftersom ServiceType inte inaktive ras på noden, kommer SF-systemets komponent FM (FailoverManager) inte att flytta repliken till en annan nod.
> 

## <a name="deactivation"></a>Inaktive ring

När ett ServicePack aktive ras på en nod spåras det för inaktive ring. Inaktiverat är den entitet som håller koll på den.
Det fungerar på två sätt:

1.  Med jämna mellanrum: vid varje **DeactivationScanInterval**söker den efter ServicePackages, som aldrig har en replik och markerar dem som kandidater för inaktive ring.
2.  ReplicaClose: om en replik stängs, hämtas en DecrementUsageCount. Om antalet går till 0 innebär det att ServicePack inte är värd för någon replik och därför är en kandidat för inaktive ring.

 Baserat på aktiverings läget [exklusivt/delat][a2]schemaläggs kandidater för inaktive ring efter **DeactivationGraceInterval** för SharedMode/ **ExclusiveModeDeactivationGraceInterval** för ExclusiveMode. Om den här tiden är en ny replik placering kommer inaktive ringen att annulleras.

### <a name="periodically"></a>Återkommande
Exempel 1: Låt oss säga att inaktive tiden gör en genomsökning vid tid T (**DeactivationScanInterval**). Nästa genomsökning kommer att finnas på 2T. Anta att en ServicePacks aktivering skedde vid T + 1. Detta ServicePack är inte värd för en replik och måste därför inaktive ras. För att ServicePack ska vara en kandidat för inaktive ring måste det vara i läget ingen replik i minst T-tid. Det innebär att den kommer att bli tillgänglig för inaktive ring vid 2T + 1. Därför hittar inte genomsökningen på 2T denna ServicePack som en kandidat för inaktive ring. Nästa inaktive 3T kommer att schemalägga detta ServicePack för inaktive ring eftersom det har varit i inget replik tillstånd för tid T.  

Exempel 2: Låt oss säga att ett ServicePack aktive ras vid tiden T-1 och att inaktive ras gör en genomsökning på T. ex. ServicePack är inte värd för en replik. Vid nästa genomsökning 2T kommer detta ServicePack att finnas som en kandidat för inaktive ring, och därför schemaläggs för inaktive ring.  

Exempel 3: Låt oss säga att ett ServicePack aktive ras vid T – 1 och inaktive ras gör en genomsökning på T. ex. ServicePack är inte värd för en replik ännu. Nu vid T + 1 placeras en replik, dvs. Värd hämtar en IncrementUsageCount, vilket innebär att en replik skapas. Nu vid 2T kommer detta ServicePack inte att schemaläggas för inaktive ring. Nu kommer inaktive ringen att flyttas till ReplicaClose-logiken som beskrivs nedan.

Exempel 4: anta att ditt ServicePack är stort, t. ex. 10 GB, kan ta en stund att ladda ned på noden. När ett program har Aktiver ATS spårar den livs cykeln. Om du nu har **DeactivationScanInterval** config Small kan du stöta på problem där ditt servicepack inte tar tid att aktivera på noden eftersom tiden laddades ned. För att lösa problemet kan du i [förväg ladda ned servicepack på noden][p1]. 

> [!NOTE]
> Det innebär att en servicepack kan inaktive ras överallt mellan (**DeactivationScanInterval** till 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>Replik stängning:
Med inaktiverat behålls antalet repliker som ett ServicePack innehåller. Om ett ServicePack innehåller en replik och repliken har stängts/avslut ATS, får du en DecrementUsageCount. När en replik öppnas får du en IncrementUsageCount med värd. Minskning innebär att servicepack nu är värd för en mindre replik och när antalet sjunker till 0, så schemaläggs servicepack för inaktive ring och den tid efter vilken det kommer att inaktive ras är **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

För t. ex. är en minskning av vad som händer när T och servicepack har schemalagts för inaktive ring vid 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**). Om den här tiden är värd för hämtar en IncrementUsage innebär det att inaktive ringen avbryts.

> [!NOTE]
>Så vad innebär dessa konfigurationer i princip: **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: den tid som ett servicepack har fått på att vara värd för en annan replik när den har varit en replik. 
**DeactivationScanInterval**: den minsta tid som har givits till servicepack som värd för en replik om den aldrig har haft någon replik, d.v.s. om det inte används.
>

### <a name="ctrlc"></a>Ctrl+C
När ett servicepack överför **DeactivationGraceInterval** / -**ExclusiveModeDeactivationGraceInterval** och fortfarande inte är värd för en replik, kan inaktive ringen inte avbrytas. CodePackage utfärdas en CTRL + C-hanterare som innebär att det nu måste gå igenom den inaktiverade pipelinen för att ta processen nedåt. Under den här tiden kommer det inte att gå att komma åt en ny replik för samma ServicePack eftersom det inte går att övergå från inaktive ringen till aktivering.

## <a name="cluster-configs"></a>Kluster konfiguration

Configs med standardvärden som påverkar aktiverings-decativation.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: standard 1. Tröskelvärdet för antalet haverier efter vilken RM (FailoverManager) meddelas om att inaktivera tjänst typen på noden och testa en annan nod för placering.
**ServiceTypeDisableGraceInterval**: standard 30 SEK. Tidsintervall efter vilket tjänst typen kan inaktive ras.
**ServiceTypeRegistrationTimeout**: standard 300 SEK. Tids gränsen för ServiceType som ska registreras för Service Fabric.

### <a name="activation"></a>Aktivering
**ActivationRetryBackoffInterval**: standard 10 SEK. Backoff-intervall vid varje aktiverings haveri.
**ActivationMaxFailureCount**: standard 20. Det högsta antalet för vilka systemet kommer att försöka aktivera igen innan det upprättas. 
**ActivationRetryBackoffExponentiationBase**: standard 1,5.
**ActivationMaxRetryInterval**: standard 3600 SEK. Max-off för aktivering vid haverier.
**CodePackageContinuousExitFailureResetInterval**: standard 300 SEK. Tids gränsen för att återställa det kontinuerliga avslutnings antalet för CodePackage.

### <a name="download"></a>Ladda ned
**DeploymentRetryBackoffInterval**: standard 10. Intervall för distributions problem.
**DeploymentMaxRetryInterval**: standard 3600 SEK. Maximal säkerhets kopiering för distribution vid haverier.
**DeploymentMaxFailureCount**: standard 20. Ett nytt försök att utföra program distributionen kommer att göras för DeploymentMaxFailureCount gånger innan distributionen av programmet på noden avbryts.

### <a name="deactivation"></a>Inaktive ring
**DeactivationScanInterval**: standard 600 SEK. Minsta tid för ServicePack som ska vara värd för en replik om den aldrig har haft någon replik, dvs. om det inte används.
**DeactivationGraceInterval**: standard 60 SEK. Den tid som ett ServicePack har fått på att vara värd för en annan replik när det har varit en replik i händelse av en **delad** process modell.
**ExclusiveModeDeactivationGraceInterval**: standard 1 s. Den tid som ett ServicePack har fått på att vara värd för en annan replik när det har varit värd för en replik i händelse av en **exklusiv** process modell.

## <a name="next-steps"></a>Nästa steg
[Paketera ett program][a3] och Förbered det för distribution.

[Distribuera och ta bort program][a4]. Den här artikeln beskriver hur du använder PowerShell för att hantera program instanser.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
