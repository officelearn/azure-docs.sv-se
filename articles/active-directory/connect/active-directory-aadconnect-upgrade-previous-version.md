---
title: 'Azure AD Connect: Uppgradera från en föregående version | Microsoft Docs'
description: Beskriver olika metoder för att uppgradera till den senaste versionen av Azure Active Directory Connect, inklusive en uppgradering på plats och swing-migrering.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b730f80faa031b1866d3c11d8a2c885ec67f965e
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144328"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Uppgradera från en tidigare version till senast
Det här avsnittet beskrivs olika metoder som du kan använda för att uppgradera din Azure Active Directory (Azure AD) Connect-installationen till den senaste versionen. Vi rekommenderar att du hålla dig uppdaterad med versionerna av Azure AD Connect. Du också använda stegen i den [Swingmigrering](#swing-migration) avsnittet när du gör en betydande konfigurationsändring.

Om du vill uppgradera från DirSync läser [uppgradera från Azure AD-synkroniseringsverktyg (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) i stället.

Det finns några olika strategier som du kan använda för att uppgradera Azure AD Connect.

| Metod | Beskrivning |
| --- | --- |
| [Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md) |Det här är den enklaste metoden för kunder med en snabb installation. |
| [Uppgradering på plats](#in-place-upgrade) |Om du har en enda server kan uppgradera du den installationen på platsen på samma server. |
| [Swingmigrering](#swing-migration) |Med två servrar, kan du förbereda en av servrarna med den nya versionen eller konfiguration och ändra den aktiva servern när du är klar. |

Behörighetsinformation finns i den [behörigheter som krävs för en uppgradering](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> När du har aktiverat den nya Azure AD Connect-servern så att den synkroniserar ändringar till Azure AD kan gå du inte tillbaka till att använda DirSync eller Azure AD Sync. Nedgradering från Azure AD Connect till äldre klienter, inklusive DirSync och Azure AD Sync, stöds inte och kan leda till problem som dataförlust i Azure AD.

## <a name="in-place-upgrade"></a>Uppgradering på plats
En uppgradering på plats fungerar för att flytta från Azure AD Sync eller Azure AD Connect. Det fungerar inte för att flytta från DirSync eller för en lösning med Forefront Identity Manager (FIM) + Azure AD-koppling.

Den här metoden rekommenderas när du har en enskild server och mindre än cirka 100 000 objekt. Om det finns ändringar av Synkroniseringsregler för out-of-box, sker en fullständig import och fullständig synkronisering efter uppgraderingen. Den här metoden garanterar att den nya konfigurationen tillämpas på alla befintliga objekt i systemet. Den här körningen kan ta några timmar, beroende på antalet objekt som finns i omfattningen för Synkroniseringsmotorn. Normal delta synkronisering scheduler (som synkroniserar var 30: e minut som standard) har pausats, men Lösenordssynkronisering fortsätter. Du utförde uppgraderingen på plats under en helg. Om det finns inga ändringar i out-of-box-konfigurationen med den nya Azure AD Connect-versionen, startas en normal import/Deltasynkronisering i stället.  
![Uppgradering på plats](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Om du har gjort ändringar i out-of-box Synkroniseringsregler, är sedan dessa regler inställda tillbaka till standardkonfigurationen vid uppgradering. För att säkerställa att din konfiguration sparas mellan uppgraderingar, se till att du gör ändringar som de är beskrivs i [bästa praxis för att ändra standardkonfigurationen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

Under uppgradering på plats, det kan finnas ändringar som införs som kräver specifika synkronisering aktiviteter (inklusive fullständig Import steg och fullständig synkronisering) som ska köras när uppgraderingen är klar. Om du vill skjuta upp sådana aktiviteter finns i avsnittet [hur du skjuta upp fullständig synkronisering efter uppgradering](#how-to-defer-full-synchronization-after-upgrade).

Om du använder Azure AD Connect med inte är standard connector (till exempel allmän LDAP Connector och allmän SQL-Anslutningsapp), måste du uppdatera motsvarande kopplingskonfigurationen i den [hanteraren för synkroniseringstjänsten](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) efter uppgradering på plats. Mer information om hur du uppdaterar kopplingskonfigurationen läser du avsnittet [Connector versionshistorik – felsökning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Om du inte uppdaterar konfigurationen, fungerar importera och exportera kör steg inte korrekt för anslutningen. Du får följande fel i programmets händelselogg med meddelandet *”Sammansättningsversionen i AAD-kopplingskonfiguration (” X.X.XXX. ”X”) är tidigare än den faktiska versionen (”X.X.XXX. ”X”) av ”C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll”.*

## <a name="swing-migration"></a>Swingmigrering
Om du har en komplex distribution eller många objekt kan vara det opraktiskt att göra en uppgradering på plats på det aktiva systemet. Den här processen kan ta flera dagar, för vissa kunder och under tiden inga deltaändringar bearbetas. Du kan också använda den här metoden när du planerar att gör betydande ändringar i konfigurationen och du vill testa dem innan de är vidare till molnet.

Den rekommenderade metoden för de här scenarierna är att använda en swingmigrering. Du behöver (minst) två servrar – en aktiv server och en mellanlagringsserver. Den aktiva servern (visas med heldragna blå linjer i följande bild) ansvarar för den aktiva produktionsbelastningen. Testservern (visas med streckad lila) förbereds med den nya versionen eller konfiguration. Den här servern har aktiverats när den är helt klar. Den föregående aktiva servern, som nu har den gamla versionen eller konfiguration som har installerats, görs till den tillfälliga servern och har uppgraderats.

De två servrarna kan använda olika versioner. Till exempel den aktiva servern som du tänker inaktivera kan använda Azure AD Sync och nya mellanlagringsserver kan använda Azure AD Connect. Om du använder swingmigrering för att utveckla en ny konfiguration, är det en bra idé att ha samma versioner på de två servrarna.  
![Mellanlagringsserver](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Vissa kunder vill ha tre eller fyra servrar för det här scenariot. När den tillfälliga servern har uppgraderats kan du inte har en sekundär server för [haveriberedskap](active-directory-aadconnectsync-operations.md#disaster-recovery). Med tre eller fyra servrar, kan du förbereda en uppsättning primär/vänteläge-servrar med den nya versionen, vilket garanterar att det finns alltid en testserver som är redo att ta över.

De här stegen fungerar även om du vill flytta från Azure AD Sync eller en lösning med FIM + Azure AD-koppling. De här stegen fungerar inte för DirSync, men samma swing-migreringsmetod (även kallat parallell distribution) med steg för DirSync finns i [uppgradera Azure Active Directory sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Använd en swingmigrering för att uppgradera
1. Om du använder Azure AD Connect på båda servrarna och planerar att endast göra en konfiguration, ändra, se till att använder din aktiva server och mellanlagringsserver båda samma version. Som gör det lättare att jämföra skillnader senare. Om du uppgraderar från Azure AD Sync har olika versioner av dessa servrar. Om du uppgraderar från en äldre version av Azure AD Connect är det en bra idé att börja med de två servrarna som använder samma version, men det krävs inte.
2. Om du har gjort en anpassad konfiguration och testservern inte har det, följer du anvisningarna under [flytta en anpassad konfiguration från den aktiva servern till testservern](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Om du uppgraderar från en tidigare version av Azure AD Connect kan du uppgradera den tillfälliga servern till den senaste versionen. Om du flyttar från Azure AD Sync, installerar du Azure AD Connect på testservern.
4. Låt Synkroniseringsmotorn köra fullständig import och fullständig synkronisering på testservern.
5. Verifiera att den nya konfigurationen inte orsakar några oväntade ändringar med hjälp av stegen under ”verifiera” i [verifiera konfigurationen för en server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Om något inte som förväntat, rätta till det genom att köra importen och synkronisera och kontrollera data tills den ser bra ut, genom att följa stegen.
6. Växla den tillfälliga servern till den aktiva servern. Detta är det sista steget ”växel active server” i [verifiera konfigurationen för en server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Om du uppgraderar Azure AD Connect kan du uppgradera den server som är nu i mellanlagringsläge till den senaste versionen. Följ samma steg som innan du hämtar data och konfiguration som uppgraderas. Om du har uppgraderat från Azure AD Sync kan nu stänga av och inaktivera den gamla servern.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Flytta en anpassad konfiguration från den aktiva servern till den tillfälliga servern
Om du har gjort ändringar i konfigurationen i den aktiva servern, måste du se till att samma ändringar tillämpas på fristående servrar. Du kan använda för att underlätta flytten den [Azure AD Connect configuration Dokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter).

Du kan flytta den anpassa synkroniseringen regler som du har skapat med hjälp av PowerShell. Du måste använda andra ändringar på samma sätt på båda systemen och du kan inte migrera ändringarna. Den [configuration Dokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter) kan du jämföra de två systemen för att kontrollera att de är identiska. Verktyget kan också i automatisera stegen i det här avsnittet.

Du måste konfigurera följande på samma sätt på båda servrarna:

* Anslutning till samma skogar
* Alla domän- och OU-filtrering
* Samma valfria funktioner, till exempel Lösenordssynkronisering och tillbakaskrivning av lösenord

**Flytta anpassade Synkroniseringsregler**  
Om du vill flytta anpassade Synkroniseringsregler, gör du följande:

1. Öppna **Synchronization Rules Editor** på din aktiva server.
2. Välj en anpassad regel. Klicka på **exportera**. Gör det öppnas ett fönster med anteckningar. Spara den temporära filen med filnamnstillägget PS1. Detta gör det till ett PowerShell-skript. Kopiera PS1-fil till den tillfälliga servern.  
   ![Synkronisera regeln export](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Connector-GUID är annorlunda på testservern och du måste ändra den. För att få GUID kan starta **Synchronization Rules Editor**, väljer du något av de out-of-box-regler som representerar samma anslutna system, och klicka på **exportera**. Ersätt GUID i dina PS1-fil med GUID som fristående server.
4. Kör PS1-fil i en PowerShell-kommandotolk. Detta skapar anpassade synkroniseringsregeln på testservern.
5. Upprepa detta för alla dina anpassade regler.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Hur du skjuta upp fullständig synkronisering efter uppgradering
Under uppgradering på plats, det kan finnas ändringar som införs som kräver specifika synkronisering aktiviteter (inklusive fullständig Import steg och fullständig synkronisering) som ska köras. Till exempel schemaändringar för anslutningen kräver **fullständig import** steg och out-of-box synkronisering ändras kräver **fullständig synkronisering** steg som ska köras på berörda anslutningar. Under uppgraderingen, Azure AD Connect avgör vilka synkronisering aktiviteter krävs och registrerar dem som *åsidosätter*. I följande synkroniseringscykel Schemaläggaren synkronisering hämtar dessa åsidosättningar och kör dem. När en åsidosättning körs har, tas den bort.

Det kan finnas situationer där du inte vill att dessa åsidosättningar omedelbart efter uppgraderingen. Exempelvis kan du har ett stort antal synkroniserade objekt och du vill att de här stegen för synkronisering ska utföras efter kontorstid. Ta bort dessa åsidosättningar:

1. Under uppgraderingen, **avmarkera** alternativet **starta synkroniseringsprocessen när konfigurationen är klar**. Detta inaktiverar schemaläggare för synkronisering och förhindrar synkroniseringscykel inträffar automatiskt innan åsidosättningarna tas bort.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync01.png)

2. När uppgraderingen är klar kör du följande cmdlet för att ta reda på vilka åsidosättningar har lagts till: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Åsidosättningarna är specifika för anslutningsappen. I följande exempel har fullständig Import steg och fullständig synkronisering lagts till både lokalerna AD-koppling och Azure AD-koppling.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync02.png)

3. Anteckna de befintliga åsidosättningar som har lagts till.
   
4. Om du vill ta bort åsidosättningar för både fullständig import och fullständig synkronisering på en godtycklig koppling, kör du följande cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Kör följande PowerShell-skript för att ta bort åsidosättningar för alla kopplingar:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Om du vill återuppta scheduler, kör du följande cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Kom ihåg att dessa steg krävs synkronisering vid första möjliga tillfälle. Du kan manuellt köra dessa steg med hjälp av hanteraren för synkroniseringstjänsten eller Lägg till åsidosättningarna tillbaka med hjälp av cmdleten Set-ADSyncSchedulerConnectorOverride.

Kör följande cmdlet för att lägga till åsidosättningar för fullständig synkronisering och fullständig import på en godtycklig koppling:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Felsökning
Följande avsnitt innehåller information som du kan använda om det uppstår ett problem som uppgraderar Azure AD Connect och felsökning.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Uppgradera Azure Active Directory connector saknas-fel under Azure AD Connect

När du uppgraderar Azure AD Connect från en tidigare version måste kanske du kommer följande fel i början av uppgraderingen 

![Fel](./media/active-directory-aadconnect-upgrade-previous-version/error1.png)

Det här felet inträffar eftersom Azure Active Directory-koppling med identifierare, b891884f-051e-4a83-95af - 2544101c 9083, inte finns i den aktuella Azure AD Connect-konfigurationen. Om du vill verifiera så är fallet, öppna ett PowerShell-fönster genom att köra Cmdlet `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

PowerShell-cmdleten rapporterar felet **gick inte att hitta den angivna MA**.

Orsaken till att detta inträffar beror på den aktuella Azure AD Connect-konfigurationen inte stöds för uppgradering. 

Om du vill installera en nyare version av Azure AD Connect: Stäng Azure AD Connect-guiden avinstallera den befintliga Azure AD Connect och utföra en ren installation av den nyare Azure AD Connect.



## <a name="next-steps"></a>Nästa steg
Läs mer om [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
