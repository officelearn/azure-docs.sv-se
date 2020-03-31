---
title: 'Synkronisering av Azure AD Connect: Operativa uppgifter och överväganden | Microsoft-dokument'
description: I det här avsnittet beskrivs operativa uppgifter för Azure AD Connect-synkronisering och hur du förbereder för att använda den här komponenten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc88640cdff4f716902a80bb149913b961d40ae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261026"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Mellanlagringsserver och haveriberedskap
Med en server i mellanlagringsläge kan du göra ändringar i konfigurationen och förhandsgranska ändringarna innan du gör servern aktiv. Du kan också köra fullständig import och fullständig synkronisering för att kontrollera att alla ändringar förväntas innan du gör dessa ändringar i produktionsmiljön.

## <a name="staging-mode"></a>Mellanlagringsläge
Mellanlagringsläge kan användas i flera scenarier, bland annat:

* Hög tillgänglighet.
* Testa och distribuera nya konfigurationsändringar.
* Introducera en ny server och inaktivera den gamla.

Under installationen kan du välja vilken server som ska vara i **mellanlagringsläge**. Den här åtgärden gör servern aktiv för import och synkronisering, men den kör inte någon export. En server i mellanlagringsläge kör inte lösenordssynkronisering eller tillbakaskrivning av lösenord, även om du har valt dessa funktioner under installationen. När du inaktiverar mellanlagringsläge börjar servern exportera, aktiverar lösenordssynkronisering och aktiverar tillbakaskrivning av lösenord.

> [!NOTE]
> Anta att du har aktiverat en Azure AD Connect med synkronisering av lösenord hash-synkronisering. När du aktiverar mellanlagringsläge slutar servern att synkronisera lösenordsändringar från lokalt AD. När du inaktiverar mellanlagringsläge återupptar servern synkroniseringen av lösenordsändringar från den plats där det senast slutade. Om servern lämnas i mellanlagringsläge under en längre tid kan det ta ett tag innan servern synkroniserar alla lösenordsändringar som har inträffat under tidsperioden.
>
>

Du kan fortfarande tvinga fram en export med hjälp av synkroniseringstjänsthanteraren.

En server i mellanlagringsläge fortsätter att ta emot ändringar från Active Directory och Azure AD och kan snabbt ta över ansvaret för en annan server i händelse av ett fel. Om du gör konfigurationsändringar på den primära servern är det ditt ansvar att göra samma ändringar på servern i mellanlagringsläge.

För dig med kunskap om äldre synkroniseringsteknik är mellanlagringsläget annorlunda eftersom servern har en egen SQL-databas. Med den här arkitekturen kan mellanlagringslägesservern finnas i ett annat datacenter.

### <a name="verify-the-configuration-of-a-server"></a>Verifiera konfigurationen av en server
Så här använder du den här metoden:

1. [Förbereda](#prepare)
2. [Konfiguration](#configuration)
3. [Importera och synkronisera](#import-and-synchronize)
4. [Verifiera](#verify)
5. [Växla aktiv server](#switch-active-server)

#### <a name="prepare"></a>Förbereda
1. Installera Azure AD Connect, välj **mellanlagringsläge**och avmarkera **startsynkronisering** på sista sidan i installationsguiden. I det här läget kan du köra synkroniseringsmotorn manuellt.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Logga ut/logga in och välj **Synkroniseringstjänst**på start-menyn .

#### <a name="configuration"></a>Konfiguration
Om du har gjort anpassade ändringar på den primära servern och vill jämföra konfigurationen med mellanlagringsservern använder du [Azure AD Connect-konfigurationsdokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importera och synkronisera
1. Välj **Anslutningsappar**och välj den första anslutningsappen med typen **Active Directory Domain Services**. Klicka på **Kör,** välj **Fullständig import**och **OK**. Gör dessa steg för alla kopplingar av den här typen.
2. Välj anslutningsappen med typen **Azure Active Directory (Microsoft)**. Klicka på **Kör,** välj **Fullständig import**och **OK**.
3. Kontrollera att fliken Kopplingar fortfarande är markerad. För varje koppling med typen **Active Directory Domain Services**klickar du på **Kör,** väljer **Delta-synkronisering**och **OK**.
4. Välj anslutningsappen med typen **Azure Active Directory (Microsoft)**. Klicka på **Kör,** välj **Deltasynkronisering**och **OK**.

Du har nu iscensatt exportändringar till Azure AD och lokalt AD (om du använder Exchange-hybriddistribution). I nästa steg kan du inspektera vad som är på väg att ändras innan du faktiskt startar exporten till katalogerna.

#### <a name="verify"></a>Verifiera
1. Starta en cmd-prompt och gå till`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Kör: `csexport "Name of Connector" %temp%\export.xml /f:x` Namnet på kopplingen finns i synkroniseringstjänsten. Den har ett namn som liknar "contoso.com – AAD" för Azure AD.
3. Kör: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Du har en fil i %temp% med namnet export.csv som kan undersökas i Microsoft Excel. Den här filen innehåller alla ändringar som är på väg att exporteras.
4. Gör nödvändiga ändringar i data eller konfiguration och kör dessa steg igen (Importera och synkronisera och verifiera) tills de ändringar som är på väg att exporteras förväntas.

**Förstå filen export.csv** Det mesta av filen är självförklarande. Några förkortningar för att förstå innehållet:
* OMODT – Objektändringstyp. Anger om åtgärden på objektnivå är en Lägg till, uppdatera eller Ta bort.
* AMODT – ändringstyp för attribut. Anger om åtgärden på attributnivå är en Lägg till, uppdatera eller ta bort.

**Hämta vanliga identifierare** Filen export.csv innehåller alla ändringar som är på väg att exporteras. Varje rad motsvarar en ändring för ett objekt i kopplingsutrymmet och objektet identifieras av DN-attributet. DN-attributet är en unik identifierare som tilldelats ett objekt i anslutningsutrymmet. När du har många rader/ändringar i export.csv att analysera kan det vara svårt för dig att ta reda på vilka objekt ändringarna är för baserat på DN-attributet ensam. Om du vill förenkla processen för att analysera ändringarna använder du skriptet csanalyzer.ps1 PowerShell. Skriptet hämtar vanliga identifierare (till exempel displayName, userPrincipalName) för objekten. Så här använder du skriptet:
1. Kopiera PowerShell-skriptet från avsnittet [CSAnalyzer](#appendix-csanalyzer) till en fil med namnet `csanalyzer.ps1`.
2. Öppna ett PowerShell-fönster och bläddra till mappen där du skapade PowerShell-skriptet.
3. Kör: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Du har nu en fil med namnet **processedusers1.csv** som kan undersökas i Microsoft Excel. Observera att filen innehåller en mappning från DN-attributet till vanliga identifierare (till exempel displayName och userPrincipalName). Den innehåller för närvarande inte de faktiska attributändringar som är på väg att exporteras.

#### <a name="switch-active-server"></a>Växla aktiv server
1. På den aktiva servern stänger du antingen av servern (DirSync/FIM/Azure AD Sync) så att den inte exporterar till Azure AD eller anger den i mellanlagringsläge (Azure AD Connect).
2. Kör installationsguiden på servern i **mellanlagringsläge** och inaktivera **mellanlagringsläge**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Haveriberedskap
En del av implementeringsdesignen är att planera för vad du ska göra om det skulle bli en katastrof där du förlorar synkroniseringsservern. Det finns olika modeller att använda och vilken man ska använda beror på flera faktorer, inklusive:

* Vad är din tolerans för att inte kunna göra ändringar i objekt i Azure AD under driftstopp?
* Om du använder lösenordssynkronisering, accepterar användarna att de måste använda det gamla lösenordet i Azure AD om de ändrar det lokalt?
* Har du ett beroende av realtidsåtgärder, till exempel tillbakaskrivning av lösenord?

Beroende på svaren på dessa frågor och organisationens policy kan en av följande strategier implementeras:

* Bygg om när det behövs.
* Har en reservserver, så kallad **mellanlagringsläge**.
* Använd virtuella datorer.

Om du inte använder den inbyggda SQL Express-databasen bör du också granska avsnittet [SQL High Availability.](#sql-high-availability)

### <a name="rebuild-when-needed"></a>Bygg om när det behövs
En hållbar strategi är att planera för en server ombyggnad när det behövs. Vanligtvis kan installation av synkroniseringsmotorn och göra den första importen och synkroniseringen slutföras inom några timmar. Om det inte finns en ledig server tillgänglig är det möjligt att tillfälligt använda en domänkontrollant som värd för synkroniseringsmotorn.

Synkroniseringsmotorservern lagrar inget tillstånd om objekten så att databasen kan återskapas från data i Active Directory och Azure AD. **Attributet sourceAnchor** används för att koppla objekten från lokala och molnet. Om du återskapar servern med befintliga objekt lokalt och i molnet matchar synkroniseringsmotorn dessa objekt igen vid ominstallation. De saker du behöver för att dokumentera och spara är konfigurationsändringar som gjorts på servern, till exempel filtrerings- och synkroniseringsregler. Dessa anpassade konfigurationer måste tillämpas på nytt innan du börjar synkronisera.

### <a name="have-a-spare-standby-server---staging-mode"></a>Ha en reservserver - mellanlagringsläge
Om du har en mer komplex miljö rekommenderas en eller flera standby-servrar. Under installationen kan du aktivera en server för att vara i **mellanlagringsläge**.

Mer information finns i [mellanlagringsläge](#staging-mode).

### <a name="use-virtual-machines"></a>Använda virtuella datorer
En vanlig och stöds metod är att köra synkroniseringsmotorn i en virtuell dator. Om värden har ett problem kan avbildningen med synkroniseringsmotorservern migreras till en annan server.

### <a name="sql-high-availability"></a>SQL hög tillgänglighet
Om du inte använder SQL Server Express som medföljer Azure AD Connect bör hög tillgänglighet för SQL Server också övervägas. De lösningar med hög tillgänglighet som stöds inkluderar SQL-kluster och AOA (Alltid på tillgänglighetsgrupper). Lösningar som inte stöds inkluderar spegling.

Stöd för SQL AOA har lagts till i Azure AD Connect i version 1.1.524.0. Du måste aktivera SQL AOA innan du installerar Azure AD Connect. Under installationen identifierar Azure AD Connect om den angivna SQL-instansen är aktiverad för SQL AOA eller inte. Om SQL AOA är aktiverat räknar Azure AD Connect vidare ut om SQL AOA är konfigurerat för att använda synkron replikering eller asynkron replikering. När du ställer in tillgänglighetsgrupplyssnaren rekommenderar vi att du anger egenskapen RegisterAllProvidersIP till 0. Detta beror på att Azure AD Connect för närvarande använder SQL Native Client för att ansluta till SQL och SQL Native Client inte stöder användning av MultiSubNetFailover-egenskapen.

## <a name="appendix-csanalyzer"></a>Tillägg CSAnalyzer
Se avsnittet [verifiera](#verify) om hur du använder det här skriptet.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Nästa steg
**Avsnitt om översikt**  

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)  
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)  
