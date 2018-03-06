---
title: "Säkerhetsfunktioner för att skydda hybrid säkerhetskopieringar som använder Azure Backup | Microsoft Docs"
description: "Lär dig hur du använder säkerhetsfunktioner i Azure Backup för att säkerhetskopiera säkrare"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pajosh
ms.openlocfilehash: f856303d4abf05178eb11a242b87390ff1484e1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Säkerhetsfunktioner för att skydda hybrid säkerhetskopieringar som använder Azure Backup
Frågor om säkerhetsproblem som skadlig programvara, är en utpressningstrojan som och intrångsidentifiering, ökar. Dessa säkerhetsproblem kan vara kostsamma vad gäller både pengar och data. För att skydda mot sådana angrepp ger Azure Backup nu säkerhetsfunktioner för att skydda hybrid säkerhetskopieringar. Den här artikeln beskriver hur du aktiverar och använder dessa funktioner med hjälp av en Azure Recovery Services-agenten och Azure Backup Server. Funktionerna är:

- **Förebyggande**. Ett extra lager av autentisering läggs till när en kritisk åtgärd som ändrar en lösenfras utförs. Den här verifieringen är att säkerställa att dessa åtgärder kan endast utföras av användare som har giltiga autentiseringsuppgifter för Azure.
- **Aviseringar**. Ett e-postmeddelande skickas till prenumerationsadministratör när en kritisk åtgärd som tar bort säkerhetskopierade data utförs. E-postmeddelandet ser till att användaren ska meddelas snabbt om sådana åtgärder.
- **Återställning**. Borttagna säkerhetskopieringsdata behålls för ytterligare 14 dagar från datumet då tas bort. Detta säkerställer informationen kan återställas inom en viss tidsperiod, så det finns inga data går förlorade även om en attack inträffar. Dessutom bevaras ett större antal minsta återställningspunkter som skyddar mot skadade data.

> [!NOTE]
> Säkerhetsfunktioner bör inte aktiveras om du använder infrastruktur som en tjänst (IaaS) VM-säkerhetskopia. De här funktionerna finns ännu inte för IaaS VM säkerhetskopiering, så att de inte har någon effekt. Säkerhetsfunktioner ska aktiveras bara om du använder: <br/>
>  * **Azure Backup-agenten**. Minsta agentversion 2.0.9052. När du har aktiverat dessa funktioner, bör du uppgradera till den agentversionen som ska utföra kritiska åtgärder. <br/>
>  * **Azure Backup Server**. Azure Backup agent minimiversionen 2.0.9052 med Azure Backup Server uppdatering 1. <br/>
>  * **System Center Data Protection Manager**. Azure Backup agent minimiversionen 2.0.9052 med Data Protection Manager 2012 R2 UR12 eller UR2 för Data Protection Manager 2016. <br/> 


> [!NOTE]
> Dessa funktioner är endast tillgängligt för Recovery Services-valvet. Alla nyligen skapade Recovery Services-valv har dessa funktioner aktiverade som standard. För befintliga Recovery Services-valv aktiverar användare dessa funktioner med hjälp av anvisningarna i följande avsnitt. När funktionerna som är aktiverade kan de gäller för alla Recovery Services-agentdatorer, Azure Backup Server-instanser och Data Protection Manager-servrar som är registrerade med valvet. Om du aktiverar den här inställningen är en engångsåtgärd, och du inaktivera inte dessa funktioner när du har aktiverat dem.
>

## <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner för
Om du skapar ett Recovery Services-valv, kan du använda säkerhetsfunktionerna för. Om du arbetar med en befintlig valvet aktivera säkerhetsfunktioner genom att följa dessa steg:

1. Logga in på Azure-portalen med dina autentiseringsuppgifter för Azure.
2. Välj **Bläddra**, och skriv **återställningstjänster**.

    ![Skärmbild av Azure portal bläddringsalternativet](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Listan över Recovery Services-valv visas. Den här listan, Välj ett valv. Instrumentpanelen för det valda valvet öppnas.
3. I listan över objekt som visas under valvet, under **inställningar**, klickar du på **egenskaper**.

    ![Skärmbild av Recovery Services-valvet alternativ](./media/backup-azure-security-feature/vault-list-properties.png)
4. Under **säkerhetsinställningar**, klickar du på **uppdatering**.

    ![Skärmbild av Recovery Services-valvet egenskaper](./media/backup-azure-security-feature/security-settings-update.png)

    Uppdateringslänken öppnar den **säkerhetsinställningar** som innehåller en översikt över funktionerna och du kan aktivera dem.
5. Från den nedrullningsbara listan **har du konfigurerat Azure Multi-Factor Authentication?**, Välj ett värde för att bekräfta om du har aktiverat [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Om den är aktiverad, uppmanas du att autentisera från en annan enhet (till exempel en mobiltelefon) när du loggar in på Azure-portalen.

   Du måste ange en säkerhets-och PIN-kod på Azure-portalen när du utför kritiska åtgärder i säkerhetskopian. Aktivera Azure Multi-Factor Authentication ger en säkerhetsnivå. Endast behöriga användare med giltiga autentiseringsuppgifter för Azure och autentiseras från en annan enhet, kan komma åt Azure-portalen.
6. Välj för att spara säkerhetsinställningar **aktivera** och på **spara**. Du kan välja **aktivera** bara när du har markerat ett värde från den **har du konfigurerat Azure Multi-Factor Authentication?** listan i föregående steg.

    ![Skärmbild av säkerhetsinställningar](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Återställa borttagna säkerhetskopierade data
Säkerhetskopiering behåller borttagna säkerhetskopierade data för ytterligare 14 dagar och ta inte bort omedelbart om den **stoppa säkerhetskopiering med ta bort säkerhetskopieringsdata** åtgärden utförs. Om du vill återställa dessa data i 14 dagar, gör du följande, beroende på vad du använder:

För **Azure Recovery Services-agenten** användare:

1. Om den dator där säkerhetskopieringar händer är fortfarande tillgängliga använder [återställa data på samma dator](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) i Azure Recovery Services att återställa från de gamla återställningspunkterna.
2. Om den här datorn inte är tillgänglig använder [Återställ till en annan dator](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) du använder en annan Azure Recovery Services-dator för att hämta dessa data.

För **Azure Backup Server** användare:

1. Om den server där säkerhetskopieringar händer fortfarande är tillgänglig, att skydda de borttagna datakällorna och använda den **återställa Data** funktion för att återställa från de gamla återställningspunkterna.
2. Om den här servern inte är tillgänglig, använda [återställa data från en annan Azure Backup Server](backup-azure-alternate-dpm-server.md) du använder en annan Azure Backup Server-instans för att hämta dessa data.

För **Data Protection Manager** användare:

1. Om den server där säkerhetskopieringar händer fortfarande är tillgänglig, att skydda de borttagna datakällorna och använda den **återställa Data** funktion för att återställa från de gamla återställningspunkterna.
2. Om den här servern inte är tillgänglig, använda [Lägg till extern DPM](backup-azure-alternate-dpm-server.md) att använda en annan Data Protection Manager-servern för att hämta dessa data.

## <a name="prevent-attacks"></a>Förhindra attacker
Kontroller har lagts till att kontrollera att endast giltiga användare kan utföra olika åtgärder. Dessa inkluderar att lägga till ett extra lager av autentisering och upprätthålla en minsta kvarhållningsperiod för återställning.

### <a name="authentication-to-perform-critical-operations"></a>Autentisering för att utföra kritiska åtgärder
Som en del för att lägga till ett extra lager av autentisering för kritiska åtgärder kan du uppmanas att ange en PIN-kod för säkerhet när du utför **stoppa skydd med radera data** och **ändra lösenfras** åtgärder.

Ta emot den här PIN-kod:

1. Logga in på Azure Portal.
2. Bläddra till **Recovery Services-valvet** > **inställningar** > **egenskaper**.
3. Under **säkerhet PIN**, klickar du på **generera**. Då öppnas ett blad som innehåller PIN-kod anges i användargränssnittet för Azure Recovery Services-agenten.
    PIN-koden är giltig i bara fem minuter och hämtar den genereras automatiskt efter den perioden.

### <a name="maintain-a-minimum-retention-range"></a>Underhålla en minsta kvarhållningsperiod
Följande kontroller har lagts till för att säkerställa att det alltid är ett giltigt antal återställningspunkter tillgängliga:

- Dagliga bevarande, minst **sju** dagarnas kvarhållning ska göras.
- För varje vecka kvarhållning, minst **fyra** veckor av kvarhållning ska göras.
- Månatliga bevarande, minst **tre** månaders kvarhållning ska göras.
- Årlig bevarande, minst **en** års kvarhållning ska göras.

## <a name="notifications-for-critical-operations"></a>Aviseringar för kritiska åtgärder
När en kritisk åtgärd utförs, vanligtvis skickas prenumeration admin ett e-postmeddelande med information om åtgärden. Du kan konfigurera ytterligare e-postmottagare för dessa meddelanden med hjälp av Azure portal.

Säkerhetsfunktioner som nämns i denna artikel har funktioner för skydd mot riktade attacker. Det är viktigt om en attack inträffar dessa funktioner ger dig möjlighet att återställa dina data.

## <a name="troubleshooting-errors"></a>Felsökning av fel
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Principändring |Det gick inte att ändra principen för säkerhetskopiering. Fel: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x29834]. Försök igen om en stund. Kontakta Microsoft-supporten om problemet kvarstår. |**Orsak:**<br/>Det här felet kommer när säkerhetsinställningar har aktiverats, försök att minska kvarhållningsintervallet under de lägsta värdena som anges ovan och du är på version som inte stöds (versioner som stöds anges i första anteckningen i den här artikeln). <br/>**Rekommenderad åtgärd:**<br/> I det här fallet bör du ställa in kvarhållningsperioden ovan minsta kvarhållning tidsperioden (sju dagar för varje dag, fyra veckor för varje vecka, tre veckor för varje månad eller ett år för varje år) att gå vidare med principen relaterade uppdateringar. Du kan också är föredragen metod att uppdatera säkerhetskopieringsagent Azure Backup-Server och/eller DPM UR utnyttja alla säkerhetsuppdateringar. |
| Ändra lösenfras |Säkerhet angiven PIN-kod är felaktigt. (ID: 100130) Ange rätt säkerhet PIN-koden för att slutföra den här åtgärden. |**Orsak:**<br/> Det här felet kommer när du anger ogiltiga eller utgångna säkerhet PIN-kod under kritiska åtgärd (t.ex. Ändra lösenfras). <br/>**Rekommenderad åtgärd:**<br/> Du måste ange en giltig säkerhet PIN-kod för att slutföra åtgärden. PIN-koden får logga in på Azure-portalen och gå till Recovery Services-valv > Inställningar > Egenskaper > Generera säkerhet PIN-kod. Använd den här PIN-kod för att ändra lösenfras. |
| Ändra lösenfras |Åtgärden kunde inte utföras. ID: 120002 |**Orsak:**<br/>Det här felet kommer när säkerhetsinställningar har aktiverats, försök att ändra lösenfras och du är på version som inte stöds (giltigt versioner som anges i första anteckningen i den här artikeln).<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösenfrasen måste du först uppdatera backup-agenten till minimiversionen minsta 2.0.9052, Azure Backup server till minsta update 1, och/eller att minsta DPM 2012 R2 UR12 DPM eller DPM 2016 UR2 (download länkarna nedan), och ange sedan giltig säkerhet PIN-kod. PIN-koden får logga in på Azure-portalen och gå till Recovery Services-valv > Inställningar > Egenskaper > Generera säkerhet PIN-kod. Använd den här PIN-kod för att ändra lösenfras. |

## <a name="next-steps"></a>Nästa steg
* [Kom igång med Azure Recovery Services-valvet](backup-azure-vms-first-look-arm.md) att aktivera dessa funktioner.
* [Hämta den senaste Azure Recovery Services-agenten](http://aka.ms/azurebackup_agent) för att skydda Windows-datorer och skydda dina säkerhetskopierade data mot attacker.
* [Hämta den senaste Azure Backup-Server](https://aka.ms/latest_azurebackupserver) för att skydda arbetsbelastningar och skydda dina säkerhetskopierade data mot attacker.
* [Hämta UR12 för System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) eller [hämta UR2 för System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) för att skydda arbetsbelastningar och skydda dina säkerhetskopierade data mot attacker.
