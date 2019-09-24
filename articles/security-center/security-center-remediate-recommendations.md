---
title: Åtgärda rekommendationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur du kan åtgärda rekommendationer i Azure Security Center som hjälper dig att skydda dina Azure-resurser och hålla dem kompatibla med säkerhets principer.
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
ms.date: 08/18/2019
ms.author: memildin
ms.openlocfilehash: 9bd1586193d2e36c370217e37b77409298821a67
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200998"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Åtgärda rekommendationer i Azure Security Center

Rekommendationer ger förslag på hur du bättre skyddar resurserna.  Du implementerar en rekommendation genom att följa de åtgärds steg som beskrivs i rekommendationen. 

## Reparations steg<a name="remediation-steps"></a>

När du har granskat alla rekommendationer, Bestäm vilken av dem som ska åtgärdas först. Vi rekommenderar att du använder en [säker Poäng påverkan](security-center-recommendations.md#monitor-recommendations) för att prioritera vad du ska göra först.

1. Klicka på rekommendationen i listan.
1. Följ anvisningarna i avsnittet **reparations steg** . Varje rekommendation har en egen uppsättning instruktioner. Följande visar åtgärds steg för att konfigurera program att bara tillåta trafik över HTTPS.

    ![Information om rekommendation](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. När åtgärden har slutförts visas ett meddelande om att reparationen lyckades.

## Reparation med ett klick (för hands version)<a name="one-click"></a>

Med snabb korrigering med ett klick kan du reparera en rekommendation för en samling resurser med ett enda klick. Det är ett alternativ som bara är tillgängligt för vissa rekommendationer. Snabb korrigering med enkel klickning och gör att du snabbt kan förbättra dina säkra poäng och öka säkerheten i din miljö.

Så här implementerar du ett-klick-reparation:

1. Klicka på rekommendationen i listan med rekommendationer som har etiketten **1 klick-Fix** .  

   ![Välj åtgärd för enkel klickning](./media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)

2. På fliken **felaktiga resurser** väljer du de resurser som du vill implementera rekommendationen på och klickar på **åtgärda**. 

    > [!NOTE]
    > Några av de listade resurserna kan vara inaktiverade eftersom du inte har de behörigheter som krävs för att ändra dem.

3. Läs korrigerings informationen och konsekvenserna i bekräftelse rutan. 

   ![Åtgärd med ett klick](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Konsekvenserna visas i den grå rutan i fönstret **Åtgärds resurser** som öppnas när du klickar på **åtgärda**. De visar vilka ändringar som inträffar när du fortsätter med 1-Klicka på reparation.

4. Infoga relevanta parametrar vid behov och godkänn reparationen.

    > [!NOTE]
    > Det kan ta flera minuter efter att reparationen har slutförts för att se resurserna på fliken **felfria resurser** . Om du vill visa reparations åtgärderna kontrollerar du [aktivitets loggen](#activity-log).

5. När åtgärden har slutförts visas ett meddelande om att reparationen lyckades.

## Reparations loggning med ett klick i aktivitets loggen<a name="activity-log"></a>

Reparations åtgärden använder en mall för distribution av mallar eller REST-PATCH-API-anrop för att tillämpa konfigurationen på resursen. De här åtgärderna loggas i [aktivitets loggen i Azure](../azure-resource-manager/resource-group-audit.md).


## <a name="recommendations-with-one-click-remediation"></a>Rekommendationer med ett klick

|Rekommendation|Indirekt|
|---|---|
|Granskning på SQL-servrar måste vara aktiverat|Med den här åtgärden aktive ras SQL-granskning på dessa servrar och deras databaser. <br>**Obs!** <ul><li>För varje region i de valda SQL-servrarna skapas och delas ett lagrings konto för att spara gransknings loggar och delas av alla servrar i den regionen.</li><li>För att säkerställa korrekt granskning ska du inte ta bort eller byta namn på resurs gruppen eller lagrings kontona.</li></ul>|
|Avancerad data säkerhet ska vara aktiverat på SQL-hanterade instanser|Med den här åtgärden aktive ras SQL Advanced Data Security (ADS) på de valda SQL-hanterade instanserna. <br>**Obs!** <ul><li>För varje region och resurs grupp för de valda SQL-hanterade instanserna skapas och delas ett lagrings konto för att spara Sök Resultat och delas av alla instanser i den regionen.</li><li> ADS debiteras med $15 per SQL-hanterad instans.</li></ul>|
|Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser|Den här åtgärden aktiverar SQL sårbarhets bedömning på valda SQL-hanterade instanser. <br>**Obs!**<ul><li>SQL sårbarhet Assessment är en del av SQL-paketet för avancerad data säkerhet (ADS). Om ADS inte är aktiverat kommer det att aktive ras automatiskt på den hanterade instansen.</li><li>För varje region och resurs grupp för de valda SQL-hanterade instanserna skapas och delas ett lagrings konto för lagring av genomsöknings resultat som delas av alla instanser i den regionen.</li><li>ADS debiteras med $15 per SQL Server.</li></ul>||
|Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar|Med den här åtgärden aktive ras avancerad data säkerhet (ADS) på de valda servrarna och databaserna. <br>**Obs!**<ul><li>För varje region och resurs grupp för de valda SQL-servrarna skapas och delas ett lagrings konto för lagring av Sök resultat som delas av alla servrar i regionen. <</li><li>ADS debiteras med $15 per SQL Server.</li></ul>||
|Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar|Den här åtgärden aktiverar utvärdering av SQL-säkerhetsproblem på de valda servrarna och deras databaser. <br>**Obs!**<ul><li>SQL sårbarhet Assessment är en del av SQL-paketet för avancerad data säkerhet (ADS). Om ADS inte redan är aktiverat aktive ras den automatiskt på SQL-servern.</li><li>För varje region och resurs grupp för de valda SQL-servrarna skapas och delas ett lagrings konto för lagring av genomsöknings resultat som delas av alla instanser i den regionen.</li><li>ADS debiteras med $15 per SQL Server.</li></ul>||
|Transparent data kryptering på SQL-databaser ska vara aktive rad|Med den här åtgärden kan SQL Database transparent datakryptering (TDE) på de valda databaserna. <br>**Obs!** Som standard används service-hanterade TDE-nycklar. 
|Säker överföring till lagrings konton ska vara aktiverat|Den här åtgärden uppdaterar lagrings kontots säkerhet för att endast tillåta begär Anden via säkra anslutningar. (HTTPS). <br>**Obs!**<ul><li>Alla begär Anden som använder HTTP kommer att avvisas.</li><li>När du använder Azure Files-tjänsten Miss Miss fungerar anslutningen utan kryptering, inklusive scenarier som använder SMB 2,1, SMB 3,0 utan kryptering och vissa varianter av Linux SMB-klienten.  Läs mer.</li></ul>|
|Webbprogram bör enbart vara åtkomliga via HTTPS|Den här åtgärden dirigerar om all trafik från HTTP till HTTPS, på de valda resurserna. <br>**Obs!**<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett "sekretess fel". Användare som har en anpassad domän måste därför kontrol lera att de har konfigurerat ett SSL-certifikat.</li><li>Se till att paket-och webb programs brand väggar skyddar app service, Tillåt vidarebefordring av HTTPS-sessioner.</li></ul>|
|Funktionen App bör enbart vara åtkomliga via HTTPS|Den här åtgärden dirigerar om all trafik från HTTP till HTTPS, på de valda resurserna. <br>**Obs!**<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett "sekretess fel". Användare som har en anpassad domän måste därför kontrol lera att de har konfigurerat ett SSL-certifikat.</li><li>Se till att paket-och webb programs brand väggar skyddar app service, Tillåt vidarebefordring av HTTPS-sessioner.</li></ul>|
|API-appen bör bara vara tillgänglig via HTTPS|Den här åtgärden dirigerar om all trafik från HTTP till HTTPS, på de valda resurserna. <br>**Obs!**<ul><li>En HTTPS-slutpunkt som inte har ett SSL-certifikat visas i webbläsaren med ett "sekretess fel". Användare som har en anpassad domän måste därför kontrol lera att de har konfigurerat ett SSL-certifikat.</li><li>Se till att paket-och webb programs brand väggar skyddar app service, Tillåt vidarebefordring av HTTPS-sessioner.</li></ul>|
|Fjärrfelsökning bör stängas av för webbprogram|Den här åtgärden inaktiverar fjärrfelsökning.|
|Fjärrfelsökning bör inaktive ras för Funktionsapp|Den här åtgärden inaktiverar fjärrfelsökning.|
|Fjärrfelsökning bör inaktive ras för API-appen|Den här åtgärden inaktiverar fjärrfelsökning.|
|CORS bör inte tillåta alla resurser åtkomst till ditt webb program|Den här åtgärden blockerar andra domäner från att komma åt ditt webb program. Om du vill tillåta vissa domäner anger du dem i fältet tillåtna ursprung (avgränsade med kommatecken). <br>**Obs!** Om fältet lämnas tomt blockeras alla kors starts anrop. "param-fält Rubrik: Tillåtna ursprung|
|CORS bör inte bevilja alla resurser att få åtkomst till din Funktionsapp|Den här åtgärden blockerar andra domäner från att komma åt ditt funktions program. Om du vill tillåta vissa domäner anger du dem i fältet tillåtna ursprung (avgränsade med kommatecken). <br>**Obs!** Om fältet lämnas tomt blockeras alla kors starts anrop. "param-fält Rubrik: Tillåtna ursprung|
|CORS bör inte tillåta alla resurser åtkomst till din API-app|Den här åtgärden blockerar andra domäner från att komma åt ditt API-program. Om du vill tillåta vissa domäner anger du dem i fältet tillåtna ursprung (avgränsade med kommatecken). <br>**Obs!** Om fältet lämnas tomt blockeras alla kors starts anrop. "param-fält Rubrik: Tillåtna ursprung|
|Övervaknings agenten ska vara aktive rad på dina virtuella datorer|Den här åtgärden installerar en övervaknings agent på de valda virtuella datorerna. Välj en arbets yta som agenten ska rapportera till.<ul><li>Om din uppdaterings princip är inställd på automatisk, kommer den att distribueras på nya befintliga instanser.</li><li>Om din uppdaterings princip är inställd på manuell och du vill installera agenten på befintliga instanser markerar du kryss rutan.  [Läs mer](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Diagnostikloggar i Key Vault ska vara aktive rad|Den här åtgärden aktiverar diagnostikloggar på nyckel valv. Diagnostikloggar och mått sparas på den valda arbets ytan.|
|Diagnostikloggar i Service Bus ska vara aktive rad|Den här åtgärden aktiverar diagnostikloggar på Service Bus. Diagnostikloggar och mått sparas på den valda arbets ytan.|

## <a name="next-steps"></a>Nästa steg

I det här dokumentet visade du hur du kan åtgärda rekommendationerna i Security Center. Mer information om Security Center finns i följande avsnitt:

* [Ställer in säkerhets principer i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
