<properties
    pageTitle="Distribuera Azure Stack POC | Microsoft Azure"
    description="Lär dig hur du förbereder Azure Stack POC och kör PowerShell-skript för att distribuera Azure Stack POC."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/29/2016"
    ms.author="erikje"/>


# Distribuera Azure Stack POC
Om du vill distribuera Azure Stack POC måste du först [förbereda distributionsdatorn](#prepare-the-deployment-machine) och sedan [köra PowerShell-distributionsskriptet](#run-the-powershell-deployment-script).

## Förbereda datorn för distribution

1. Kontrollera att du kan ansluta fysiskt till distributionsdatorn eller har fysisk konsolåtkomst (till exempel KVM). Du behöver sådan åtkomst efter omstart av distributionsdatorn för distribution i steg 9.
 
2. Kontrollera att distributionsdatorn uppfyller [minimikraven](azure-stack-deploy.md). Du kan använda [Deployment Checker for Azure Stack Technical Preview 1](https://gallery.technet.microsoft.com/Deployment-Checker-for-76d824e1) (Distributionskontrollen för Azure Stack Technical Preview 1)för att bekräfta dina krav.

3.  [Hämta](http://aka.ms/ReqOSforAzureStack) och installera Windows Server 2016 Datacenter Edition Technical Preview 4 EN-US (Full Edition).

4.  [Hämta](https://azure.microsoft.com/overview/azure-stack/try/?v=try) Azure Stack POC-distributionspaketet till en mapp på enhet C, (till exempel c:\\AzureStack).

5.  Kör filen **Microsoft Azure Stack POC.exe**.

    Då skapas \\Microsoft Azure Stack POC\\-mappen som innehåller följande objekt:

    -   DeployAzureStack.ps1: PowerShell-installationsskript för Azure Stack POC

    -   MicrosoftAzureStackPOC.vhdx: Azure Stack-datapaket

    -   SQLServer2014.vhdx: SQL Server VHD

    -   WindowsServer2012R2DatacenterEval.vhd

    -   WindowsServer2016Datacenter.vhdx: Windows Server 2016 Datacenter VHD (innehåller KB 3124262)

    > [AZURE.IMPORTANT] Du måste ha minst 128 GB ledigt utrymme på den fysiska startvolymen.

6. Kopiera WindowsServer2016Datacenter.vhdx till C:\-enheten och byt namn till MicrosoftAzureStackPOCBoot.vhdx.

7. I Utforskaren högerklickar du på MicrosoftAzureStackPOCBoot.vhdx och klickar på **Montera**.

8. Öppna ett kommandotolksfönster som administratör och kör kommandot bcdboot. Kommandot startar en dubbel startmiljö. Härifrån kan du starta med det övre startalternativet.

        bcdboot <mounted drive letter>:\windows

9. Starta om datorn. Installationsprogrammet för Windows körs automatiskt när VHD-systemet är förberett. Sedan behöver du fysiskt ansluta till distributionsdatorn ha åtkomst till den fysiska konsolen för att utföra stegen nedan.

10. Om BIOS innehåller dessa alternativ ska du konfigurera den för lokal tid istället för UTC-tid.

11. När du blir ombedd ska du ange ditt land, språk, tangentbord och andra inställningar. Om du tillfrågas om produktnyckeln du kan hitta den i [Systemkrav och installation](https://technet.microsoft.com/library/mt126134.aspx).

12. När du blir ombedd ska du ange lösenord för administratörskontot och logga in med det kontonamnet och lösenordet.

13. När du har startat om och loggat in och inte har DHCP ställer du in statisk konfiguration på nätverkskortet.

14. Aktivera anslutningar till fjärrskrivbord genom att gå till **Systemegenskaper** och välja alternativet **Tillåt fjärranslutningar till den här datorn**.

15. Logga in med ett lokalt konto med administratörsbehörighet.

16. Kontrollera att **exakt** fyra enheter för Azure Stack POC-data:
  - Visas i Diskhantering
  - Inte används
  - Visas som online, allokerade

17. Kontrollera att värden är ansluten till en domän.

18. Med Internet Explorer kontrollerar du nätverksanslutningen till Azure.com.

> [AZURE.IMPORTANT] TP1 POC-distributionen har stöd för exakt fyra enheter för lagringsfunktionerna och endast ett nätverkskort för nätverk.
>
> - **För lagring** ska du använda Enhetshanteraren eller WMI för att inaktivera alla andra enheter (det räcker inte att sätta diskar i offlineläge via diskhanteraren).
>
> - Om du har flera nätverkskort **för nätverket** ska du kontrollera att endast ett är aktiverat (och att alla andra har inaktiverats) innan du kör distributionsskriptet nedan.
>
> Om du genomförde VHD-startstegen som definieras ovan måste du göra uppdateringarna när du har startat VHD och innan du startar distributionsskriptet.

## Kör PowerShell-skriptet för distribution

1. Flytta följande filer för distribution från D:\Microsoft Azure Stack POC\ till C:\Microsoft Azure Stack POC\.
    - DeployAzureStack.ps1
    - MicrosoftAzureStackPOC.vhdx
    - SQLServer14.vhdx
    - WindowsServer2012R2DatacenterEval.vhd
    - WindowsServer2016Datacenter.vhdx

2.  Öppna PowerShell som administratör.

3.  Gå till mappen Azure Stack i PowerShell (\\Microsoft Azure Stack POC\\ om du använder standardinställningarna).

4.  Kör kommandot för att distribuera:

        .\DeployAzureStack.ps1 –Verbose

    Använd följande kommando i stället i Kina:

        .\DeployAzureStack.ps1 –Verbose -UseAADChina $true

    Distributionen startar och domännamnet Azure Stack POC är hårdkodat som azurestack.local.

5.  När du uppmanas att **ange lösenordet för det inbyggda administratörskontot** anger du ett lösenord och bekräftar det sedan. Detta är lösenordet för alla virtuella datorer. Se till att registrera tjänstadministratörslösenordet.

6.  På **popup-autentiseringssidan för att logga in på Azure-kontot** trycker du på valfri tangent för att öppna dialogrutan för inloggning på Microsoft Azure.

7.  Ange autentiseringsuppgifterna för ditt Azure Active Directory-konto. Den här användaren måste vara global administratör i Directory-klienten

8.  I PowerShell anger du *y* i bekräftelsemeddelandet för val av konto. Då skapas två användare och tre program för Azure Stack i den Directory-klienten: en administratörsanvändare för Azure Stack, en klientanvändare för TiP-testerna och ett program för portalen, API och resursprovider för övervakning. Utöver detta kan installationsprogrammet lägga till medgivanden för Azure PowerShell, XPlat CLI och Visual Studio som Directory-klient.

9.  När du ser att **Microsoft Azure Stack POC är redo att distribuera och får frågan om du vill fortsätta** anger du *j*.

10.  Distributionsprocessen tar några timmar, då flera automatiska omstarter utförs. När du loggar in under distributionen startas ett PowerShell-fönster automatiskt som visar förloppet för distributionen. PowerShell-fönstret stängs när distributionen är klar.

11. På datorn med Azure Stack POC loggar du in som AzureStack\administratör, öppnar **Serverhanteraren** och stänger av **Förbättrad säkerhetskonfiguration i IE** både för administratörer och användare.

Om distributionen misslyckas på grund av ett tid- eller datumfel konfigurerar du BIOS för lokal tid istället för UTC. Omdistribuera sedan.

Om skriptet misslyckas startar du om det. Om det fortsätter att misslyckas rensar du det och startar om.

Du kan hitta skriptloggarna på POC-värden `C:\ProgramData\microsoft\azurestack`.

### Valfria parametrar för DeployAzureStack.ps1

**AADCredential** (PSCredential) – Anger användarnamn och lösenord för Azure Active Directory. Om den här parametern inte anges ber skriptet om användarnamn och lösenord.

**AADTenant** (sträng) – Anger klientkatalog. Använd den här parametern om du vill ange en viss katalog när AAD-kontot har behörighet att hantera flera kataloger. Om den här parametern inte anges ber skriptet om katalogen.

**AdminPassword** (SecureString) – Anger standardadministratörslösenord. Om den här parametern inte anges ber skriptet om lösenordet.

**Tvinga** (växla) – Anger att cmdleten ska köras utan bekräftelse.

**NATVMStaticGateway** (sträng) – Anger vilken standardgateway som används i den statiska IP-adressen för NATVM. Använd enbart den här parametern om DHCP inte kan tilldela en giltig IP-adress för att få åtkomst till Internet. Om du använder den här parametern måste du också använda parametern NATVMStaticIP.
Exempel: `.\DeployAzureStack.ps1 –Verbose -NATVMStaticIP 10.10.10.10/24 – NATVMStaticGateway 10.10.10.1`

**NATVMStaticIP** (sträng) – Ställer in ytterligare en statisk IP-adress för NATVM. Använd enbart den här parametern om DHCP inte kan tilldela en giltig IP-adress för att få åtkomst till Internet.
Exempel: `.\DeployAzureStack.ps1 –Verbose -NATVMStaticIP 10.10.10.10/24`

**NoAutoReboot** (växla) – Anger att skriptet ska köras utan automatiska omstarter.

**ProxyServer** (sträng) – Ställer in proxyinformation. Använd bara den här parametern om din miljö måste använda en proxyserver för att få åtkomst till Internet. Proxyservrar som kräver autentiseringsuppgifter stöds inte.
Exempel: `.\DeployAzureStack.ps1 -Verbose -ProxyServer 172.11.1.1:8080`

**PublicVLan** (sträng) – Ställer in VLAN-ID. Använd bara den här parametern om värden och NATVM måste konfigurera VLAN-ID för att komma åt det fysiska nätverket (och Internet).
Exempel: `.\DeployAzureStack.ps1 –Verbose –PublicVLan 305`

**TIPServiceAdminCredential** (PSCredential) – Anger autentiseringsuppgifterna för en befintlig tjänstadministratör för ett Azure Active Directory-konto. Det här kontot används av TiP (Test i produktion). Om den här parametern inte anges skapas automatiskt ett konto.

**TIPTenantAdminCredential** (PSCredential) – Anger autentiseringsuppgifterna för en befintlig klientadministratör för ett Azure Active Directory-konto. Det här kontot används av TiP (Test i produktion). Om den här parametern inte anges skapas automatiskt ett konto.

**UseAADChina**(boolesk) – Ställ in den här booleska parametern på $sant om du vill distribuera Microsoft Azure Stack POC med Azure China (Mooncake).

## Inaktivera automatiska TiP-tester (valfritt)

Microsoft Azure Stack Technical Preview 1 innehåller en uppsättning verifieringstester som används under distributionsprocessen och på ett återkommande dagsschema. De simulerar åtgärder som vidtas av en Azure Stack-klient och TiP-användarkonton (Test-in-POC) skapas i Azure Active Directory för att testerna ska kunna köras. Efter slutförd distribution kan du inaktivera TiP-testerna. 

**Inaktivera automatiska TiP-tester**

  - Kör följande cmdlet på ClientVM:

  `Disable-ScheduledTask -TaskName AzureStackSystemvalidationTask`

**Visa testresultaten**

  - Kör följande cmdlet på ClientVM:

  `Get-AzureStackTiPTestsResult`



## Inaktivera telemetri för Microsoft Azure Stack POC (valfritt)


Innan du distribuerar Microsoft Azure Stack POC kan du stänga av telemetri för Microsoft Azure Stack på datorn där distributionen utförs. Om du vill stänga av den här funktionen på en dator går du till: [http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq](http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq) och ändrar inställningen **Diagnostik- och användningsdata** till **Basic**.



När du har distribuerat Microsoft Azure Stack POC kan du inaktivera telemetri på alla virtuella datorer som anslöts till Azure Stack-domänen. Om du vill skapa en grupprincip och hantera dina telemetriinställningar på de virtuella datorerna går du till: [https://technet.microsoft.com/library/mt577208(v=vs.85).aspx\#BKMK\_UTC](https://technet.microsoft.com/library/mt577208%28v=vs.85%29.aspx#BKMK_UTC) och väljer **0** eller **1** för gruppolicyn **Tillåt telemetri**. Två virtuella datorer (bgpvm och natvm) anslöts inte till Azure Stack-domänen. Om du vill ändra inställningarna för feedback och diagnostik på de virtuella datorerna separat går du till:  [http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq](http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq).

## Nästa steg

[Anslut till Azure Stack](azure-stack-connect-azure-stack.md)



<!--HONumber=Sep16_HO3-->


