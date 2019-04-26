---
title: Felsöka lösenordshashsynkronisering med Azure AD Connect-synkronisering | Microsoft Docs
description: Den här artikeln innehåller information om hur du felsöker problem med lösenord hash-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/13/2017
ms.date: 04/09/2019
ms.subservice: hybrid
ms.author: v-junlch
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6feed11fcfc597658f3ec148b5dd18bb7e3f8f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383294"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Felsöka lösenordshashsynkronisering med Azure AD Connect-synkronisering

Det här avsnittet innehåller anvisningar att felsöka problem med synkronisering av lösenordshash. Om lösenord inte synkroniseras som förväntat, kan det vara antingen för en delmängd användare eller för alla användare.

För Azure Active Directory (Azure AD) Connect-distribution med version 1.1.614.0 eller efter användning av felsökningsaktiviteten i guiden för att felsöka synkronisering av lösenordshash-frågor:

* Om du har ett problem där inga lösenord synkroniseras, referera till den [inga lösenord synkroniseras: felsöka med hjälp av felsökningsaktiviteten](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) avsnittet.

* Om du har ett problem med enskilda objekt, referera till den [ett objekt synkroniseras inte lösenord: felsöka med hjälp av felsökningsaktiviteten](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) avsnittet.

För distribution med version 1.1.524.0 eller senare, det finns en diagnostisk cmdlet som du kan använda för att felsöka problem med lösenordshash-synkronisering:

* Om du har ett problem där inga lösenord synkroniseras, referera till den [inga lösenord synkroniseras: felsöka med hjälp av cmdleten diagnostiska](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) avsnittet.

* Om du har ett problem med enskilda objekt, referera till den [ett objekt synkroniseras inte lösenord: felsöka med hjälp av cmdleten diagnostiska](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) avsnittet.

För äldre versioner av Azure AD Connect-distribution:

* Om du har ett problem där inga lösenord synkroniseras, referera till den [inga lösenord synkroniseras: manuell felsökningssteg](#no-passwords-are-synchronized-manual-troubleshooting-steps) avsnittet.

* Om du har ett problem med enskilda objekt, referera till den [ett objekt synkroniseras inte lösenord: manuell felsökningssteg](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) avsnittet.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Inga lösenord synkroniseras: felsöka med hjälp av felsökningsaktiviteten

Du kan använda av felsökningsaktiviteten för att ta reda på varför inga lösenord synkroniseras.

> [!NOTE]
> Av felsökningsaktiviteten är endast tillgänglig för Azure AD Connect-version 1.1.614.0 eller senare.

### <a name="run-the-troubleshooting-task"></a>Kör av felsökningsaktiviteten

Felsökning av problem där inga lösenord synkroniseras:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-server med den **kör som administratör** alternativet.

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.

3. Starta Azure AD Connect-guiden.

4. Navigera till den **ytterligare uppgifter** väljer **Felsök**, och klicka på **nästa**.

5. På sidan felsökning **starta** till startmenyn felsökning i PowerShell.

6. Välj i huvudmenyn **Felsök synkronisering av lösenordshash**.

7. I menyn sub väljer **lösenordshashsynkronisering inte fungerar alls**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av av felsökningsaktiviteten

Av felsökningsaktiviteten utför följande kontroller:

* Verifierar att funktionen lösenord hash-synkronisering är aktiverat för Azure AD-klienten.

* Verifierar att Azure AD Connect-servern inte är i mellanlagringsläge.

* För den befintliga lokala Active Directory-koppling (som motsvarar en befintlig Active Directory-skog):

   * Verifierar att funktionen lösenord hash-synkronisering är aktiverat.
   
   * Söker efter lösenord hash-synkronisering pulsslagshändelser i händelseloggar för Windows-program.

   * För varje Active Directory-domän under den lokala Active Directory-koppling:

      * Verifierar att domänen kan nås från Azure AD Connect-servern.

      * Verifierar att Active Directory Domain Services (AD DS)-konton som används av den lokala Active Directory-koppling har rätt användarnamn, lösenord och behörigheter som krävs för synkronisering av lösenordshash.

I följande diagram visas resultatet av cmdlet: en för en enda domän, en lokal Active Directory-topologi:

![Diagnostikdata för synkronisering av lösenordshash](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

Resten av det här avsnittet beskriver specifika resultat som returneras av aktiviteten och motsvarande problem.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>lösenord hash-synkroniseringsfunktionen är inte aktiverad

Om du inte har aktiverat synkronisering av lösenordshash med hjälp av Azure AD Connect-guiden, returneras följande fel:

![synkronisering av lösenordshash har inte aktiverats](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect-servern är i mellanlagringsläge

Om Azure AD Connect-servern är i mellanlagringsläge, synkronisering av lösenordshash inaktiveras tillfälligt och följande fel returneras:

![Azure AD Connect-servern är i mellanlagringsläge](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Inga lösenord hash-synkronisering pulsslagshändelser

Varje lokala Active Directory-koppling har sin egen lösenord hash-synkronisering kanal. När lösenord hash-synkronisering kanal upprättas och det inte finns några lösenordsändringar som ska synkroniseras, genereras ett pulsslag händelse (händelse-ID 654) var 30: e minut under Windows Loggboken program. För varje lokal Active Directory-koppling söker cmdlet: en för motsvarande händelser för pulsslag under de senaste tre timmarna. Om ingen pulsslag händelse hittas returneras följande fel:

![Inga lösenord hash-synkronisering hjärtat beat händelse](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS-kontot har inte rätt behörighet

Om AD DS-kontot som används av den lokala Active Directory-koppling för att synkronisera lösenords-hash inte har de behörigheter som krävs, returneras följande fel:

![Felaktiga autentiseringsuppgifter](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Felaktigt användarnamn för AD DS-konto eller lösenord

Om AD DS-kontot som används av den lokala Active Directory-koppling för synkronisering av lösenords-hash har ett felaktigt användarnamn eller lösenord, returneras följande fel:

![Felaktiga autentiseringsuppgifter](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Ett objekt synkroniseras inte lösenord: felsöka med hjälp av felsökningsaktiviteten

Du kan använda av felsökningsaktiviteten för att avgöra varför en objekt synkroniseras inte lösenord.

> [!NOTE]
> Av felsökningsaktiviteten är endast tillgänglig för Azure AD Connect-version 1.1.614.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör cmdleten diagnostik

Felsökning av problem för en viss användare-objekt:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-server med den **kör som administratör** alternativet.

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.

3. Starta Azure AD Connect-guiden.

4. Navigera till den **ytterligare uppgifter** väljer **Felsök**, och klicka på **nästa**.

5. På sidan felsökning **starta** till startmenyn felsökning i PowerShell.

6. Välj i huvudmenyn **Felsök synkronisering av lösenordshash**.

7. I menyn sub väljer **lösenord synkroniseras inte för ett visst användarkonto**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av av felsökningsaktiviteten

Av felsökningsaktiviteten utför följande kontroller:

* Undersöker tillståndet för Active Directory-objekt i Active Directory-anslutarplatsen, metaversum och Azure AD-anslutningsplatsen.

* Verifierar att det inte finns Synkroniseringsregler med synkronisering av lösenordshash aktiverad och tillämpas på Active Directory-objektet.

* Försöker att hämta och visa resultatet av det senaste försöket att synkronisera lösenord för objektet.

Följande diagram visar resultaten av cmdlet: en när du felsöker synkronisering av lösenordshash för ett enda objekt:

![Diagnostikdata för synkronisering av lösenordshash - objekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

Resten av det här avsnittet beskriver specifika resultat som returneras av cmdlet och motsvarande problem.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory-objektet är inte exporteras till Azure AD

Det går inte att synkronisering av lösenordshash för den här lokala Active Directory-konto eftersom det finns inget motsvarande objekt i Azure AD-klient. Följande fel returneras:

![Azure AD-objekt som saknas](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Användaren har ett tillfälligt lösenord

För närvarande stöder inte Azure AD Connect synkroniserar tillfälliga lösenord med Azure AD. Ett lösenord betraktas som tillfälliga om den **ändra lösenord vid nästa inloggning** alternativet är inställt på den lokala Active Directory-användare. Följande fel returneras:

![Tillfälligt lösenord exporteras inte](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Resultat av senaste försöket att synkronisera lösenord inte är tillgängliga

Som standard lagrar Azure AD Connect resultatet av lösenordsförsök hash-synkronisering i sju dagar. Om det finns inga resultat för det valda objektet i Active Directory, returnerade följande varning:

![Diagnostikdata för objekt - ingen historik för synkronisering av lösenord](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Inga lösenord synkroniseras: felsöka med hjälp av cmdleten diagnostik

Du kan använda den `Invoke-ADSyncDiagnostics` cmdlet för att ta reda på varför inga lösenord synkroniseras.

> [!NOTE]
> Den `Invoke-ADSyncDiagnostics` cmdlet är endast tillgänglig för Azure AD Connect version 1.1.524.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör cmdleten diagnostik

Felsökning av problem där inga lösenord synkroniseras:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-server med den **kör som administratör** alternativet.

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.

3. Kör `Import-Module ADSyncDiagnostics`.

4. Kör `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Ett objekt synkroniseras inte lösenord: felsöka med hjälp av cmdleten diagnostik

Du kan använda den `Invoke-ADSyncDiagnostics` cmdlet för att avgöra varför en objekt synkroniseras inte lösenord.

> [!NOTE]
> Den `Invoke-ADSyncDiagnostics` cmdlet är endast tillgänglig för Azure AD Connect version 1.1.524.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör cmdleten diagnostik

Felsökning av problem där inga lösenord synkroniseras för en användare:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-server med den **kör som administratör** alternativet.

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.

3. Kör `Import-Module ADSyncDiagnostics`.

4. Kör följande cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Exempel:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Inga lösenord synkroniseras: manuell felsökningssteg

Följ dessa steg för att avgöra varför inga lösenord synkroniseras:

1. Är Connect-server i [mellanlagringsläge](how-to-connect-sync-staging-server.md)? En server i mellanlagringsläge synkroniserar inte eventuella lösenord.

2. Kör skriptet den [hämta status för lösenord synkroniseringsinställningar](#get-the-status-of-password-sync-settings) avsnittet. Det ger dig en översikt över konfigurationen för synkronisering av lösenord.  

    ![Utdata i PowerShell-skriptet från inställningar för synkronisering av lösenord](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Om funktionen inte är aktiverad i Azure AD eller om synkroniseringsstatus för kanal inte har aktiverats, kör du installationsguiden för Connect. Välj **anpassa synkroniseringsalternativ**, och avmarkera Lösenordssynkronisering. Den här ändringen kan du tillfälligt inaktiverar funktionen. Sedan kör guiden på nytt och återaktivera synkronisering av lösenord. Kör skriptet igen för att kontrollera att konfigurationen är korrekt.

4. Titta i händelseloggen efter fel. Sök efter följande händelser, vilket visar att det finns ett problem:
    * Källa: ”Katalogsynkronisering”-ID: 0, 611, 652, 655 om du ser dessa händelser du har anslutningsproblem med. Händelseloggmeddelande innehåller information om skog där du har problem. Mer information finns i [anslutningsproblem](#connectivity problem).

5. Om du ser inga pulsslag eller om inget annat arbetat köra [Utlös en fullständig synkronisering av alla lösenord](#trigger-a-full-sync-of-all-passwords). Kör skriptet bara en gång.

6. Se Felsök ett objekt som inte synkroniseras lösenord avsnittet.

### <a name="connectivity-problems"></a>Problem med nätverksanslutningen

Har du anslutningen med Azure AD?

Har kontot behörighet att läsa lösenordshasher i alla domäner? Om du har installerat Connect med standardinställningar, bör behörigheterna som redan vara korrekt. 

Om du har använt anpassad installation kan du ange behörigheter manuellt genom att göra följande:
    
1. För att hitta kontot som används av Active Directory-kopplingen, starta **hanteraren för synkroniseringstjänsten**. 
 
2. Gå till **Anslutningsappar**, och söker sedan efter den lokala Active Directory-skog som du felsöker. 
 
3. Välj kopplingen och klicka sedan på **egenskaper**. 
 
4. Gå till **ansluta till Active Directory-skog**.  
    
    ![Konto som används av Active Directory-koppling](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Observera användarnamnet och den domän där kontot finns.
    
5. Starta **Active Directory-användare och datorer**, och sedan kontrollera att det konto som du hittat tidigare har följande behörigheter som har angetts i roten för alla domäner i skogen:
    * Replikera katalogändringar
    * Replikera katalogen ändras alla

6. Är domänkontrollanter kan nås av Azure AD Connect? Om Connect-servern inte kan ansluta till alla domänkontrollanter, konfigurera **endast använda prioriterade domänkontrollanten**.  
    
    ![Domänkontrollanten som används av Active Directory-koppling](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Gå tillbaka till **hanteraren för synkroniseringstjänsten** och **konfigurera-katalogpartition**. 
 
8. Välj din domän i **Välj katalogpartitioner**väljer den **endast använda prioriterade domänkontrollanter** och klicka sedan på **konfigurera**. 

9. Ange de domänkontrollanter som Connect ska använda för synkronisering av lösenord i listan. Samma lista används för import och export samt. Utföra de här stegen för dina domäner.

10. Om skriptet visar att det finns inga pulsslag, kör du skriptet i [Utlös en fullständig synkronisering av alla lösenord](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Ett objekt synkroniseras inte lösenord: manuell felsökningssteg

Du kan enkelt felsöka lösenord hash-synkroniseringsproblem genom att granska statusen för ett objekt.

1. I **Active Directory-användare och datorer**, Sök efter användaren och kontrollerar att den **användaren måste byta lösenord vid nästa inloggning** kryssrutan är avmarkerad.  

    ![Active Directory-produktiva lösenord](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Om kryssrutan är markerad, be användaren att logga in och ändra lösenordet. Tillfälliga lösenord synkroniseras inte med Azure AD.

2. Om lösenordet verkar vara korrekta i Active Directory, följer du användaren i Synkroniseringsmotorn. Genom att följa användaren från en lokal Active Directory till Azure AD, kan du se om det finns en beskrivande fel på objektet.

    a. Starta den [hanteraren för synkroniseringstjänsten](how-to-connect-sync-service-manager-ui.md).

    b. Klicka på **Anslutningsappar**.

    c. Välj den **Active Directory-koppling** där användaren finns.

    d. Välj **söka Anslutarplats**.

    e. I den **omfång** väljer **DN eller fästpunkt**, och ange det fullständiga unika namnet för den användare som du felsöker.

    ![Sök efter användare i anslutarplatsen med DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Hitta användaren du letar efter och klicka sedan på **egenskaper** att se alla attribut. Om användaren inte är i sökresultatet, kontrollera din [filtreringsregler](how-to-connect-sync-configure-filtering.md) och se till att du kör [tillämpa och verifiera ändringar av](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) för användaren att visas i Connect.

    g. Klicka för att visa lösenordet synkronisera information om objektet för den senaste veckan **Log**.  

    ![Log Objektinformation](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Om objektet loggen är tom, Azure AD Connect kunde inte läsa lösenords-hash från Active Directory. Fortsätta felsökningen med anslutningsfel. Om du ser ett annat värde än **lyckades**, finns i tabellen i [lösenord synkroniseringsloggen](#password-sync-log).

    h. Välj den **härkomst** fliken och kontrollera att finns minst en synkroniseringsregel i den **PasswordSync** kolumnen är **SANT**. I standardkonfigurationen, namnet på synkroniseringsregel är **i från AD - användare AccountEnabled**.  

    ![Härkomstinformation om en användare](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Klicka på **Metaversumobjektegenskaperna** att visa en lista över användarattribut.  

    ![Metaversum-information](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Kontrollera att det finns inga **cloudFiltered** attributen. Se till att de förväntade värdena på attributen domän (domainFQDN och domainNetBios).

    j. Klicka på den **kopplingar** fliken. Kontrollera att du ser kopplingar till både lokala Active Directory och Azure AD.

    ![Metaversum-information](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Välj raden som representerar Azure AD, klickar du på **egenskaper**, och klicka sedan på den **härkomst** fliken. I anslutarplatsen bör ha en utgående regel i den **PasswordSync** kolumnen **SANT**. I standardkonfigurationen, namnet på synkroniseringsregel är **på AAD - användare ansluta**.  

    ![Dialogrutan för anslutningstjänsten utrymme objektegenskaper](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Lösenord synkroniseringsloggen

I statuskolumnen kan ha följande värden:

| Status | Beskrivning |
| --- | --- |
| Lyckades |Lösenordet har synkroniserats. |
| FilteredByTarget |Lösenordet är inställt på **användaren måste byta lösenord vid nästa inloggning**. Lösenordet har inte synkroniserats. |
| NoTargetConnection |Inga objekt i metaversumet eller i Azure AD-anslutningsplatsen. |
| SourceConnectorNotPresent |Inga objekt hittades i den lokala Active Directory-anslutarplatsen. |
| TargetNotExportedToDirectory |Objektet i Azure AD-anslutarplatsen har inte ännu exporterats. |
| MigratedCheckDetailsForMoreInfo |Loggpost skapades före version 1.0.9125.0 och visas i det tidigare tillståndet. |
| Fel |Tjänsten returnerade ett okänt fel. |
| Inte tillgängligt |Ett fel uppstod vid försök att bearbeta en batch med lösenords-hash.  |
| MissingAttribute |Specifika attribut (till exempel Kerberos-hash) som krävs av Azure AD Domain Services är inte tillgängliga. |
| RetryRequestedByTarget |Specifika attribut (till exempel Kerberos-hash) som krävs av Azure AD Domain Services var inte tillgängliga tidigare. Det görs ett försök att omsynkronisera användarens lösenords-hash. |

## <a name="scripts-to-help-troubleshooting"></a>Skript för att felsöka

### <a name="get-the-status-of-password-sync-settings"></a>Hämta status för inställningar för synkronisering av lösenord

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Utlös en fullständig synkronisering av alla lösenord

> [!NOTE]
> Kör skriptet bara en gång. Om du vill köra den mer än en gång är något annat problemet. Kontakta Microsoft support om du vill felsöka problemet.

Du kan utlösa en fullständig synkronisering av alla lösenord med hjälp av följande skript:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Nästa steg

* [Implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect-synkronisering: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

<!-- Update_Description: wording update -->