---
title: Vanliga frågor och svar om Azure Backup övervaknings avisering
description: I den här artikeln hittar du svar på vanliga frågor om Azure Backup övervaknings avisering och Azure Backup rapporter.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: dacurwin
ms.openlocfilehash: dc0225092d23371ca97dfedd48a2d3ffcf85a9be
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747393"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup övervaknings avisering – vanliga frågor och svar

I den här artikeln besvaras vanliga frågor om Azures övervaknings avisering.

## <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Hur gör jag för att kontrollerar du om rapport data har börjat flöda in i ett lagrings konto?

Gå till det lagrings konto som du har konfigurerat och välj behållare. Om behållaren har en post för Insights-logs-azurebackupreport, betyder det att rapporterings data har börjat flöda in.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Vad är frekvensen för data-push till ett lagrings konto och Azure Backup innehålls paketet i Power BI?

  För användare på dag 0 tar det cirka 24 timmar att skicka data till ett lagrings konto. När den första push-sändningen är färdig uppdateras data med den frekvens som visas i följande figur.

* Data som rör **jobb**, **aviseringar**, **säkerhets kopierings objekt**, **valv**, **skyddade servrar**och **principer** skickas till ett kund lagrings konto som och när det loggas.

* Data som rör **lagring** skickas till ett kund lagrings konto var 24: e timme.

    ![Azure Backup rapporter data push-frekvens](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Power BI har en [schemalagd uppdatering en gång om dagen](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Du kan utföra en manuell uppdatering av data i Power BI för innehålls paketet.

### <a name="how-long-can-i-retain-reports"></a>Hur länge kan jag behålla rapporter?

När du konfigurerar ett lagrings konto kan du välja en kvarhållningsperiod för rapport data i lagrings kontot. Följ steg 6 i avsnittet [Konfigurera lagrings konto för rapporter](backup-azure-configure-reports.md#configure-storage-account-for-reports) . Du kan också [analysera rapporter i Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) och spara dem under en längre kvarhållningsperiod, baserat på dina behov.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Ser jag alla mina data i rapporter efter att jag har konfigurerat lagrings kontot?

 Alla data som genereras efter att du har konfigurerat ett lagrings konto skickas till lagrings kontot och är tillgängliga i rapporter. Pågående jobb flyttas inte för rapportering. När jobbet har slutförts eller misslyckats skickas det till rapporter.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Kan jag ändra konfigurationen till att använda ett annat lagrings konto om jag redan har konfigurerat lagrings kontot för att visa rapporter?

Ja, du kan ändra konfigurationen så att den pekar på ett annat lagrings konto. Använd det nyligen konfigurerade lagrings kontot när du ansluter till Azure Backup innehålls paketet. När ett annat lagrings konto har kon figurer ATS kan nya data flödas i det här lagrings kontot. Äldre data (innan du ändrar konfigurationen) finns kvar i det äldre lagrings kontot.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Kan jag visa rapporter över valv och prenumerationer?

Ja, du kan konfigurera samma lagrings konto i olika valv för att Visa kors valv rapporter. Du kan också konfigurera samma lagrings konto för valv över prenumerationer. Sedan kan du använda det här lagrings kontot medan du ansluter till Azure Backup innehålls paketet i Power BI för att visa rapporterna. Det valda lagrings kontot måste finnas i samma region som Recovery Services-valvet.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hur lång tid tar det för jobb status för Azure Backup-agenten att avspeglas i portalen?

Det kan ta upp till 15 minuter för Azure Portal att avspegla jobb statusen för Azure Backup Agent.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hur lång tid tar det att utlösa en avisering när ett säkerhets kopierings jobb Miss lyckas?

En avisering aktive ras inom 20 minuter efter ett Azure Backup-haveri.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Finns det ett fall där ett e-postmeddelande inte skickas om meddelanden har kon figurer ATS?

Ja. I följande situationer skickas inte meddelanden.

* Om meddelanden konfigureras varje timme och en avisering höjs och löses inom timmen
* När ett jobb avbryts
* Om ett andra säkerhets kopierings jobb Miss lyckas eftersom det ursprungliga säkerhets kopierings jobbet pågår

## <a name="recovery-services-vault"></a>Recovery Services valv

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hur lång tid tar det för jobb status för Azure Backup-agenten att avspeglas i portalen?

Det kan ta upp till 15 minuter för Azure Portal att avspegla jobb statusen för Azure Backup Agent.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hur lång tid tar det att utlösa en avisering när ett säkerhets kopierings jobb Miss lyckas?

En avisering aktive ras inom 20 minuter efter ett Azure Backup-haveri.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Finns det ett fall där ett e-postmeddelande inte skickas om meddelanden har kon figurer ATS?

Ja. I följande situationer skickas inga meddelanden:

* Om meddelanden konfigureras varje timme och en avisering höjs och löses inom timmen
* När ett jobb avbryts
* Om ett andra säkerhets kopierings jobb Miss lyckas eftersom det ursprungliga säkerhets kopierings jobbet pågår

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

* [Vanliga frågor](backup-azure-vm-backup-faq.md) om säkerhets kopiering av virtuella Azure-datorer.
* [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup Agent
