---
title: Hot identifiering för data tjänster i Azure Security Center | Microsoft Docs
description: I det här avsnittet presenteras Data Services-aviseringar som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 763349c11a7777bfc807a60e1806c3bb4010245e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202577"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Hot identifiering för data tjänster i Azure Security Center

 Azure Security Center analyserar loggarna för data lagrings tjänster och utlöser aviseringar när ett hot upptäcks för dina data resurser. I det här avsnittet visas en lista över de aviseringar som Security Center genererar för följande tjänster:

* [Azure SQL Database och Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database och SQL Data Warehouse<a name="data-sql"></a>

SQL hot identifiering identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Security Center analyserar SQL-gransknings loggarna och körs internt i SQL-motorn.

|Varning|Beskrivning|
|---|---|
|**Sårbarhet för SQL-inmatning**|Ett program har genererat en felaktig SQL-instruktion i databasen. Detta kan tyda på en möjlig sårbarhet vid SQL-injektering. Det finns två möjliga orsaker till en felaktig instruktion. Ett fel i program koden kan ha konstruerat den felaktiga SQL-instruktionen. Eller, program kod eller lagrade procedurer har inte sanerat användarindata vid konstruktion av den felaktiga SQL-instruktionen, som kan utnyttjas för SQL-inmatning.|
|**Potentiell SQL-inmatning**|En aktiv sårbarhet har inträffat mot ett identifierat program som är sårbart för SQL-inmatning. Det innebär att en angripare försöker mata in skadliga SQL-uttryck med hjälp av den sårbara program koden eller lagrade procedurer.|
|**Åtkomst från ovanlig plats**|Åtkomst mönstret har ändrats till SQL Server, där någon har loggat in på servern från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (en tidigare anställd eller extern angripare).|
|**Åtkomst från okända huvud konton**|Åtkomst mönstret ändrades till SQL Server. Någon har loggat in på servern med hjälp av en ovanlig huvud server (användare). I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (en tidigare anställd eller extern angripare).|
|**Åtkomst från ett potentiellt skadligt program**|Ett potentiellt skadligt program har använts för att komma åt databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack som använder vanliga verktyg.|
|**Brute Force SQL-autentiseringsuppgifter**|Ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter har inträffat. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en brute force-attack.|

Mer information om varningar om SQL-hotet finns [Azure SQL Database hot identifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview). Läs särskilt avsnittet om aviseringar om hot identifiering. Se också [hur Azure Security Center visar en cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) för att se ett exempel på hur Security Center använde skadlig SQL-aktivitets identifiering för att identifiera ett angrepp.

## Lagrings<a name="azure-storage"></a>

>[!NOTE]
> Avancerat skydd för lagring är för närvarande endast tillgängligt för Blob Storage.

Avancerat skydd för lagring ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Det här skydds lagret gör att du kan åtgärda hot utan att du behöver vara säkerhets expert och hantera säkerhets övervaknings system.

Security Center analyserar diagnostiska loggar med Läs-, skriv-och borttagnings begär anden till Blob Storage för att identifiera hot och den utlöser aviseringar när avvikelser i aktivitet inträffar. Mer information finns i [konfigurera Lagringsanalys loggning](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Ovanlig plats åtkomst avvikelse**|Analys av exempel på nätverks trafik upptäckte avvikande RDP-kommunikation (utgående Remote Desktop Protocol) som härstammar från en resurs i distributionen. Den här aktiviteten betraktas som onormal för den här miljön. Det kan betyda att din resurs har komprometterats och att den nu används för att bryta Force-angrepp till en extern RDP-slutpunkt. Observera att den här typen av aktivitet kan orsaka att din IP-adress flaggas som skadlig av externa entiteter.|
|**Åtkomst avvikelse för program**|Indikerar att ett ovanligt program har åtkomst till det här lagrings kontot. En möjlig orsak är att en angripare har åtkomst till ditt lagrings konto med hjälp av ett nytt program.|
|**Avvikelse för anonym åtkomst**|Anger att åtkomst mönstret har ändrats till ett lagrings konto. Till exempel har kontot åtkomst till anonymt (utan autentisering), vilket är oväntat jämfört med det senaste åtkomst mönstret för det här kontot. En möjlig orsak är att en angripare har utnyttjat offentlig Läs behörighet för en behållare som innehåller Blob Storage.|
|**Tor avvikelse**|Anger att det här kontot har öppnats från en IP-adress som kallas en aktiv stängningsmodul för Tor (en maskera proxy). Allvarlighets graden för den här aviseringen tar hänsyn till den autentiseringstyp som används (om det finns någon) och om detta är första fallet för sådan åtkomst. Potentiella orsaker kan vara en angripare som har åtkomst till ditt lagrings konto med hjälp av Tor eller en legitim användare som har åtkomst till ditt lagrings konto med hjälp av Tor.|
|**Data exfiltrering avvikelse**|Anger att en ovanligt stor mängd data har extraherats jämfört med senaste aktivitet på den här lagrings behållaren. En möjlig orsak är att en angripare har extraherat en stor mängd data från en behållare som innehåller blob-lagring.|
|**Oväntad borttagnings avvikelse**|Indikerar att en eller flera oväntade borttagnings åtgärder har inträffat i ett lagrings konto, jämfört med senaste aktivitet på det här kontot. En möjlig orsak är att en angripare har tagit bort data från ditt lagrings konto.|
|**Ladda upp Azure Cloud Services-paket**|Indikerar att ett Azure Cloud Services-paket (. cspkg-fil) har överförts till ett lagrings konto på ett onormalt sätt, jämfört med senaste aktivitet på det här kontot. En möjlig orsak är att en angripare har förberett att distribuera skadlig kod från ditt lagrings konto till en Azure-moln tjänst.|
|**Behörighet till åtkomst avvikelse**|Anger att åtkomst behörigheterna för den här lagrings behållaren har ändrats på ett onormalt sätt. En möjlig orsak är att en angripare har ändrat behållar behörigheter för att begränsa dess säkerhets position eller få beständighet.|
|**Avvikelse vid gransknings åtkomst**|Anger att åtkomst behörigheterna för ett lagrings konto har inspekterats på ett onormalt sätt, jämfört med senaste aktivitet på det här kontot. En möjlig orsak är att en angripare har genomfört rekognosering för framtida angrepp.|
|**Avvikelse för data utforskning**|Anger att blobbar eller behållare i ett lagrings konto har räknats upp på ett onormalt sätt, jämfört med senaste aktivitet på det här kontot. En möjlig orsak är att en angripare har genomfört rekognosering för framtida angrepp.|

>[!NOTE]
>Avancerat skydd för lagring är för närvarande inte tillgängligt i Azure myndigheter och suveräna moln regioner.

Mer information om aviseringar för lagring finns i [Avancerat skydd mot Azure Storage](../storage/common/storage-advanced-threat-protection.md). Läs särskilt avsnittet "skydds aviseringar".

## Azure Cosmos DB<a name="cosmos-db"></a>

Följande aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos DB-konton:

|Varning|Beskrivning|
|---|---|
|**Åtkomst från ovanlig plats**|Anger att åtkomst mönstret har ändrats till ett Azure Cosmos DB-konto. Någon har använt det här kontot från en okänd IP-adress jämfört med senaste aktivitet. Antingen har en angripare åtkomst till kontot eller så har en legitim användare åtkomst till den från en ny och ovanlig geografisk plats. Ett exempel på den senare är fjärrunderhåll från ett nytt program eller en ny utvecklare.|
|**Ovanliga data exfiltrering**|Indikerar att det har ändrats ett data extraherings mönster från ett Azure Cosmos DB konto. Någon har extraherat en ovanlig mängd data jämfört med senaste aktivitet. En angripare kan ha extraherat en stor mängd data från en Azure Cosmos DB databas (till exempel data exfiltrering eller läckage eller en obehörig överföring av data). Eller så kanske en legitim användare eller ett program har extraherat en ovanlig mängd data från en behållare (till exempel för aktiviteten underhålls säkerhets kopiering).|

Mer information finns i [Avancerat skydd mot Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).