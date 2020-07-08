---
title: Åtgärda rekommendationer i Azure Security Center | Microsoft Docs
description: Den här artikeln förklarar hur du kan åtgärda rekommendationer i Azure Security Center för att skydda dina resurser och följa säkerhets principer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9beb617ed8626b1fda1c9db98d626ca70ee01755
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042925"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Reparationsrekommendationer i Azure Security Center

Rekommendationer ger förslag på hur du bättre skyddar resurserna. Du implementerar en rekommendation genom att följa de åtgärds steg som beskrivs i rekommendationen.

## <a name="remediation-steps"></a>Reparations steg<a name="remediation-steps"></a>

När du har granskat alla rekommendationer, Bestäm vilken av dem som ska åtgärdas först. Vi rekommenderar att du använder en [säker Poäng påverkan](security-center-recommendations.md#monitor-recommendations) för att prioritera vad du ska göra först.

1. Klicka på rekommendationen i listan.

1. Följ anvisningarna i avsnittet **reparations steg** . Varje rekommendation har en egen uppsättning instruktioner. Följande skärm bild visar åtgärds steg för att konfigurera program att bara tillåta trafik över HTTPS.

    ![Rekommendations information](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. När åtgärden har slutförts visas ett meddelande om att reparationen lyckades.

## <a name="quick-fix-remediation"></a>Snabb korrigerings reparation<a name="one-click"></a>

Med snabb korrigering kan du snabbt åtgärda en rekommendation om flera resurser. Den är endast tillgänglig för vissa rekommendationer. Snabb korrigering fören klar reparationen och gör att du snabbt kan öka dina säkra poäng och förbättra din miljös säkerhet.

Implementera snabb korrigerings reparation:

1. I listan över rekommendationer som har **snabb korrigering!** etikett klickar du på rekommendationen.

    [![Välj snabb korrigering!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. På fliken **felaktiga resurser** väljer du de resurser som du vill implementera rekommendationen på och klickar på **åtgärda**.

    > [!NOTE]
    > Några av de listade resurserna kan vara inaktiverade eftersom du inte har rätt behörighet för att ändra dem.

1. Läs korrigerings informationen och konsekvenserna i bekräftelse rutan.

    ![Snabb korrigering](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Konsekvenserna visas i den grå rutan i fönstret **Åtgärds resurser** som öppnas när du klickar på **åtgärda**. De listar vilka ändringar som sker när du fortsätter med snabb korrigerings reparationen.

1. Infoga relevanta parametrar vid behov och godkänn reparationen.

    > [!NOTE]
    > Det kan ta flera minuter efter att reparationen har slutförts för att se resurserna på fliken **felfria resurser** . Om du vill visa reparations åtgärderna kontrollerar du [aktivitets loggen](#activity-log).

1. När åtgärden har slutförts visas ett meddelande om att reparationen lyckades.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Snabb korrigering av reparations loggning i aktivitets loggen<a name="activity-log"></a>

Reparations åtgärden använder en mall för distribution av mallar eller REST-PATCH-API-anrop för att tillämpa konfigurationen på resursen. De här åtgärderna loggas i [aktivitets loggen i Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Rekommendationer med snabb korrigerings åtgärder

|Rekommendation|Indirekt|
|---|---|
|Granskning på SQL Database ska vara aktive rad|Med den här åtgärden aktive ras SQL-granskning på dessa servrar och deras databaser. <br>**Obs**: <ul><li>För varje region i den valda SQL Database skapas och delas ett lagrings konto för att spara gransknings loggar och delas av alla servrar i den regionen.</li><li>För att säkerställa korrekt granskning ska du inte ta bort eller byta namn på resurs gruppen eller lagrings kontona.</li></ul>|
|Avancerad data säkerhet ska vara aktiverat på SQL-hanterad instans|Med den här åtgärden aktive ras SQL Advanced Data Security (ADS) på de valda SQL-hanterade instanserna. <br>**Obs**: <ul><li>För varje region och resurs grupp för den valda SQL-hanterade instansen skapas och delas ett lagrings konto för att spara Sök Resultat och delas av alla instanser i den regionen.</li><li> ADS debiteras med $15 per SQL-hanterad instans.</li></ul>|
|Sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans|Den här åtgärden aktiverar SQL sårbarhets bedömning på den valda SQL-hanterade instansen. <br>**Obs**:<ul><li>SQL sårbarhet Assessment är en del av SQL-paketet för avancerad data säkerhet (ADS). Om ADS inte redan är aktiverat aktive ras den automatiskt på den hanterade instansen.</li><li>För varje region och resurs grupp för den valda SQL-hanterade instansen skapas och delas ett lagrings konto för lagring av Sök resultat som delas av alla instanser i den regionen.</li><li>ADS debiteras med $15 per SQL Database.</li></ul>||
|Avancerad data säkerhet ska vara aktiverat på din SQL Database|Med den här åtgärden aktive ras avancerad data säkerhet (ADS) på de valda servrarna och databaserna. <br>**Obs**:<ul><li>För varje region och resurs grupp för den valda SQL Database skapas och delas ett lagrings konto för lagring av Sök resultat som delas av alla servrar i regionen. <</li><li>ADS debiteras med $15 per SQL Database.</li></ul>||
|Sårbarhets bedömning ska aktive ras på SQL Database|Den här åtgärden aktiverar utvärdering av SQL-säkerhetsproblem på de valda servrarna och deras databaser. <br>**Obs**:<ul><li>SQL sårbarhet Assessment är en del av SQL-paketet för avancerad data säkerhet (ADS). Om ADS inte redan är aktiverat aktive ras den automatiskt på SQL Database.</li><li>För varje region och resurs grupp för den valda SQL Database skapas och delas ett lagrings konto för lagring av Sök resultat som delas av alla instanser i den regionen.</li><li>ADS debiteras med $15 per SQL Database.</li></ul>||
|Transparent data kryptering på SQL Database ska vara aktive rad|Med den här åtgärden kan SQL Database transparent datakryptering (TDE) på de valda databaserna. <br>**Obs**: som standard används service-hanterade TDE-nycklar.
|Säker överföring till lagringskonton ska vara aktiverat|Den här åtgärden uppdaterar lagrings kontots säkerhet för att endast tillåta begär Anden via säkra anslutningar. (HTTPS). <br>**Obs**:<ul><li>Alla begär Anden som använder HTTP kommer att avvisas.</li><li>När du använder Azure Files-tjänsten Miss söker inte anslutningen utan kryptering, inklusive scenarier som använder SMB 2,1, SMB 3,0 utan kryptering och vissa varianter av Linux SMB-klienten. Läs mer.</li></ul>|
|Webb program bör endast vara tillgängliga via HTTPS|Den här åtgärden dirigerar om all trafik från HTTP till HTTPS, på de valda resurserna. <br>**Obs**:<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett "sekretess fel". Användare som har en anpassad domän måste kontrol lera att de har konfigurerat ett SSL-certifikat.</li><li>Se till att paket-och webb programs brand väggar skyddar app service, Tillåt vidarebefordring av HTTPS-sessioner.</li></ul>|
|Funktionsapp bör endast vara tillgängligt via HTTPS|Den här åtgärden dirigerar om all trafik från HTTP till HTTPS, på de valda resurserna. <br>**Obs**:<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett "sekretess fel". Användare som har en anpassad domän måste kontrol lera att de har konfigurerat ett SSL-certifikat.</li><li>Se till att paket-och webb programs brand väggar skyddar app service, Tillåt vidarebefordring av HTTPS-sessioner.</li></ul>|
|API-appen bör bara vara tillgänglig via HTTPS|Den här åtgärden dirigerar om all trafik från HTTP till HTTPS, på de valda resurserna. <br>**Obs**:<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett "sekretess fel". Användare som har en anpassad domän måste kontrol lera att de har konfigurerat ett SSL-certifikat.</li><li>Se till att paket-och webb programs brand väggar skyddar app service, Tillåt vidarebefordring av HTTPS-sessioner.</li></ul>|
|Fjärrfelsökning bör inaktive ras för webb program|Den här åtgärden inaktiverar fjärrfelsökning.|
|Fjärrfelsökning bör inaktive ras för Funktionsapp|Den här åtgärden inaktiverar fjärrfelsökning.|
|Fjärrfelsökning bör inaktive ras för API-appen|Den här åtgärden inaktiverar fjärrfelsökning.|
|CORS bör inte tillåta alla resurser åtkomst till ditt webb program|Den här åtgärden blockerar andra domäner från att komma åt ditt webb program. Om du vill tillåta vissa domäner anger du dem i fältet tillåtna ursprung (avgränsade med kommatecken). <br>**Obs!** om fältet lämnas tomt blockeras alla kors ursprungs anrop. "param-fält Rubrik:" tillåtna ursprung "|
|CORS bör inte tillåta alla resurser att komma åt din Funktionsapp|Den här åtgärden blockerar andra domäner från att komma åt ditt funktions program. Om du vill tillåta vissa domäner anger du dem i fältet tillåtna ursprung (avgränsade med kommatecken). <br>**Obs!** om fältet lämnas tomt blockeras alla kors ursprungs anrop. "param-fält Rubrik:" tillåtna ursprung "|
|CORS bör inte tillåta alla resurser åtkomst till din API-app|Den här åtgärden blockerar andra domäner från att komma åt ditt API-program. Om du vill tillåta vissa domäner anger du dem i fältet tillåtna ursprung (avgränsade med kommatecken). <br>**Obs!** om fältet lämnas tomt blockeras alla kors ursprungs anrop. "param-fält Rubrik:" tillåtna ursprung "|
|Övervaknings agenten ska vara aktive rad på dina virtuella datorer|Den här åtgärden installerar en övervaknings agent på de valda virtuella datorerna. Välj en arbets yta som agenten ska rapportera till.<ul><li>Om din uppdaterings princip är inställd på automatisk, kommer den att distribueras på nya befintliga instanser.</li><li>Om din uppdaterings princip är inställd på manuell och du vill installera agenten på befintliga instanser markerar du kryss rutan. [Läs mer](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Diagnostikloggar i Key Vault ska vara aktive rad|Den här åtgärden aktiverar diagnostikloggar på nyckel valv. Diagnostikloggar och mått sparas på den valda arbets ytan.|
|Diagnostikloggar i Service Bus ska vara aktive rad|Den här åtgärden aktiverar diagnostikloggar på Service Bus. Diagnostikloggar och mått sparas på den valda arbets ytan.|

## <a name="next-steps"></a>Nästa steg

I det här dokumentet visade du hur du kan åtgärda rekommendationerna i Security Center. Mer information om Security Center finns i följande avsnitt:

* [Ange säkerhets principer i Azure Security Center](tutorial-security-policy.md) – lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
