---
title: Uppdatering av Azure Stack 1901 | Microsoft Docs
description: Läs mer om 1901 uppdateringen för integrerade Azure Stack-system, inklusive nyheter, kända problem och var du kan hämta uppdateringen.
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
ms.date: 02/27/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 77b3d8bd1d16e90e9929c41f0f28940694dc7906
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889842"
---
# <a name="azure-stack-1901-update"></a>Uppdatering av Azure Stack 1901

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln beskriver innehållet i 1901 uppdateringspaketet. Uppdateringen innehåller nya funktioner för den här versionen av Azure Stack, korrigeringar och förbättringar. Den här artikeln kan du också beskrivs kända problem i den här versionen, och innehåller en länk för att hämta uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]  
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure Stack 1901 update build-nummer är **1.1901.0.95** eller **1.1901.0.99** efter den 26 februari 2019. Se följande kommentar:

> [!IMPORTANT]  
> Microsoft har upptäckt ett problem som kan påverka kunder uppdaterar från 1811 (1.1811.0.101) till 1901 och har publicerat ett uppdaterade 1901-paket för att åtgärda problemet: skapa 1.1901.0.99, uppdateras från 1.1901.0.95. Kunder som redan har uppdaterats till 1.1901.0.95 behöver inte vidta några ytterligare åtgärder.
>
> Anslutna kunder som finns på 1811 kommer automatiskt att se det nya 1901 (1.1901.0.99)-paketet i administratörsportalen och ska installera den när du är klar. Frånkopplade kunder kan hämta och importera det nya 1901 paketet med hjälp av samma process [som beskrivs här](azure-stack-apply-updates.md).
>
> Kunder med alla versioner av 1901 påverkas inte när du installerar nästa paket för full- eller snabbkorrigering.

## <a name="hotfixes"></a>Snabbkorrigeringar

Azure Stack snabbkorrigeringar med jämna mellanrum. Se till att installera den [senaste Azure Stack-snabbkorrigeringen](#azure-stack-hotfixes) för 1811 innan du uppdaterar Azure Stack till 1901.

Azure Stack-snabbkorrigeringar gäller endast för integrerade Azure Stack-system. Försök inte att installera snabbkorrigeringar på ASDK.

> [!TIP]  
> Prenumerera på följande *RSS* eller *Atom* flöden, hålla jämna steg med Azure Stack snabbkorrigeringar:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack-snabbkorrigeringar

- **1809**: [KB 4481548 – Azure Stack-snabbkorrigeringen 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Inga aktuella snabbkorrigeringen är tillgänglig.
- **1901**: Inga aktuella snabbkorrigeringen är tillgänglig.

## <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
- Installera den [senaste Azure Stack-snabbkorrigeringen](#azure-stack-hotfixes) för 1811 (i förekommande fall) innan du uppdaterar till 1901.

- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) med följande parametrar för att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittades, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd:

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

## <a name="new-features"></a>Nya funktioner

Den här uppdateringen innehåller följande nya funktioner och förbättringar för Azure Stack:

- Hanterade avbildningar på Azure Stack-Aktivera disk du att skapa en hanterad avbildning-objekt på en generaliserad virtuell dator (både ohanterade och hanterade) som kan bara skapa hanterade virtuella datorer framöver. Mer information finns i [Azure Stack Managed Disks](user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Felkorrigering – `Import-AzureRmContext` rekonstrukci sparade token korrekt.  
   * **AzureRm.Resources**  
         Felkorrigering – `Get-AzureRmResource` till fråga skiftläge insensitively efter resurstyp.  
   * **Azure.Storage**  
         Insamling av AzureRm-modulen innehåller nu stöd för redan publicerade versionen 4.5.0 den **api-versionen 2017-07-29**.  
   * **AzureRm.Storage**  
         Insamling av AzureRm-modulen innehåller nu stöd för redan publicerade versionen 5.0.4 den **api-versionen 2017-10-01**.  
   * **AzureRm.Compute**  
         Enkel parameter har lagts till som anger i `New-AzureRMVM` och `NewAzureRMVMSS`, `-ImageName` parametern har stöd för att ange användare avbildningar.  
   * **AzureRm.Insights**  
         Insamling av AzureRm-modulen innehåller nu stöd för redan publicerade versionen 5.1.5 den **api-versionen 2018-01-01** för mått, definitioner av mätvärden för resurstyper.

- **AzureStack 1.7.0** detta en viktig ändring versionen. Mer information om de senaste ändringarna finns i https://aka.ms/azspshmigration170
   * **Azs.Backup.Admin modul**  
         Icke-bakåtkompatibel ändring: Backup ändras till certifikatsbaserat krypteringsläge. Stöd för symmetriska nycklar fasas ut.  
   * **Azs.Fabric.Admin modul**  
         `Get-AzsInfrastructureVolume` är inaktuell. Använd den nya cmdleten `Get-AzsVolume`.  
         `Get-AzsStorageSystem` är inaktuell.  Använd den nya cmdleten `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` är inaktuell. Den `StorageSubSystem` objektet innehåller egenskapen kapacitet.  
   * **Azs.Compute.Admin Module**  
         Felkorrigering – `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: Anropa `ConvertTo-PlatformImageObject` endast i rätt väg.  
         BugFix - `Add-AzsVmExtension`, `Get-AzsVmExtension`: Anropa ConvertTo-VmExtensionObject endast i rätt väg.  
   * **Azs.Storage.Admin Module**  
         Felkorrigering – nya lagringskvoten använder standardvärdena om inget anges.

Referens för de uppdaterade modulerna finns [Azure Stack-modulreferens](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0).

## <a name="fixed-issues"></a>Åtgärdade problem

- Ett problem där portalen visade ett alternativ för att skapa principbaserad VPN-gateways som inte stöds i Azure Stack har åtgärdats. Det här alternativet har tagits bort från portalen.

<!-- 16523695 – IS, ASDK -->
- Ett problem har åtgärdats där när du har uppdaterat din DNS-inställningarna för det virtuella nätverket från **Använd Azure Stack DNS** till **anpassad DNS**, instanser har inte uppdaterats med de nya inställningarna.

- <!-- 3235634 – IS, ASDK --> Ett problem har åtgärdats där distribuera de virtuella datorerna med storlekar som innehåller en **v2** suffix, till exempel **Standard_A2_v2**, obligatoriskt att ange suffixet som **Standard_A2_v2** () gemener v). Som med globala Azure kan du nu använda **Standard_A2_V2** (versaler V).

<!-- 2869209 – IS, ASDK --> 
- Ett problem har åtgärdats när du använder den [cmdlet Add-AzsPlatformImage](/powershell/module/azs.compute.admin/add-azsplatformimage), i vilket du var tvungen att använda den **- OsUri** parameter som lagringskontot URI där disken har laddats upp. Du kan nu också använda den lokala sökvägen till disken.

<!--  2795678 – IS, ASDK --> 
- Ett problem som gav en varning när du har använt portalen för att skapa virtuella datorer (VM) i en premium VM-storlek (DS, Ds_v2, FS, FSv2) har åtgärdats. Den virtuella datorn har skapats i ett standardlagringskonto. Även om detta inte påverkade funktionellt, IOPs och fakturering, har varningen åtgärdats.

<!-- 1264761 - IS ASDK -->  
- Åtgärdat ett problem med den **hälsotillstånd controller** komponent som genererar följande aviseringar. Aviseringarna kan ignoreras:

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


<!-- 3507629 - IS, ASDK --> 
- Ett problem har åtgärdats när du ställer in värdet för Managed Disks kvoter under [compute kvottyper](azure-stack-quota-types.md#compute-quota-types) till 0, det motsvarar att standardvärdet 2 048 GiB. Kvotvärdet noll respekteras nu.

<!-- 2724873 - IS --> 
- Ett problem har åtgärdats när du använder PowerShell-cmdlets **Start AzsScaleUnitNode** eller **Stop-AzsScaleUnitNode** att hantera skalningsenheter, där det första försöket att starta eller stoppa skalningsenheten misslyckas.

<!-- 2724961- IS ASDK --> 
- Ett problem som du registrerade har åtgärdats i **Microsoft.Insight** resursprovidern i prenumerationsinställningarna för och skapat en virtuell Windows-dator med Guest OS diagnostiska aktiverat, men CPU-procent diagrammet på översiktssidan för virtuell dator inte visat mätvärden. Data korrekt visas nu.

- Ett problem har åtgärdats i vilka körs den **Get-AzureStackLog** cmdlet misslyckades efter körning **Test AzureStack** i samma session för privilegierad slutpunkt (program). Du kan nu använda samma program-session där du körde **Test-AzureStack**.

<!-- bug 3615401, IS -->
- Problem har åtgärdats med automatisk säkerhetskopiering där tjänsten scheduler övergår i inaktiverat tillstånd oväntat. 

<!--2850083, IS ASDK -->
- Ta bort den **återställa Gateway** knappen från Azure Stack portal, vilket orsakade ett fel om var användaren klickar på knappen. Den här knappen har ingen funktion i Azure Stack, som Azure Stack har en gateway för flera innehavare i stället för dedikerade VM-instanser för varje klient VPN-Gateway, så att den togs bort för att förhindra förvirring. 

<!-- 3209594, IS ASDK -->
- Tas bort den **effektiva säkerhetsreglerna** länka från den **nätverk egenskaper** bladet som den här funktionen stöds inte i Azure Stack. Med länken finns gav intryck av att den här funktionen har stöds men fungerar inte. Vi tagit bort länken för att minska förvirring.

<!-- 3139614 | IS -->
- Ett problem som har åtgärdats när en uppdatering har tillämpats på Azure Stack från en OEM-tillverkare i **uppdatering** meddelande fanns inte i Azure Stack-administratörsportalen.

## <a name="changes"></a>Ändringar

<!-- 3083238 IS -->
- Säkerhetsförbättringar i den här uppdateringen resultera i en ökning av säkerhetskopiering storleken på rollen för katalogen. Uppdaterad ändrar storlek på riktlinjer för extern lagringsplats, finns i den [dokumentation om backup-infrastruktur](azure-stack-backup-reference.md#storage-location-sizing). Den här ändringen resulterar i en längre tid att slutföra säkerhetskopieringen på grund av större storlek dataöverföringen. Den här ändringen påverkar integrerade system. 

- Med början i januari 2019 kan distribuera du Kubernetes-kluster på Active Directory Federation Services (AD FS) registrerat, anslutna Azure Stack stämplar (Internetanslutning krävs). Följ instruktionerna [här](azure-stack-solution-template-kubernetes-cluster-add.md) att ladda ned det nya Kubernetes Marketplace-objektet. Följ instruktionerna [här](user/azure-stack-solution-template-kubernetes-adfs.md) att distribuera ett Kubernetes-kluster. Observera de nya parametrarna för som anger om måldatorn är Lägg till eller AD FS registrerad. Information om AD FS finns är nya fält tillgängliga att ange parametrarna Key Vault där distributionen certifikatet lagras.

   Observera att distributionen av Kubernetes-kluster även med stöd för AD FS kräver Internetåtkomst.

- När du har installerat uppdateringar eller snabbkorrigeringar till Azure Stack, kan nya funktioner införas som kräver nya behörigheter beviljas till en eller flera program med identiteter. Bevilja de här behörigheterna kräver administrativ åtkomst till arbetskatalogen och så den kan inte utföras automatiskt. Exempel:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Det finns en ny beräkningen för korrekt planera kapacitet för Azure Stack. Med 1901-uppdateringen finns nu en gräns för det totala antalet virtuella datorer som kan skapas.  Den här gränsen är avsedd att vara tillfällig att undvika lösning instabilitet. Källan till problemet stabilitet på högre antal virtuella datorer behandlas men en specifik tidslinje för reparation ännu inte har fastställts. Med 1901-uppdateringen finns nu en per server gräns på 60 virtuella datorer med en komplett lösning högst 700.  Till exempel vore ett 8 Azure Stack VM gränsen 480 (8 * 60).  För en server med 12 till 16 Azure Stack-lösningen är gränsen 700. Den här gränsen har skapats och all beräkningskraft kapacitetsöverväganden Tänk till exempel återhämtning reservera och CPU virtuella till fysiska förhållande som en operatör vill behålla på stämpeln. Mer information finns i den nya versionen av capacity planner.  
I händelse av att VM scale gränsen har uppnåtts, skulle följande felkoder returneras som ett resultat: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- Compute-API-versionen har ökat till 2017-12-01.

- Infrastruktur för säkerhetskopiering nu kräver ett certifikat med en offentlig nyckel (. CER) för kryptering av säkerhetskopierade data. Stöd för symmetrisk kryptografisk nyckel är inaktuell från och med 1901. Om infrastruktur för säkerhetskopiering har konfigurerats innan du uppdaterar till 1901, förblir krypteringsnycklarna på plats. Du har minst 2 mer uppdaterar med bakåtkompatibilitet kompatibilitet stöd för att uppdatera inställningar för säkerhetskopiering. Mer information finns i [bästa praxis för Azure Stack-infrastruktur säkerhetskopiering](azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Vanliga säkerhetsproblem och exponeringar

Den här uppdateringen installeras följande säkerhetsuppdateringar:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Klicka på föregående länkarna för mer information om dessa säkerhetsrisker eller finns i Microsoft Knowledge Base-artiklar [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- När du kör [Test-AzureStack](azure-stack-diagnostic-test.md)om antingen den **AzsInfraRoleSummary** eller **AzsPortalApiSummary** testet misslyckas, uppmanas du att köra  **Test-AzureStack** med den `-Repair` flaggan.  Om du kör det här kommandot misslyckas med följande felmeddelande visas:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

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
- Efter att ha tillämpat 1901 uppdatera, följande problem kan uppstå när du distribuerar virtuella datorer med hanterade diskar:

   - Om prenumerationen har skapats innan uppdateringen gjordes 1808, distribution av virtuella datorer med Managed Disks kan misslyckas med felmeddelandet internt. Följ dessa steg för varje prenumeration för att lösa problemet:
      1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Välj **Resursprovidrar**och välj sedan **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
      2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **AzureStack-DiskRP-klient** visas.
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
- Azure Stack har inte stöd för att koppla fler än 4 nätverksgränssnitt (NIC) till en VM-instanser i dag, oavsett storlek på instansen.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Du måste registrera lagringsresursprovidern innan du skapar din första Azure-funktion i prenumerationen.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned Azure Stack 1901 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload). 

I anslutna scenarier, Azure Stack-distributioner Kontrollera regelbundet en säker slutpunkt och meddela automatiskt dig om en uppdatering är tillgänglig för ditt moln. Mer information finns i [hantering av uppdateringar för Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Nästa steg

- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).  
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).
- Underhåll principen för integrerade Azure Stack-system och vad du måste göra för att behålla ditt system i ett läge som stöds finns i [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).  
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).  
