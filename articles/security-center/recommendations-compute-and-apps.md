---
title: Azure Security Center rekommendationer för datorer & appar
description: Azure Security Center säkerhets rekommendationer som hjälper dig att skydda dina virtuella datorer, datorer, webbappar och App Service-miljöer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782169"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Beräknings-och program rekommendationer – referens guide

Den här artikeln innehåller en fullständig lista över de rekommendationer som du kan se i Azure Security Center om följande resurs typer:

* Virtuella datorer och datorer
* VM Scale Sets
* Cloud Services
* App Services
* Containers
* Beräknings resurser

En förklaring av hur du hittar dessa och hur du löser dem finns [här](security-center-virtual-machine-protection.md).

## Beräknings-och program rekommendationer<a name="compute-and-app-recs"></a>
|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|App Service|20|Webb program bör endast vara tillgängliga via HTTPS|Begränsa åtkomsten till webb program endast över HTTPS.|
|App Service|20|Funktionsapp bör endast vara tillgängligt via HTTPS|Begränsa åtkomsten till funktions appar endast över HTTPS.|
|App Service|5|Diagnostikloggar i App Services ska vara aktive rad|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|App Service|10|Fjärrfelsökning bör inaktive ras för webb program|Inaktivera fel sökning för webb program om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|App Service|10|Fjärrfelsökning bör inaktive ras för Function-program|Inaktivera fel sökning för Funktionsapp om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp.|
|App Service|10|Tillåt inte alla (' * ') resurser för att få åtkomst till ditt program| Tillåt inte att WEBSITE_LOAD_CERTIFICATES-parametern anges till "". Om du anger parametern till, innebär det att alla certifikat har lästs in i dina webb programs personliga certifikat arkiv. Detta kan leda till missbruk av principen om minsta behörighet eftersom det är osannolikt att platsen behöver åtkomst till alla certifikat vid körning.|
|App Service|20|CORS bör inte tillåta alla resurser åtkomst till dina webb program|Tillåt endast domäner som krävs för att interagera med ditt webb program. Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt ditt webb program.|
|App Service|20|CORS bör inte tillåta alla resurser att komma åt din Funktionsapp| Tillåt endast domäner som krävs för att interagera med ditt funktions program. Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt ditt funktions program.|
|Beräknings resurser (batch)|1|Mått varnings regler ska konfigureras för batch-konton|Konfigurera mått varnings regler för batch-kontot och aktivera mått poolen ta bort Slutför händelser och ta bort start händelser för poolen|
|Beräknings resurser (Service Fabric)|10|Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering|Genomför endast klientautentisering via Azure Active Directory i Service Fabric.|
|Beräknings resurser (Automation-konto)|5|Variabler för Automation-konton ska vara krypterade|Aktivera kryptering av variabel till gångar för Automation-kontot vid lagring av känsliga data.|
|Beräknings resurser (Sök)|5|Granska aktivering av diagnostikloggar för Sök tjänster|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Service Bus)|5|Diagnostikloggar i Service Bus ska vara aktive rad|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Stream Analytics)|5|Diagnostikloggar i Azure Stream Analytics ska vara aktive rad|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (batch)|5|Aktivera diagnostikloggar i batch-konton|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Event Hub)|5|Diagnostikloggar i Händelsehubben måste vara aktive rad|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Logic Apps)|5|Aktivera diagnostikloggar i Logic Apps|Aktivera loggar och behåll dem på ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |
|Beräknings resurser (Service Fabric)|15|Ange egenskapen ClusterProtectionLevel till EncryptAndSign i Service Fabric|Service Fabric tillhandahåller tre skydds nivåer (ingen, sign och EncryptAndSign) för nod-till-nod-kommunikation med ett primärt kluster certifikat. Ange skydds nivå för att säkerställa att alla nod-till-nod-meddelanden är krypterade och digitalt signerade. |
|Beräknings resurser (Service Bus)|1|Ta bort alla auktoriseringsregler utom RootManageSharedAccessKey från Service Bus namnrymd |Service Bus-klienter bör inte använda en åtkomst princip för namn områdes nivå som ger åtkomst till alla köer och ämnen i ett namn område. Om du vill justera med minsta behörighets säkerhets modell bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten.|
|Beräknings resurser (Event Hub)|1|Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från Event Hub-namnområdet|Event Hub-klienter bör inte använda en åtkomst princip för namn områdes nivå som ger åtkomst till alla köer och ämnen i ett namn område. Om du vill justera med minsta behörighets säkerhets modell bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten.|
|Beräknings resurser (Event Hub)|5|Auktoriseringsregler i Event Hub-entiteten måste definieras|Granska auktoriseringsregler i entiteten Event Hub för att bevilja åtkomst med lägsta privilegier.|
|Dator|50|Installera övervaknings agenten på dina datorer|Installera övervaknings agenten för att aktivera data insamling, uppdaterings genomsökning, bas linje genomsökning och Endpoint Protection på varje dator.|
|Dator|50|Aktivera automatisk etablering och data insamling för dina prenumerationer |Aktivera automatisk etablering och data insamling för datorer i dina prenumerationer för att aktivera insamling av data, uppdaterings genomsökning, bas linje genomsökning och Endpoint Protection på varje dator som läggs till i dina prenumerationer.|
|Dator|40|Lös problem med övervaknings agentens hälso tillstånd på dina datorer|Lös problem med övervaknings agenten på datorerna genom att följa anvisningarna i fel söknings guiden för fullständig Security Center skydd| 
|Dator|40|Lös problem med hälso tillstånd för slut punkts skydd på dina datorer|Lös problem med övervaknings agenten på datorerna genom att följa anvisningarna i fel söknings guiden för fullständig Security Center skydd.|
|Dator|40|Felsök saknade Sök data på dina datorer|Felsök saknade Sök data på virtuella datorer och datorer. Genomsöknings data som saknas på dina datorer resulterar i att säkerhets utvärderingen saknas, till exempel uppdaterings genomsökning, bas linje sökning och en slut punkts skydds lösning som saknas.|
|Dator|40|System uppdateringar bör installeras på dina datorer|Installera system säkerhet och viktiga uppdateringar som saknas för att skydda dina virtuella Windows-och Linux-datorer och datorer
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en brand vägg för webbaserade program (WAF) för att skydda dina webb program. |
|Dator|40|Uppdatera OS-versionen för dina moln tjänst roller|Uppdatera operativ system versionen för dina moln tjänst roller till den senaste versionen som är tillgänglig för din OS-familj.|
|Dator|35|Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas|Åtgärda sårbarheter i säkerhets konfiguration på dina datorer för att skydda dem från attacker.|
|Dator|35|Åtgärda sårbarheter i säkerhets konfiguration på dina behållare|Åtgärda sårbarheter i säkerhets konfiguration på datorer med Docker installerat för att skydda dem från attacker.|
|Dator|25|Aktivera anpassningsbara program kontroller|Aktivera program kontroll för att styra vilka program som kan köras på dina virtuella datorer som finns i Azure. På så sätt kan dina virtuella datorer öka mot skadlig kod. Security Center använder Machine Learning för att analysera de program som körs på varje virtuell dator och hjälper dig att tillämpa Tillåt-regler med hjälp av den här intelligensen. Den här funktionen fören klar processen att konfigurera och underhålla regler för att tillåta program.|
|Dator|20|Installera Endpoint Protection-lösning på dina datorer|Installera en Endpoint Protection-lösning på dina virtuella datorer för att skydda dem mot hot och sårbarheter.|
|Dator|20|Starta om datorerna för att tillämpa system uppdateringar|Starta om datorerna för att tillämpa system uppdateringar och skydda datorn från sårbarheter.|
|Dator|15|Disk kryptering bör tillämpas på virtuella datorer|Kryptera dina virtuella dator diskar med Azure Disk Encryption både för virtuella Windows-och Linux-datorer. Azure Disk Encryption (ADE) använder branschens standard BitLocker-funktion i Windows och funktionen DM-crypt i Linux för att tillhandahålla operativ system-och data disk kryptering för att skydda och skydda dina data och hjälpa dig att uppfylla organisationens säkerhet och efterlevnad åtaganden i kund Azure Key Vault. När ditt krav på efterlevnad och säkerhet kräver att du krypterar data slut för att sluta använda dina krypterings nycklar, inklusive kryptering av den tillfälliga (lokalt anslutna temporära) disken, använder du Azure Disk Encryption. Som standard är Managed Disks som standard krypterade i vila som standard med hjälp av Azure Storage tjänst kryptering där krypterings nycklarna är Microsoft-hanterade nycklar i Azure. Om detta uppfyller dina krav på efterlevnad och säkerhet kan du utnyttja den förvalda hanterade disk krypteringen för att uppfylla dina krav.|
|Dator|30|Installera en lösning för sårbarhets bedömning på dina virtuella datorer|Installera en lösning för sårbarhets bedömning på dina virtuella datorer|
|Dator|15|Lägg till en brandvägg för webbappar| Distribuera en brand vägg för webbaserade program (WAF) för att skydda dina webb program. |
|Dator|30|Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning|Virtuella datorer som en sårbarhets bedömning från tredje part har distribuerats till utvärderas kontinuerligt mot program-och OS-sårbarheter. När sådana sårbarheter hittas är dessa tillgängliga för mer information som en del av rekommendationen.|
|Dator|30|Installera en lösning för sårbarhets bedömning på dina virtuella datorer|Installera en lösning för sårbarhets bedömning på dina virtuella datorer|
|Dator|1|Virtuella datorer ska migreras till nya AzureRM-resurser|Använd Azure Resource Manager för dina virtuella datorer för att ge säkerhets förbättringar som: starkare åtkomst kontroll (RBAC), bättre granskning, Resource Manager-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till nyckel valv för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resurs grupper för enklare säkerhets hantering. |
|Dator|30|Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning|Virtuella datorer som en sårbarhets bedömning från tredje part har distribuerats till utvärderas kontinuerligt mot program-och OS-sårbarheter. När sådana sårbarheter hittas är dessa tillgängliga för mer information som en del av rekommendationen.|
|Skaluppsättning för virtuella datorer |4|Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad|Aktivera loggar och behåll dem i upp till ett år. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte. Detta är användbart när en säkerhets incident inträffar eller nätverket komprometteras.|
|Skaluppsättning för virtuella datorer|35|Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas|Åtgärda sårbarheter i säkerhets konfiguration på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp. |
|Skaluppsättning för virtuella datorer|5|Åtgärda problem med hälso tillstånd för Endpoint Protection på virtuella datorers skalnings uppsättningar|Åtgärda problem med slut punkts skydd på den virtuella datorns skalnings uppsättningar för att skydda dem mot hot och sårbarheter. |
|Skaluppsättning för virtuella datorer|10|Endpoint Protection bör installeras på virtuella datorer|Installera en Endpoint Protection-lösning på den virtuella datorns skalnings uppsättningar för att skydda dem mot hot och sårbarheter. |
|Skaluppsättning för virtuella datorer|40|System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras|Installera system säkerhet och viktiga uppdateringar som saknas för att skydda dina skalnings uppsättningar för virtuella Windows-och Linux-datorer. |
|


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande avsnitt:

* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)
