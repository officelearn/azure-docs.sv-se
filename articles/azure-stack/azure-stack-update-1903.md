---
title: Uppdatering av Azure Stack 1903 | Microsoft Docs
description: Läs mer om 1903 uppdateringen för integrerade Azure Stack-system, inklusive nyheter, kända problem och var du kan hämta uppdateringen.
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
ms.openlocfilehash: a62c4dced78ef75588ef0fcc90e56bd6969c15a9
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048817"
---
# <a name="azure-stack-1903-update"></a>Uppdatering av Azure Stack 1903

*Gäller för Azure Stack-integrerade system*

Den här artikeln beskriver innehållet i 1903 uppdateringspaketet. Uppdateringen innehåller nya funktioner för den här versionen av Azure Stack, korrigeringar och förbättringar. Den här artikeln kan du också beskrivs kända problem i den här versionen, och innehåller en länk för att hämta uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]  
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure Stack 1903 update build-nummer är **1.1903.0.35**.

> [!IMPORTANT]
> 1903 nyttolasten innehåller inte en ASDK version.

## <a name="hotfixes"></a>Snabbkorrigeringar

Azure Stack snabbkorrigeringar med jämna mellanrum. Se till att installera den [senaste Azure Stack-snabbkorrigeringen](#azure-stack-hotfixes) för 1902 innan du uppdaterar Azure Stack till 1903.

Azure Stack-snabbkorrigeringar gäller endast för integrerade Azure Stack-system. Försök inte att installera snabbkorrigeringar på ASDK.

> [!TIP]  
> Prenumerera på följande *RSS* eller *Atom* flöden, hålla jämna steg med Azure Stack snabbkorrigeringar:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack-snabbkorrigeringar

- **1902**: [KB 4481548 – Azure Stack-snabbkorrigeringen 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>Förbättringar

- Nyttolasten 1903 uppdateringen innehåller en uppdatering av komponenter i Azure Stack som inte innehåller det underliggande operativsystemet vilka värdar Azure Stack. Detta gör att vissa uppdateringar att vara begränsad. Därför kan den förväntade tid det tar för uppdateringen av 1903 slutförs är mindre (ca 16 timmar, men exakt gånger kan variera). Den här minskningen i runtime är specifik för 1903 uppdateringen och efterföljande uppdateringar kan innehålla uppdateringar av operativsystemet, innebär olika körningar. Framtida uppdateringar hjälper dig liknande den förväntade tiden för uppdateringen tar för att slutföra beroende på den nyttolast som ingår.

- Ett fel har åtgärdats i nätverk som förhindrade ändringar av den **timeout för inaktivitet (minuter)** värdet för en **offentliga IP-adressen** från ticka. Tidigare ignorerades ändringar till det här värdet, så att oavsett alla ändringar du gjort, värdet som standard 4 minuter. Den här inställningen styr hur många minuter att hålla en TCP-anslutning öppna utan att behöva klienter skickar keep-alive-meddelanden. Obs den här buggen påverkas endast instans nivå offentliga IP-adresser, inte offentliga IP-adresser tilldelade till en belastningsutjämnare.

- Förbättringar i tillförlitlighet på uppdateringsmotorn, inklusive automatisk reparation av vanliga problem så att uppdateringar tillämpas utan avbrott.

- Förbättringar för att upptäcka och reparation av ont om ledigt utrymme villkor.

### <a name="secret-management"></a>Hemlighetshantering

- Azure Stack har nu stöd för rotation av rotcertifikatet som används av certifikat för externa hemliga rotation. Mer information [finns i den här artikeln](azure-stack-rotate-secrets.md).

- 1903 innehåller prestandaförbättringar för hemliga rotation som minskar tiden det tar för att köra interna hemliga rotation.

## <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Installera den [senaste Azure Stack-snabbkorrigeringen](#azure-stack-hotfixes) för 1902 (i förekommande fall) innan du uppdaterar till 1903.

- Se till att använda den senaste versionen av den [kapacitetsplaneringsverktyget för Azure Stack](https://aka.ms/azstackcapacityplanner) att göra din arbetsbelastning planering och storlek. Den senaste versionen innehåller felkorrigeringar och tillhandahåller nya funktioner som släpps med varje Azure Stack-uppdatering.

- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) med följande parameter att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittades, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd:

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- När Azure Stack hanteras av System Center Operations Manager, se till att uppdatera den [Management Pack för Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) till version 1.0.3.11 innan du tillämpar 1903.

- Formatet för paket för Azure Stack-uppdatering har ändrats från **.bin/.exe/.xml** till **.zip/.xml** från och med 1902. Kunder med anslutna Azure Stack-skalningsenheter ser den **uppdatering** meddelande i portalen. Kunder som inte är anslutna nu bara hämta och importera ZIP-filen med motsvarande XML-filen.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- När du kör [Test-AzureStack](azure-stack-diagnostic-test.md), visas ett varningsmeddelande från den Hanteringsstyrenheten för baskort (BMC). Du kan ignorera den här varningen.

- <!-- 2468613 - IS --> Under installationen av den här uppdateringen kan du se aviseringar med rubriken **fel – mall för FaultType UserAccounts.New saknas.** Du kan ignorera dessa aviseringar. Aviseringarna stängs automatiskt när installationen av uppdateringen har slutförts.

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

<!-- Daniel 3/28 -->
- I användarportalen, när du navigerar till en blob i ett lagringskonto och försök att öppna **åtkomstprincip** i navigeringsträdet fönstret efterföljande inte kan läsas in.

<!-- Daniel 3/28 -->
- I användarportalen, när du försöker ladda upp en blob med hjälp av den **OAuth(preview)** alternativet uppgiften misslyckas med felmeddelandet. Undvik problemet genom att ladda upp en blob med hjälp av den **SAS** alternativet.

- När du har loggat in på Azure Stack-portalerna kan du se meddelanden om offentlig Azure-portalen. Du kan ignorera dessa meddelanden eftersom de inte för närvarande gäller för Azure Stack (till exempel ”1 ny uppdatering - följande uppdateringar är nu tillgängliga: Azure portal April 2019-uppdatering ”).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- När du skapar en ny Windows virtuell dator (VM), visas följande fel:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Felet inträffar om du vill aktivera startdiagnostik på en virtuell dator men ta bort ditt lagringskonto för startdiagnostik. Undvik problemet genom att återskapa lagringskontot med samma namn som du använde tidigare.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Upplevelse för Virtual Machine Scale Sets när du skapar innehåller 7.2 CentOS-baserade som ett alternativ för distribution. Eftersom avbildningen inte är tillgänglig på Azure Stack, Välj ett annat operativsystem för din distribution eller använder en Azure Resource Manager-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace genom den operator.  

<!-- TBD - IS ASDK --> 
- Efter att ha tillämpat 1903 uppdatera, följande problem kan uppstå när du distribuerar virtuella datorer med hanterade diskar:

   - Om prenumerationen har skapats innan uppdateringen gjordes 1808, distribution av virtuella datorer med Managed Disks kan misslyckas med felmeddelandet internt. Följ dessa steg för varje prenumeration för att lösa problemet:
      1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Välj **Resursprovidrar**och välj sedan **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
      2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
   - Om du har konfigurerat en miljö med flera organisationer kan kan distribuera virtuella datorer i en prenumeration som är associerade med en gäst-katalog misslyckas med ett internt felmeddelande. Lös felet genom att följa stegen i [i den här artikeln](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) att konfigurera om var och en av dina gäst-kataloger.

- En dator med Ubuntu 18.04 skapas med SSH-auktorisering aktiverat kan inte du använda SSH-nycklar för inloggning. Som en lösning kan använda för åtkomst till virtuell dator för Linux-tillägget för att implementera SSH-nycklar när du har etablerat eller använder lösenordsbaserad autentisering.

- Om du inte har en maskinvara livscykel värden (HLH): före build 1902, var du tvungen att ställa in Grupprincip **Datorkonfiguration\Windows-inställningar\Säkerhetsinställningar\Lokala Principer\säkerhetsalternativ** till **Skicka LM & NTLM – Använd NTLMv2-sessionssäkerhet om förhandlas**. Sedan build 1902, måste du lämna den som **inte har definierats** eller anger **endast skicka NTLMv2-svar** (vilket är standardvärdet). Annars kan du inte längre att upprätta en PowerShell-fjärrsession så ser du en **åtkomst nekas** fel:

   ```shell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>Nätverk  

<!-- 3239127 - IS, ASDK -->
- I Azure Stack-portalen när du ändrar en statisk IP-adress för en IP-konfiguration som är bunden till ett nätverkskort som är anslutet till en VM-instans visas ett varningsmeddelande som säger 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

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

## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned Azure Stack 1903 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload). 

I anslutna scenarier, Azure Stack-distributioner Kontrollera regelbundet en säker slutpunkt och meddela automatiskt dig om en uppdatering är tillgänglig för ditt moln. Mer information finns i [hantering av uppdateringar för Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Nästa steg

- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).  
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).
- Underhåll principen för integrerade Azure Stack-system och vad du måste göra för att behålla ditt system i ett läge som stöds finns i [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).  
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).  
