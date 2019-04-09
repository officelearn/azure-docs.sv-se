---
title: Uppdatering av Azure Stack 1902 | Microsoft Docs
description: Läs mer om 1902-uppdateringen för integrerade Azure Stack-system, inklusive nyheter, kända problem och var du kan hämta uppdateringen.
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
ms.date: 04/05/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/05/2019
ms.openlocfilehash: 1cb99bba1fa5c762af57a1ad26d034974ff196a6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271840"
---
# <a name="azure-stack-1902-update"></a>Uppdatering av Azure Stack 1902

*Gäller för Azure Stack-integrerade system*

Den här artikeln beskriver innehållet i 1902 uppdateringspaketet. Uppdateringen innehåller nya funktioner för den här versionen av Azure Stack, korrigeringar och förbättringar. Den här artikeln kan du också beskrivs kända problem i den här versionen, och innehåller en länk för att hämta uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]  
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure Stack 1902 update build-nummer är **1.1902.0.69**.

## <a name="hotfixes"></a>Snabbkorrigeringar

Azure Stack snabbkorrigeringar med jämna mellanrum. Se till att installera den [senaste Azure Stack-snabbkorrigeringen](#azure-stack-hotfixes) för 1901 innan du uppdaterar Azure Stack till 1902.

Azure Stack-snabbkorrigeringar gäller endast för integrerade Azure Stack-system. Försök inte att installera snabbkorrigeringar på ASDK.

> [!TIP]  
> Prenumerera på följande *RSS* eller *Atom* flöden, hålla jämna steg med Azure Stack snabbkorrigeringar:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack-snabbkorrigeringar

- **1809**: [KB 4481548 – Azure Stack-snabbkorrigeringen 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Inga aktuella snabbkorrigeringen är tillgänglig.
- **1901**: [KB 4495662 – Azure Stack-snabbkorrigeringen 1.1901.3.105](https://support.microsoft.com/help/4495662)
- **1902**: [KB 4494719 – Azure Stack-snabbkorrigeringen 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Du kan installera 1902 direkt från antingen den [1.1901.0.95 eller 1.1901.0.99](azure-stack-update-1901.md#build-reference) versionen utan att första installera en snabbkorrigering 1901. Men om du har installerat den äldre **1901.2.103** snabbkorrigeringen, måste du installera den nyare [1901.3.105 snabbkorrigering](https://support.microsoft.com/help/4495662) innan du fortsätter att 1902.

- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) med följande parametrar för att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittades, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Om den `AzsControlPlane` parametern är inkluderade när **Test-AzureStack** är körs, visas följande fel i den **Test AzureStack** utdata: **MISSLYCKAS Azure Stack kontroll plan webbplatser sammanfattning**. Du kan ignorera det här specifika felet.

- När Azure Stack hanteras av System Center Operations Manager (SCOM), se till att uppdatera den [Management Pack för Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) till version 1.0.3.11 innan du tillämpar 1902.

- Formatet för paket för Azure Stack-uppdatering har ändrats från **.bin/.exe/.xml** till **.zip/.xml** från och med 1902. Kunder med anslutna Azure Stack-skalningsenheter ser den **uppdatering** meddelande i portalen. Kunder som inte är anslutna nu bara hämta och importera ZIP-filen med motsvarande XML-filen.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Förbättringar

- 1902-build introducerar ett nytt användargränssnitt på Azure Stack-administratörsportalen för att skapa planer, erbjudanden, kvoter och tilläggsplaner. Mer information, inklusive skärmdumpar, se [skapa planer, erbjudanden och kvoter](azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Förbättringar i tillförlitlighet på kapacitetsexpansion under en Lägg till nod när du växlar skala enhet läget från ”Expanding storage” till ”körs”.

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- För att förbättra paketets integritet och säkerhet samt enklare hantering för offline-inmatning, har Microsoft ändrats formatet för uppdateringspaketet från .exe och .bin-filer till en .zip-fil. Det nya formatet lägger till ytterligare tillförlitlighet för packar upp processen som ibland orsaka förberedelsen av uppdateringen uppehåll. Samma paket format gäller även om du vill uppdatera paket från OEM-tillverkaren.
- För att förbättra Azure Stack operatörens upplevelse när du kör testet AzureStack, operatörer kan nu helt enkelt använda ”, Test-AzureStack-gruppen UpdateReadiness” till skillnad från skicka tio ytterligare parametrar när du har en Include-sats.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- För att förbättra på övergripande tillförlitlighet och tillgänglighet av grundläggande infrastruktur för tjänster under uppdateringen, interna Update-resursprovidern som en del av åtgärdsplan update identifierar och anropa automatisk globala reparationer när det behövs. Global reparation ”reparera” arbetsflöden är:
    - Söker efter infrastrukturens virtuella datorer som är i ett icke-optimala tillstånd och försöker reparera dem efter behov 
    - Sök efter problem med SQL-tjänsten som en del av plan för åtkomstkontroll och försöker reparera dem efter behov
    - Kontrollera tillståndet för tjänsten Software Load Balancer (SLB) som en del av Network Controller (NC) och försöker reparera dem efter behov
    - Kontrollera tillståndet för tjänsten Network Controller (NC) och försöker reparera det vid behov
    - Kontrollera tillståndet för service fabric-noder för nödadministration Recovery konsolen Service (ERCS) och åtgärda eventuella
    - Kontrollera tillståndet för XRP service fabric-noder och åtgärda eventuella
    - Kontrollera tillståndet hos Azure konsekvent Storage (ACS) service fabric-noder och åtgärda eventuella

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Förbättringar av Azure stack diagnostiska verktyg för att förbättra log samling tillförlitlighet och prestanda. Ytterligare loggning för Nätverks-och identitetstjänster. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Förbättringar i tillförlitlighet för Test-AzureStack för hemliga rotation beredskap testa.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Förbättringar som ökar AD Graph tillförlitlighet vid kommunikation med kundens Active Directory-miljö

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Förbättringar av maskinvarusamlingen i Get-AzureStackStampInformation.

- För att förbättra tillförlitligheten för åtgärder som körs på ERCS infrastruktur, ökar minne för varje instans ERCS 8 GB till 12 GB. På en installation av integrerade Azure Stack-system resulterar detta i en ökning av 12 GB övergripande.

> [!IMPORTANT]
> För att säkerställa patch- och uppdateringsprocessen resulterar i den minsta uppsättningen klient driftstopp, kontrollera att Azure Stack-stämpel har mer än 12 GB tillgängligt utrymme i den **kapacitet** bladet. Du kan se det här minnet öka återspeglas i den **kapacitet** bladet efter installation av uppdateringen.

## <a name="common-vulnerabilities-and-exposures"></a>Vanliga säkerhetsproblem och exponeringar

Den här uppdateringen installeras följande säkerhetsuppdateringar:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

Klicka på föregående länkarna för mer information om dessa säkerhetsrisker eller finns i Microsoft Knowledge Base-artiklar [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- När du kör [Test-AzureStack](azure-stack-diagnostic-test.md), visas ett varningsmeddelande från den Hanteringsstyrenheten för baskort (BMC). Du kan ignorera den här varningen.

- <!-- 2468613 - IS --> Under installationen av den här uppdateringen kan du se aviseringar med rubriken `Error – Template for FaultType UserAccounts.New is missing.` du kan ignorera dessa aviseringar. Aviseringarna stängs automatiskt när installationen av uppdateringen har slutförts.

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

<!-- 3557860 - IS ASDK --> 
- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan först ta bort användarresurser eller hela resursgruppen och sedan ta bort prenumerationer för användare.

<!-- 1663805 - IS ASDK --> 
- Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda [PowerShell för att kontrollera behörigheterna](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- När du skapar en ny Windows virtuell dator (VM), visas följande fel:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Felet inträffar om du vill aktivera startdiagnostik på en virtuell dator men ta bort ditt lagringskonto för startdiagnostik. Undvik problemet genom att återskapa lagringskontot med samma namn som du använde tidigare.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Upplevelse VM scale set (VMSS) när du skapar innehåller 7.2 CentOS-baserade som ett alternativ för distribution. Eftersom avbildningen inte är tillgänglig på Azure Stack, Välj ett annat operativsystem för din distribution eller använder en Azure Resource Manager-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace genom den operator.  

<!-- TBD - IS ASDK --> 
- Efter att ha tillämpat 1902 uppdatera, följande problem kan uppstå när du distribuerar virtuella datorer med hanterade diskar:

   - Om prenumerationen har skapats innan uppdateringen gjordes 1808, distribution av virtuella datorer med Managed Disks kan misslyckas med felmeddelandet internt. Följ dessa steg för varje prenumeration för att lösa problemet:
      1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Välj **Resursprovidrar**och välj sedan **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
      2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
   - Om du har konfigurerat en miljö med flera organisationer kan kan distribuera virtuella datorer i en prenumeration som är associerade med en gäst-katalog misslyckas med ett internt felmeddelande. Lös felet genom att följa stegen i [i den här artikeln](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) att konfigurera om var och en av dina gäst-kataloger.

- En dator med Ubuntu 18.04 skapas med SSH-auktorisering aktiverat kan inte du använda SSH-nycklar för att logga in. Som en lösning kan använda för åtkomst till virtuell dator för Linux-tillägget för att implementera SSH-nycklar när du har etablerat eller använder lösenordsbaserad autentisering.

### <a name="networking"></a>Nätverk  

<!-- 3239127 - IS, ASDK -->
- I Azure Stack-portalen när du ändrar en statisk IP-adress för en IP-konfiguration som är bunden till ett nätverkskort som är anslutet till en VM-instans visas ett varningsmeddelande som säger 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Du kan ignorera det här meddelandet. IP-adressen ändras även om den Virtuella datorinstansen inte startar.

<!-- 3632798 - IS, ASDK -->
- I portalen om du lägger till en inkommande säkerhetsregel och välj **Tjänsttagg** som källa, flera alternativ visas i den **Källtagg** listan som inte är tillgängliga för Azure Stack. De enda alternativ som är giltiga i Azure Stack är följande:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  De andra alternativen stöds inte som källkod i Azure Stack. På samma sätt, om du lägger till en utgående säkerhetsregel och välj **Tjänsttagg** som mål, samma lista över alternativ för **Källtagg** visas. De enda giltiga alternativ är desamma som för **Källtagg**, enligt beskrivningen i föregående lista.

- Nätverkssäkerhetsgrupper (NSG) fungerar inte i Azure Stack på samma sätt som globala Azure. I Azure, kan du ställa in flera portar på en NSG-regel (med hjälp av portal, PowerShell och Resource Manager-mallar). I Azure Stack men kan inte du ange flera portar på en NSG-regel via portalen. Undvik problemet genom att använda en Resource Manager-mall eller PowerShell för att ange dessa ytterligare regler.

<!-- 3203799 - IS, ASDK -->
- Azure Stack har inte stöd för att koppla fler än 4 nätverksgränssnitt (NIC) till en VM-instans i dag, oavsett storlek på instansen.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Du måste registrera lagringsresursprovidern innan du skapar din första Azure-funktion i prenumerationen.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Syslog 
- Systemloggkonfigurationen behålls inte via en uppdateringscykeln, vilket resulterar i syslog-klienten att förlora sin konfiguration och syslog-meddelanden för att stoppa vidarebefordras. Det här problemet gäller alla versioner av Azure Stack sedan den allmänt tillgängliga versionen av syslog-klienten (1809).
Lösningen är att konfigurera om syslog-klienten efter att ha tillämpat en Azure Stack-uppdatering.

## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned Azure Stack 1902 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload). 

I anslutna scenarier, Azure Stack-distributioner Kontrollera regelbundet en säker slutpunkt och meddela automatiskt dig om en uppdatering är tillgänglig för ditt moln. Mer information finns i [hantering av uppdateringar för Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Nästa steg

- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).  
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).
- Underhåll principen för integrerade Azure Stack-system och vad du måste göra för att behålla ditt system i ett läge som stöds finns i [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).  
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).  
