---
title: Distribuera Azure Stack Development Kit | Microsoft Docs
description: "Lär dig hur du förbereder Azure Stack Development Kit och köra PowerShell-skript för att distribuera den."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: b67cabf0ecdb48f137bfcfbce95eee568a1c298d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Distribuera Azure Stack Development Kit

*Gäller för: Azure stacken Development Kit*

Att distribuera den [Azure Stack Development Kit](azure-stack-poc.md), måste du utföra följande steg:

1. [Hämta distributionspaketet](https://azure.microsoft.com/overview/azure-stack/try/?v=try) att hämta Cloudbuilder.vhdx.
2. [Förbereda cloudbuilder.vhdx](#prepare-the-development-kit-host) genom att köra skriptet asdk installer.ps1 för att konfigurera datorn (development kit värden) som du vill installera development kit. Efter det här steget startas development kit värden med Cloudbuilder.vhdx.
3. [Distribuera i development kit](#deploy-the-development-kit) på development kit värden.

> [!NOTE]
> För bästa resultat, även om du vill använda en frånkopplad miljö i Azure-stacken, är det bäst att distribuera medan du är ansluten till internet. På så sätt kan kan Windows Server 2016-utvärderingsversion aktiveras vid tidpunkten för distribution.
> 
> 

## <a name="download-and-extract-the-development-kit"></a>Ladda ned och extrahera development kit
1. Kontrollera att datorn uppfyller följande krav innan du startar hämtningen:

   * Datorn måste ha minst 60 GB ledigt diskutrymme.
   * [.NET framework 4.6 (eller en senare version)](https://aka.ms/r6mkiy) måste vara installerad.

2. [Gå till sidan komma igång](https://azure.microsoft.com/overview/azure-stack/try/?v=try), ange din information och klickar på **skicka**.
3. Under **ladda ned programvaran**, klickar du på **Azure Stack Development Kit**.
4. Kör den nedladdade AzureStackDownloader.exe-filen.
5. I den **Azure Stack Development Kit Installationshämtaren** fönster, Följ steg 1 till 5.
6. När nedladdningen är klar klickar du på **kör** att starta MicrosoftAzureStackPOC.exe.
7. Granska licensavtal och information i guiden Self-Extractor och klicka sedan på **nästa**.
8. Granska sekretesspolicyn skärmen och information i guiden Self-Extractor och klicka sedan på **nästa**.
9. Välj mål för filer extraheras genom att klicka på **nästa**.
   * Standardvärdet är: <drive letter>:\<aktuella mappen > \Microsoft Azure Stack
10. Granska skärmen för platsen för mål och information i guiden Self-Extractor och klicka sedan på **extrahera** att extrahera CloudBuilder.vhdx (~ 25 GB) och ThirdPartyLicenses.rtf filer. Den här processen kommer ta lite tid att slutföra.

> [!NOTE]
> När du har extraherat filerna kan du ta bort exe och bin-filer om du vill återställa utrymme på datorn. Eller så kan du flytta filerna till en annan plats så att om du behöver distribuera om du inte behöver hämta filerna igen.
> 
> 

## <a name="prepare-the-development-kit-host"></a>Förbereda development kit värden
1. Kontrollera att du kan fysiskt ansluter till development kit värden, eller har fysiska konsolåtkomst (till exempel KVM). Du måste ha tillgång till när du startar om development kit värden i steg 13 nedan.
2. Kontrollera att värden development kit uppfyller de [minimikrav](azure-stack-deploy.md). Du kan använda den [distribution av förutsättningar för Azure-stacken](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) bekräfta dina krav.
3. Logga in som lokal administratör på värddatorn development kit.
4. Kopiera eller flytta filen CloudBuilder.vhdx till roten på enhet C:\ (C:\CloudBuilder.vhdx).
5. Kör följande skript för att hämta installationsfilen för development kit (asdk installer.ps1) till mappen c:\AzureStack_Installer på development kit värden.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. Öppna en upphöjd PowerShell-konsol > Kör skriptet C:\AzureStack_Installer\asdk-installer.ps1 > klickar du på **förbereda miljön**.
7. På den **Välj Cloudbuilder vhdx** sidan i installationsprogrammet, bläddra till och välj den cloudbuilder.vhdx-fil som du hämtade i föregående steg.
8. Valfritt: Kontrollera den **lägga till drivrutiner** om du vill ange en mapp som innehåller ytterligare drivrutiner som du vill använda på värden.
9. På den **valfria inställningar** anger det lokala administratörskontot för development kit värden. Om du inte anger dessa autentiseringsuppgifter måste KVM-åtkomst till värden under installationen nedan.
10. Även på den **valfria inställningar** sida har du möjlighet att ange följande:
    - **ComputerName**: det här alternativet anger namn för development kit värden. Namnet måste uppfylla krav för FQDN och måste vara högst 15 tecken eller färre. Standardvärdet är ett slumpmässigt datornamn som genererats av Windows.
    - **Tidszon**: Anger tidszonen för development kit värden. Standardvärdet är (UTC-8:00) Stillahavstid (USA och Kanada).
    - **Statisk IP-konfiguration**: Anger distributionen för att använda en statisk IP-adress. När installationsprogrammet startar om i cloudbuilder.vhx, annars konfigureras nätverksgränssnitten med DHCP.
11. Klicka på **Nästa**.
12. Om du väljer en statisk IP-konfiguration i föregående steg, måste du nu:
    - Välj ett nätverkskort. Kontrollera att du kan ansluta till nätverkskortet innan du klickar på **nästa**.
    - Se till att den **IP-adress**, **Gateway**, och **DNS** värden är korrekta och klicka sedan på **nästa**.
13. Klicka på **nästa** att starta förberedelseprocessen.
14. När förberedelsen anger **slutförd**, klickar du på **nästa**.
15. Klicka på **starta om nu** starta i cloudbuilder.vhdx och fortsätta distributionsprocessen.

## <a name="deploy-the-development-kit"></a>Distribuera i development kit
1. Logga in som lokal administratör på development kit värden. Använd autentiseringsuppgifterna som angetts i föregående steg.

    > [!IMPORTANT]
    > Azure Stack kräver åtkomst till Internet, antingen direkt eller via en transparent proxy för Azure Active Directory-distributioner. Distributionen har stöd för ett nätverkskort för nätverk. Om du har flera nätverkskort kan du kontrollera att endast en är aktiverat (och alla andra har inaktiverats) innan du kör skriptet för distribution i nästa avsnitt.
    
2. Öppna en upphöjd PowerShell-konsol > Kör skriptet \AzureStack_Installer\asdk-installer.ps1 (som kan finnas på en annan enhet i Cloudbuilder.vhdx) > klickar du på **installera**.
3. I den **typen** väljer **Azure-molnet** eller **ADFS**.
    - **Azure-molnet**: Azure Active Directory är identitetsleverantören. Använd den här parametern om du vill ange en specifik katalog där AAD-konto har globala administratörsbehörigheter. Fullständigt namn på en AAD-Directory-klient. Till exempel. onmicrosoft.com. 
    - **AD FS**: standard stämpeln katalogtjänsten är identitetsleverantören är standardkontot logga in med azurestackadmin@azurestack.local, och lösenordet är det du angav som en del av installationen.
4. Under **lokala administratörslösenordet**i den **lösenord** rutan skriver du det lokala administratörslösenordet (som måste matcha det aktuella konfigurerade lokala administratörslösenordet) och klicka sedan på **Nästa**.
5. Välj ett nätverkskort som ska användas för development kit och klicka sedan på **nästa**.
6. Välj DHCP eller statiska nätverkskonfigurationen för den virtuella datorn BGPNAT01.
    - **DHCP** (standard): den virtuella datorn hämtar den IP-konfigurationen från DHCP-servern.
    - **Statisk**: Använd bara det här alternativet om DHCP inte kan tilldela en giltig IP-adress för Azure stackutrymme för att få åtkomst till Internet. En statisk IP-adress måste anges med nätmask längd (till exempel 10.0.0.5/24).
7. Du kan också ange följande värden:
    - **VLAN-ID**: Anger VLAN-ID. Använd bara det här alternativet om värden och AzS BGPNAT01 måste konfigurera VLAN-ID för att komma åt den fysiska nätverk (och Internet). 
    - **DNS-vidarebefordrare**: en DNS-server har skapats som en del av distributionen av Azure-stacken. Ange din befintliga infrastruktur för DNS-server så att datorer i lösningen för att matcha namn utanför stämpeln. DNS-servern i stämpel vidarebefordras okänt namn på den här servern.
    - **Tidsserver**: här obligatoriska fältet anger tid och måste vara en IP-adress. Du hittar en IP-adress [pool.ntp.org](http:\\pool.ntp.org) eller pinga time.windows.com. 
8. Klicka på **Nästa**. 
9. På den **Kontrollera egenskaper för nätverksgränssnitt kort** sidan visas en förloppsindikator. 
    - Om det står **kan inte hämtas en uppdatering**, följ instruktionerna på sidan.
    - När det står **slutförd**, klickar du på **nästa**.
10. På **sammanfattning** klickar du på **distribuera**.
11. Om du använder en Azure Active Directory-distribution ska du ombedd att ange autentiseringsuppgifterna för global administratör ditt Azure Active Directory.
12. Distributionen kan ta några timmar, då systemet startar om automatiskt en gång.
   
   > [!IMPORTANT]
   > Om du vill övervaka förloppet för distributionen kan du logga in som azurestack\AzureStackAdmin. Om du loggar in som lokal administratör när datorn är ansluten till domänen, visas inte förloppet för distributionen. Inte köra distribution, i stället logga in som azurestack\AzureStackAdmin att verifiera att den körs.
   > 
   > 
   
    När distributionen lyckas PowerShell-konsolen visar: **Slutför: åtgärden 'Distribution'**.
   
Om distributionen av misslyckas, kan du använda följande Kör PowerShell-skript från samma upphöjd PowerShell-fönstret:

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

Det här skriptet kommer att startas om distributionen från det sista steget har slutförts.

Du kan [omdistribuera](azure-stack-redeploy.md) från början.


## <a name="reset-the-password-expiration-to-180-days"></a>Återställ lösenord upphör att gälla 180 dagar

Följ dessa steg om du vill kontrollera att lösenordet för development kit värden inte upphör att gälla för tidigt, när du har distribuerat:

Ändra förfalloprincipen för lösenord från Powershell:
1. Från fönstret Powershell kör du kommandot. Set-ADDefaultDomainPasswordPolicy - MaxPasswordAge 180.00:00:00-identitet azurestack.local

Ändra förfalloprincipen för lösenord manuellt:
1. Öppna på development kit värden **Grupprinciphantering** och gå till **Grupprinciphantering** – **skog: azurestack.local** – **domäner** – **azurestack.local**.
2. Högerklicka på **Standarddomänprincip** och på **redigera**.
3. I den Redigeraren för Grupprinciphantering, navigera till **Datorkonfiguration** – **principer** – **Windowsinställningar** – **säkerhetsinställningar**– **Konto principer** – **lösenordsprincip**.
4. I den högra rutan, dubbelklickar du på **högsta ålder för lösenord**.
5. I den **högsta ålder för lösenord egenskaper** i dialogrutan den **lösenord upphör att gälla om** värde till 180 och klicka sedan på **OK**.


## <a name="next-steps"></a>Nästa steg

[Installera PowerShell](azure-stack-powershell-configure-quickstart.md)

[Registrera Azure stacken med din Azure-prenumeration](azure-stack-register.md)

[Anslut till Azure Stack](azure-stack-connect-azure-stack.md)

