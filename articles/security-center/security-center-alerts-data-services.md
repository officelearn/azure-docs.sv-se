---
title: Hotidentifiering för datatjänster i Azure Security Center | Microsoft Docs
description: Det här avsnittet innehåller de data services-aviseringarna tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626292"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Hotidentifiering för datatjänster i Azure Security Center

 Security Center analyserar loggarna för datalagringstjänster och utlöser varningar när den identifierar ett hot mot dina dataresurser. Det här avsnittet beskrivs de aviseringar som genereras av Security Center för följande tjänster:

* [Azure SQL Database och SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)

## Azure SQL Database och SQL Data Warehouse <a name="data-sql"></a>

SQL threat detection identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försöker komma åt eller utnyttja databaser. Security Center analyserar SQL-granskningsloggar och körs internt i SQL-motor.

|Varning|Beskrivning|
|---|---|
|**Sårbarhet för SQL-inmatning**|Ett program har genererat en felaktig SQL-instruktion i databasen. Detta kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Det finns två möjliga orsaker till att en felaktig instruktion genereras: Ett fel i programkoden konstruerade, den felaktiga SQL-instruktionen. Eller programkod eller lagrade procedurer Rensa inte indata från användaren när den felaktiga SQL-instruktionen, som kan utnyttjas för SQL-inmatning.|
|**Potentiell SQL-inmatning**|Det uppstod en aktiv sårbarhet mot ett identifierade program som är sårbara för SQL-inmatning. Det innebär att en angripare försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.|
|**Åtkomst från ovanlig plats**|Det har skett en ändring i åtkomstmönstret till SQLServer, där någon har loggat in till SQLServer från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).|
|**Åtkomst från okänd huvudnamn**|Det har skett en ändring i åtkomstmönstret till SQLServer - någon har loggat in till SQLServer med ett ovanligt huvudkonto (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).|
|**Åtkomst från ett potentiellt skadligt program**|Ett potentiellt skadligt program har använts för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.|
|**Brute force SQL-autentiseringsuppgifter**|Ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter har inträffat. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.|

Mer information om SQL threat detection aviseringar finns i[Azure SQL Database-hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview), och Läs avsnittet threat identifiering av aviseringar. Se även [hur Azure Security Center hjälper till att visa en Cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) att se ett exempel på hur Security Center används identifiering av skadlig SQL-aktivitet för att upptäcka en attack.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Avancerat skydd för Azure Storage är endast tillgänglig för Blob Storage. 

Med Advanced Threat Protection för Azure Storage får du en ytterligare nivå med säkerhetsinsikter som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Den här skyddslager kan du hantera hot utan att behöva vara säkerhetsexpert och hantera säkerhetssystem för övervakning.

Security Center analyserar diagnostikloggar för Läs-, Skriv- och delete-begäranden till Blob storage att identifiera hot och utlöser varningar när avvikelser i aktivitet inträffar. Mer information finns i så här [konfigurera Storage Analytics loggning](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) för mer information.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Ovanlig plats åtkomst avvikelseidentifiering**|Provade analysen av nätverkstrafik identifierade avvikande utgående Remote Desktop Protocol (RDP)-kommunikation från en resurs i din distribution. Den här aktiviteten anses onormalt för den här miljön och kan indikera att din resurs har komprometterats och nu används nu för råstyrkeattacker mot externa RDP-slutpunkt. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Programmet åtkomst avvikelseidentifiering**|Anger att ett ovanligt program har åtkomst till det här lagringskontot. En möjlig orsak är att en angripare har åtkomst till ditt storage-konto med hjälp av ett nytt program.|
|**Anonym åtkomst avvikelseidentifiering**|Anger att det finns en ändring i åtkomstmönstret för till ett lagringskonto. Till exempel har kontot kommit åt anonymt (utan någon autentisering), vilket är oväntat jämfört med de senaste åtkomstmönstret på det här kontot. En möjlig orsak är att en angripare har utnyttjat offentlig läsbehörighet till en behållare som innehåller blobbar lagring.|
|**Avvikelseidentifiering för Exfiltrering av data**|Anger att ett ovanligt stora mängder data har extraherats jämfört med senaste aktivitet på denna lagringsbehållare. En möjlig orsak är att en angripare har extraherats en stor mängd data från en behållare som innehåller blobbar lagring.|
|**Oväntat delete-avvikelseidentifiering**|Anger att en eller flera oväntade delete-åtgärder har inträffat i ett lagringskonto, jämfört med senaste aktiviteter för det här kontot. En möjlig orsak är att en angripare har tagit bort data från ditt lagringskonto.|
|**Överföra Azure Cloud Service-paket**|Anger att ett Azure Cloud Service-paket (.cspkg-fil) har överförts till ett lagringskonto på ett annorlunda sätt, jämfört med senaste aktiviteter för det här kontot. En möjlig orsak är att en angripare har förbereder distribuera skadlig kod från ditt lagringskonto till ett Azure-molntjänst.|
|**Behörighet för åtkomst-avvikelseidentifiering**|Anger att åtkomstbehörigheterna för den här lagringsbehållaren har ändrats på ett annorlunda sätt. Möjlig orsak är att en angripare har ändrats behörigheter för behållare så här begränsar du sin säkerhetsposition eller få persistence.|
|**Inspektion åtkomst avvikelseidentifiering**|Anger att åtkomstbehörigheterna för ett lagringskonto har kontrollerats på ett annorlunda sätt, jämfört med senaste aktiviteter för det här kontot. En möjlig orsak är att en angripare har utfört rekognosering för ett framtida angrepp.|
|**Data utforskning avvikelseidentifiering**|Anger att BLOB eller behållare i ett lagringskonto har räknats upp på ett onormalt sätt, jämfört med senaste aktiviteter för det här kontot. En möjlig orsak är att en angripare har utfört rekognosering för ett framtida angrepp.|

>[!NOTE]
>Avancerat skydd för Azure Storage finns för närvarande inte i Azure government och nationellt molnregioner.

Mer information om aviseringar för lagring finns i den [Advanced Threat Protection för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) artikel, så Läs avsnittet Protection-aviseringar.
