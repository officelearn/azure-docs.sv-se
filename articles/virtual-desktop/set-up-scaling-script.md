---
title: Skala automatiskt Windows Virtual Desktop Preview hosts-Azure
description: Beskriver hur du konfigurerar skriptet för automatisk skalning för Windows Virtual Desktop Preview-värdar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: d7b91e3e74c65919a3afe80addfbd0fadd23b03c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931810"
---
# <a name="automatically-scale-session-hosts"></a>Skala sessionsvärdar automatiskt

För många Windows-distributioner av virtuella Windows-datorer i Azure representerar de virtuella dator kostnaderna en betydande del av den totala distributions kostnaden för Windows Virtual Desktop. För att minska kostnaderna, är det bäst att stänga av och frigöra sessioner virtuella datorer (VM) under låg belastnings tider, och sedan starta om dem under hög användnings tid.

I den här artikeln används ett enkelt skalnings skript som automatiskt skalar virtuella datorer i Windows Virtual Desktop-miljön. Mer information om hur skalnings skriptet fungerar finns i avsnittet [hur skalnings skriptet fungerar](#how-the-scaling-script-works) .

## <a name="prerequisites"></a>Förutsättningar

Den miljö där du kör skriptet måste ha följande saker:

- En Windows-klient för virtuella skriv bord och-konto eller ett huvud namn för tjänsten med behörighet att fråga klienten (till exempel RDS-deltagare).
- VM-poolen för sessioner har kon figurer ATS och registrerats med Windows Virtual Desktop-tjänsten.
- En ytterligare virtuell dator som kör den schemalagda aktiviteten via Schemaläggaren och har nätverks åtkomst till sessionsbaserade värdar. Detta kommer att hänvisas till senare i dokumentet som en virtuell dator med scaleor.
- [PowerShell-modulen för Microsoft Azure Resource Manager](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) är installerad på den virtuella datorn som kör den schemalagda aktiviteten.
- [Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) är installerad på den virtuella datorn som kör den schemalagda aktiviteten.

## <a name="recommendations-and-limitations"></a>Rekommendationer och begränsningar

Tänk på följande när du kör skalnings skriptet:

- Detta skalnings skript kan bara hantera en adresspool per instans av den schemalagda aktivitet som kör skalnings skriptet.
- Schemalagda aktiviteter som kör skalnings skript måste finnas på en virtuell dator som alltid är aktive rad.
- Skapa en separat mapp för varje instans av skalnings skriptet och dess konfiguration.
- Det här skriptet stöder inte inloggning som administratör till Windows Virtual Desktop med Azure AD-användarkonton som kräver Multi-Factor Authentication. Vi rekommenderar att du använder tjänstens huvud namn för att få åtkomst till tjänsten Windows Virtual Desktop och Azure. Följ [den här](create-service-principal-role-powershell.md) självstudien för att skapa ett huvud namn för tjänsten och en roll tilldelning med PowerShell.
- Azures service avtals garanti gäller endast för virtuella datorer i en tillgänglighets uppsättning. Den aktuella versionen av dokumentet beskriver en miljö med en enskild virtuell dator som utför skalningen, vilket kanske inte uppfyller tillgänglighets kraven.

## <a name="deploy-the-scaling-script"></a>Distribuera skalnings skriptet

Följande procedurer visar hur du distribuerar skalnings skriptet.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Förbereda din miljö för skalnings skriptet

Börja med att förbereda din miljö för skalnings skriptet:

1. Logga in på den virtuella datorn (scaleor VM) som ska köra den schemalagda aktiviteten med ett domän administratörs konto.
2. Skapa en mapp på den virtuella dator som innehåller skalnings skriptet och dess konfiguration (till exempel **C:\\skalning-HostPool1**).
3. Hämta filerna **basicScale. ps1**, **config. XML**och **Functions-PSStoredCredentials. ps1** och mappen **PowershellModules** från [skalnings skriptets lagrings plats](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) och kopiera dem till den mapp som du skapade i steg 2. Det finns två huvudsakliga sätt att hämta filerna innan du kopierar dem till den virtuella datorns skalar:
    - Klona git-lagringsplatsen till den lokala datorn.
    - Visa den **råa** versionen av varje fil, kopiera och klistra in innehållet i varje fil i en text redigerare och spara sedan filerna med motsvarande fil namn och filtyp. 

### <a name="create-securely-stored-credentials"></a>Skapa säkerhets lagrade autentiseringsuppgifter

Därefter måste du skapa de säkerhets lagrade autentiseringsuppgifterna:

1. Öppna PowerShell ISE som administratör.
2. Importera modulen RDS PowerShell genom att köra följande cmdlet:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Öppna fönstret Redigera och Läs in filen **Function-PSStoredCredentials. ps1** och kör sedan hela skriptet (F5)
4. Kör följande cmdlet:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Ange till exempel **variabel namn nyckel Sök väg – omfattning globalt-värde "c:\\skalning-HostPool1"**
5. Kör cmdleten **New-StoredCredential-nyckel \$** Path. När du uppmanas till det anger du dina autentiseringsuppgifter för ditt Windows-konto med behörigheter för att ställa frågor till fakturapoolen (värden anges i **config. XML**).
    - Om du använder olika tjänst huvud namn eller standard konto kör du cmdleten **New-StoredCredential-sökväg \$Path** en gång för varje konto för att skapa lokala lagrade autentiseringsuppgifter.
6. Kör **Get-StoredCredential-List** för att bekräfta att autentiseringsuppgifterna har skapats.

### <a name="configure-the-configxml-file"></a>Konfigurera filen config. XML

Ange de relevanta värdena i följande fält för att uppdatera skalnings skript inställningarna i config. XML:

| Fält                     | Beskrivning                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | Azure AD-klient-ID som associerar prenumerationen där sessionens värd för virtuella datorer körs     |
| AADApplicationId              | Program-ID för tjänstens huvud namn                                                       |
| AADServicePrincipalSecret     | Detta kan anges under test fasen, men ska vara tom när du skapar autentiseringsuppgifter med **Functions-PSStoredCredentials. ps1**    |
| currentAzureSubscriptionId    | ID: t för den Azure-prenumeration där sessionens värd för virtuella datorer körs                        |
| tenantName                    | Klient namn för Windows Virtual Desktop                                                    |
| hostPoolName                  | Namn på Windows-adresspool för värd för virtuell dator                                                 |
| RDBroker                      | URL till WVD-tjänsten, standardvärde https:\//rdbroker.WVD.Microsoft.com             |
| Användarnamn                      | Det program-ID för tjänstens huvud namn (det är möjligt att ha samma huvud namn för tjänsten som i AADApplicationId) eller en standard användare utan Multi-Factor Authentication |
| isServicePrincipal            | Godkända värden är **True** eller **false**. Anger om den andra uppsättningen autentiseringsuppgifter som används är ett huvud namn för tjänsten eller ett standard konto. |
| BeginPeakTime                 | När den högsta användnings tiden börjar                                                            |
| EndPeakTime                   | När tiden för maximal användning upphör                                                              |
| TimeDifferenceInHours         | Tids skillnad mellan lokal tid och UTC, i timmar                                   |
| SessionThresholdPerCPU        | Maximalt antal sessioner per CPU-tröskel som används för att fastställa när en ny virtuell dator i sessionen måste startas under hög belastnings tid.  |
| MinimumNumberOfRDSH           | Minsta antal virtuella värdar för virtuella datorer som ska fortsätta att köras vid låg belastnings tider             |
| LimitSecondsToForceLogOffUser | Antal sekunder att vänta innan användarna tvingas att logga ut. Om värdet är 0 tvingas användarna inte att logga ut.  |
| LogOffMessageTitle            | Rubriken på meddelandet som skickas till en användare innan de tvingas logga ut                  |
| LogOffMessageBody             | Texten i varnings meddelandet som skickas till användarna innan de loggas ut. Exempel: "den här datorn stängs av om X minuter. Spara ditt arbete och logga ut. " |

### <a name="configure-the-task-scheduler"></a>Konfigurera Schemaläggaren

När du har konfigurerat Configuration. XML-filen måste du konfigurera Schemaläggaren för att köra filen basicScaler. ps1 med jämna mellanrum.

1. Starta **Schemaläggaren**.
2. I fönstret **Schemaläggaren** väljer du **Skapa aktivitet...**
3. I dialog rutan **Skapa uppgift** väljer du fliken **Allmänt** , anger ett **namn** (till exempel "dynamiskt RDSH") och väljer **Kör oavsett om användaren är inloggad eller inte** och **Kör med högsta behörighet**.
4. Gå till fliken utlösare och välj sedan **nytt...**
5. I dialog **rutan ny** utlösare, under **Avancerade inställningar**, markerar du **Upprepa aktivitet varje** och väljer lämplig period och varaktighet (till exempel **15 minuter** eller **oändligt**).
6. Välj fliken **åtgärder** och **ny...**
7. I dialog rutan **ny åtgärd** anger du **PowerShell. exe** i fältet **program/skript** och anger sedan **C:\\skala\\basicScale. ps1** i fältet **Lägg till argument (valfritt)** .
8. Gå till fliken **villkor** och **Inställningar** och välj **OK** om du vill godkänna standardinställningarna för var och en.
9. Ange lösen ordet för det administratörs konto där du planerar att köra skalnings skriptet.

## <a name="how-the-scaling-script-works"></a>Hur skalnings skriptet fungerar

Detta skalnings skript läser inställningar från en config. XML-fil, inklusive start och slut för den högsta användnings perioden under dagen.

Under den högsta användnings tiden kontrollerar skriptet det aktuella antalet sessioner och den aktuella RDSH-kapaciteten som körs för varje adresspool. Det beräknar om de virtuella datorerna som körs på den virtuella datorn har tillräckligt med kapacitet för att stödja befintliga sessioner baserat på den SessionThresholdPerCPU-parameter som definierats i filen config. xml. Om inte, startar skriptet ytterligare virtuella datorer i sessions värden i poolen.

Under den högsta användnings tiden avgör skriptet vilken virtuell dator i sessionen som ska stängas av baserat på parametern MinimumNumberOfRDSH i filen config. xml. Skriptet ställer in sessionens värden för virtuella datorer i tömnings läge för att förhindra att nya sessioner ansluter till värdarna. Om du anger parametern **LimitSecondsToForceLogOffUser** i filen config. xml till ett positivt värde som inte är noll, meddelar skriptet om användare som är inloggade att spara arbete, väntar på den konfigurerade tiden och tvingar sedan användarna att logga ut. När alla användarsessioner har signerats på en virtuell dator i en fjärrskrivbordssession stängs-servern av.

Om du ställer in parametern **LimitSecondsToForceLogOffUser** i config. XML-filen på noll, tillåter skriptet konfigurations inställningen för sessionen i egenskaperna för den värdbaserade poolen för att hantera signering av användarsessioner. Om det finns några sessioner på en virtuell dator i en session, lämnar den virtuella datorn som körs av sessionen. Om det inte finns några sessioner stängs skriptet av den virtuella datorns sessions värd.

Skriptet är utformat för att köras regelbundet på den virtuella dator servern för skalnings datorer med hjälp av Schemaläggaren. Välj lämpligt tidsintervall baserat på storleken på din Fjärrskrivbordstjänsters miljö och kom ihåg att det kan ta en stund att starta och stänga av virtuella datorer. Vi rekommenderar att du kör skalnings skriptet var 15: e minut.

## <a name="log-files"></a>Loggfiler

Skalnings skriptet skapar två loggfiler, **WVDTenantScale. log** och **WVDTenantUsage. log**. **WVDTenantScale. log** -filen kommer att logga händelser och fel (om sådana finns) under varje körning av skalnings skriptet.

Filen **WVDTenantUsage. log** registrerar det aktiva antalet kärnor och det aktiva antalet virtuella datorer varje gång du kör skalnings skriptet. Du kan använda den här informationen för att uppskatta den faktiska användningen av Microsoft Azure virtuella datorer och kostnaden. Filen formateras som kommaavgränsade värden, där varje objekt innehåller följande information:

>tid, värdbaserad pool, kärnor, virtuella datorer

Fil namnet kan också ändras till att ha ett. csv-tillägg som läses in i Microsoft Excel och analyseras.
