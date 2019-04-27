---
title: Skala automatiskt Windows Virtual Desktop förhandsversion session-värdar – Azure
description: Beskriver hur du ställer in skriptet för automatisk skalning för Windows Virtual Desktop Preview session-värdar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 379e73c33aa4570c3e56f902b011d75944c94a8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870731"
---
# <a name="automatically-scale-session-hosts"></a>Skala sessionsvärdar automatiskt

För många förhandsversion för virtuella skrivbord i Windows-distributioner i Azure representerar VM-kostnader stor del av den totala kostnaden för distribution av virtuella Windows-skrivbordet. Det är bäst att stänga av och frigör session virtuella datorer (VM) under perioder med låg användningstimmar och sedan starta om dem under belastning för användning för att minska kostnaderna.

Den här artikeln används ett enkelt skala skript för automatisk skalning session virtuella datorer i din miljö för virtuella Windows-skrivbordet. Mer information om hur skalning skriptet fungerar finns i [hur skalning skriptet fungerar](#how-the-scaling-script-works) avsnittet.

## <a name="prerequisites"></a>Nödvändiga komponenter

Miljön där du kör skriptet måste ha följande saker:

- En klient för virtuella Windows-skrivbordet och konto eller ett huvudnamn för tjänsten med behörighet att fråga den klienten (till exempel RDS deltagare).
- Sessionen värd pool med virtuella datorer konfigureras och registrerats för virtuellt skrivbord i Windows-tjänsten.
- En ytterligare scaler virtuell dator som kör den schemalagda aktiviteten via Schemaläggaren och som har åtkomst till sessionen värdar.
- Microsoft Azure Resource Managers PowerShell-modulen installerad på den virtuella datorn som kör den schemalagda aktiviteten.
- Windows Virtual Desktop PowerShell-modulen installerad på den virtuella datorn som kör den schemalagda aktiviteten.

## <a name="recommendations-and-limitations"></a>Rekommendationer och begränsningar

När du kör skriptet skalning kan du ha följande i åtanke:

- Skalning skriptet kan endast hantera en värd pool per instans av den schemalagda aktiviteten som körs skriptet skalning.
- Schemalagda aktiviteter som kör skalning skript måste finnas på en virtuell dator som är alltid aktiverat.
- Skapa en separat mapp för varje instans av skalning skriptet och dess konfiguration.
- Det här skriptet stöd inte för konton med Multi-Factor authentication. Vi rekommenderar att du använder tjänstens huvudnamn åtkomst till virtuella skrivbord i Windows-tjänsten och Azure.
- Azure SLA-garantin gäller bara för virtuella datorer i en tillgänglighetsuppsättning. Den aktuella versionen av dokumentet beskriver en miljö med en enda virtuell dator gör skalning, vilket inte kanske uppfyller kraven på tillgänglighet.

## <a name="deploy-the-scaling-script"></a>Distribuera skriptet skalning

Följande procedurer kommer berättar hur du distribuerar skalning skriptet.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Förbereda miljön för skriptet skalning

Börja med att förbereda din miljö för skalning skriptet:

1. Logga in på den virtuella datorn (**skalning VM**) som ska köra den schemalagda aktiviteten med ett administratörskonto för domänen.
2. Skapa en mapp på skalning VM skalning skriptet och dess konfiguration (till exempel **C:\\skalning HostPool1**).
3. Ladda ned den **basicScaler.ps1**, **Config.xml**, och **Functions PSStoredCredentials.ps1** filer, och **PowershellModules** mappen från den [skalning skriptdatabas](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) och kopiera dem till den mapp du skapade i steg 2.

### <a name="create-securely-stored-credentials"></a>Skapa säkert lagrade autentiseringsuppgifter

Därefter måste du skapa säkert lagrade autentiseringsuppgifter:

1. Öppna PowerShell ISE som administratör.
2. Öppna redigeringsrutan och Läs in den **funktionen PSStoredCredentials.ps1** fil.
3. Kör följande cmdlet:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Till exempel **Set-Variable - namnet KeyPath-omfång globala-värdet ”c:\\skalning HostPool1”**
4. Kör den **New-StoredCredential - KeyPath \$KeyPath** cmdlet. När du uppmanas, anger du dina autentiseringsuppgifter för virtuella Windows-skrivbordet med behörighet att fråga poolen värden (värd-adresspool har angetts i den **config.xml**).
    - Om du använder olika tjänstens huvudnamn eller konto av standardtyp kan köra den **New-StoredCredential - nyckelsökvägen \$KeyPath** cmdlet när lagras för varje konto för att skapa lokala autentiseringsuppgifter.
5. Kör **Get-StoredCredentials-listan** att bekräfta autentiseringsuppgifterna som har skapats.

### <a name="configure-the-configxml-file"></a>Konfigurera filen config.xml

Ange de relevanta värdena i följande fält i uppdatera skalningsinställningarna i config.xml skript:

| Fält                     | Beskrivning                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | Azure AD-klient-ID som associerar den prenumeration som var värd för fjärrskrivbordssession virtuella datorer körs     |
| AADApplicationId              | Service principal program-ID                                                       |
| AADServicePrincipalSecret     | Detta kan anges under testfasen men det är behålls tom när du skapar autentiseringsuppgifter med **Functions PSStoredCredentials.ps1**    |
| currentAzureSubscriptionId    | ID för Azure-prenumerationen där sessionen vara värd för virtuella datorer kör                        |
| tenantName                    | Virtuella Windows-skrivbordet klientnamn                                                    |
| hostPoolName                  | Virtuella Windows-skrivbordet värdnamn för poolen                                                 |
| RDBroker                      | URL till tjänsten WVD standard värdet https:\//rdbroker.wvd.microsoft.com             |
| Användarnamn                      | ID för tjänstens huvudnamn programmet (det är möjligt att ha samma tjänstens huvudnamn som i AADApplicationId) eller standardanvändare utan Multi-Factor authentication |
| isServicePrincipal            | Godkända värden är **SANT** eller **FALSKT**. Anger om den andra uppsättningen av autentiseringsuppgifter som används är ett huvudnamn för tjänsten eller ett standardkonto. |
| BeginPeakTime                 | När tid för användning med högsta användningsnivå börjar                                                            |
| EndPeakTime                   | När tid för användning med högsta användningsnivå slutar                                                              |
| TimeDifferenceInHours         | Tidsskillnaden mellan lokal tid och UTC, i timmar                                   |
| SessionThresholdPerCPU        | Maximalt antal sessioner per CPU-tröskelvärdet som används för att avgöra när en ny RDSH-server måste vara igång under belastning.  |
| MinimumNumberOfRDSH           | Minsta antal värden pool med virtuella datorer kan fortsätta att köras under tider med mindre tid             |
| LimitSecondsToForceLogOffUser | Antal sekunder som ska förflyta innan tvinga användare att logga ut. Om värdet är 0 användare inte tvingas att logga ut.  |
| LogOffMessageTitle            | Rubriken på meddelandet som skickas till en användare innan de måste logga ut                  |
| LogOffMessageBody             | Brödtexten i varningsmeddelandet som skickas till användare innan de har loggat ut. Exempel ”: den här datorn stängs ned i X minuter. Spara ditt arbete och logga ut ”. |

### <a name="configure-the-task-scheduler"></a>Konfigurera Schemaläggaren

När du har konfigurerat configuration XML-filen måste du konfigurera Schemaläggaren för att köra filen basicScaler.ps1 med jämna mellanrum.

1. Starta **Schemaläggaren**.
2. I den **Schemaläggaren** väljer **Skapa uppgift...**
3. I **Skapa uppgift** dialogrutan den **Allmänt** ange en **namn** (t.ex. ”dynamisk RDSH”), Välj **kör oavsett om användaren är inloggad eller inte** och **kör med högsta behörighet**.
4. Gå till den **utlösare** och sedan välja **New...**
5. I den **ny utlösare** dialogrutan under **avancerade inställningar**, kontrollera **Upprepa aktiviteten varje** och välj lämplig tid och varaktighet (till exempel **15 minuter** eller **på obestämd tid**).
6. Välj den **åtgärder** fliken och **New...**
7. I den **ny åtgärd** dialogrutan Ange **powershell.exe** till den **Program eller skript** fältet och klicka sedan på returtangenten **C:\\skalning\\ RDSScaler.ps1** till den **lägga till argument (valfritt)** fält.
8. Gå till den **villkor** och **inställningar** flikar och välj **OK** att acceptera standardinställningarna för var och en.
9. Ange lösenordet för administratörskontot som du planerar att köra skriptet skalning.

## <a name="how-the-scaling-script-works"></a>Hur skalning skriptet fungerar

Det här skalning skriptet läser inställningar från en config.xml-fil, inklusive början och slutet av högsta användningsperiod under dagen.

Under tiden för högsta användning kan kontrollerar skriptet det aktuella antalet sessioner och den aktuella körs RDSH-kapaciteten för varje samling. Beräknas om de körs RDSH-servrarna har tillräcklig kapacitet för befintliga sessioner baserat på parametern SessionThresholdPerCPU som definieras i filen config.xml. Om inte skriptet startar ytterligare RDSH-servrar i samlingen.

Under perioder med låg användningstid avgör skriptet vilka RDSH-servrar bör stänga av baserat på parametern MinimumNumberOfRDSH i filen Config.XML. Skriptet anger RDSH-servrar för att tömma läge för att förhindra att nya sessioner som ansluter till värdarna. Om du ställer in den **LimitSecondsToForceLogOffUser** parameter i Config.XML ett positivt värde inte är noll, skriptet meddelar någon inloggad användare spara arbete, vänta angiven tidsperiod och tvinga sedan den användare kan logga ut. När alla användarsessioner har loggats på en RDSH-server, kommer skriptet stänga av servern.

Om du ställer in den **LimitSecondsToForceLogOffUser** parameter i Config.XML noll med skriptet kan Konfigurationsinställningen sessionen i samlingsegenskaperna att hantera logga ut ur användarsessioner. Om det finns alla sessioner på en RDSH-server, lämnar den RDSH-server som kör. Om det inte alla sessioner, stängs skriptet RDSH-server.

Skriptet är avsedd att köras med jämna mellanrum på scaler VM-servern med hjälp av Schemaläggaren. Välj önskat tidsintervall baserat på storleken på din miljö för Fjärrskrivbordstjänster och Kom ihåg att starta och stänga av virtuella datorer kan ta lite tid. Vi rekommenderar att skriptet har körts skalning var 15: e minut.

## <a name="log-files"></a>Loggfiler

Skalning skriptet skapar två loggfiler **WVDTenantScale.log** och **WVDTenantUsage.log**. Den **WVDTenantScale.log** filen loggas händelser och fel (i förekommande fall) under varje körningen av skriptet skalning.

Den **WVDTenantUsage.log** filen antecknar active antalet kärnor och aktiva antalet virtuella datorer varje gång du kör skriptet skalning. Du kan använda den här informationen för att uppskatta den faktiska användningen av Microsoft Azure Virtual Machines och kostnaden. Filen formateras som kommateckenavgränsade värden med varje objekt som innehåller följande information:

>tid, samling och kärnor, virtuella datorer

Filnamnet kan också ändras så att den har filnamnstillägget .csv läses in i Microsoft Excel och analyseras.