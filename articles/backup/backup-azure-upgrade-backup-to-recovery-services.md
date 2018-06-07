---
title: Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet
description: Anvisningar och supportinformation för att uppgradera din Azure Backup-valv till Recovery Services-valvet.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: 551bc2aa4ff80feb3f28b5698e25dfd1b03dc870
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607345"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet

Den här artikeln förklarar hur du uppgraderar ett säkerhetskopieringsvalv till Recovery Services-valvet. Uppgraderingen påverkar inte körs säkerhetskopieringsjobb och inga säkerhetskopierade data går förlorade. De främsta orsakerna till att uppgradera ett säkerhetskopieringsvalv till Recovery Services-valv:
- Alla funktioner i ett säkerhetskopieringsvalv behålls i Recovery Services-valvet.
- Recovery Services-valv har fler funktioner än säkerhetskopieringsvalv, inklusive: bättre säkerhet, integrerad övervakning, snabbare återställningar och återställning på objektnivå.
- Hantera Säkerhetskopiera objekt från en bättre och förenklad portal.
- Nya funktioner gäller endast för Recovery Services-valv.

## <a name="impact-to-operations-during-upgrade"></a>Påverkan på åtgärder under uppgradering

När du uppgraderar ett säkerhetskopieringsvalv till Recovery Services-valvet, finns det ingen inverkan på din plan dataåtgärder. Alla säkerhetskopieringsjobb Fortsätt som vanligt, och alla aktiva jobb fortsätta utan avbrott. Under uppgraderingen hanteringsåtgärder innebära ett kort driftstopp och du kan inte skydda nya objekt eller skapa ad hoc säkerhetskopieringar jobb. Återställningsjobb för IaaS-VM körs inte under uppgraderingen. Uppgraderingen valvet tar en timma att slutföra. När du är klar ersätter ett Recovery Services-valv Backup-valvet.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Ändringar i dina automation och verktyget efter uppgraderingen

När du förberedde din infrastruktur för valvet uppgraderingen, måste du uppdatera din befintliga automation eller verktygsuppsättning så att den fortsätter att fungera efter uppgraderingen.
Finns i PowerShell-cmdlets referenser för den [Resource Manager-distributionsmodellen](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Innan du uppgraderar

Kontrollera följande innan du uppgraderar din Backup-valv till återställningstjänsten valv.

- **Minsta agentversion**: Om du vill uppgradera ditt valv, kontrollera att Microsoft Azure Recovery Services MARS-agenten är minst version 2.0.9083.0. Om MARS-agenten är äldre än 2.0.9083.0, uppdatera agenten innan du startar uppgraderingsprocessen.
- **Instans-baserade faktureringsmodell som tillämpas**: återställningstjänsten valv stöder bara den fakturering instans-modellen. Om du har ett säkerhetskopieringsvalv som använder den äldre fakturering Storage-baserade-modellen, konvertera vilken faktureringsmodell som tillämpas under uppgraderingen.
- **Inga åtgärder i pågående säkerhetskopieringskonfigurationen**: under uppgraderingen, åtkomst till management-plan är begränsad. Slutför alla hanteringsåtgärder plan och starta sedan uppgraderingen.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Med hjälp av PowerShell-skript för att uppgradera ditt valv

Du kan använda PowerShell-skript för att uppgradera din Backup-valv till Recovery Services-valv. Kontrollera att du har de nödvändiga komponenterna i PowerShell som utlöser uppgraderingen valvet. PowerShell-skript för säkerhetskopieringsvalv fungerar inte för Recovery Services-valv. Förbereda din miljö för att uppgradera valv:

1. Installera eller uppgradera [Windows Management Framework (WMF) till version 5](https://www.microsoft.com/download/details.aspx?id=50395) eller senare.
2. [Installera Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Hämta den [PowerShell-skript](https://aka.ms/vaultupgradescript2) så här uppgraderar du ditt valv.

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet

Du kan använda följande skript för att uppgradera ditt valv. Följande exempelskript har förklaringar av parametrar.

RecoveryServicesVaultUpgrade 1.0.2.ps1 **- SubscriptionID** `<subscriptionID>` **- VaultName** `<vaultname>` **-plats** `<location>` **- ResourceType** `BackupVault` **- TargetResourceGroupName** `<rgname>`

**SubscriptionID** -prenumeration ID-numret för valvet som uppgraderas.<br/>
**VaultName** -namnet på det säkerhetskopieringsvalv som uppgraderas.<br/>
**Plats** -platsen för valvet som uppgraderas.<br/>
**ResourceType** -Använd BackupVault.<br/>
**TargetResourceGroupName** - eftersom du uppgraderar valvet Resource Manager-baserade distribution, ange en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en genom att ange ett nytt namn. Om du skriver namnet på en resursgrupp kan du skapa en ny resursgrupp. Om du vill veta mer om resursgrupper kan läsa det här [översikt över resursgrupper](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Namn för resursgruppen har begränsningar. Se till att följa riktlinjerna; Det gick inte att göra det kan orsaka valvet uppgraderingar misslyckas.
>
>**Azure som tillhör amerikanska myndigheter** kunder måste ställa in miljön till ”AzureUSGovernment” när skriptet kördes.
>**Azure Kina** kunder måste ställa in miljön till ”AzureChinaCloud” när skriptet kördes.

Följande kodavsnitt är ett exempel på vad din PowerShell-kommandot ska se ut som:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Du kan också köra skriptet utan några parametrar och du uppmanas att ange indata för alla obligatoriska parametrar.

PowerShell-skriptet uppmanas du att ange dina autentiseringsuppgifter. Ange dina autentiseringsuppgifter två gånger: en gång för Service Manager-konto och en gång för Resource Manager-kontot.

### <a name="pre-requisites-checking"></a>Kontroll av förutsättningar
När du har angett dina autentiseringsuppgifter för Azure, kontrollerar Azure att din miljö uppfyller följande krav:

- **Minsta agentversion** -säkerhetskopieringsvalv Recovery Services-valv uppgraderar MARS-agenten vara på minst version 2.0.9083.0. Om du har artiklar som registrerats för ett säkerhetskopieringsvalv med en agent före 2.0.9083.0 misslyckas förutsättningskontrollen. Om kravkontrollen misslyckas, uppdatera agenten och försök att uppgradera valvet igen. Du kan hämta den senaste versionen av agenten från [ http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe ](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Pågående configuration jobb**: om någon konfigurerar jobbet för ett säkerhetskopieringsvalv som angetts som ska uppgraderas eller registrera en artikel kravkontrollen misslyckas. Slutföra konfigurationen, eller avsluta registrering av objektet och sedan startar uppgraderingsprocessen valvet.
- **Storage-baserade faktureringsmodell som tillämpas**: Recovery Services-valv stöder instans-baserade fakturering modellen. Om du kör uppgraderingen valvet på ett säkerhetskopieringsvalv som använder Storage-baserade fakturering modellen, uppmanas du att uppgradera faktureringsmodellen tillsammans med valvet. Annars kan du uppdatera faktureringsmodellen först och sedan uppgraderar valvet.
- Identifiera en resursgrupp för Recovery Services-valvet. Om du vill dra nytta av funktionerna för Resource Manager distribution, måste du placera ett Recovery Services-valv i en resursgrupp. Om du inte vet vilken resursgrupp genom att ange ett namn och uppgraderingsprocessen skapas resursgruppen. Uppgraderingsprocessen associerar även valvet med den nya resursgruppen.

När uppgraderingen är klar kontrollerar du att förutsättningar uppmanas processen du att starta uppgraderingen valvet. När du har bekräftat ta uppgraderingsprocessen brukar cirka 15-20 minuter för att slutföra beroende på storleken på ditt valv. Om du har ett stort valv kan ta upp till 90 minuter.

## <a name="managing-your-recovery-services-vaults"></a>Hantera Recovery Services-valv

Följande skärmar visas ett nytt Recovery Services-valv som uppgraderats från säkerhetskopieringsvalvet i Azure-portalen. Den första skärmen visar valvet instrumentpanelen som visar viktiga entiteter för valvet.

![exempel på Recovery Services-valv som uppgraderats från ett säkerhetskopieringsvalv](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Den andra skärmen visar hjälp länkarna som är tillgängliga som hjälper dig att komma igång med Recovery Services-valvet.

![länkar i bladet Snabbstart](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Åtgärder efter uppgradering
Recovery Services-ventilen har stöd för att ange Tidszonsinformationen i princip för säkerhetskopiering. När valvet har uppgraderats, gå till principer för säkerhetskopiering från valvet inställningsmenyn och uppdatera informationen om tidszonen för var och en av de principer som konfigurerats i valvet. Den här skärmen visas redan Säkerhetskopieringsschemat tiden som angetts som per lokal tidszon som används när du har skapat principen. 

## <a name="enhanced-security"></a>Förbättrad säkerhet

När ett säkerhetskopieringsvalv uppgraderas till Recovery Services-valvet, aktiveras automatiskt säkerhetsinställningarna för det valvet. När säkerhetsinställningarna finns på vissa åtgärder, till exempel ta bort säkerhetskopior eller ändra en lösenfras kräva en [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN-kod. Mer information om förbättrad säkerhet finns i artikeln [säkerhetsfunktioner för att skydda hybrid säkerhetskopieringar](backup-azure-security-feature.md). 

När Förbättrad säkerhet är aktiverad bevaras data upp till 14 dagar efter punkt återställningsinformation har tagits bort från valvet. Kunder som debiteras för lagring av den här säkerhetsdata. Säkerhet datalagring gäller återställningspunkter för Azure Backup-agenten, Azure Backup Server och System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Samla in data på ditt valv

När du uppgraderar till Recovery Services-valvet, konfigurera rapporter för Azure Backup (för IaaS-VM och Microsoft Azure Recovery Services MARS-) och använda Power BI åtkomst till rapporter. Mer information om insamling av data finns i artikeln [konfigurera Azure Backup rapporterar](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Påverkar uppgraderingsplanen mina pågående säkerhetskopior?**</br>
Nej. Säkerhetskopiorna pågående fortsätta utan avbrott under och efter uppgraderingen.

**Vad händer med min valv om jag inte uppgradera snart kan?**</br>
Eftersom alla nya funktioner som gäller endast för Recovery Services-valv, bör du uppgradera ditt valv. Startar den 1 September 2017 börjar Microsoft automatiskt uppgradera säkerhetskopieringsvalv till Recovery Services-valv. Efter November 30,2017, kan du inte längre skapa säkerhetskopieringsvalv med hjälp av PowerShell. Ditt valv kan uppgraderas automatiskt i mellan. Microsoft rekommenderar att du uppgraderar ditt valv så snart som möjligt.

**Vad betyder det här uppgradering för Mina befintliga verktygsuppsättning?**</br>
Uppdatera din verktygsuppsättning till Resource Manager-distributionsmodellen. Recovery Services-valv skapades för använder i Resource Manager-distributionsmodellen. Det är viktigt att planera för Resource Manager-distributionsmodellen och redovisning för skillnaden i ditt valv. 

**Under uppgraderingen, är det mycket driftavbrott?**</br>
Det beror på antalet resurser som har uppgraderats. För mindre distributioner (några tiotal skyddade instanser), bör hela uppgraderingen ta mindre än 20 minuter. För större distributioner bör ta högst en timme.

**Kan jag återställa efter uppgraderingen?**</br>
Nej. Återställning stöds inte när resurserna har uppgraderats.

**Kan jag verifiera min prenumeration eller resurser för att se om de är kompatibelt med uppgraderingen?**</br>
Ja. Det första steget i uppgraderingen validerar att resurserna för uppgraderingen. Om det inte går att valideringen av förutsättningar, kan du få meddelanden på grund av alla inte går att slutföra uppgraderingen.

**Kan jag uppgradera min CSP-baserade Backup-valvet?**</br>
Nej. För närvarande kan uppgradera du inte CSP-baserade säkerhetskopieringsvalv. Vi lägger till stöd för uppgradering av CSP-baserade säkerhetskopieringsvalv i nästa versioner.

**Kan jag visa min klassiska valvet efter uppgradering?**</br>
Nej. Du kan inte visa eller hantera din klassiska valvet efter uppgradering. Du kommer bara att kunna använda den nya Azure-portalen för alla hanteringsåtgärder på valvet.

**Min uppgraderingen misslyckades, men den dator som lagras agenten kräver uppdatering är inte finns längre. Vad gör jag i så fall?**</br>
Om du behöver använda arkivet säkerhetskopior av den här datorn för långsiktig kvarhållning och du inte uppgradera valvet. I kommande versioner vi lägga till stöd för uppgradering av sådana valvet.
Om du inte behöver lagra säkerhetskopior av den här datorn längre sedan du avregistrera den här datorn från valvet och gör om uppgraderingen.

**Varför visas inte jobb-information för mina resurser efter uppgraderingen?**</br>
Övervakning för säkerhetskopiering (MARS-agenten och IaaS) är en ny funktion som du får när du uppgraderar din Backup-valvet till Recovery Services-valvet. Övervakning information tar upp till 12 timmar att synkronisera med tjänsten.

**Hur rapporterar problem?**</br>
Observera åtgärds-ID som anges i felet om en del av valvet uppgraderingen misslyckas. Microsoft-supporten fungerar proaktivt för att lösa problemet. Du kan nå ut till Support eller skicka e-post på rsvaultupgrade@service.microsoft.com med prenumerations-ID, valvnamnet och åtgärds-ID. Vi kommer att försöka lösa problemet så snabbt som möjligt. Försök inte igen såvida inte uttryckligen uppmanas att göra det från Microsoft.


## <a name="next-steps"></a>Nästa steg
Använd följande artikel till:</br>
[Säkerhetskopiera en IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[Säkerhetskopiera en Azure Backup-Server](backup-azure-microsoft-azure-backup.md)</br>
[Säkerhetskopiera Windows Server](backup-configure-vault.md).
