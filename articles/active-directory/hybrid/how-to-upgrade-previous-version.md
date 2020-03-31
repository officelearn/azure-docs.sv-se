---
title: 'Azure AD Connect: Uppgradera från en tidigare version | Microsoft-dokument'
description: Förklarar de olika metoderna för att uppgradera till den senaste versionen av Azure Active Directory Connect, inklusive en uppgradering på plats och en svängmigrering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3e7373a8b0354a3d08debf944f2f77f1609382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347759"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Uppgradera från en tidigare version till den senaste
I det här avsnittet beskrivs de olika metoder som du kan använda för att uppgradera din Azure Active Directory (Azure AD) Connect-installation till den senaste versionen. Vi rekommenderar att du håller dig uppdaterad med versionerna av Azure AD Connect. Du kan också använda stegen i avsnittet [Swing-migrering](#swing-migration) när du gör en omfattande konfigurationsändring.

>[!NOTE]
> Det stöds för närvarande att uppgradera från alla versioner av Azure AD Connect till den aktuella versionen. Uppgraderingar på plats av DirSync eller ADSync stöds inte och en svängmigrering krävs.  Om du vill uppgradera från DirSync läser du [Uppgradera från Azure AD-synkroniseringsverktyget (DirSync)](how-to-dirsync-upgrade-get-started.md) eller avsnittet [Swing-migrering.](#swing-migration)  </br>I praktiken kan kunder på extremt gamla versioner stöta på problem som inte är direkt relaterade till Azure AD Connect. Servrar som har varit i produktion i flera år, vanligtvis har haft flera patchar tillämpas på dem och inte alla av dessa kan redovisas.  Generellt, kunder som inte har uppgraderat i 12-18 månader bör överväga en swing uppgradering istället eftersom detta är det mest konservativa och minst riskfyllda alternativet.

Om du vill uppgradera från DirSync läser du [Uppgradera från Azure AD-synkroniseringsverktyget (DirSync)](how-to-dirsync-upgrade-get-started.md) i stället.

Det finns några olika strategier som du kan använda för att uppgradera Azure AD Connect.

| Metod | Beskrivning |
| --- | --- |
| [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) |Detta är den enklaste metoden för kunder med en expressinstallation. |
| [Uppgradering på plats](#in-place-upgrade) |Om du har en enda server kan du uppgradera installationen på samma server. |
| [Swingmigrering](#swing-migration) |Med två servrar kan du förbereda en av servrarna med den nya versionen eller konfigurationen och ändra den aktiva servern när du är redo. |

Information om behörigheter finns i de [behörigheter som krävs för en uppgradering](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> När du har aktiverat din nya Azure AD Connect-server för att börja synkronisera ändringar i Azure AD får du inte återställa till att använda DirSync eller Azure AD Sync. Nedgradering från Azure AD Connect till äldre klienter, inklusive DirSync och Azure AD Sync, stöds inte och kan leda till problem som dataförlust i Azure AD.

## <a name="in-place-upgrade"></a>Uppgradering på plats
En uppgradering på plats fungerar för att flytta från Azure AD Sync eller Azure AD Connect. Det fungerar inte för att flytta från DirSync eller för en lösning med Forefront Identity Manager (FIM) + Azure AD Connector.

Den här metoden är att föredra när du har en enda server och färre än cirka 100 000 objekt. Om det finns några ändringar i de färdiga synkroniseringsreglerna sker en fullständig import och fullständig synkronisering efter uppgraderingen. Den här metoden säkerställer att den nya konfigurationen tillämpas på alla befintliga objekt i systemet. Den här körningen kan ta några timmar, beroende på antalet objekt som finns i synkroniseringsmotorns omfattning. Den normala deltasynkroniseringsschemaläggaren (som synkroniseras var 30:e minut som standard) är pausad, men lösenordssynkroniseringen fortsätter. Du kan överväga att göra på plats uppgradering under en helg. Om det inte finns några ändringar i den färdiga konfigurationen med den nya Azure AD Connect-versionen startar en normal deltaimport/synkronisering i stället.  
![Uppgradering på plats](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Om du har gjort ändringar i de färdiga synkroniseringsreglerna ställs dessa regler tillbaka till standardkonfigurationen vid uppgradering. Kontrollera att konfigurationen hålls mellan uppgraderingarna genom att se till att du gör ändringar som de beskrivs i [Metodtips för att ändra standardkonfigurationen](how-to-connect-sync-best-practices-changing-default-configuration.md).

Under uppgradering på plats kan det införas ändringar som kräver att specifika synkroniseringsaktiviteter (inklusive steget Fullständig import och hela synkroniseringssteget) körs när uppgraderingen har slutförts. Om du vill skjuta upp sådana aktiviteter läser du avsnittet [Så här skjuter du upp fullständig synkronisering efter uppgradering](#how-to-defer-full-synchronization-after-upgrade).

Om du använder Azure AD Connect med icke-standardkoppling (till exempel Generic LDAP Connector och Generic SQL Connector) måste du uppdatera motsvarande anslutningskonfiguration i [Synkroniseringstjänsthanteraren](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) efter uppgradering på plats. Mer information om hur du uppdaterar anslutningskonfigurationen finns i artikelavsnittet [Connector Version Release History - Troubleshooting](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Om du inte uppdaterar konfigurationen fungerar inte stegen för import och export för kopplingen. Följande fel visas i programhändelseloggen med meddelandet *"Assembly version in AAD Connector configuration ("X.X.XXX. X") är tidigare än den faktiska versionen ("X.X.XXX. X") i "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Swingmigrering
Om du har en komplex distribution eller många objekt kan det vara opraktiskt att göra en uppgradering på plats i live-systemet. För vissa kunder kan den här processen ta flera dagar - och under den här tiden bearbetas inga deltaändringar. Du kan också använda den här metoden när du planerar att göra betydande ändringar i konfigurationen och du vill prova dem innan de skjuts till molnet.

Den rekommenderade metoden för dessa scenarier är att använda en svängmigrering. Du behöver (minst) två servrar – en aktiv server och en mellanlagringsserver. Den aktiva servern (visas med heldragna blå linjer i följande bild) ansvarar för den aktiva produktionsbelastningen. Mellanlagringsservern (visas med streckade lila linjer) förbereds med den nya versionen eller konfigurationen. När den är helt klar aktiveras den här servern. Den tidigare aktiva servern, som nu har den gamla versionen eller konfigurationen installerad, görs till mellanlagringsservern och uppgraderas.

De två servrarna kan använda olika versioner. Den aktiva servern som du planerar att inaktivera kan till exempel använda Azure AD Sync och den nya mellanlagringsservern kan använda Azure AD Connect. Om du använder swing migrering för att utveckla en ny konfiguration, är det en bra idé att ha samma versioner på de två servrarna.  
![Mellanlagringsserver](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Vissa kunder föredrar att ha tre eller fyra servrar för det här scenariot. När mellanlagringsservern har uppgraderats har du ingen säkerhetskopieringsserver för [haveriberedskap](how-to-connect-sync-staging-server.md#disaster-recovery). Med tre eller fyra servrar kan du förbereda en uppsättning primära/standby-servrar med den nya versionen, vilket säkerställer att det alltid finns en mellanlagringsserver som är redo att ta över.

De här stegen fungerar också för att flytta från Azure AD Sync eller en lösning med FIM + Azure AD Connector. De här stegen fungerar inte för DirSync, men samma swingmigreringsmetod (kallas även parallell distribution) med steg för DirSync är i [Upgrade Azure Active Directory sync (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Använd en svängmigrering för att uppgradera
1. Om du använder Azure AD Connect på båda servrarna och planerar att bara göra en konfigurationsändring kontrollerar du att båda använder samma version. Det gör det lättare att jämföra skillnader senare. Om du uppgraderar från Azure AD Sync har dessa servrar olika versioner. Om du uppgraderar från en äldre version av Azure AD Connect är det en bra idé att börja med de två servrar som använder samma version, men det krävs inte.
2. Om du har gjort en anpassad konfiguration och mellanlagringsservern inte har den följer du stegen under [Flytta en anpassad konfiguration från den aktiva servern till mellanlagringsservern](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Om du uppgraderar från en tidigare version av Azure AD Connect uppgraderar du mellanlagringsservern till den senaste versionen. Om du flyttar från Azure AD Sync installerar du Azure AD Connect på mellanlagringsservern.
4. Låt synkroniseringsmotorn köra fullständig import och fullständig synkronisering på mellanlagringsservern.
5. Kontrollera att den nya konfigurationen inte orsakade några oväntade ändringar med hjälp av stegen under Verifiera i [Verifiera konfigurationen av en server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Om något inte är som förväntat korrigerar du det, kör importen och synkroniseringen och verifierar data tills de ser bra ut genom att följa stegen.
6. Växla mellanlagringsservern så att den är den aktiva servern. Detta är det sista steget "Växla aktiv server" i [Verifiera konfigurationen av en server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Om du uppgraderar Azure AD Connect uppgraderar du servern som nu är i mellanlagringsläge till den senaste versionen. Följ samma steg som tidigare för att få data och konfiguration uppgraderas. Om du har uppgraderat från Azure AD Sync kan du nu stänga av och inaktivera din gamla server.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Flytta en anpassad konfiguration från den aktiva servern till mellanlagringsservern
Om du har gjort konfigurationsändringar på den aktiva servern måste du se till att samma ändringar tillämpas på mellanlagringsservern. Om du vill hjälpa till med det här steget kan du använda [konfigurationsdokumenteraren Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Du kan flytta de anpassade synkroniseringsregler som du har skapat med PowerShell. Du måste tillämpa andra ändringar på samma sätt på båda systemen och du kan inte migrera ändringarna. [Konfigurationsdokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter) kan hjälpa dig att jämföra de två systemen för att se till att de är identiska. Verktyget kan också hjälpa till att automatisera stegen som finns i det här avsnittet.

Du måste konfigurera följande saker på samma sätt på båda servrarna:

* Anslutning till samma skogar
* Alla domän- och organisationsenhetsfiltrering
* Samma valfria funktioner, till exempel lösenordssynkronisering och tillbakaskrivning av lösenord

**Flytta anpassade synkroniseringsregler**  
Så här flyttar du anpassade synkroniseringsregler:

1. Öppna Redigeraren för **synkroniseringsregler** på den aktiva servern.
2. Välj en anpassad regel. Klicka på **Exportera**. Detta tar upp ett Anteckningsblock fönster. Spara den temporära filen med ett PS1-tillägg. Detta gör det till ett PowerShell-skript. Kopiera PS1-filen till mellanlagringsservern.  
   ![Exportera synkroniseringsregel](./media/how-to-upgrade-previous-version/exportrule.png)
3. Anslutnings-GUID är annorlunda på mellanlagringsservern och du måste ändra det. Om du vill hämta GUID startar du **Redigeraren för synkroniseringsregler**, väljer en av de färdiga regler som representerar samma anslutna system och klickar på **Exportera**. Ersätt GUID i PS1-filen med GUID från mellanlagringsservern.
4. I en PowerShell-prompt kör du PS1-filen. Detta skapar den anpassade synkroniseringsregeln på mellanlagringsservern.
5. Upprepa detta för alla dina anpassade regler.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Så här skjuter du upp fullständig synkronisering efter uppgradering
Under uppgradering på plats kan det införas ändringar som kräver att specifika synkroniseringsaktiviteter (inklusive steget Fullständig import och ett fullständigt synkroniseringssteg) körs. Anslutningsschemaändringar kräver till exempel fullständiga ändringar av **synkroniseringssteg** och ändringar av synkroniseringsregeln för direkt möjligheten att utföra **fullständiga synkroniseringssteg** som ska utföras på berörda kopplingar. Under uppgraderingen avgör Azure AD Connect vilka synkroniseringsaktiviteter som krävs och registrerar dem som *åsidosättningar*. I följande synkroniseringscykel hämtar synkroniseringsschemat dessa åsidosättningar och kör dem. När en åsidosättning har körts tas den bort.

Det kan finnas situationer där du inte vill att dessa åsidosättningar ska ske omedelbart efter uppgraderingen. Du har till exempel många synkroniserade objekt och vill att synkroniseringsstegen ska inträffa efter kontorstid. Så här tar du bort dessa åsidosättningar:

1. Avmarkera **alternativet** **Starta synkroniseringsprocessen när konfigurationen är klar under**uppgraderingen. Detta inaktiverar synkroniseringsschemaläggaren och förhindrar att synkroniseringscykeln sker automatiskt innan åsidosättningarna tas bort.

   ![InaktiveraFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. När uppgraderingen är klar kör du följande cmdlet för att ta reda på vilka åsidosättningar som har lagts till:`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Åsidosättningarna är kopplingsspecifika. I följande exempel har steget Fullständig import och fullständigt synkronisering lagts till i både den lokala AD Connector- och Azure AD-anslutningsappen.

   ![InaktiveraFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Notera de befintliga åsidosättningar som har lagts till.
   
4. Om du vill ta bort åsidosättningarna för både fullständig import och fullständig synkronisering på en godtycklig koppling kör du följande cmdlet:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Om du vill ta bort åsidosättningarna på alla kopplingar kör du följande PowerShell-skript:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Om du vill återuppta schemaläggaren kör du följande cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Kom ihåg att köra de nödvändiga synkroniseringsstegen så snart som möjligt. Du kan antingen köra dessa steg manuellt med hjälp av Synkroniseringstjänsthanteraren eller lägga till åsidosättningar tillbaka med cmdlet set-ADSyncSchedulerConnectorOverride.

Om du vill lägga till åsidosättningar för både fullständig import och fullständig synkronisering på en godtycklig koppling kör du följande cmdlet:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Felsökning
Följande avsnitt innehåller felsökning och information som du kan använda om du stöter på ett problem med att uppgradera Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Azure Active Directory-anslutningsfel saknas under Azure AD Connect-uppgradering

När du uppgraderar Azure AD Connect från en tidigare version kan du få följande fel i början av uppgraderingen 

![Fel](./media/how-to-upgrade-previous-version/error1.png)

Det här felet beror på att Azure Active Directory-anslutningen med identifierare, b891884f-051e-4a83-95af-2544101c9083, inte finns i den aktuella Azure AD Connect-konfigurationen. Om du vill kontrollera att så är fallet öppnar du ett PowerShell-fönster och kör Cmdlet`Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

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

PowerShell-cmdleten rapporterar felet **som det inte gick att hitta den angivna ma-platsen**.

Anledningen till att detta inträffar beror på att den aktuella Azure AD Connect-konfigurationen inte stöds för uppgradering. 

Om du vill installera en nyare version av Azure AD Connect: stäng Azure AD Connect-guiden avinstallerar du den befintliga Azure AD Connect och utför en ren installation av den nyare Azure AD Connect.



## <a name="next-steps"></a>Nästa steg
Läs mer om [hur du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
