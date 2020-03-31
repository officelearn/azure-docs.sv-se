---
title: Säkerhetsfunktioner som skyddar hybridsäkerhetskopior
description: Lär dig hur du använder säkerhetsfunktioner i Azure Backup för att göra säkerhetskopior säkrare
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 7213f26493a118c2cb32f8f9935b4954176b99a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586401"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Säkerhetsfunktioner för att skydda hybridsäkerhetskopior som använder Azure Backup

Oron för säkerhetsfrågor, som skadlig kod, ransomware och intrång, ökar. Dessa säkerhetsfrågor kan bli kostsamma, både när det gäller pengar och data. För att skydda dig mot sådana attacker tillhandahåller Azure Backup nu säkerhetsfunktioner för att skydda hybridsäkerhetskopior. Den här artikeln beskriver hur du aktiverar och använder dessa funktioner med hjälp av en Azure Recovery Services-agent och Azure Backup Server. Dessa funktioner omfattar:

- **Förebyggande**åtgärder . Ett extra autentiseringslagret läggs till när en kritisk åtgärd som att ändra en lösenfras utförs. Den här valideringen är att säkerställa att sådana åtgärder endast kan utföras av användare som har giltiga Azure-autentiseringsuppgifter.
- **Varna**. Ett e-postmeddelande skickas till prenumerationsadministratören när en kritisk åtgärd som att ta bort säkerhetskopierade data utförs. Det här e-postmeddelandet säkerställer att användaren snabbt meddelas om sådana åtgärder.
- **Återhämtning**. Borttagna säkerhetskopierade data sparas i ytterligare 14 dagar från datumet för borttagningen. Detta säkerställer återställning av data inom en viss tidsperiod, så det finns ingen dataförlust även om en attack inträffar. Dessutom upprätthålls ett större antal lägsta återställningspunkter för att skydda mot korrupta data.

> [!NOTE]
> Säkerhetsfunktioner bör inte aktiveras om du använder infrastruktur som en säkerhetskopiering av virtuella datorer (IaaS). Dessa funktioner är ännu inte tillgängliga för IaaS VM-säkerhetskopiering, så att aktivera dem kommer inte att ha någon inverkan. Säkerhetsfunktioner bör endast aktiveras om du använder: <br/>
>  * **Azure Backup agent**. Minsta agent version 2.0.9052. När du har aktiverat dessa funktioner bör du uppgradera till den här agentversionen för att utföra kritiska åtgärder. <br/>
>  * **Azure Backup Server**. Minsta Azure Backup agent version 2.0.9052 med Azure Backup Server uppdatering 1. <br/>
>  * **Data protection manager för systemcenter**. Minsta Azure Backup agent version 2.0.9052 med Data Protection Manager 2012 R2 UR12 eller Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Dessa funktioner är endast tillgängliga för Recovery Services-valv. Alla nyligen skapade Recovery Services-valv har dessa funktioner aktiverade som standard. För befintliga Recovery Services-valv aktiverar användarna dessa funktioner med hjälp av stegen som nämns i följande avsnitt. När funktionerna har aktiverats gäller de alla Recovery Services-agentdatorer, Azure Backup Server-instanser och dataskyddshanterareservrar som är registrerade i valvet. Att aktivera den här inställningen är en engångsåtgärd och du kan inte inaktivera dessa funktioner när du har aktiverat dem.
>

## <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

Om du skapar ett Recovery Services-valv kan du använda alla säkerhetsfunktioner. Om du arbetar med ett befintligt valv aktiverar du säkerhetsfunktioner genom att följa dessa steg:

1. Logga in på Azure-portalen med dina Azure-autentiseringsuppgifter.
2. Välj **Bläddra**och skriv **Återställningstjänster**.

    ![Skärmbild av alternativet Bläddra i Azure Portal](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Listan över Recovery Services-valv visas. Välj ett valv i den här listan. Instrumentpanelen för det valda valvet öppnas.
3. Klicka på **Egenskaper**under **Inställningar**i listan över objekt som visas under valvet .

    ![Skärmbild av alternativen för Återställningstjänster](./media/backup-azure-security-feature/vault-list-properties.png)
4. Klicka på **Uppdatera**under **Säkerhetsinställningar.**

    ![Skärmbild av valven för Återställningstjänster](./media/backup-azure-security-feature/security-settings-update.png)

    Uppdateringslänken öppnar bladet **Säkerhetsinställningar,** som ger en sammanfattning av funktionerna och låter dig aktivera dem.
5. Välj ett värde för att bekräfta om du har aktiverat [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)i listrutan Har **du konfigurerat Azure Multi-Factor Authentication?** Om den är aktiverad uppmanas du att autentisera från en annan enhet (till exempel en mobiltelefon) när du loggar in på Azure-portalen.

   När du utför kritiska åtgärder i Säkerhetskopiering måste du ange en säkerhets-PIN-kod som är tillgänglig på Azure-portalen. Om du aktiverar Azure Multi Factor Authentication läggs ett lager av säkerhet till ett lager av säkerhet. Endast behöriga användare med giltiga Azure-autentiseringsuppgifter och autentiserade från en andra enhet kan komma åt Azure-portalen.
6. Om du vill spara säkerhetsinställningar väljer du **Aktivera** och klickar på **Spara**. Du kan välja **Aktivera** först när du har valt ett värde i listan **Har du konfigurerat Azure Multi-Factor Authentication?**

    ![Skärmbild av säkerhetsinställningar](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Återställa borttagna säkerhetskopierade data

Säkerhetskopiering behåller borttagna säkerhetskopierade data i ytterligare 14 dagar och tar inte bort dem omedelbart om **stoppa säkerhetskopian med borttagning av säkerhetskopieringsdata** utförs. Så här återställer du dessa data under 14-dagarsperioden, beroende på vad du använder:

För **Azure Recovery Services-agentanvändare:**

1. Om datorn där säkerhetskopieringar fortfarande var tillgängliga skyddar du de borttagna datakällorna och använder [återställningsdata till samma dator](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) i Azure Recovery Services för att återställa från alla gamla återställningspunkter.
2. Om den här datorn inte är tillgänglig använder du [Återställ till en alternativ dator](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) för att använda en annan Azure Recovery Services-dator för att hämta dessa data.

För **Azure Backup Server-användare:**

1. Om servern där säkerhetskopieringar fortfarande var tillgängliga skyddar du de borttagna datakällorna och använder funktionen **Återställ data** för att återställa från alla gamla återställningspunkter.
2. Om den här servern inte är tillgänglig använder du [Återställ data från en annan Azure Backup Server](backup-azure-alternate-dpm-server.md) för att använda en annan Azure Backup Server-instans för att hämta dessa data.

Användare **av Data Protection Manager:**

1. Om servern där säkerhetskopieringar fortfarande var tillgängliga skyddar du de borttagna datakällorna och använder funktionen **Återställ data** för att återställa från alla gamla återställningspunkter.
2. Om den här servern inte är tillgänglig använder du [Lägg till extern DPM](backup-azure-alternate-dpm-server.md) för att använda en annan Data Protection Manager-server för att hämta dessa data.

## <a name="prevent-attacks"></a>Förhindra attacker

Kontroller har lagts till för att se till att endast giltiga användare kan utföra olika åtgärder. Dessa inkluderar att lägga till ett extra lager av autentisering och upprätthålla ett minsta kvarhållningsintervall för återställningsändamål.

### <a name="authentication-to-perform-critical-operations"></a>Autentisering för kritiska åtgärder

Som en del av att lägga till ett extra lager av autentisering för kritiska åtgärder uppmanas du att ange en säkerhets-PIN-kod när du utför **Stoppa skydd med Ta bort data** och Ändra **lösenfrasåtgärder.**

> [!NOTE]
>
> För närvarande stöds inte säkerhetsnål för **stoppskydd med ta bort data** för DPM och MABS.

Så här får du den här PIN-koden:

1. Logga in på Azure Portal.
2. Bläddra till egenskaper för **återställningstjänsters valvinställningar** > **Settings** > **Properties**.
3. Klicka på **Generera**under **Säkerhets-PIN-** kod . Detta öppnar ett blad som innehåller PIN-koden som ska anges i användargränssnittet för Azure Recovery Services-agenten.
    Den här PIN-koden är giltig i endast fem minuter och genereras automatiskt efter den perioden.

### <a name="maintain-a-minimum-retention-range"></a>Underhåll ett minsta kvarhållningsintervall

För att säkerställa att det alltid finns ett giltigt antal återställningspunkter tillgängliga har följande kontroller lagts till:

- För daglig kvarhållning bör minst **sju** dagars kvarhållande göras.
- För kvarhållande varje vecka bör minst **fyra** veckors kvarhållande göras.
- För månatlig kvarhållande bör minst **tre** månaders kvarhållande göras.
- För årlig lagring bör minst **ett** års kvarhållande göras.

## <a name="notifications-for-critical-operations"></a>Meddelanden för kritiska åtgärder

När en kritisk åtgärd utförs skickas vanligtvis ett e-postmeddelande med information om åtgärden när en kritisk åtgärd utförs. Du kan konfigurera ytterligare e-postmottagare för dessa meddelanden med hjälp av Azure-portalen.

De säkerhetsfunktioner som nämns i den här artikeln ger försvarsmekanismer mot riktade attacker. Ännu viktigare är att om en attack inträffar ger dessa funktioner dig möjlighet att återställa dina data.

## <a name="troubleshooting-errors"></a>Felsöka fel

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Ändring av politiken |Det gick inte att ändra säkerhetskopieringsprincipen. Fel: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x29834]. Försök igen efter någon gång. Kontakta Microsoft-supporten om problemet kvarstår. |**Orsaka:**<br/>Det här felet kommer när säkerhetsinställningar är aktiverade, du försöker minska kvarhållningsområdet under de minimivärden som anges ovan och du finns på version som inte stöds (versioner som stöds anges i första anteckningen i den här artikeln). <br/>**Rekommenderad åtgärd:**<br/> I det här fallet bör du ange kvarhållningsperiod över den angivna minsta kvarhållningsperioden (sju dagar för dagligen, fyra veckor för varje vecka, tre veckor för månadsvis eller ett år för årliga) för att gå vidare med policyrelaterade uppdateringar. Alternativt skulle den föredragna metoden vara att uppdatera säkerhetskopieringsagenten, Azure Backup Server och/eller DPM UR för att utnyttja alla säkerhetsuppdateringar. |
| Ändra lösenfras |Säkerhets-PIN-kod är felaktig. (ID: 100130) Ange rätt säkerhets-PIN för att slutföra den här åtgärden. |**Orsaka:**<br/> Det här felet kommer när du anger ogiltig eller utgångna säkerhets-PIN-kod när du utför kritiska åtgärder (som ändringsformfras). <br/>**Rekommenderad åtgärd:**<br/> För att slutföra åtgärden måste du ange giltig säkerhets-PIN-kod. To get the PIN, sign in to Azure portal and navigate to Recovery Services vault > Settings > Properties > Generate Security PIN. Använd den här PIN-koden för att ändra lösenfras. |
| Ändra lösenfras |Åtgärden misslyckades. ID: 120002 |**Orsaka:**<br/>Det här felet kommer när säkerhetsinställningar är aktiverade, du försöker ändra lösenfras och du är på version som inte stöds (giltiga versioner som anges i första anteckningen i den här artikeln).<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösenfras måste du först uppdatera säkerhetskopieringsagenten till minsta version minst 2.0.9052, Azure Backup-server till minsta uppdatering 1 och/eller DPM till lägsta DPM 2012 R2 UR12 eller DPM 2016 UR2 (hämta länkar nedan) och ange sedan giltig säkerhets-PIN. To get the PIN, sign in to Azure portal and navigate to Recovery Services vault > Settings > Properties > Generate Security PIN. Använd den här PIN-koden för att ändra lösenfras. |

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure Recovery Services-valvet](backup-azure-vms-first-look-arm.md) för att aktivera dessa funktioner.
- [Ladda ned den senaste Azure Recovery Services-agenten](https://aka.ms/azurebackup_agent) för att skydda Windows-datorer och skydda dina säkerhetskopierade data mot attacker.
- [Ladda ned den senaste Azure Backup Server](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) för att skydda arbetsbelastningar och skydda dina säkerhetskopierade data mot attacker.
- [Ladda ner UR12 för System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) eller [ladda ner UR2 för System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) för att skydda arbetsbelastningar och skydda dina säkerhetskopierade data mot attacker.
