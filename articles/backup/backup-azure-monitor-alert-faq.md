---
title: Vanliga frågor och svar om övervakning av aviseringar och rapporter
description: I den här artikeln kan du hitta svar på vanliga frågor om Azure Backup Monitoring Alert och Azure Backup-rapporter.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989577"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Övervakningsvarning för Azure-säkerhetskopiering – vanliga frågor och svar

Den här artikeln besvarar vanliga frågor om övervakning och rapportering av Azure Backup.

## <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Hur kontrollerar jag om rapportdata har börjat flöda till en Log Analytics -arbetsyta (Log Analytics) ?

Navigera till den LA-arbetsyta som du har konfigurerat, navigera till menyalternativet **Loggar** och kör frågan CoreAzureBackup | ta 1. Om du ser en post som returneras betyder det att data har börjat flöda in på arbetsytan. Den första data push kan ta upp till 24 timmar.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Hur ofta data ska gå till en LA-arbetsyta?

Diagnostikdata från valvet pumpas till Log Analytics-arbetsytan med viss fördröjning. Varje händelse anländer till Log Analytics arbetsytan 20 till 30 minuter efter att den har skjutits från Recovery Services-valvet. Här är mer information om eftersläpningen:

* Alla lösningar skjuts säkerhetskopieringstjänstens inbyggda aviseringar så fort de skapas. Så de visas vanligtvis i Log Analytics arbetsytan efter 20 till 30 minuter.
* Alla lösningar skjuts säkerhetskopieringsjobb på begäran och återställningsjobb så fort de är klara.
* För alla lösningar utom SQL-säkerhetskopiering skjuts schemalagda säkerhetskopieringsjobb ut så fort de är klara.
* För SQL-säkerhetskopiering, eftersom säkerhetskopiering av loggar kan ske var 15:e minut, batchas och skjuts information om alla slutförda schemalagda säkerhetskopieringsjobb, inklusive loggar, och skjuts in var 6:e timme.
* Alla lösningar, annan information som säkerhetskopieringsobjekt, princip, återställningspunkter, lagring och så vidare, skjuts minst en gång per dag.
* En ändring i konfigurationen för säkerhetskopiering (till exempel ändra princip eller redigeringsprincip) utlöser en push av all relaterad säkerhetskopieringsinformation.

### <a name="how-long-can-i-retain-reporting-data"></a>Hur länge kan jag behålla rapporteringsdata?

När du har skapat en LA-arbetsyta kan du välja att behålla data i högst två år. Som standard behåller en LA-arbetsyta data i 31 dagar.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Kommer jag att se alla mina data i rapporter när jag har konfigurerat LA Workspace?

 Alla data som genereras när du har konfigurerat diagnostikinställningarna överförs till LA Workspace och är tillgängliga i rapporter. Pågående jobb drivs inte för rapportering. När jobbet har slutförts eller misslyckats skickas det till rapporter.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Kan jag visa rapporter över valv och prenumerationer?

Ja, du kan visa rapporter över valv och prenumerationer samt regioner. Dina data kan finnas i en enda LA Workspace eller en grupp LA Workspaces.

### <a name="can-i-view-reports-across-tenants"></a>Kan jag visa rapporter över klienter?

Om du är en [Azure Lighthouse-användare](https://azure.microsoft.com/services/azure-lighthouse/) med delegerad åtkomst till dina kunders prenumerationer eller LA-arbetsytor kan du använda säkerhetskopieringsrapporter för att visa data för alla dina klienter.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hur lång tid tar det för Azure backup agent jobbstatus att reflektera i portalen?

Azure-portalen kan ta upp till 15 minuter för att återspegla Azure-jobbstatus för säkerhetskopieringsagenten.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hur lång tid tar det innan en avisering utlöses när ett säkerhetskopieringsjobb misslyckas?

En avisering utlöses inom 20 minuter efter Azure-säkerhetskopieringsfelet.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Finns det ett fall där ett e-postmeddelande inte skickas om meddelanden är konfigurerade?

Ja. I följande situationer skickas inte meddelanden.

* Om meddelanden konfigureras varje timme och en avisering utlöses och löses inom en timme
* När ett jobb avbryts
* Om ett andra säkerhetskopieringsjobb misslyckas eftersom det ursprungliga säkerhetskopieringsjobbet pågår

## <a name="recovery-services-vault"></a>Recovery Services-valv

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hur lång tid tar det för Azure backup agent jobbstatus att reflektera i portalen?

Azure-portalen kan ta upp till 15 minuter för att återspegla Azure-jobbstatus för säkerhetskopieringsagenten.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hur lång tid tar det innan en avisering utlöses när ett säkerhetskopieringsjobb misslyckas?

En avisering utlöses inom 20 minuter efter Azure-säkerhetskopieringsfelet.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Finns det ett fall där ett e-postmeddelande inte skickas om meddelanden är konfigurerade?

Ja. I följande situationer skickas inte meddelanden:

* Om meddelanden konfigureras varje timme och en avisering utlöses och löses inom en timme
* När ett jobb avbryts
* Om ett andra säkerhetskopieringsjobb misslyckas eftersom det ursprungliga säkerhetskopieringsjobbet pågår

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

* [Vanliga frågor](backup-azure-vm-backup-faq.md) om säkerhetskopiering av virtuella Azure-datorer.
* [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup-agenten
