---
title: Hot identifiering för data tjänster i Azure Security Center | Microsoft Docs
description: I det här avsnittet presenteras Data Services-aviseringar som är tillgängliga i Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: 1cafd8a3c766e57aed67634d7da8498c9a6ee120
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295821"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Hot identifiering för data tjänster i Azure Security Center

 Security Center analyserar loggarna för data lagrings tjänster och utlöser aviseringar när ett hot upptäcks för dina data resurser. I det här avsnittet visas en lista över de aviseringar som Security Center genererar för följande tjänster:

* [Azure SQL Database och SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)

## Azure SQL Database och SQL Data Warehouse<a name="data-sql"></a>

SQL hot identifiering identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Security Center analyserar SQL-gransknings loggarna och körs internt i SQL-motorn.

|Varning|Beskrivning|
|---|---|
|**Sårbarhet för SQL-inmatning**|Ett program har genererat en felaktig SQL-instruktion i databasen. Detta kan tyda på en möjlig sårbarhet vid SQL-injektering. Det finns två möjliga orsaker till att en felaktig instruktion genereras: Ett fel i program koden konstruerade den felaktiga SQL-instruktionen. Eller, program kod eller lagrade procedurer har inte sanerat användarindata vid konstruktion av den felaktiga SQL-instruktionen, som kan utnyttjas för SQL-inmatning.|
|**Potentiell SQL-inmatning**|En aktiv sårbarhet har inträffat mot ett identifierat program som är sårbart för SQL-inmatning. Det innebär att en angripare försöker mata in skadliga SQL-uttryck med den sårbara program koden eller lagrade procedurer.|
|**Åtkomst från ovanlig plats**|Åtkomst mönstret till SQL Server har ändrats, där någon har loggat in på SQL-servern från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).|
|**Åtkomst från okända huvud konton**|Åtkomst mönstret har ändrats till SQL Server – någon har loggat in på SQL-servern med en ovanlig huvud server (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).|
|**Åtkomst från ett potentiellt skadligt program**|Ett potentiellt skadligt program har använts för att komma åt databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.|
|**Brute Force SQL-autentiseringsuppgifter**|Ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter har inträffat. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.|

Mer information om varningar om SQL-hotet finns i[Azure SQL Database hot identifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)och i avsnittet om hot identifierings aviseringar. Se också [hur Azure Security Center visar en cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) för att se ett exempel på hur Security Center använde skadlig SQL-aktivitets identifiering för att identifiera ett angrepp.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Avancerat skydd för Azure Storage är för närvarande endast tillgängligt för Blob Storage. 

Med Advanced Threat Protection för Azure Storage får du en ytterligare nivå med säkerhetsinsikter som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Det här skydds lagret gör att du kan åtgärda hot utan att du behöver vara säkerhets expert och hantera säkerhets övervaknings system.

Security Center analyserar diagnostiska loggar med Läs-, skriv-och borttagnings begär anden till Blob Storage för att identifiera hot och den utlöser aviseringar när avvikelser i aktivitet inträffar. Mer information finns i [konfigurera Lagringsanalys loggning](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) för mer information.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Ovanlig plats åtkomst avvikelse**|Analys av exempel på nätverks trafik upptäckte avvikande RDP-kommunikation (utgående Remote Desktop Protocol) som kommer från en resurs i distributionen. Den här aktiviteten betraktas som onormal för den här miljön och kan tyda på att din resurs har komprometterats och nu används för att stoppa extern RDP-slutpunkt. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Åtkomst avvikelse för program**|Indikerar att ett ovanligt program har åtkomst till det här lagrings kontot. En möjlig orsak är att en angripare har åtkomst till ditt lagrings konto med hjälp av ett nytt program.|
|**Avvikelse för anonym åtkomst**|Anger att åtkomst mönstret har ändrats till ett lagrings konto. Till exempel har kontot åtkomst till anonymt (utan autentisering), vilket är oväntat jämfört med det senaste åtkomst mönstret för det här kontot. En möjlig orsak är att en angripare har utnyttjat offentlig Läs behörighet till en behållare som innehåller BLOB (s)-lagring.|
|**Data exfiltrering avvikelse**|Anger att en ovanligt stor mängd data har extraherats jämfört med senaste aktivitet på den här lagrings behållaren. En möjlig orsak är att en angripare har extraherat en stor mängd data från en behållare som innehåller BLOB (s)-lagring.|
|**Oväntad borttagnings avvikelse**|Indikerar att en eller flera oväntade borttagnings åtgärder har inträffat i ett lagrings konto, jämfört med senaste aktivitet på det här kontot. En möjlig orsak är att en angripare har tagit bort data från ditt lagrings konto.|
|**Ladda upp Azure Cloud Service-paket**|Indikerar att ett Azure Cloud Service-paket (. cspkg-fil) har laddats upp till ett lagrings konto på ett onormalt sätt, jämfört med senaste aktivitet på det här kontot. En möjlig orsak är att en angripare har förberett att distribuera skadlig kod från ditt lagrings konto till en Azure-moln tjänst.|
|**Behörighet till åtkomst avvikelse**|Anger att åtkomst behörigheterna för den här lagrings behållaren har ändrats på ett onormalt sätt. Möjlig orsak är att en angripare har ändrat behållar behörigheter för att begränsa dess säkerhets position eller få beständighet.|
|**Avvikelse vid gransknings åtkomst**|Anger att åtkomst behörigheterna för ett lagrings konto har inspekterats på ett onormalt sätt, jämfört med senaste aktivitet på det här kontot. En möjlig orsak är att en angripare har genomfört rekognosering för framtida angrepp.|
|**Avvikelse för data utforskning**|Anger att blobbar eller behållare i ett lagrings konto har räknats upp på ett onormalt sätt, jämfört med senaste aktivitet på det här kontot. En möjlig orsak är att en angripare har genomfört rekognosering för framtida angrepp.|

>[!NOTE]
>Avancerat skydd för Azure Storage är för närvarande inte tillgängligt i moln regioner i Azure myndigheter och i vår suveräna region.

Mer information om aviseringar för lagring finns i artikeln [Avancerat skydd mot Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) och i avsnittet skydds aviseringar.
