---
title: Azure för säkerhetskopiering av avisering vanliga frågor och svar
description: 'Svar på vanliga frågor om: Avisering för Azure Backup-övervakning'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: bb684f65539b4429862b2dce0e378d8f659d2975
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705034"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure för säkerhetskopiering av avisering – vanliga frågor och svar
Den här artikeln får du svar på vanliga frågor om Azure övervakningsaviseringen.

## <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Hur kontrollerar jag om rapportering av data har börjat flödar till ett lagringskonto?
Gå till det lagringskonto som du har konfigurerat och Välj behållare. Om behållaren har en post för insights-logs-azurebackupreport, indikerar det att rapportdata har startat flödar i.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Vad är frekvensen för data-push till ett lagringskonto och Azure Backup-Innehållspaketet i Power BI?
  Det tar cirka 24 timmar för att skicka data till ett lagringskonto för dag 0 användare. När den här inledande push är klar, uppdateras data med vilken frekvens som visas i följande bild.

  * Data som är relaterade till **jobb**, **aviseringar**, **Säkerhetskopieringsobjekt**, **valv**, **skyddade servrar**, och  **Principer** skickas till en kundens lagringskonto och när den är inloggad.

  * Data som är relaterade till **Storage** skickas till ett lagringskonto för kunden var 24: e timme.

       ![Azure Backup-rapporter push datafrekvensen](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Powerbi har en [schemalagd uppdatering en gång om dagen](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Du kan utföra en manuell uppdatering av data i Power BI för Innehållspaketet.

### <a name="how-long-can-i-retain-reports"></a>Hur länge kan jag behålla rapporter?
När du konfigurerar ett lagringskonto kan välja du en kvarhållningsperiod för rapportdata i lagringskontot. Följ steg 6 i den [konfigurera lagringskonto för rapporter](backup-azure-configure-reports.md#configure-storage-account-for-reports) avsnittet. Du kan också [analysera rapporter i Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) och spara dem under en längre period, utifrån dina behov.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Ser jag mina data i rapporter när du har konfigurerat storage-konto?
 Alla data som genereras när du har konfigurerat en storage-konto skickas till lagringskontot och är tillgänglig i rapporter. Pågående jobb skickas inte för rapportering. När jobbet har slutförts eller misslyckas, skickas den till rapporter.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Om jag redan har konfigurerat storage-konto om du vill visa rapporter kan jag ändra konfigurationen för att använda ett annat lagringskonto?
Ja, du kan ändra konfiguration och referera till ett annat lagringskonto. Använd det nyligen konfigurerade lagringskontot när du ansluter till Azure Backup-Innehållspaketet. När ett annat lagringskonto har konfigurerats flödar också nya data i det här lagringskontot. Äldre data (innan du ändrar konfigurationen) finns fortfarande kvar i äldre storage-konto.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Kan jag visa rapporter över valv och prenumerationer?
Ja, du kan konfigurera samma lagringskonto över olika valv cross-valv visas. Du kan också konfigurera samma lagringskonto för valv mellan prenumerationer. Du kan sedan använda det här lagringskontot när du ansluter till Azure Backup-Innehållspaketet i Power BI för att visa rapporterna. Det valda lagringskontot måste vara i samma region som Recovery Services-valvet.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hur lång tid tar det för Azure backup-agenten jobbstatusen återspeglas i portalen?
Azure-portalen kan ta upp till 15 minuter att återspegla jobbstatusen Azure backup-agenten.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>När ett säkerhetskopieringsjobb misslyckas, hur lång tid tar det för att utlösa en avisering?
En avisering genereras i 20 minuter för Azure Säkerhetskopieringsfel.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Finns det fall där inte ett e-postmeddelande skickas om aviseringarna är inställda?
Ja. I följande situationer skickas inte meddelanden.

* Om meddelanden har konfigurerats för varje timme och en avisering skickas ut och löses inom en timme
* När ett jobb har avbrutits
* Om en andra säkerhetskopieringsjobb misslyckas eftersom det ursprungliga säkerhetskopieringsjobbet pågår

## <a name="recovery-services-vault"></a>Recovery Services Vault

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hur lång tid tar det för Azure backup-agenten jobbstatusen återspeglas i portalen?
Azure-portalen kan ta upp till 15 minuter att återspegla jobbstatusen Azure backup-agenten.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>När ett säkerhetskopieringsjobb misslyckas, hur lång tid tar det för att utlösa en avisering?
En avisering genereras i 20 minuter för Azure Säkerhetskopieringsfel.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Finns det fall där inte ett e-postmeddelande skickas om aviseringarna är inställda?
Ja. I följande situationer skickas inte meddelanden.

* Om meddelanden har konfigurerats för varje timme och en avisering skickas ut och löses inom en timme
* När ett jobb har avbrutits
* Om en andra säkerhetskopieringsjobb misslyckas eftersom det ursprungliga säkerhetskopieringsjobbet pågår

## <a name="next-steps"></a>Nästa steg

Läs de andra vanliga frågor och svar:

- [Vanliga frågor](backup-azure-vm-backup-faq.md) om Virtuella Azure-säkerhetskopieringar.
- [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup-agenten
