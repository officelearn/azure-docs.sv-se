---
title: Åtgärda rekommendationer i Azure Security Center | Microsoft-dokument
description: I den här artikeln beskrivs hur du åtgärdar rekommendationer i Azure Security Center för att skydda dina resurser och följa säkerhetsprinciper.
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
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603496"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Reparationsrekommendationer i Azure Security Center

Rekommendationer ger dig förslag på hur du bättre kan säkra dina resurser. Du implementerar en rekommendation genom att följa de åtgärdssteg som anges i rekommendationen.

## <a name="remediation-steps"></a>Åtgärder för reparation<a name="remediation-steps"></a>

Efter att ha granskat alla rekommendationer, besluta vilken som ska åtgärdas först. Vi rekommenderar att du använder [effekten Säker poäng](security-center-recommendations.md#monitor-recommendations) för att prioritera vad du ska göra först.

1. Klicka på rekommendationen i listan.

1. Följ instruktionerna i avsnittet **Reparationssteg.** Varje rekommendation har sin egen uppsättning instruktioner. Följande skärmbild visar reparationssteg för att konfigurera program så att de bara tillåter trafik via HTTPS.

    ![Information om rekommendation](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. När du är klar visas ett meddelande som informerar dig om reparationen har slutförts.

## <a name="quick-fix-remediation"></a>Snabbkorrigeringsåtgärd<a name="one-click"></a>

Med Quick Fix kan du snabbt åtgärda en rekommendation om flera resurser. Den är endast tillgänglig för specifika rekommendationer. Quick Fix förenklar reparationen och gör att du snabbt kan öka din secure score, vilket förbättrar miljöns säkerhet.

Så här implementerar du snabbkorrigering:

1. Från listan med rekommendationer som har **Quick Fix!** klickar du på rekommendationen.

    [![Välj Quick Fix!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. På fliken **Felfritt resurser** väljer du de resurser som du vill implementera rekommendationen på och klickar på **Åtgärda**.

    > [!NOTE]
    > Vissa av de angivna resurserna kan vara inaktiverade eftersom du inte har rätt behörighet att ändra dem.

1. I bekräftelserutan läser du reparationsinformationen och konsekvenserna.

    ![Snabbkorrigering](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Konsekvenserna visas i rutan Grå i fönstret **Åtgärda resurser** som öppnas när du har klickat på **Åtgärda**. De listar vilka ändringar som ska hända när snabbkorrigeringen fortsätter.

1. Infoga relevanta parametrar om det behövs och godkänn reparationen.

    > [!NOTE]
    > Det kan ta flera minuter efter att reparationen har slutförts för att se resurserna på fliken **Felfria resurser.** Om du vill visa reparationsåtgärderna kontrollerar du [aktivitetsloggen](#activity-log).

1. När du är klar visas ett meddelande som informerar dig om reparationen har slutförts.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Snabbkorrigeringsreparation logga in i aktivitetsloggen<a name="activity-log"></a>

Reparationsåtgärden använder ett malldistributions- eller REST PATCH API-anrop för att tillämpa konfigurationen på resursen. Dessa åtgärder loggas i [Azure-aktivitetsloggen](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Rekommendationer med quick fix-reparation

|Rekommendation|Implikationen|
|---|---|
|Granskning på SQL-servrar bör aktiveras|Den här åtgärden aktiverar SQL-granskning på dessa servrar och deras databaser. <br>**Obs!** <ul><li>För varje region i de valda SQL-servrarna skapas och delas ett lagringskonto för att spara granskningsloggar av alla servrar i den regionen.</li><li>Om du vill säkerställa korrekt granskning ska du inte ta bort eller byta namn på resursgruppen eller lagringskontona.</li></ul>|
|Avancerad datasäkerhet bör aktiveras på dina SQL-hanterade instanser|Den här åtgärden aktiverar SQL Advanced Data Security (ADS) på de valda SQL-hanterade instanserna. <br>**Obs!** <ul><li>För varje region och resursgrupp för de valda SQL-hanterade instanserna skapas och delas ett lagringskonto för att spara genomsökningsresultat av alla instanser i den regionen.</li><li> ADS debiteras på $ 15 per SQL hanterad instans.</li></ul>|
|Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser|Den här åtgärden aktiverar SQL Vulnerability Assessment på de valda SQL-hanterade instanserna. <br>**Obs!**<ul><li>SQL Vulnerability Assessment är en del av SQL Advanced Data Security (ADS) paketet. Om ADS inte redan är aktiverat aktiveras det automatiskt på den hanterade instansen.</li><li>För varje region och resursgrupp för de valda SQL-hanterade instanserna skapas och delas ett lagringskonto för lagring av skanningsresultat av alla instanser i den regionen.</li><li>ADS debiteras på $ 15 per SQL-server.</li></ul>||
|Avancerad datasäkerhet bör aktiveras på dina SQL-servrar|Den här åtgärden aktiverar ADS (Advanced Data Security) på dessa valda servrar och deras databaser. <br>**Obs!**<ul><li>För varje region och resursgrupp för de valda SQL-servrarna skapas och delas ett lagringskonto för lagring av skanningsresultat av alla servrar i den regionen.<</li><li>ADS debiteras på $ 15 per SQL-server.</li></ul>||
|Sårbarhetsbedömning bör aktiveras på dina SQL-servrar|Den här åtgärden aktiverar SQL Vulnerability Assessment på dessa valda servrar och deras databaser. <br>**Obs!**<ul><li>SQL Vulnerability Assessment är en del av SQL Advanced Data Security (ADS) paketet. Om ADS inte redan är aktiverat aktiveras det automatiskt på SQL-servern.</li><li>För varje region och resursgrupp för de valda SQL-servrarna skapas och delas ett lagringskonto för lagring av skanningsresultat av alla instanser i den regionen.</li><li>ADS debiteras på $ 15 per SQL-server.</li></ul>||
|Transparent datakryptering i SQL-databaser bör aktiveras|Den här åtgärden aktiverar TDE (SQL Database Transparent Data Encryption) i de valda databaserna. <br>**Som**standard används tjänsthanterade TDE-nycklar.
|Säker överföring till lagringskonton ska vara aktiverat|Den här åtgärden uppdaterar säkerheten för ditt lagringskonto så att endast begäranden tillåts av säkra anslutningar. (HTTPS). <br>**Obs!**<ul><li>Alla begäranden som använder HTTP kommer att avvisas.</li><li>När du använder Azure-filtjänsten misslyckas anslutningen utan kryptering, inklusive scenarier med SMB 2.1, SMB 3.0 utan kryptering och vissa smaker av Linux SMB-klienten. Läs mer.</li></ul>|
|Webbprogrammet ska endast vara tillgängligt via HTTPS|Den här åtgärden omdirigerar all trafik från HTTP till HTTPS på de valda resurserna. <br>**Obs!**<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett sekretessfel. Så användare som har en anpassad domän måste verifiera att de har konfigurerat ett SSL-certifikat.</li><li>Kontrollera att brandväggar för paket- och webbprogram skyddar apptjänsten, tillåt vidarebefordrade HTTPS-sessioner.</li></ul>|
|Funktionsappen ska endast vara tillgänglig via HTTPS|Den här åtgärden omdirigerar all trafik från HTTP till HTTPS på de valda resurserna. <br>**Obs!**<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett sekretessfel. Så användare som har en anpassad domän måste verifiera att de har konfigurerat ett SSL-certifikat.</li><li>Kontrollera att brandväggar för paket- och webbprogram skyddar apptjänsten, tillåt vidarebefordrade HTTPS-sessioner.</li></ul>|
|API-app bör endast vara tillgänglig via HTTPS|Den här åtgärden omdirigerar all trafik från HTTP till HTTPS på de valda resurserna. <br>**Obs!**<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett sekretessfel. Så användare som har en anpassad domän måste verifiera att de har konfigurerat ett SSL-certifikat.</li><li>Kontrollera att brandväggar för paket- och webbprogram skyddar apptjänsten, tillåt vidarebefordrade HTTPS-sessioner.</li></ul>|
|Fjärrfelsökning bör inaktiveras för webbprogram|Den här åtgärden inaktiverar fjärrfelsökning.|
|Fjärrfelsökning bör inaktiveras för Funktionsapp|Den här åtgärden inaktiverar fjärrfelsökning.|
|Fjärrfelsökning bör inaktiveras för API-app|Den här åtgärden inaktiverar fjärrfelsökning.|
|CORS bör inte tillåta alla resurser att komma åt ditt webbprogram|Den här åtgärden blockerar andra domäner från att komma åt webbprogrammet. Om du vill tillåta specifika domäner anger du dem i fältet Tillåtet ursprung (avgränsade med kommatecken). <br>**Obs:** Om du lämnar fältet tomt blockeras alla anrop med flera ursprung.'Param-fältrubrik: "Tillåtet ursprung"|
|CORS bör inte tillåta alla resurser att komma åt din funktionsapp|Den här åtgärden blockerar andra domäner från att komma åt ditt funktionsprogram. Om du vill tillåta specifika domäner anger du dem i fältet Tillåtet ursprung (avgränsade med kommatecken). <br>**Obs:** Om du lämnar fältet tomt blockeras alla anrop med flera ursprung.'Param-fältrubrik: "Tillåtet ursprung"|
|CORS bör inte tillåta alla resurser att komma åt din API-app|Den här åtgärden blockerar andra domäner från att komma åt ditt API-program. Om du vill tillåta specifika domäner anger du dem i fältet Tillåtet ursprung (avgränsade med kommatecken). <br>**Obs:** Om du lämnar fältet tomt blockeras alla anrop med flera ursprung.'Param-fältrubrik: "Tillåtet ursprung"|
|Övervakningsagenten bör aktiveras på dina virtuella datorer|Den här åtgärden installerar en övervakningsagent på de valda virtuella datorerna. Välj en arbetsyta som agenten ska rapportera till.<ul><li>Om uppdateringsprincipen är inställd på automatisk distribueras den på nya befintliga instanser.</li><li>Om uppdateringsprincipen är inställd på manuell och du vill installera agenten på befintliga instanser markerar du alternativet kryssruta. [Läs mer](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Diagnostikloggar i Key Vault ska aktiveras|Den här åtgärden möjliggör diagnostikloggar i nyckelvalv. Diagnostikloggar och mått sparas på den valda arbetsytan.|
|Diagnostikloggar i Servicebuss ska aktiveras|Den här åtgärden möjliggör diagnostikloggar på servicebussen. Diagnostikloggar och mått sparas på den valda arbetsytan.|

## <a name="next-steps"></a>Nästa steg

I det här dokumentet visades hur du åtgärdar rekommendationer i Security Center. Mer information om Security Center finns i följande avsnitt:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Lär dig hur du konfigurerar säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser.
