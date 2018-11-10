---
title: Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet
description: Instruktioner och supportinformation för att uppgradera Azure Backup-valv till ett Recovery Services-valv.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: 431ca75a653b93342b61a9b39dc42a93270519f1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228529"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet

Den här artikeln förklarar hur du uppgraderar ett säkerhetskopieringsvalv till Recovery Services-valvet. Uppgraderingsprocessen påverkar inte alla pågående säkerhetskopieringar och inga säkerhetskopierade data går förlorade. De viktigaste aspekterna att uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet:
- Alla funktioner i ett Backup-valv finns kvar i Recovery Services-valvet.
- Recovery Services-valv har fler funktioner än Backup-valv, inklusive: bättre säkerhet, integrerad övervakning, snabbare återställningar och återställningar på objektnivå.
- Hantera objekt att säkerhetskopiera från en förbättrad och förenklad portal.
- Nya funktioner gäller endast för Recovery Services-valv.

## <a name="impact-to-operations-during-upgrade"></a>Påverkan på åtgärder under uppgraderingen

När du uppgraderar ett säkerhetskopieringsvalv till Recovery Services-valvet, påverkas inte till dina data kontrollplansåtgärder. Alla säkerhetskopieringsjobb fortsätta som vanligt, och alla aktiva återställningsjobb fortsätta utan avbrott. Under uppgraderingen hanteringsåtgärder medföra ett kort driftstopp och du kan inte skydda nya objekt eller skapa ad hoc säkerhetskopior jobb. Återställningsjobb för virtuella IaaS-datorer kör inte under uppgraderingen. Uppgradera valvet tar mindre än en timme att slutföra. När du är klar ersätter ett Recovery Services-valv Backup-valvet.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Ändringar av automatiseringstjänster och verktyg när du har uppgraderat

När du förberedde din infrastruktur för uppgradering av valvet, måste du uppdatera dina befintliga automatiseringstjänster och verktyg så att de fortsätter att fungera efter uppgraderingen.
Finns i PowerShell-cmdlet: ar referenser för den [Resource Manager-distributionsmodellen](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Innan du uppgraderar

Kontrollera följande innan du uppgraderar dina säkerhetskopieringsvalv till Recovery Service-valv.

- **Lägsta agentversion**: Om du vill uppgradera ditt valv, se till att Microsoft Azure Recovery Services MARS-agenten är minst version 2.0.9083.0. Om MARS-agenten är äldre än 2.0.9083.0 uppdaterar du agenten innan du startar uppgraderingsprocessen.
- **Instansbaserade faktureringsmodellen**: Recovery Service-valv stöder endast-instansbaserade faktureringsmodellen. Om du har ett säkerhetskopieringsvalv som använder äldre Storage-baserade faktureringsmodellen kan du konvertera faktureringsmodellen under uppgraderingen.
- **Inga åtgärder i pågående säkerhetskopieringskonfiguration**: under uppgraderingen, åtkomst till Hanteringsplanet begränsas. Slutför alla hanteringsåtgärder plan och starta sedan uppgraderingen.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Använda PowerShell-skript för att uppgradera dina valv

Du kan använda PowerShell-skript för att uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Kontrollera att du har de nödvändiga komponenterna för PowerShell att utlösa uppgradera valvet. PowerShell-skript för Backup-valv fungerar inte för Recovery Services-valv. Förbereda din miljö för att uppgradera valv:

1. Installera eller uppgradera [Windows Management Framework (WMF) till version 5](https://www.microsoft.com/download/details.aspx?id=50395) eller senare.
2. [Installera Azure PowerShell-MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Ladda ned den [PowerShell-skript](https://aka.ms/vaultupgradescript2) att uppgradera ditt valv.

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet

Använd följande skript för att uppgradera ditt valv. Följande exempelskript har förklaringar av parametrarna.

RecoveryServicesVaultUpgrade 1.0.2.ps1 **- SubscriptionID** `<subscriptionID>` **- VaultName** `<vaultname>` **-plats** `<location>` **- ResourceType** `BackupVault` **- TargetResourceGroupName** `<rgname>`

**SubscriptionID** -ID-nummer för prenumerationen för det valv som håller på att uppgraderas.<br/>
**VaultName** -namnet på Backup-valvet håller på att uppgraderas.<br/>
**Plats** -platsen för valvet håller på att uppgraderas.<br/>
**ResourceType** – använda BackupVault.<br/>
**TargetResourceGroupName** – eftersom du uppgraderar valvet till en Resource Manager-baserade distribution, ange en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en genom att ange ett nytt namn. Om du stavar namnet på en resursgrupp kan du skapa en ny resursgrupp. Mer information om resursgrupper finns i det här [översikt över resursgrupper](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Namn på resursgrupp har konfigurerat begränsningar. Se till att följa vägledningen; Det gick inte att göra det kan orsaka vault uppgraderingar misslyckas.
>
>**Azure US Government** kunder måste ställa in miljön till azureusgovernment ”eller” när du körde skriptet.
>**Azure Kina** kunder behöver att ställa in miljön till ”AzureChinaCloud” när du körde skriptet.

Följande kodavsnitt är ett exempel på vad din PowerShell-kommandot ska se ut:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Du kan också köra skriptet utan några parametrar och du uppmanas att ange indata för alla obligatoriska parametrar.

PowerShell-skriptet uppmanas du att ange dina autentiseringsuppgifter. Ange dina autentiseringsuppgifter för två gånger: en gång för Service Manager-konto och en gång för Resource Manager-konto.

### <a name="pre-requisites-checking"></a>Kontrollerar krav
När du har angett dina autentiseringsuppgifter för Azure, kontrollerar Azure att din miljö uppfyller följande krav:

- **Lägsta agentversion** -uppgraderingen säkerhetskopieringsvalv till Recovery Services-valv kräver MARS-agenten ska vara minst version 2.0.9083.0. Om du har objekt som har registrerats till ett säkerhetskopieringsvalv med en agent före 2.0.9083.0 misslyckas förutsättningskontrollen. Om kravkontrollen misslyckas kan uppdatera agenten och försök att uppgradera valvet igen. Du kan hämta den senaste versionen av agenten från [ http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe ](https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Pågående configuration jobb**: om någon konfigurerar jobb för ett säkerhetskopieringsvalv som angetts som ska uppgraderas eller registrera ett objekt, kravkontrollen misslyckas. Slutföra konfigurationen av eller avslutar registreringen objektet och starta sedan uppgraderingen valv.
- **Storage-baserade faktureringsmodellen**: Recovery Services-valv stöder-instansbaserade faktureringsmodellen. Om du kör valvuppgraderingen på ett säkerhetskopieringsvalv som använder Storage-baserade faktureringsmodellen uppmanas du att uppgradera faktureringsmodellen tillsammans med valvet. I annat fall kan du uppdatera faktureringsmodellen först och kör sedan uppgradera valvet.
- Identifiera en resursgrupp för Recovery Services-valvet. Om du vill dra nytta av distributionsfunktioner för Resource Manager-, måste du placera ett Recovery Services-valv i en resursgrupp. Om du inte vet vilken resursgrupp att använda, ange ett namn och uppgraderingsprocessen skapas en resursgrupp för dig. Uppgraderingsprocessen associerar även valvet med den nya resursgruppen.

När uppgraderingen är klar kontrollerar kraven uppmanas processen du att starta uppgradering av valvet. När du har bekräftat tar uppgraderingsprocessen vanligtvis cirka 15-20 minuter för att slutföra beroende på storleken på ditt valv. Om du har ett stort valv, kan det ta upp till 90 minuter att uppgradera.

## <a name="managing-your-recovery-services-vaults"></a>Hantera dina Recovery Services-valv

Följande skärmbilder visar en ny Recovery Services-valvet som uppgraderats från Backup-valv i Azure-portalen. Den första skärmen visar instrumentpanelen för valvet som visar viktiga entiteter för valvet.

![exempel på Recovery Services-valv som uppgraderats från ett säkerhetskopieringsvalv](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Den andra skärmen visar hjälp länkarna som är tillgängliga som hjälper dig att komma igång med Recovery Services-valvet.

![hjälplänkar i Snabbstart-bladet](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Aktiviteter efter uppgradering
Recovery Services-valv stöder konfiguration av återskrivningscachens tidszonsinformation i säkerhetskopieringsprincipen. När valvet har uppgraderats, gå till principer för säkerhetskopiering från inställningsmenyn och uppdatera informationen om tidszonen för var och en av principerna som konfigureras i valvet. Den här skärmen visar redan Säkerhetskopieringsschemat tiden som angetts som per lokala tidszon som används när du har skapat principen. 

## <a name="enhanced-security"></a>Förbättrad säkerhet

När ett säkerhetskopieringsvalv uppgraderas till ett Recovery Services-valv, aktiveras automatiskt säkerhetsinställningarna för det valvet. När säkerhetsinställningarna finns på vissa åtgärder, till exempel tar bort säkerhetskopior eller om du ändrar en lösenfras måste en [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN-kod. Mer information om förbättrad säkerhet finns i artikeln [säkerhetsfunktioner som skyddar hybridsäkerhetskopieringar](backup-azure-security-feature.md). 

När Förbättrad säkerhet är aktiverat, sparas data upp till 14 dagar efter punkt återställningsinformation har tagits bort från valvet. Kunderna debiteras för lagring av den här säkerhetsdata. Security datakvarhållning gäller återställningspunkter som vidtas för Azure Backup agent, Azure Backup Server och System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Samla in data i valvet

När du har uppgraderat till ett Recovery Services-valv, konfigurera rapporter för Azure Backup (för virtuella IaaS-datorer och Microsoft Azure Recovery Services (MARS)) och använda Power BI för att komma åt rapporten. Mer information om datainsamling finns i artikeln [konfigurera Azure Backup-rapporter](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Påverkar uppgraderingsplanen min pågående säkerhetskopiering?**</br>
Nej. Säkerhetskopiorna pågående fortsätter utan avbrott under och efter uppgraderingen.

**Om jag inte planerar att uppgradera snart, vad händer med Mina valv?**</br>
Eftersom alla nya funktioner gäller endast för Recovery Services-valv, bör du uppgradera ditt valv. Från den 1 September 2017 börjar Microsoft automatiskt – uppgradera säkerhetskopieringsvalv till Recovery Services-valv. Efter November 30,2017, kan du inte längre skapa Backup-valv med hjälp av PowerShell. Ditt valv kan uppgraderas automatiskt som helst däremellan. Microsoft rekommenderar att du uppgraderar ditt valv så snart som möjligt.

**Vad betyder detta uppgradering för Mina befintliga verktyg?**</br>
Uppdatera ditt verktyg till Resource Manager-distributionsmodellen. Recovery Services-valv har skapats för använda i Resource Manager-distributionsmodellen. Det är viktigt att planera för distributionsmodellen i Resource Manager och redovisning för skillnaden i ditt valv. 

**Under uppgraderingen, finns det stora driftstopp?**</br>
Det beror på hur många resurser som har uppgraderats. För mindre distributioner (ett tiotal skyddade instanser) bör hela uppgraderingen tar mindre än 20 minuter. Det bör ta högst en timme för större distributioner.

**Kan jag återställa när du har uppgraderat?**</br>
Nej. Återställning stöds inte när resurserna har uppgraderats.

**Kan jag verifiera prenumeration eller resurser för att se om de kan uppgraderingen?**</br>
Ja. Det första steget i uppgraderingen verifierar att resurserna som klarar av uppgraderingen. Om det inte går att valideringen av förutsättningar, får du meddelanden om alla orsaker uppgraderingen inte kan slutföras.

**Kan jag uppgradera min CSP-baserade Backup-valv?**</br>
Nej. För närvarande kan uppgradera du inte CSP-baserade säkerhetskopieringsvalv. Vi lägger till stöd för uppgradering av CSP-baserade Backup-valv i nästa versioner.

**Kan jag visa mina klassiska efter uppgradering valv?**</br>
Nej. Du kan inte visa eller hantera ditt klassiska valv efter uppgradering. Du kommer endast att kunna använda den nya Azure-portalen för alla hanteringsåtgärder på valvet.

**Min uppgraderingen misslyckades, men den dator som lagras agenten kräver uppdatering är inte finns längre. Vad gör jag i så fall?**</br>
Om du vill använda arkivet kommer säkerhetskopior av den här datorn för långsiktig kvarhållning, så du inte att uppgradera valvet. I framtida versioner kommer vi lägga till stöd för uppgradering av sådana ett valv.
Om du inte behöver lagra säkerhetskopior av den här datorn längre sedan du avregistrera den här datorn från valvet och gör om uppgraderingen.

**Varför kan jag inte se informationen om jobb för mina resurser efter uppgraderingen?**</br>
Övervakning av säkerhetskopior (MARS-agenten och IaaS) är en ny funktion som du får när du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valvet. Övervakningsinformation tar upp till 12 timmar att synkronisera med tjänsten.

**Hur gör jag för att rapportera ett problem?**</br>
Observera att åtgärds-ID anges i felet om en del av valvuppgraderingen misslyckas. Microsoft Support fungerar proaktivt för att lösa problemet. Du kan kontakta Support eller maila rsvaultupgrade@service.microsoft.com med ditt prenumerations-ID, valvnamnet och åtgärds-ID. Vi försöker lösa problemet så snabbt som möjligt. Försök inte igen såvida inte uttryckligen har angett att göra detta av Microsoft.


## <a name="next-steps"></a>Nästa steg
Använd följande artikel:</br>
[Säkerhetskopiera en IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[Säkerhetskopiera en Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Säkerhetskopiera en Windows Server](backup-configure-vault.md).
