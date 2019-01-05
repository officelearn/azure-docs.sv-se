---
title: Uppdatering av Azure Stack 1811 | Microsoft Docs
description: Läs mer om 1811 uppdateringen för integrerade Azure Stack-system, inklusive nyheter, kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2018
ms.author: sethm
ms.reviewer: adepue
ms.openlocfilehash: 15f358f76504436dd6a3cf6a39b10531a9e1b376
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54055174"
---
# <a name="azure-stack-1811-update"></a>Uppdatering av Azure Stack 1811

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln beskriver innehållet i 1811 uppdateringspaketet. Uppdateringspaketet innehåller förbättringar, korrigeringar och nya funktioner för den här versionen av Azure Stack. Den här artikeln kan du också beskrivs kända problem i den här versionen, och innehåller en länk så att du kan ladda ned uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]  
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure Stack 1811 update build-nummer är **1.1811.0.101**.

## <a name="hotfixes"></a>Snabbkorrigeringar

Azure Stack snabbkorrigeringar med jämna mellanrum. Se till att installera den [senaste Azure Stack-snabbkorrigeringen](#azure-stack-hotfixes) för 1809 innan du uppdaterar Azure Stack till 1811.

> [!TIP]  
> Prenumerera på följande *RRS* eller *Atom* flöden, hålla jämna steg med Azure Stack snabbkorrigeringar:
> - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
> - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...

### <a name="azure-stack-hotfixes"></a>Azure Stack-snabbkorrigeringar

- **1809**: [KB 4481548 – Azure Stack-snabbkorrigeringen 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Inga aktuella snabbkorrigeringen är tillgänglig.

## <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Under installationen av uppdateringen 1811 bör du se till att alla instanser av administratörsportalen är stängda. Användarportalen kan förbli öppen, men admin portal måste stängas.

- Förbereda distributionen av Azure Stack för Azure Stack-tillägg-värden. Förbered datorn med hjälp av följande riktlinjer: [Förbereda för tillägget för värd för Azure Stack](azure-stack-extension-host-prepare.md). 
 
- Installera den [senaste Azure Stack-snabbkorrigeringen](#azure-stack-hotfixes) för 1809 innan du uppdaterar till 1811.

- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) med följande parametrar för att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittades, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd.  

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Om du inte har tillägget värdkrav uppfylls, den `Test-AzureStack` utdata visar följande meddelande: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- Azure Stack 1811 uppdateringen kräver att du har importerat de värden för obligatoriska tilläggscertifikat korrekt till Azure Stack-miljön. Om du vill fortsätta med installationen av uppdateringen 1811, måste du importera SSL-certifikat som krävs för tillägget värden. Om du vill importera certifikat, se [i det här avsnittet](azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Om du ignorera alla varningar och fortfarande välja att installera uppdateringen 1811, misslyckas uppdateringen i ungefär en timma med följande meddelande:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    När du har importerat de obligatoriska tilläggscertifikat värd, kan du återuppta 1811 uppdateringen från administratörsportalen. Medan Azure Stack-operatörer att placera skalningsenheten i underhållsläge under uppdateringen rekommenderas, bör inte ett fel på grund av att de saknas värden tilläggscertifikat påverka befintliga arbetsbelastningar eller tjänster.  

    Användarportalen för Azure Stack är inte tillgänglig under installationen av uppdateringen, medan tillägget värden konfigureras. Konfigurationen av tillägget värd kan ta upp till 5 timmar. Under den tiden kan du kontrollera status för en uppdatering eller återuppta en misslyckad uppdatering installationen med hjälp av [Azure Stack-administratören PowerShell eller Privilegierade slutpunkten](azure-stack-monitor-update.md).

## <a name="new-features"></a>Nya funktioner

Den här uppdateringen innehåller följande nya funktioner och förbättringar för Azure Stack:

- Den här versionen kan den [tillägget värden](azure-stack-extension-host-prepare.md) är aktiverad. Tillägget värden förenklar nätverksintegration och förbättrar säkerhetspositionen för Azure Stack.

- Stöd för autentisering med Active Directory Federation Services (AD FS) har lagts till när du använder Azure CLI i synnerhet. Mer information finns i [Använd API-versionsprofiler med Azure CLI i Azure Stack](./user/azure-stack-version-profiles-azurecli2.md)

- Tillagt stöd för tjänstens huvudnamn med hjälp av en klienthemlighet med Active Directory Federation Services (AD FS). Mer information finns i [skapa tjänstens huvudnamn för AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Den här versionen lägger till stöd för följande Azure Storage Service-API-versioner: **2017-07-29**, **2017-11-09**. Stöd har också lagts till för följande Azure Storage Resource Provider API-versioner: **2016-05-01**, **2016-12-01**, **2017-06-01**, och **2017-10-01**. Mer information finns i [Azure Stack-lagring: Skillnader och överväganden](./user/azure-stack-acs-differences.md).

- Kommandon har lagts till nya privilegierad slutpunkt att uppdatera och ta bort tjänsten principer för AD FS. Mer information finns i [skapa tjänstens huvudnamn för AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Har lagts till nya skala enhet noden åtgärder som gör att Azure Stack-operatör att starta, stoppa och stänga av en scale unit-nod. Mer information finns i [skala enhet noden åtgärder i Azure Stack](azure-stack-node-actions.md).

- Lägga till en ny region egenskapsbladet som visar information om registrering av miljön. Du kan visa den här informationen genom att klicka på den **Regionshantering** rutan på standardinstrumentpanel i administratörsportalen och sedan välja **egenskaper**.

- Lägga till en ny privilegierad slutpunkt-kommando för att uppdatera BMC-autentiseringsuppgifter med användarnamn och lösenord som används för att kommunicera med de fysiska datorerna. Mer information finns i [uppdatera hanteringsstyrenheten för baskort \(BMC) autentiseringsuppgifter](azure-stack-rotate-secrets.md).

- Tillagd möjlighet att komma åt Azure-översikten via ikonen Hjälp och support (frågetecken) i det övre högra hörnet av administratörs- och portaler liknar det sätt som den är tillgänglig i Azure-portalen.

- Lägga till en förbättrad Marketplace-hantering av användare. Uppladdningen att publicera ett Marketplace-objekt i en frånkopplad miljö har förenklats till ett steg i stället för att ladda upp avbildningen och Marketplace-paketet separat. Överförda produkten kommer också att visas i bladet för hantering i Marketplace. Mer information finns i [i det här avsnittet](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher). 

- Den här versionen minskar den nödvändiga underhållsperiod för hemliga rotation genom att lägga till Rotera endast externa certifikat under [hemliga rotation av Azure Stack](azure-stack-rotate-secrets.md).

- [Azure Stack PowerShell](azure-stack-powershell-install.md) har uppdaterats till version 1.6.0 eller senare. Uppdateringen har stöd för nya storage-relaterade funktioner i Azure Stack. Mer information finns i viktig information om den [Azure Stack-administrationsmodulen 1.6.0 eller senare i PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureStack/1.6.0) information om uppdatering eller installation av Azure Stack PowerShell finns i [installera PowerShell för Azure Stack](azure-stack-powershell-install.md).

- Hanterade diskar är nu aktiverad som standard när du skapar virtuella datorer med Azure Stack-portalen. Se den [kända problem](#known-issues-post-installation) för ytterligare steg som krävs för Managed Disks att undvika att skapa en VM-fel.

- Den här versionen innehåller aviseringen **reparera** åtgärder för Azure Stack-operatör. Vissa aviseringar i 1811 tillhandahåller en **reparera** knappen i aviseringen som du kan välja för att lösa problemet. Mer information finns i [övervaka hälsotillstånd och aviseringar i Azure Stack](azure-stack-monitor-health.md).

## <a name="fixed-issues"></a>Åtgärdade problem

<!-- TBD - IS ASDK --> 
- Ett problem där den offentliga IP-adress mätaren användningsdata visade samma har åtgärdats **EventDateTime** värde för varje post i stället för den **TimeDate** stämpel som visar när posten skapades. Du kan nu använda dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!-- 3099544 – IS, ASDK --> 
- Ett problem uppstod när du skapar en ny virtuell dator (VM) med hjälp av Azure Stack-portalen har åtgärdats. Välja VM-storlek som orsakade den **USD/månad** kolumn att visa en **ej tillgänglig** meddelande. Den här kolumnen visas inte längre. Visar den virtuella datorn stöds prissättning kolumn inte i Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Ett problem har åtgärdats där administratörsportalen vid åtkomst till information om alla användarprenumeration efter stänga bladet och klicka på **senaste**, fanns inte användaren prenumerationens namn. Användarnamn för prenumerationen visas nu.

<!-- 3060156 - IS ASDK --> 
- Ett problem har åtgärdats i både den administratörs- och analytikerportaler: när du klickar på portalinställningar och välja **ta bort alla inställningar och privata instrumentpaneler** inte fungerar som förväntat och ett felmeddelande visas. Det här alternativet nu fungerar korrekt.

<!-- 2930799 - IS ASDK --> 
- Ett problem har åtgärdats i både den administratörs- och analytikerportaler: under **alla tjänster**, tillgången **DDoS-skyddsplaner** angavs felaktigt. Det är inte tillgängliga i Azure Stack. Listan har tagits bort.
 
<!--2760466 – IS  ASDK --> 
- Ett problem uppstod när du har installerat en ny Azure Stack-miljö har åtgärdats där aviseringen-värde som anger **aktivering krävs** visas inte. Nu ska visas.

<!--1236441 – IS  ASDK --> 
- Ett problem som förhindrade tillämpning av RBAC-principer till en användargrupp när du använder AD FS har åtgärdats.

<!--3463840 - IS, ASDK --> 
- Ett problem har åtgärdats med infrastruktur säkerhetskopieringar misslyckas på grund av ett otillgängligt filservern från det offentliga VIP-nätverket. Den här snabbkorrigeringen flyttar infrastruktur säkerhetskopieringstjänsten tillbaka till nätverkets infrastruktur för offentliga. Om du tillämpar senast [Azure Stack snabbkorrigering för 1809](#azure-stack-hotfixes) som löser problemet, 1811 uppdateringen inte gör några ändringar. 

<!-- 2967387 – IS, ASDK --> 
- Ett problem där visas det konto som du använde för att logga in på portalen för Azure Stack-administratör eller användare som har åtgärdats **Oidentifierad användare**. Det här meddelandet visades när kontot inte har antingen en **första** eller **senaste** namnen.   

<!--  2873083 - IS ASDK --> 
- Åtgärdat ett problem i vilka använda portal för att skapa en VM-skalningsuppsättning (VMSS) som orsakade den **instansstorlek** listrutan för att inte in korrekt när du använder Internet Explorer. Den här webbläsaren nu fungerar korrekt.  

<!-- 3190553 - IS ASDK -->
- Ett problem som genererade bort störande aviseringar som tyder på att en infrastruktur-Rollinstans inte var tillgänglig eller skala enhet noden var offline har åtgärdats.

## <a name="changes"></a>Ändringar

- Ett nytt sätt att visa och redigera kvoter i en plan introducerades i 1811. Mer information finns i [visa en befintlig kvot](azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Säkerhetsförbättringar i den här uppdateringen resultera i en ökning av säkerhetskopiering storleken på rollen för katalogen. Uppdaterad ändrar storlek på riktlinjer för extern lagringsplats, finns i den [dokumentation om backup-infrastruktur](azure-stack-backup-reference.md#storage-location-sizing). Den här ändringen resulterar i en längre tid att slutföra säkerhetskopieringen på grund av större storlek dataöverföringen. Den här ändringen påverkar integrerade system. 

- Den befintliga program-cmdleten för att hämta nycklar för BitLocker-återställning har bytt namn i 1811 från Get-AzsCsvsRecoveryKeys till Get-AzsRecoveryKeys. Mer information om hur du hämtar återställningsnycklar BitLocker finns i [anvisningar om hur du hämtar nycklarna](azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

Den här uppdateringen installeras följande säkerhetsuppdateringar:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)


Klicka på föregående länkarna för mer information om dessa säkerhetsrisker eller finns i Microsoft Knowledge Base-artiklar [4467684](https://support.microsoft.com/help/4467684).

## <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- Uppdatera under installationen av 1811, se till att alla instanser av administratörsportalen stängs under den här tiden. Användarportalen kan förbli öppen, men admin portal måste stängas.

- När du kör [Test-AzureStack](azure-stack-diagnostic-test.md)om antingen den **AzsInfraRoleSummary** eller **AzsPortalApiSummary** testet misslyckas, uppmanas du att köra  **Test-AzureStack** med den `-Repair` flaggan.  Om du kör det här kommandot misslyckas med följande felmeddelande visas:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  Det här problemet korrigeras i en framtida version.

- Använd portalen för Azure Stack är inte tillgänglig under installationen av uppdateringen 1811 medan tillägget värden konfigureras. Konfigurationen av tillägget värd kan ta upp till 5 timmar. Under den tiden kan du kontrollera status för en uppdatering eller återuppta en misslyckad uppdatering installationen med hjälp av [Azure Stack-administratören PowerShell eller Privilegierade slutpunkten](azure-stack-monitor-update.md). 

- Under installationen av uppdateringen 1811 på portalens användarinstrumentpanel kanske inte är tillgänglig och anpassningar kan gå förlorade. Du kan återställa instrumentpanelen till standardinställningen när uppdateringen har slutförts genom att öppna inställningarna för användarportalen och välja **Återställ standardinställningarna**.

- När du kör [Test-AzureStack](azure-stack-diagnostic-test.md), visas ett varningsmeddelande från den Hanteringsstyrenheten för baskort (BMC). Du kan ignorera den här varningen.

- <!-- 2468613 - IS --> Under installationen av den här uppdateringen kan du se aviseringar med rubriken `Error – Template for FaultType UserAccounts.New is missing.` du kan ignorera dessa aviseringar. Aviseringarna stängs automatiskt när installationen av uppdateringen har slutförts.

- <!-- 3139614 | IS --> Om du har använt en uppdatering till Azure Stack från din OEM, den **uppdatering** meddelande får inte visas i Azure Stack-administratörsportalen. Om du vill installera Microsoft update, hämta och importera den manuellt med hjälp av anvisningarna här [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).

## <a name="post-update-steps"></a>Steg efter uppdateringen

- Installera alla tillämpliga snabbkorrigeringar efter installationen av uppdateringen. Mer information finns i [snabbkorrigeringar](#hotfixes), samt våra [Servicing princip](azure-stack-servicing-policy.md).  

- Hämta data på rest-krypteringsnycklar och lagra dem på ett säkert sätt utanför Azure Stack-distributioner. Följ den [anvisningar om hur du hämtar nycklarna](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

Här följer efter installation kända problem för den här build-versionen.

### <a name="portal"></a>Portalen

<!-- 2930820 - IS ASDK --> 
- Om du söker efter ”Docker”, i både den administratörs- och analytikerportaler är returnerade felaktigt objektet. Det är inte tillgängliga i Azure Stack. Om du försöker skapa den, visas ett blad med fel uppgift. 

<!-- 2931230 – IS  ASDK --> 
- Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

<!-- TBD - IS ASDK --> 
- Två administrativa prenumerationstyper som introducerades med version 1804 ska inte användas. Typerna av prenumeration är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den **Standard Provider** prenumerationstyp.

<!-- TBD - IS ASDK --> 
- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan först ta bort användarresurser eller hela resursgruppen och sedan ta bort prenumerationer för användare.

<!-- TBD - IS ASDK --> 
- Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda [PowerShell för att kontrollera behörigheterna](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Hälsa och övervakning

<!-- 1264761 - IS ASDK -->  
- Du kan se aviseringar för den **hälsotillstånd controller** komponent som har följande information:  

    - Avisera #1:
       - NAMN:  Infrastrukturrollen defekt
       - ALLVARLIGHETSGRAD: Varning
       - KOMPONENT: Hälsotillstånd controller
       - DESCRIPTION (BESKRIVNING): Kontrollanten hälsotillstånd pulsslag skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.  

    - Avisera #2:
       - NAMN:  Infrastrukturrollen defekt
       - ALLVARLIGHETSGRAD: Varning
       - KOMPONENT: Hälsotillstånd controller
       - DESCRIPTION (BESKRIVNING): Kontrollanten hälsotillstånd fel skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.

    Båda aviseringarna kan ignoreras. De stängs automatiskt över tid.  

### <a name="compute"></a>Compute

- När du skapar en ny Windows virtuell dator (VM), den **inställningar** bladet kräver att du väljer en offentlig inkommande port för att kunna fortsätta. I 1811, den här inställningen är obligatorisk, men har ingen effekt. Det beror på att funktionen är beroende av Brandvägg för Azure som inte har implementerats i Azure Stack. Du kan välja **inga offentliga inkommande portar**, eller någon av de andra alternativen att fortsätta med skapa en virtuell dator. Inställningen har ingen effekt.

<!-- 3235634 – IS, ASDK -->
- Distribuera virtuella datorer med storlekarna som innehåller en **v2** suffix, till exempel **Standard_A2_v2**, anger du suffix som **Standard_A2_v2** (gemener v). Använd inte **Standard_A2_V2** (versaler V). Detta fungerar i globala Azure och är en inkonsekvens i Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- När du använder den [ **Lägg till AzsPlatformImage** cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage), måste du använda den **- OsUri** parameter som lagringskontot URI där disken har laddats upp. Om du använder den lokala sökvägen på disken kan misslyckas cmdleten med följande fel: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- När du använder portalen för att skapa virtuella datorer (VM) i en premium VM-storlek (DS, Ds_v2, FS, FSv2), skapas den virtuella datorn i ett standardlagringskonto. Du skapar i ett standardlagringskonto påverkar inte samma funktioner, IOPs, eller fakturering. Du kan ignorera varningen där det står: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- Upplevelse VM scale set (VMSS) när du skapar innehåller 7.2 CentOS-baserade som ett alternativ för distribution. Eftersom avbildningen inte är tillgänglig på Azure Stack, Välj ett annat operativsystem för din distribution eller använder en Azure Resource Manager-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace genom den operator.  

<!-- 2724873 - IS --> 
- När du använder PowerShell-cmdlets **Start AzsScaleUnitNode** eller **Stop-AzsScaleunitNode** för att hantera skalningsenheter, det första försöket att starta eller stoppa skalningsenheten kan misslyckas. Om cmdleten misslyckas på den första körningen, kör du cmdlet en gång. Den andra körningen ska slutföra åtgärden. 

<!-- TBD - IS ASDK --> 
- Om det tar för lång tid att etablera ett tillägg på en VM-distribution, kan du etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

<!-- 1662991 IS ASDK --> 
- Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  

<!-- 2724961- IS ASDK --> 
- När du registrerar den **Microsoft.Insight** resursprovidern i prenumerationsinställningarna för och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverat, CPU-procent diagrammet på översiktssidan för virtuell dator har inte visa mått-data.

   Du hittar mätvärden, till exempel CPU-procent diagrammet för den virtuella datorn genom att gå till den **mått** fönster och visa alla Windows-VM som stöds gästen mått.

<!-- 3507629 - IS, ASDK --> 
- Hanterade diskar skapar två nya [compute kvottyper](azure-stack-quota-types.md#compute-quota-types) att begränsa den maximala kapaciteten på hanterade diskar som kan etableras. Som standard tilldelas 2 048 GiB för varje typ av kvot för hanterade diskar. Men du kan stöta på följande problem:

   - Av kvoter som skapats före uppdateringen 1808, visa kvot på Managed Disks 0 värden i administratörsportalen kan även om 2 048 GiB har allokerats. Du kan öka eller minska värdet utifrån dina faktiska behov och den nyligen ange kvotvärde åsidosätter 2048 GiB standard.
   - Om du uppdaterar kvotvärdet till 0, motsvarar det att standardvärdet 2 048 GiB. Som en lösning kan du ange kvotvärdet till 1.

<!-- TBD - IS ASDK --> 
- Efter att ha tillämpat 1811 uppdatera, följande problem kan uppstå när du distribuerar virtuella datorer med hanterade diskar:

   - Om prenumerationen har skapats innan uppdateringen gjordes 1808, distribution av virtuella datorer med Managed Disks kan misslyckas med felmeddelandet internt. Följ dessa steg för varje prenumeration för att lösa problemet:
      1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Välj **Resursprovidrar**och välj sedan **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
      2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
   - Om du har konfigurerat en miljö med flera organisationer kan kan distribuera virtuella datorer i en prenumeration som är associerade med en gäst-katalog misslyckas med ett internt felmeddelande. Lös felet genom att följa stegen i [i den här artikeln](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) att konfigurera om var och en av dina gäst-kataloger.

- En dator med Ubuntu 18.04 skapas med SSH-auktorisering aktiverat kan inte du använda SSH-nycklar för att logga in. Som en lösning kan använda för åtkomst till virtuell dator för Linux-tillägget för att implementera SSH-nycklar när du har etablerat eller använder lösenordsbaserad autentisering.

### <a name="networking"></a>Nätverk  

<!-- 1766332 - IS ASDK --> 
- Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress avvisas.

<!-- 16309153 - IS ASDK --> 
- I ett virtuellt nätverk som har skapats med en DNS-Serverinställningen för **automatisk**, ändra till en anpassad DNS-server misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

<!-- 2529607 - IS ASDK --> 
- Under Azure Stack *hemlighet Rotation*, där offentliga IP-adresser inte kan nås i två till fem minuter på en stund.

<!-- 2664148 - IS ASDK --> 
-   De kan stöta på ett scenario där anslutningsförsök misslyckas om det lokala undernätet har lagts till i den lokala nätverksgatewayen när gatewayen har redan skapats i scenarier där klienten har åtkomst till virtuella datorer med hjälp av en S2S VPN-tunnel. 

- I Azure Stack-portalen när du ändrar en statisk IP-adress för en IP-konfiguration som är bunden till ett nätverkskort som är anslutet till en VM-instans visas ett varningsmeddelande som säger 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Du kan ignorera det här meddelandet. IP-adressen ändras även om den Virtuella datorinstansen inte startar.

- I portalen på den **nätverk egenskaper** bladet som det finns en länk för **effektiva säkerhetsreglerna** för varje nätverkskort. Om du väljer den här länken öppnas ett nytt blad som visar ett felmeddelande `Not Found.` det här felet beror på att Azure Stack inte stöder ännu **effektiva säkerhetsreglerna**.

- I portalen om du lägger till en inkommande säkerhetsregel och välj **Tjänsttagg** som källa, flera alternativ visas i den **Källtagg** listan som inte är tillgängliga för Azure Stack. De enda alternativ som är giltiga i Azure Stack är följande:

    - **Internet**
    - **VirtualNetwork**
    - **AzureLoadBalancer**
  
    De andra alternativen stöds inte som källkod i Azure Stack. På samma sätt, om du lägger till en utgående säkerhetsregel och välj **Tjänsttagg** som mål, samma lista över alternativ för **Källtagg** visas. De enda giltiga alternativ är desamma som för **Källtagg**, enligt beskrivningen i föregående lista.

- Den **New-AzureRmIpSecPolicy** PowerShell-cmdleten stöder inte inställningen **DHGroup24** för den `DHGroup` parametern.

### <a name="infrastructure-backup"></a>Infrastruktur för säkerhetskopiering

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- När du har aktiverat automatiska säkerhetskopieringar, hamnar scheduler-tjänsten i inaktiverat tillstånd oväntat. Domänkontrollanttjänst för säkerhetskopiering identifierar att automatiska säkerhetskopieringar är inaktiverade och aktiverar en varning i administratörsportalen. Den här varningen är förväntat när automatisk säkerhetskopiering är inaktiverade. 
    - Orsak: Det här problemet beror på ett fel i tjänsten som leder till förlust av konfiguration av Schemaläggaren. Den här buggen ändras inte lagringsplats, användarnamn, lösenord eller krypteringsnyckeln.   
    - Reparation: Öppna bladet säkerhetskopiering controller inställningar i infrastruktur för säkerhetskopiering resursprovidern för att lösa det här problemet, och välj **aktivera automatiska säkerhetskopieringar**. Se till att ange den önskade frekvens och kvarhållning perioden.
    - Förekomst: Låg 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Du måste registrera lagringsresursprovidern innan du skapar din första Azure-funktion i prenumerationen.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned Azure Stack 1811 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload). 

I anslutna scenarier, Azure Stack-distributioner Kontrollera regelbundet en säker slutpunkt och meddela automatiskt dig om en uppdatering är tillgänglig för ditt moln. Mer information finns i [hantering av uppdateringar för Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Nästa steg

- Underhåll principen för integrerade Azure Stack-system och vad du måste göra för att behålla ditt system i ett läge som stöds finns i [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).  
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).  
- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).  
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).  
