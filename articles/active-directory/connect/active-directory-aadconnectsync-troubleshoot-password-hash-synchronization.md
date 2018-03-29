---
title: Felsöka Lösenordssynkronisering med Azure AD Connect-synkronisering med hash | Microsoft Docs
description: Den här artikeln innehåller information om hur du felsöker problem med lösenord hash-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: billmath
ms.openlocfilehash: bcf266813476c682d47bfd483db77f5d8b73837a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Felsöka hash Lösenordssynkronisering med Azure AD Connect-synkronisering
Det här avsnittet innehåller anvisningar att felsöka problem med synkronisering av lösenords-hash. Om lösenord inte synkroniseras som förväntat, kan det vara för en delmängd av användare eller för alla användare.

Anslut Azure Active Directory (AD Azure) distribution med version 1.1.614.0 eller senare kan använda aktiviteten felsökning i guiden felsökning av problem med lösenord hash-synkronisering:

* Om du har ett problem om inga lösenord synkroniseras, referera till den [utan lösenord synkroniseras: felsökning med hjälp av aktiviteten felsökning](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) avsnitt.

* Om du har problem med enskilda objekt, referera till den [ett objekt synkroniserar inte lösenord: felsökning med hjälp av aktiviteten felsökning](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) avsnitt.

För distribution med version 1.1.524.0 eller senare, det finns en diagnostiska cmdlet som du kan använda för att felsöka problem med lösenord hash-synkronisering:

* Om du har ett problem om inga lösenord synkroniseras, referera till den [utan lösenord synkroniseras: felsökning med hjälp av cmdleten diagnostiska](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) avsnitt.

* Om du har problem med enskilda objekt, referera till den [ett objekt synkroniserar inte lösenord: felsökning med hjälp av cmdleten diagnostiska](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) avsnitt.

För äldre versioner av Azure AD Connect-distribution:

* Om du har ett problem om inga lösenord synkroniseras, referera till den [utan lösenord synkroniseras: manuell felsökningssteg](#no-passwords-are-synchronized-manual-troubleshooting-steps) avsnitt.

* Om du har problem med enskilda objekt, referera till den [ett objekt synkroniserar inte lösenord: manuell felsökningssteg](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) avsnitt.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Inga lösenord ska synkroniseras: felsökning med hjälp av aktiviteten felsökning
Du kan använda aktiviteten felsökning för att ta reda på varför utan lösenord synkroniseras.

> [!NOTE]
> Felsökning aktiviteten är endast tillgänglig för Azure AD Connect-version 1.1.614.0 eller senare.

### <a name="run-the-troubleshooting-task"></a>Kör aktiviteten felsökning
Felsökning av problem där ingen lösenord ska synkroniseras:

1. Öppna en ny Windows PowerShell-session på Azure AD Connect-servern med den **kör som administratör** alternativet.

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.

3. Starta Azure AD Connect-guiden.

4. Navigera till den **ytterligare uppgifter** väljer **Felsök**, och klicka på **nästa**.

5. På sidan felsökning **starta** starta menyn felsökning i PowerShell.

6. Markera i på huvudmenyn **Felsöka Lösenordssynkronisering för hash-**.

7. Välj menyn sub **hash-synkronisering av lösenord fungerar inte alls**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av aktiviteten felsökning
Felsökning aktiviteten utför följande kontroller:

* Verifierar att funktionen lösenord hash-synkronisering är aktiverat för Azure AD-klienten.

* Verifierar att Azure AD Connect-servern inte är i mellanlagringsläge.

* För varje befintliga lokala Active Directory-koppling (som motsvarar en befintlig Active Directory-skog):

   * Verifierar att funktionen lösenord hash-synkronisering är aktiverat.
   
   * Söker efter lösenord hash-synkronisering pulsslag händelser i händelseloggarna i Windows-program.

   * För varje Active Directory-domän under lokala Active Directory-kopplingen:

      * Validerar att domänen kan nås från Azure AD Connect-servern.

      * Verifierar att Active Directory Domain Services (AD DS)-konton som används av den lokala Active Directory-kopplingen har rätt användarnamn, lösenord och behörigheter som krävs för synkronisering av lösenords-hash.

Följande diagram visar resultaten av cmdlet: en för en domän, lokala Active Directory-topologi:

![Diagnostikdata för synkronisering av lösenords-hash](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalgeneral.png)

Resten av det här avsnittet beskriver specifika resultat som returneras av aktiviteten och motsvarande problem.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>lösenord hash-synkroniseringsfunktionen är inte aktiverad
Om du inte har aktiverat hash-synkronisering av lösenord med hjälp av Azure AD Connect-guiden, returneras följande fel:

![synkronisering av lösenords-hash är inte aktiverat](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect-servern är i mellanlagringsläge
Om Azure AD Connect-servern är i fristående läge, hash-synkronisering av lösenord är tillfälligt inaktiverad och följande fel returnerades:

![Azure AD Connect-servern är i mellanlagringsläge](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Inga lösenord hash-synkronisering heartbeat-händelser
Varje lokala Active Directory-koppling har sin egen lösenord hash-synkronisering kanal. När lösenord hash-synkronisering kanal upprättas och det inte finns några lösenordsändringar som ska synkroniseras genereras heartbeat-händelser (händelse-ID 654) var 30: e minut under Windows händelselogg. För varje Active Directory connector lokalt cmdleten söker efter motsvarande heartbeat-händelser under de senaste tre timmarna. Om ingen händelse från pulsslag hittas returneras följande fel:

![Inga lösenord hash-synkronisering hjärta slå händelse](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS-kontot har inte rätt behörighet
Om AD DS-konto som används av lokala Active Directory-kopplingen för att synkronisera hash-lösenordshashvärden inte har rätt behörigheter returnerade följande fel:

![Felaktig autentiseringsuppgifter](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Felaktigt användarnamn för AD DS-konto eller lösenord
Om AD DS-konto som används av den lokala Active Directory-kopplingen för att synkronisera lösenordshashvärden har ett felaktigt användarnamn eller lösenord, returneras följande fel:

![Felaktig autentiseringsuppgifter](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Ett objekt synkroniserar inte lösenord: felsökning med hjälp av aktiviteten felsökning

Du kan använda aktiviteten felsökning för att avgöra varför ett objekt inte synkroniserar lösenord.

> [!NOTE]
> Felsökning aktiviteten är endast tillgänglig för Azure AD Connect-version 1.1.614.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör cmdleten diagnostik
Felsökning av problem för en viss användarobjektet:

1. Öppna en ny Windows PowerShell-session på Azure AD Connect-servern med den **kör som administratör** alternativet.

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.

3. Starta Azure AD Connect-guiden.

4. Navigera till den **ytterligare uppgifter** väljer **Felsök**, och klicka på **nästa**.

5. På sidan felsökning **starta** starta menyn felsökning i PowerShell.

6. Markera i på huvudmenyn **Felsöka Lösenordssynkronisering för hash-**.

7. Välj menyn sub **lösenord synkroniseras inte för ett specifikt användarkonto**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av aktiviteten felsökning
Felsökning aktiviteten utför följande kontroller:

* Undersöker tillståndet för Active Directory-objekt i Active Directory connector utrymme, metaversum och Azure AD-anslutningsplatsen.

* Verifierar att det finns Synkroniseringsregler med synkronisering av lösenords-hash-aktiverad och tillämpas på Active Directory-objekt.

* Försöker hämta och visa resultatet av det senaste försöket att synkronisera lösenord för objektet.

Följande diagram illustrerar resultaten av cmdlet när du felsöker synkronisering av lösenords-hash för ett enskilt objekt:

![Diagnostikdata för Lösenordssynkronisering för hash - objekt](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectgeneral.png)

Resten av det här avsnittet beskriver specifika resultat som returneras av cmdlet och motsvarande problem.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory-objekt exporteras inte till Azure AD
synkronisering av lösenords-hash för den här lokala Active Directory-konto misslyckas eftersom det finns inget motsvarande objekt i Azure AD-klient. Följande fel returnerades:

![Azure AD-objekt som saknas](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Användaren har ett tillfälligt lösenord
För närvarande stöder inte Azure AD Connect synkroniserar tillfälliga lösenord med Azure AD. Ett lösenord ska anses vara tillfälligt om den **ändra lösenord vid nästa inloggning** alternativet är inställt på den lokala Active Directory-användaren. Följande fel returnerades:

![Tillfälligt lösenord exporteras inte](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Resultaten av senaste försöket att synkronisera lösenord är inte tillgängliga
Som standard lagrar Azure AD Connect resultaten av lösenordsförsök hash-synkronisering i sju dagar. Om det finns inga resultat för det valda objektet i Active Directory, returnerade följande varning:

![Diagnostikdata för objekt - ingen historik för synkronisering av lösenord](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Inga lösenord ska synkroniseras: felsökning med hjälp av cmdleten diagnostik
Du kan använda den `Invoke-ADSyncDiagnostics` för att ta reda på varför utan lösenord synkroniseras.

> [!NOTE]
> Den `Invoke-ADSyncDiagnostics` cmdlet är endast tillgänglig för Azure AD Connect-version 1.1.524.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör cmdleten diagnostik
Felsökning av problem där ingen lösenord ska synkroniseras:

1. Öppna en ny Windows PowerShell-session på Azure AD Connect-servern med den **kör som administratör** alternativet.

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.

3. Kör `Import-Module ADSyncDiagnostics`.

4. Kör `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Ett objekt synkroniserar inte lösenord: felsökning med hjälp av cmdleten diagnostik
Du kan använda den `Invoke-ADSyncDiagnostics` för att avgöra varför ett objekt inte synkroniserar lösenord.

> [!NOTE]
> Den `Invoke-ADSyncDiagnostics` cmdlet är endast tillgänglig för Azure AD Connect-version 1.1.524.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör cmdleten diagnostik
Felsökning av problem där utan lösenord synkroniseras för en användare:

1. Öppna en ny Windows PowerShell-session på Azure AD Connect-servern med den **kör som administratör** alternativet.

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.

3. Kör `Import-Module ADSyncDiagnostics`.

4. Kör följande cmdlet:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   Exempel:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Inga lösenord ska synkroniseras: manuell felsökningssteg
Följ dessa steg för att avgöra varför utan lösenord synkroniseras:

1. Är Connect-server i [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode)? En server i mellanlagringsläge synkroniseras inte eventuella lösenord.

2. Kör skriptet den [hämta status för inställningar för synkronisering av lösenord](#get-the-status-of-password-sync-settings) avsnitt. Den ger dig en översikt över konfigurationen för synkronisering av lösenord.  

    ![PowerShell-skriptets utdata från inställningar för synkronisering av lösenord](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/psverifyconfig.png)  

3. Om funktionen inte är aktiverad i Azure AD eller om sync kanalen inte är aktiverad, kör du guiden Anslut. Välj **anpassa synkroniseringsalternativ**, och avmarkera Lösenordssynkronisering. Den här ändringen kan du tillfälligt inaktiverar funktionen. Sedan kör guiden igen och aktivera synkronisering av lösenord på nytt. Kör skriptet igen för att kontrollera att konfigurationen är korrekt.

4. Titta i händelseloggen för fel. Leta efter följande händelser, vilket visar att det finns ett problem:
    * Meddelandekälla: ID för ”katalogsynkronisering”: 0, 611, 652, 655 om du ser dessa händelser du har anslutningsproblem med. Händelseloggmeddelande innehåller information om skog där du har problem. Mer information finns i [anslutningsproblem](#connectivity problem).

5. Om du ser inget pulsslag från eller om inget annat arbetat köra [utlösa en fullständig synkronisering av alla lösenord](#trigger-a-full-sync-of-all-passwords). Kör skriptet en gång.

6. Finns det [felsökning av ett objekt som inte synkroniserar lösenord](#one-object-is-not-synchronizing-passwords) avsnitt.

### <a name="connectivity-problems"></a>Problem med nätverksanslutningen

Har du anslutningen till Azure AD?

Har kontot behörighet att läsa lösenordshashvärden i alla domäner? Om du har installerat Connect med standardinställningar vara behörigheterna redan rätt. 

Om du har använt anpassad installation kan du ange behörigheter manuellt genom att göra följande:
    
1. Om du vill hitta det konto som används av Active Directory-kopplingen, starta **Synchronization Service Manager**. 
 
2. Gå till **kopplingar**, och sök sedan efter den lokala Active Directory-skog som du felsöker. 
 
3. Välj kopplingen och klicka sedan på **egenskaper**. 
 
4. Gå till **ansluta till Active Directory-skog**.  
    
    ![Konto som används av Active Directory-koppling](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/connectoraccount.png)  
    Observera användarnamn och domän där kontot.
    
5. Starta **Active Directory-användare och datorer**, och sedan kontrollera att det konto som du hittade tidigare har följande behörigheter i roten för alla domäner i skogen:
    * Replikera katalogändringar
    * Replikera katalogen ändras alla

6. Är domänkontrollanter kan nås av Azure AD Connect? Om Connect-servern inte kan ansluta till alla domänkontrollanter kan du konfigurera **bara använda prioriterade domänkontrollant**.  
    
    ![Domänkontrollanten som används av Active Directory-koppling](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/preferreddc.png)  
    
7. Gå tillbaka till **Synchronization Service Manager** och **konfigurera katalogpartitionen**. 
 
8. Välj din domän i **Välj katalogpartitioner**, Välj den **endast använder prioriterade domänkontrollanter** kryssrutan och klicka sedan på **konfigurera**. 

9. Ange de domänkontrollanter som Connect ska använda för synkronisering av lösenord i listan. Samma lista används för import och export samt. Utföra de här stegen för alla domäner.

10. Om skriptet visar att det finns inget pulsslag, köra skriptet i [utlösa en fullständig synkronisering av alla lösenord](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Ett objekt synkroniserar inte lösenord: manuell felsökningssteg
Du kan enkelt felsöka lösenord hash-synkroniseringsproblem genom att granska statusen för ett objekt.

1. I **Active Directory-användare och datorer**, söker du efter användaren och kontrollera att den **användaren måste byta lösenord vid nästa inloggning** kryssrutan är avmarkerad.  

    ![Active Directory-produktiva lösenord](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/adprodpassword.png)  

    Om kryssrutan är markerad, be användaren att logga in och ändra lösenordet. Temporära lösenord är inte synkroniserade med Azure AD.

2. Om lösenordet verkar vara korrekta i Active Directory, följer du användaren i Synkroniseringsmotorn. Genom att följa användaren från lokala Active Directory till Azure AD kan se du om det finns ett felmeddelande på objektet.

    a. Starta den [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

    b. Klicka på **kopplingar**.

    c. Välj den **Active Directory-kopplingen** där användaren finns.

    d. Välj **söka Anslutarplats**.

    e. I den **omfång** väljer **DN eller fästpunkt**, och ange sedan fullständig DN för den användare som du felsöker.

    ![Sök efter användare i anslutningsplatsen med DN](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/searchcs.png)  

    f. Leta upp den användare du söker efter och klicka sedan på **egenskaper** att se alla attribut. Om användaren inte är i sökresultatet, kontrollera din [filtreringsregler](active-directory-aadconnectsync-configure-filtering.md) och se till att du kör [tillämpa och verifiera ändringar](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) för användaren att visas i Connect.

    g. Klicka för att visa lösenordet synkronisera information om objektet under den gångna veckan **loggen**.  

    ![Information om objekt logg](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/csobjectlog.png)  

    Om grupprincipobjektet loggar är tom, Azure AD Connect har inte kunnat läsa lösenords-hash från Active Directory. Fortsätta felsökningen med [anslutningsfel](#connectivity-errors). Om du ser något annat värde än **lyckade**, se tabellen i [loggen för synkronisering av lösenord](#password-sync-log).

    h. Välj den **härkomst** fliken och kontrollera att finns minst en synkroniseringsregel i den **PasswordSync** kolumnen är **SANT**. I standardkonfigurationen, namnet på regeln sync är **i från AD - användare AccountEnabled**.  

    ![Övriga information om en användare](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync.png)  

    i. Klicka på **metaversum objektegenskaper** att visa en lista över användarattribut.  

    ![Metaversum-information](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvpasswordsync.png)  

    Kontrollera att det finns inga **cloudFiltered** attributet. Kontrollera att de förväntade värdena på attributen domän (domainFQDN och domainNetBios).

    j. Klicka på den **kopplingar** fliken. Kontrollera att du ser kopplingar till både lokala Active Directory och Azure AD.

    ![Metaversum-information](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvconnectors.png)  

    k. Välj raden som representerar Azure AD, klickar du på **egenskaper**, och klicka sedan på den **härkomst** fliken. Utrymme kopplingsobjektet ska ha en utgående regel i den **PasswordSync** i kolumnen **SANT**. I standardkonfigurationen, namnet på regeln sync är **på AAD - användare ansluta**.  

    ![Dialogrutan för koppling objektegenskaper för Anslutarplats](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Logg för synkronisering av lösenord
I statuskolumnen kan ha följande värden:

| Status | Beskrivning |
| --- | --- |
| Lyckades |Lösenordet har synkroniserats. |
| FilteredByTarget |Lösenordet är inställt på **användaren måste byta lösenord vid nästa inloggning**. Lösenordet har inte synkroniserats. |
| NoTargetConnection |Inga objekt i metaversumet eller i Azure AD-anslutningsplatsen. |
| SourceConnectorNotPresent |Inga objekt hittades i det lokala Active Directory connector utrymmet. |
| TargetNotExportedToDirectory |Objektet i Azure AD-anslutningsplatsen har inte ännu exporterats. |
| MigratedCheckDetailsForMoreInfo |Loggposten skapades innan version 1.0.9125.0 och visas i det tidigare tillståndet. |
| Fel |Tjänsten returnerade ett okänt fel. |
| Okänt |Ett fel uppstod vid försök att bearbeta en batch med lösenordshashvärden.  |
| MissingAttribute |Specifika attribut (till exempel Kerberos-hash) krävs av Azure AD Domain Services är inte tillgängliga. |
| RetryRequestedByTarget |Specifika attribut (till exempel Kerberos-hash) krävs av Azure AD Domain Services var inte tillgängliga tidigare. Det görs ett försök att synkronisera om användarens lösenords-hash. |

## <a name="scripts-to-help-troubleshooting"></a>Skript för att felsöka

### <a name="get-the-status-of-password-sync-settings"></a>Hämta status för inställningar för synkronisering av lösenord
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Starta en fullständig synkronisering av alla lösenord
> [!NOTE]
> Kör skriptet en gång. Om du behöver köra den mer än en gång är något annat problemet. Kontakta Microsoft support om du vill felsöka problemet.

Du kan utlösa en fullständig synkronisering av alla lösenord med hjälp av följande skript:

```
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
* [Implementera hash Lösenordssynkronisering med Azure AD Connect-synkronisering](active-directory-aadconnectsync-implement-password-hash-synchronization.md)
* [Azure AD Connect Sync: Anpassa synkroniseringsalternativ](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
