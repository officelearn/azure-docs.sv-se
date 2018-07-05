---
title: Säkerhetsfunktioner som hjälper att skydda hybridsäkerhetskopieringar som använder Azure Backup
description: Lär dig hur du använder funktioner för säkerhet i Azure Backup kan skydda säkerhetskopior
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: trinadhk
ms.openlocfilehash: 714c8fde28be63e5173f89f92d186445f0990214
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447388"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Säkerhetsfunktioner som hjälper att skydda hybridsäkerhetskopieringar som använder Azure Backup
Frågor om säkerhetsproblem, t.ex. skadlig kod, utpressningstrojaner och intrång, ökar. Dessa säkerhetsproblem kan vara kostsamma när det gäller både pengar och data. För att skydda mot sådana attacker, ger Azure Backup nu säkerhetsfunktioner för att skydda hybridsäkerhetskopieringar. Den här artikeln beskriver hur du aktiverar och använder dessa funktioner med hjälp av ett Azure Recovery Services-agenten och Azure Backup Server. Dessa funktioner:

- **Förebyggande**. Ett extra lager av autentisering har lagts till när en kritisk åtgärd som ändrar en lösenfras utförs. Den här verifieringen är att säkerställa att sådana åtgärder kan bara utföras av användare som har giltiga autentiseringsuppgifter för Azure.
- **Aviseringar**. Ett e-postmeddelande skickas till administratören för prenumeration när en kritisk åtgärd som tar bort säkerhetskopierade data utförs. Det här e-garanterar att användaren ska snabbt meddelas om sådana åtgärder.
- **Recovery**. Borttagna bevaras säkerhetskopior för ytterligare 14 dagar från datumet då borttagningen. Detta säkerställer informationen kan återställas inom en viss tidsperiod, så det finns inga data går förlorade även om en attack inträffar. Dessutom bevaras ett större antal minsta återställningspunkter som skydd mot skadade data.

> [!NOTE]
> Säkerhetsfunktioner bör inte aktiveras om du använder infrastruktur som en tjänst (IaaS)-säkerhetskopiering. De här funktionerna finns ännu inte för IaaS VM-säkerhetskopiering, så att aktivera dem inte har någon effekt. Säkerhetsfunktioner ska aktiveras bara om du använder: <br/>
>  * **Azure Backup-agenten**. Minst agentversion 2.0.9052. När du har aktiverat dessa funktioner, bör du uppgradera till den agentversionen som ska utföra kritiska åtgärder. <br/>
>  * **Azure Backup Server**. Minsta Azure Backup-agentversionen 2.0.9052 med Azure Backup Server uppdatering 1. <br/>
>  * **System Center Data Protection Manager**. Minsta Azure Backup-agentversionen 2.0.9052 med Data Protection Manager 2012 R2 UR12 eller Data Protection Manager 2016 UR2. <br/> 


> [!NOTE]
> Dessa funktioner är endast tillgänglig för Recovery Services-valvet. Alla nyligen skapade Recovery Services-valv har dessa funktioner aktiverade som standard. För befintliga Recovery Services-valv aktivera användare dessa funktioner med hjälp av stegen som beskrivs i följande avsnitt. När funktionerna som är aktiverade, de gäller för alla Recovery Services-agentdatorer, Azure Backup Server-instanser och Data Protection Manager-servrar som är registrerade med valvet. Om du aktiverar den här inställningen är en engångsåtgärd och du kan inte inaktivera dessa funktioner när du har aktiverat dem.
>

## <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner
Om du skapar ett Recovery Services-valv, kan du använda alla säkerhetsfunktioner. Om du arbetar med ett befintligt valv, kan du aktivera funktioner för säkerhet genom att följa dessa steg:

1. Logga in på Azure-portalen med dina autentiseringsuppgifter för Azure.
2. Välj **Bläddra**, och skriv **återställningstjänster**.

    ![Skärmbild av Azure portal bläddringsalternativet](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Listan över Recovery Services-valv visas. Välj ett valv i den här listan. Instrumentpanelen för det valda valvet öppnas.
3. I listan med objekt som visas under valvet under **inställningar**, klickar du på **egenskaper**.

    ![Skärmbild av Recovery Services-valv alternativ](./media/backup-azure-security-feature/vault-list-properties.png)
4. Under **säkerhetsinställningar**, klickar du på **uppdatering**.

    ![Skärmbild av Recovery Services-valvegenskaper](./media/backup-azure-security-feature/security-settings-update.png)

    Uppdateringslänken öppnas den **säkerhetsinställningar** blad som innehåller en sammanfattning av funktionerna och du kan aktivera dem.
5. Från den nedrullningsbara listan **har du konfigurerat Azure Multi-Factor Authentication?**, Välj ett värde för att bekräfta om du har aktiverat [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Om den är aktiverad, uppmanas du att autentisera från en annan enhet (till exempel en mobiltelefon) när du loggar in på Azure Portal.

   Du måste ange en säkerhetskod, tillgängliga på Azure portal när du utför kritiska åtgärder i säkerhetskopian. Aktivera Azure Multi-Factor Authentication lägger till ett lager av säkerhet. Endast behöriga användare med giltiga autentiseringsuppgifter för Azure och autentiseras från en andra enhet kan komma åt Azure-portalen.
6. Välj för att spara säkerhetsinställningar **aktivera** och klicka på **spara**. Du kan välja **aktivera** bara när du har valt ett värde från den **har du konfigurerat Azure Multi-Factor Authentication?** lista i föregående steg.

    ![Skärmbild av säkerhetsinställningar](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Återställa borttagna säkerhetskopierade data
Säkerhetskopiering behåller raderade säkerhetskopieringsdata för ytterligare 14 dagar och ta inte bort omedelbart om det **Avbryt säkerhetskopiering med ta bort säkerhetskopieringsdata** åtgärden utförs. Om du vill återställa dessa data i 14 dagars perioden, gör du följande, beroende på vad du använder:

För **Azure Recovery Services-agenten** användare:

1. Om den dator där säkerhetskopior händer är fortfarande tillgängliga använder [återställa data på samma dator](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) i Azure Recovery Services att komma tillrätta med de gamla återställningspunkterna.
2. Om den här datorn inte är tillgänglig använder [Återställ till en annan dator](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) du använder en annan Azure Recovery Services-dator för att hämta dessa data.

För **Azure Backup Server** användare:

1. Om servern där säkerhetskopior händer är fortfarande tillgänglig, skydda igen borttagna datakällorna och använder den **Återställ Data** funktionen för att återställa från de gamla återställningspunkterna.
2. Om den här servern inte är tillgänglig, använda [återställa data från en annan Azure Backup Server](backup-azure-alternate-dpm-server.md) du använder en annan Azure Backup Server-instans för att hämta dessa data.

För **Data Protection Manager** användare:

1. Om servern där säkerhetskopior händer är fortfarande tillgänglig, skydda igen borttagna datakällorna och använder den **Återställ Data** funktionen för att återställa från de gamla återställningspunkterna.
2. Om den här servern inte är tillgänglig, använda [Lägg till extern DPM](backup-azure-alternate-dpm-server.md) du använder en annan Data Protection Manager-server för att hämta dessa data.

## <a name="prevent-attacks"></a>Förhindra attacker
Kontroller har lagts till att se till att endast giltiga användare kan utföra olika åtgärder. Dessa omfattar att lägga till ett extra lager av autentisering och underhålla en minsta Kvarhållningsintervall för återställningen.

### <a name="authentication-to-perform-critical-operations"></a>Autentisering för att utföra kritiska åtgärder
Som en del av att lägga till ett extra lager av autentisering för kritiska åtgärder kan du uppmanas att ange en säkerhetskod när du utför **stoppa skydd med borttagningsdata** och **ändra lösenfras** åtgärder.

Att ta emot den här PIN-kod:

1. Logga in på Azure Portal.
2. Bläddra till **Recovery Services-valv** > **inställningar** > **egenskaper**.
3. Under **SÄKERHETSKODEN**, klickar du på **generera**. Då öppnas ett blad som innehåller PIN-kod anges i användargränssnittet för Azure Recovery Services-agenten.
    Den här PIN-kod är giltig i fem minuter och den hämtar genererat automatiskt efter den perioden.

### <a name="maintain-a-minimum-retention-range"></a>Underhålla en minsta Kvarhållningsintervall
Följande kontroller har lagts till för att säkerställa att det finns alltid ett giltigt antal återställningspunkter:

- För bevarande varje dag, minst **sju** dagars kvarhållning ska göras.
- För kvarhållning av veckovis, minst **fyra** veckors kvarhållning ska göras.
- För kvarhållning av månatlig, minst **tre** månaders kvarhållning ska göras.
- För kvarhållning av årlig, minst **en** års kvarhållning ska göras.

## <a name="notifications-for-critical-operations"></a>Meddelanden om kritiska åtgärder
När en kritisk åtgärd utförs vanligtvis skickas administratören för prenumeration ett e-postmeddelande med information om åtgärden. Du kan konfigurera ytterligare e-postmottagare för dessa meddelanden med hjälp av Azure portal.

Säkerhetsfunktioner som nämns i den här artikeln har funktioner för skydd mot riktade attacker. Viktigast av allt, om en attack inträffar kan ger de här funktionerna dig möjlighet att återställa dina data.

## <a name="troubleshooting-errors"></a>Felsöka fel
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Ändring av |Det gick inte att ändra säkerhetskopieringsprincipen. Fel: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x29834]. Försök igen om en stund. Kontakta Microsoft-supporten om problemet kvarstår. |**Orsak:**<br/>Det här felet är när säkerhetsinställningar har aktiverats, försök att minska kvarhållningsintervallet under de lägsta värdena som anges ovan och du har en version som inte stöds (versioner som stöds anges i första anteckna den här artikeln). <br/>**Rekommenderad åtgärd:**<br/> I det här fallet bör du ange kvarhållningsperiod ovanför den minsta Kvarhållningsintervall angiven tidsperiod (sju dagar för varje dag, fyra veckor för varje vecka, tre veckor för varje månad eller ett år för varje år) relaterade uppdateringar att fortsätta med principen. Du kan också är önskad metod att uppdatera backup-agenten, Azure Backup Server och/eller DPM UR utnyttja alla säkerhetsuppdateringar. |
| Ändra lösenfras |Säkerhetskoden är felaktig. (ID: 100130) Ange rätt SÄKERHETSKOD för att slutföra den här åtgärden. |**Orsak:**<br/> Det här felet kommer när du anger ogiltiga eller utgångna SÄKERHETSKODEN under kritiska åtgärd (t.ex. Ändra lösenfras). <br/>**Rekommenderad åtgärd:**<br/> Du måste ange giltiga SÄKERHETSKODEN för att slutföra åtgärden. PIN-koden får logga in på Azure-portalen och gå till Recovery Services-valvet > Inställningar > Egenskaper > Skapa SÄKERHETSKOD. Använd den här PIN-kod för att ändra lösenfras. |
| Ändra lösenfras |Åtgärden kunde inte utföras. ID: 120002 |**Orsak:**<br/>Det här felet kommer när säkerhetsinställningar har aktiverats, försök att ändra lösenfras och du har en version som inte stöds (giltigt versioner som anges i första anteckna den här artikeln).<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösenfras måste du först uppdatera backup-agenten till minimiversion minsta 2.0.9052, Azure Backup server till minsta update 1, och/eller att DPM ska minsta DPM 2012 R2 UR12 eller DPM 2016 UR2 (download länkarna nedan), och ange sedan giltig säkerhets-PIN. PIN-koden får logga in på Azure-portalen och gå till Recovery Services-valvet > Inställningar > Egenskaper > Skapa SÄKERHETSKOD. Använd den här PIN-kod för att ändra lösenfras. |

## <a name="next-steps"></a>Nästa steg
* [Kom igång med Azure Recovery Services-valv](backup-azure-vms-first-look-arm.md) att aktivera dessa funktioner.
* [Hämta den senaste Azure Recovery Services-agenten](http://aka.ms/azurebackup_agent) för att skydda Windows-datorer och skydda dina säkerhetskopierade data mot attacker.
* [Ladda ned den senaste Azure Backup Server](https://aka.ms/latest_azurebackupserver) för att skydda arbetsbelastningar och skydda dina säkerhetskopierade data mot attacker.
* [Ladda ned UR12 för System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) eller [hämta UR2 för System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) för att skydda arbetsbelastningar och skydda dina säkerhetskopierade data mot attacker.
