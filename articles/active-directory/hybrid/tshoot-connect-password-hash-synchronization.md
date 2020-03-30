---
title: Felsöka synkronisering av lösenordshÃ¶ning med Azure AD Connect-synkronisering | Microsoft-dokument
description: Den här artikeln innehåller information om hur du felsöker synkroniseringsproblem med lösenordshÃ¤nning.
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
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6feed11fcfc597658f3ec148b5dd18bb7e3f8f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253551"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Felsöka synkronisering av lösenordshash med Azure AD Connect-synkronisering

Det här avsnittet innehåller steg för felsÃ¶kning av problem med synkronisering av lösenordshÃ¥er. Om lösenord inte synkroniseras som förväntat kan det vara antingen för en delmängd av användare eller för alla användare.

För Azure Active Directory (Azure AD) Connect-distribution med version 1.1.614.0 eller efter använder du felsökningsuppgiften i guiden för att felsöka problem med synkronisering av lösenordshÃ¤hÃ¤hÃ¤knohning:

* Om du har ett problem där inga lösenord synkroniseras läser du [inga lösenord synkroniseras: felsöka med hjälp av avsnittet felsökningsaktivitet.](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task)

* Om du har problem med enskilda objekt läser du [one-objektet inte synkroniserar lösenord: felsöka med hjälp av avsnittet felsökningsaktivitet.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task)

För distribution med version 1.1.524.0 eller senare finns det en diagnostik-cmdlet som du kan använda för att felsöka problem med synkronisering av lösenordshÃ¤nsproblem:

* Om du har ett problem där inga lösenord synkroniseras läser du [inga lösenord synkroniseras: felsöka med hjälp av avsnittet diagnostikcmdlet.](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet)

* Om du har problem med enskilda objekt läser du [one-objektet som inte synkroniserar lösenord: felsöka med hjälp av avsnittet diagnostikcmdlet.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet)

För äldre versioner av Azure AD Connect-distribution:

* Om du har ett problem där inga lösenord synkroniseras läser du avsnittet [Inga lösenord synkroniseras: avsnittet Manuell felsökningssteg.](#no-passwords-are-synchronized-manual-troubleshooting-steps)

* Om du har problem med enskilda objekt läser du ett objekt som [inte synkroniserar lösenord:](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) avsnittet manuell felsökning.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Inga lösenord synkroniseras: felsöka med hjälp av felsökningsuppgiften

Du kan använda felsökningsuppgiften för att ta reda på varför inga lösenord synkroniseras.

> [!NOTE]
> Felsökningsuppgiften är endast tillgänglig för Azure AD Connect version 1.1.614.0 eller senare.

### <a name="run-the-troubleshooting-task"></a>Kör felsökningsuppgiften

Så här felsöker du problem där inga lösenord synkroniseras:

1. Öppna en ny Windows PowerShell-session på Din Azure AD Connect-server med alternativet **Kör som administratör.**

2. Kör `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`eller .

3. Starta Azure AD Connect-guiden.

4. Navigera till sidan **Ytterligare uppgifter,** välj **Felsöka**och klicka på **Nästa**.

5. På sidan Felsökning klickar du på **Starta** för att starta felsökningsmenyn i PowerShell.

6. Välj **Felsöka synkronisering av lösenordshÃ¶ring av lösenordshÃ¶er**.

7. På undermenyn väljer du **Synkronisering av lösenord hash fungerar inte alls**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av felsökningsuppgiften

Felsökningsuppgiften utför följande kontroller:

* Verifierar att synkroniseringsfunktionen för lösenord hash är aktiverad för din Azure AD-klientorganisation.

* Verifierar att Azure AD Connect-servern inte är i mellanlagringsläge.

* För varje befintlig lokal Active Directory-anslutning (som motsvarar en befintlig Active Directory-skog):

   * Verifierar att synkroniseringsfunktionen för lösenord hash är aktiverad.
   
   * Söker efter lösenordshã¶ringsnavd-händelseslag i windows application event logs.

   * För varje Active Directory-domän under den lokala Active Directory-anslutningen:

      * Verifierar att domänen kan nås från Azure AD Connect-servern.

      * Verifierar att AD DS-konton (Active Directory Domain Services) som används av den lokala Active Directory-anslutningen har rätt användarnamn, lösenord och behörigheter som krävs för synkronisering av lösenord hash.

Följande diagram illustrerar resultatet av cmdlet för en enda domän, lokal Active Directory-topologi:

![Diagnostikutdata för synkronisering av lösenordsh hash](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

I resten av det här avsnittet beskrivs specifika resultat som returneras av aktiviteten och motsvarande problem.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>synkroniseringsfunktionen för lösenord hash är inte aktiverad

Om du inte har aktiverat synkronisering av lösenordshÃ¤nda med guiden Azure AD Connect returneras följande fel:

![synkronisering av lösenord hash är inte aktiverad](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect-servern är i mellanlagringsläge

Om Azure AD Connect-servern är i mellanlagringsläge inaktiveras synkronisering av lösenordshÃ¤nsproblem tillfälligt och följande fel returneras:

![Azure AD Connect-servern är i mellanlagringsläge](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Inga pulsslag för lösenordsh hash-synkronisering

Varje lokal Active Directory-anslutning har en egen synkroniseringskanal för lösenordshönad. När synkroniseringskanalen för lösenord hash upprättas och inga lösenordsändringar ska synkroniseras genereras en pulsslagshändelse (EventId 654) en gång var 30:e minut under händelseloggen för Windows-program. För varje lokal Active Directory-koppling söker cmdleten efter motsvarande pulsslagshändelser under de senaste tre timmarna. Om ingen pulsslagshändelse hittas returneras följande fel:

![Inget lösenord hash synkronisering heart beat händelse](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS-kontot har inte rätt behörighet

Om AD DS-kontot som används av den lokala Active Directory-anslutningen för att synkronisera lösenordsharhar inte har rätt behörighet returneras följande fel:

![Felaktig autentiseringsuppgifter](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Felaktigt användarnamn eller lösenord för AD DS-kontot

Om AD DS-kontot som används av den lokala Active Directory-anslutningen för att synkronisera lösenordsharhar ett felaktigt användarnamn eller lösenord returneras följande fel:

![Felaktig autentiseringsuppgifter](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Ett objekt synkroniserar inte lösenord: felsöka med hjälp av felsökningsuppgiften

Du kan använda felsökningsuppgiften för att avgöra varför ett objekt inte synkroniserar lösenord.

> [!NOTE]
> Felsökningsuppgiften är endast tillgänglig för Azure AD Connect version 1.1.614.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör diagnostiken cmdlet

Så här felsöker du problem för ett visst användarobjekt:

1. Öppna en ny Windows PowerShell-session på Din Azure AD Connect-server med alternativet **Kör som administratör.**

2. Kör `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`eller .

3. Starta Azure AD Connect-guiden.

4. Navigera till sidan **Ytterligare uppgifter,** välj **Felsöka**och klicka på **Nästa**.

5. På sidan Felsökning klickar du på **Starta** för att starta felsökningsmenyn i PowerShell.

6. Välj **Felsöka synkronisering av lösenordshÃ¶ring av lösenordshÃ¶er**.

7. På undermenyn väljer du **Lösenord synkroniseras inte för ett visst användarkonto**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av felsökningsuppgiften

Felsökningsuppgiften utför följande kontroller:

* Undersöker tillståndet för Active Directory-objektet i Active Directory-anslutningsutrymmet, Metavers och Azure AD-anslutningsutrymmet.

* Verifierar att det finns synkroniseringsregler med synkronisering av lösenord hash aktiverat och tillämpat på Active Directory-objektet.

* Försöker hämta och visa resultatet av det senaste försöket att synkronisera lösenordet för objektet.

Följande diagram illustrerar cmdletens resultat vid felsökning av synkronisering av lösenordshã¤nning för ett enda objekt:

![Diagnostikutdata för synkronisering av lösenordsh hash - ett enda objekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

I resten av det här avsnittet beskrivs specifika resultat som returneras av cmdlet och motsvarande problem.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory-objektet exporteras inte till Azure AD

synkronisering av lösenord hash för det här lokala Active Directory-kontot misslyckas eftersom det inte finns något motsvarande objekt i Azure AD-klienten. Följande fel returneras:

![Azure AD-objektet saknas](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Användaren har ett tillfälligt lösenord

Azure AD Connect stöder för närvarande inte synkronisering av tillfälliga lösenord med Azure AD. Ett lösenord anses vara tillfälligt om alternativet **Ändra lösenord vid nästa inloggning** är inställt på den lokala Active Directory-användaren. Följande fel returneras:

![Tillfälligt lösenord exporteras inte](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Resultatet av det senaste försöket att synkronisera lösenordet är inte tillgängliga

Som standard lagrar Azure AD Connect resultatet av synkroniseringsförsök för lösenord hash i sju dagar. Om det inte finns några resultat tillgängliga för det valda Active Directory-objektet returneras följande varning:

![Diagnostikutdata för ett objekt - ingen lösenordssynkroniseringshistorik](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Inga lösenord synkroniseras: felsöka med hjälp av diagnostik-cmdlet

Du kan `Invoke-ADSyncDiagnostics` använda cmdlet för att ta reda på varför inga lösenord synkroniseras.

> [!NOTE]
> Cmdleten `Invoke-ADSyncDiagnostics` är endast tillgänglig för Azure AD Connect version 1.1.524.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör diagnostiken cmdlet

Så här felsöker du problem där inga lösenord synkroniseras:

1. Öppna en ny Windows PowerShell-session på Din Azure AD Connect-server med alternativet **Kör som administratör.**

2. Kör `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`eller .

3. Kör `Import-Module ADSyncDiagnostics`.

4. Kör `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Ett objekt synkroniserar inte lösenord: felsöka med hjälp av diagnostik-cmdlet

Du kan `Invoke-ADSyncDiagnostics` använda cmdleten för att avgöra varför ett objekt inte synkroniserar lösenord.

> [!NOTE]
> Cmdleten `Invoke-ADSyncDiagnostics` är endast tillgänglig för Azure AD Connect version 1.1.524.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör diagnostiken cmdlet

Så här felsöker du problem där inga lösenord synkroniseras för en användare:

1. Öppna en ny Windows PowerShell-session på Din Azure AD Connect-server med alternativet **Kör som administratör.**

2. Kör `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`eller .

3. Kör `Import-Module ADSyncDiagnostics`.

4. Kör följande cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Ett exempel:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Inga lösenord synkroniseras: steg för manuell felsökning

Följ dessa steg för att ta reda på varför inga lösenord synkroniseras:

1. Är Connect-servern i [mellanlagringsläge?](how-to-connect-sync-staging-server.md) En server i mellanlagringsläge synkroniserar inga lösenord.

2. Kör skriptet i avsnittet [Hämta status för inställningar för lösenordssynkronisering.](#get-the-status-of-password-sync-settings) Det ger dig en översikt över konfigurationen av lösenordssynkronisering.  

    ![PowerShell-skriptutdata från inställningar för lösenordssynkronisering](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Om funktionen inte är aktiverad i Azure AD eller om synkroniseringskanalstatusen inte är aktiverad kör du installationsguiden för Anslut. Välj **Anpassa synkroniseringsalternativ**och avmarkera lösenordssynkronisering. Den här ändringen inaktiverar tillfälligt funktionen. Kör sedan guiden igen och aktivera lösenordssynkroniseringen igen. Kör skriptet igen för att kontrollera att konfigurationen är korrekt.

4. Leta efter fel i händelseloggen i händelseloggen. Leta efter följande händelser, vilket tyder på ett problem:
    * Källa: "Katalogsynkronisering" ID: 0, 611, 652, 655 Om du ser dessa händelser har du ett anslutningsproblem. Händelseloggmeddelandet innehåller skogsinformation där du har problem. Mer information finns i [Anslutningsproblem](#connectivity problem).

5. Om du inte ser några pulsslag eller om inget annat fungerade kör du [Utlösa en fullständig synkronisering av alla lösenord](#trigger-a-full-sync-of-all-passwords). Kör skriptet bara en gång.

6. Se avsnittet Felsöka ett objekt som inte synkroniserar lösenord.

### <a name="connectivity-problems"></a>Anslutningsproblem

Har du anslutning till Azure AD?

Har kontot behörighet att läsa lösenordsharhar i alla domäner? Om du har installerat Connect med hjälp av Express-inställningar bör behörigheterna redan vara korrekta. 

Om du använde anpassad installation anger du behörigheterna manuellt genom att göra följande:
    
1. Om du vill söka efter kontot som används av Active Directory-anslutningen startar du **Synkroniseringstjänsthanteraren**. 
 
2. Gå till **Kopplingar**och sök sedan efter den lokala Active Directory-skog som du felsöker. 
 
3. Markera kopplingen och klicka sedan på **Egenskaper**. 
 
4. Gå till **Anslut till Active Directory Forest**.  
    
    ![Konto som används av Active Directory-anslutning](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Notera användarnamnet och domänen där kontot finns.
    
5. Starta **Active Directory – Användare och datorer**och kontrollera sedan att kontot som du hittade tidigare har följande behörigheter som angetts vid roten för alla domäner i skogen:
    * Replikera katalogändringar
    * Replikera katalog ändrar alla

6. Kan domänkontrollanterna nås via Azure AD Connect? Om Connect-servern inte kan ansluta till alla domänkontrollanter konfigurerar **du Endast använd önskad domänkontrollant**.  
    
    ![Domänkontrollant som används av Active Directory-anslutning](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Gå tillbaka till **Synkroniseringstjänsthanteraren** och **konfigurera katalogpartition**. 
 
8. Markera domänen i **Markera katalogpartitioner,** markera kryssrutan **Använd endast önskade domänkontrollanter** och klicka sedan på **Konfigurera**. 

9. I listan anger du de domänkontrollanter som Connect ska använda för lösenordssynkronisering. Samma lista används även för import och export. Gör dessa steg för alla dina domäner.

10. Om skriptet visar att det inte finns några pulsslag kör du skriptet i [Utlösa en fullständig synkronisering av alla lösenord](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Ett objekt synkroniserar inte lösenord: manuella felsökningssteg

Du kan enkelt felsöka problem med synkronisering av lösenordshÃ¶n genom att granska statusen för ett objekt.

1. Sök efter användaren i **Active Directory – användare och datorer**och kontrollera sedan att kryssrutan Användaren måste ändra lösenord vid nästa **inloggning.**  

    ![Active Directory-produktiva lösenord](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Om kryssrutan är markerad ber du användaren att logga in och ändra lösenordet. Tillfälliga lösenord synkroniseras inte med Azure AD.

2. Om lösenordet ser korrekt ut i Active Directory följer du användaren i synkroniseringsmotorn. Genom att följa användaren från lokala Active Directory till Azure AD kan du se om det finns ett beskrivande fel på objektet.

    a. Starta [Synkroniseringstjänsthanteraren](how-to-connect-sync-service-manager-ui.md).

    b. Klicka på **Kopplingar**.

    c. Välj **Active Directory Connector** där användaren finns.

    d. Välj **Sökkopplingsutrymme**.

    e. I rutan **Omfattning** väljer du **DN eller Ankare**och anger sedan hela DN för den användare som du felsöker.

    ![Sök efter användare i anslutningsutrymme med DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Leta reda på den användare du letar efter och klicka sedan på **Egenskaper** för att se alla attribut. Om användaren inte finns med i sökresultatet kontrollerar du [filtreringsreglerna](how-to-connect-sync-configure-filtering.md) och kontrollerar att du kör [Verkställ och verifiera ändringar](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) för att användaren ska visas i Anslut.

    g. Om du vill visa information om lösenordssynkronisering för objektet under den senaste veckan klickar du på **Logga**.  

    ![Information om objektlogg](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Om objektloggen är tom har Azure AD Connect inte kunnat läsa lösenordshönaden från Active Directory. Fortsätt felsökningen med anslutningsfel. Om du ser något annat värde än **framgång**läser du tabellen i [synkroniseringsloggen Lösenord](#password-sync-log).

    h. Markera **fliken härstamning** och kontrollera att minst en synkroniseringsregel i kolumnen **PasswordSync** är **Sant**. I standardkonfigurationen är namnet på synkroniseringsregeln **In från AD - User AccountEnabled**.  

    ![Härstamningsinformation om en användare](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Klicka på **Egenskaper för metaversumobjekt** om du vill visa en lista över användarattribut.  

    ![Information om metaversum](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Kontrollera att det inte finns något **molnFilterat** attribut. Kontrollera att domänattributen (domainFQDN och domainNetBios) har de förväntade värdena.

    j. Klicka på fliken **Kopplingar.** Kontrollera att du ser kopplingar till både lokala Active Directory och Azure AD.

    ![Information om metaversum](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Markera raden som representerar Azure AD, klicka på **Egenskaper**och klicka sedan på fliken **Härstamning.** Anslutningsutrymmesobjektet ska ha en utgående regel i kolumnen **PasswordSync** inställt på **Sant**. I standardkonfigurationen är namnet på synkroniseringsregeln **Ut till AAD - Användarkoppling**.  

    ![Egenskaper för kopplingsutrymmesobjekt](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Logg för synkronisering av lösenord

Statuskolumnen kan ha följande värden:

| Status | Beskrivning |
| --- | --- |
| Lyckades |Lösenordet har synkroniserats. |
| FiltreradEftermål |Lösenordet är inställt **på Användaren måste ändra lösenord vid nästa inloggning**. Lösenordet har inte synkroniserats. |
| Ingen Målanslutning |Inget objekt i metaversumet eller i Azure AD-anslutningsutrymmet. |
| SourceConnectorNotPresent |Inget objekt hittades i det lokala Active Directory-anslutningsutrymmet. |
| TargetNotExportedToDirectory |Objektet i Azure AD-anslutningsutrymmet har ännu inte exporterats. |
| MigreradeCheckDetailsForMoreInfo |Loggposten skapades före version 1.0.9125.0 och visas i dess äldre tillstånd. |
| Fel |Tjänsten returnerade ett okänt fel. |
| Okänt |Ett fel uppstod när en batch med lösenords hashar skulle bearbetas.  |
| MissingAttribute |Specifika attribut (till exempel Kerberos hash) som krävs av Azure AD Domain Services är inte tillgängliga. |
| ÅterförsökRequestedByTarget |Specifika attribut (till exempel Kerberos hash) som krävs av Azure AD Domain Services var inte tillgängliga tidigare. Ett försök att synkronisera om användarens lösenordsh hash görs. |

## <a name="scripts-to-help-troubleshooting"></a>Skript som hjälper till att felsöka

### <a name="get-the-status-of-password-sync-settings"></a>Få status för inställningar för synkronisering av lösenord

```powershell
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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Utlösa en fullständig synkronisering av alla lösenord

> [!NOTE]
> Kör bara skriptet en gång. Om du behöver köra det mer än en gång, något annat är problemet. Kontakta Microsoft-supporten om du vill felsöka problemet.

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

* [Implementera synkronisering av lösenordsh hash-synkronisering med Azure AD Connect-synkronisering](how-to-connect-password-hash-synchronization.md)
* [Synkronisering av Azure AD Connect: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)