---
title: 'Azure AD Connect: Uppgradera från en tidigare version | Microsoft Docs'
description: Beskriver olika metoder för att uppgradera till den senaste versionen av Azure Active Directory Connect, inklusive en uppgradering på plats och en öppning migrering.
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
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1a6fe4fc7fd5f47bfd4bc4d9168f76c31c78b47b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592484"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Uppgradera från en tidigare version till den senaste versionen
Det här avsnittet beskrivs olika metoder som du kan använda för att uppgradera installationen av Azure Active Directory (AD Azure) Anslut till den senaste versionen. Vi rekommenderar att du hålla dig uppdaterad med versionerna av Azure AD Connect. Du också använda stegen i den [svänga migrering](#swing-migration) avsnittet när du gör en betydande konfigurationsändring.

Om du vill uppgradera från DirSync läser [uppgradera från Azure AD-synkroniseringsverktyget (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) i stället.

Det finns några olika strategier som kan användas för att uppgradera Azure AD Connect.

| Metod | Beskrivning |
| --- | --- |
| [Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md) |Detta är det enklaste sättet för kunder med en snabb installation. |
| [Uppgradering på plats](#in-place-upgrade) |Om du har en enda server, kan du uppgradera den installationen på platsen på samma server. |
| [Öppning migrering](#swing-migration) |Med två servrar du förbereda en av servrarna med den nya versionen eller konfiguration och ändra den aktiva servern när du är klar. |

Behörighetsinformation finns i [behörigheter som krävs för en uppgradering](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> När du har aktiverat din nya Azure AD Connect-server att starta synkronisera ändringarna till Azure AD, måste du inte återställa med DirSync eller Azure AD Sync. Nedgradering från Azure AD Connect till äldre klienter, inklusive DirSync och Azure AD Sync stöds inte och kan leda till problem, till exempel förlust av data i Azure AD.

## <a name="in-place-upgrade"></a>Uppgradering på plats
En uppgradering på plats fungerar för att flytta från Azure AD Sync eller Azure AD Connect. Det fungerar inte för att flytta från DirSync eller för en lösning med Forefront Identity Manager (FIM) + Azure AD-koppling.

Den här metoden är att föredra när du har en enda server och på mindre än 100 000 objekt. Om det finns ändringar i regler för synkronisering av out-of-box en fullständig import och en fullständig synkronisering sker efter uppgraderingen. Den här metoden garanterar att den nya konfigurationen tillämpas alla befintliga objekt i systemet. Det här körs kan ta några timmar, beroende på antalet objekt i omfånget för Synkroniseringsmotorn. Normal delta synkronisering Schemaläggaren (som synkroniserar var 30: e minut som standard) har pausats, men Lösenordssynkronisering fortsätter. Du kan även göra uppgradering på plats när en helg. Om det finns inga ändringar av out-of-box-konfiguration med nya Azure AD Connect släpper, startas en normal import/Deltasynkronisering i stället.  
![Uppgradering på plats](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Om du har gjort ändringar i out-of-box Synkroniseringsregler, är sedan dessa regler inställda tillbaka till standardkonfigurationen vid uppgradering. Kontrollera att konfigurationen förblir mellan uppgraderingar, se till att du gör ändringar som de är beskrivs i [bästa praxis för att ändra standardkonfigurationen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

Under uppgradering på plats, kanske ändringar som kräver specifika synkronisering aktiviteter (inklusive fullständig Import steg och fullständig synkronisering) som ska utföras efter uppgraderingen har slutförts. Om du vill skjuta upp dessa aktiviteter finns i avsnittet [så att skjuta upp fullständig synkronisering efter uppgraderingen](#how-to-defer-full-synchronization-after-upgrade).

Om du använder Azure AD Connect med icke-standard-anslutningen (till exempel allmän LDAP Connector och allmänna SQL Connector), måste du uppdatera motsvarande kopplingskonfigurationen i den [Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) efter uppgradering på plats. Information om hur du uppdaterar kopplingens konfiguration finns i artikeln avsnittet [Connector versionshistorik - felsökning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Om du inte uppdaterar konfigurationen, fungerar importera och exportera köra steg inte korrekt för anslutningen. Du får följande fel i programmets händelselogg med meddelande *”Sammansättningsversionen i AAD-kopplingskonfiguration (” X.X.XXX. X ”) är tidigare än den faktiska versionen (” X.X.XXX. X ”) för” C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll ”.*

## <a name="swing-migration"></a>Swingmigrering
Om du har en komplex distribution eller många objekt, kan det vara opraktiska att göra en uppgradering på plats på live-systemet. Den här processen kan ta flera dagar--för vissa kunder och under denna tid kan ingen deltaändringar bearbetas. Du kan också använda den här metoden när du planerar du gör betydande ändringar i konfigurationen och du vill testa dem innan de är vidare till molnet.

Den rekommenderade metoden för dessa scenarier är att använda en öppning migrering. Du måste (minst) två servrar – en active server och en fristående server. Den aktiva servern (visas med fasta blå linjer i följande bild) ansvarar för den aktiva produktion. Fristående server (visas med streckad lila) har förberetts med den nya versionen eller konfigurationen. Den här servern har aktiverats när den är helt klar. Tidigare aktiva servern som nu har den gamla versionen eller konfiguration installerad, görs i fristående server och har uppgraderats.

De två servrarna kan använda olika versioner. Till exempel den aktiva servern som du tänker inaktivera kan använda Azure AD Sync och nya testservern kan använda Azure AD Connect. Om du använder öppning migrering för att utveckla en ny konfiguration, är en bra idé att ha samma versioner på de två servrarna.  
![Server för Förproduktion](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Vissa kunder föredrar att ha tre eller fyra servrar för det här scenariot. När den fristående servern har uppgraderats kan du inte har en reservserver för [katastrofåterställning](active-directory-aadconnectsync-operations.md#disaster-recovery). Med tre eller fyra servrar, kan du förbereda en uppsättning primära/vänteläge-servrar med den nya versionen, vilket garanterar att det alltid är en testserver som är redo att ta över.

De här stegen fungerar även om du vill flytta från Azure AD Sync eller en lösning med FIM + Azure AD-koppling. De här stegen fungerar inte för DirSync, men samma öppning migreringsmetod (kallas även parallell distribution) med stegen för DirSync finns i [uppgradera Azure Active Directory sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Använd en öppning migrering för att uppgradera
1. Om du använder Azure AD Connect på båda servrarna och planerar att göra en konfiguration, ändra, se till att endast använder aktiva servern och testserver båda samma version. Som gör det enklare att jämföra skillnader senare. Om du uppgraderar från Azure AD Sync har olika versioner av dessa servrar. Om du uppgraderar från en äldre version av Azure AD Connect är det en bra idé att börja med två servrar som använder samma version, men det krävs inte.
2. Om du har gjort en anpassad konfiguration och testservern inte har det, följer du stegen under [flytta en anpassad konfiguration från den aktiva servern till den tillfälliga servern](#move-custom-configuration-from-active-to-staging-server).
3. Uppgradera fristående server till den senaste versionen om du uppgraderar från en tidigare version av Azure AD Connect. Om du flyttar från Azure AD Sync, installera Azure AD Connect på testservern.
4. Kan Synkroniseringsmotorn köra fullständig import och fullständig synkronisering på testservern.
5. Kontrollera att den nya konfigurationen inte orsaka oväntade ändringar med hjälp av stegen i ”verifiera” i [Kontrollera konfigurationen av en server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Om något inte som förväntat, korrigera det genom att köra import och synkronisering och kontrollera data förrän den ser bra ut, genom att följa stegen.
6. Växla den fristående servern till den aktiva servern. Detta är det sista steget ”växel active server” i [Kontrollera konfigurationen av en server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Uppgradera den server som är nu i mellanlagringsläge till den senaste versionen om du uppgraderar Azure AD Connect. Följ på samma sätt som innan för att hämta data och konfiguration som uppgraderas. Om du har uppgraderat från Azure AD Sync kan nu stänga av och inaktivera den gamla servern.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Flytta en anpassad konfiguration från den aktiva servern till den tillfälliga servern
Om du har gjort konfigurationsändringar i den aktiva servern, måste du kontrollera att samma ändringar tillämpas på fristående servrar. Du kan använda för att hjälpa till med flyttningen, den [Azure AD Connect configuration Dokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter).

Du kan flytta den anpassa synkroniseringen regler som du har skapat med hjälp av PowerShell. Du måste tillämpa andra ändringar på samma sätt i båda systemen och du kan inte migrera ändringarna. Den [configuration Dokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter) kan hjälpa dig att jämföra de två systemen att kontrollera att de är identiska. Verktyget kan också vid automatisering av stegen i det här avsnittet.

Du behöver konfigurera följande på samma sätt som på båda servrarna:

* Anslutning till samma skogar
* Alla domän- och organisationsenhetsfiltrering
* Samma valfria funktioner, till exempel Lösenordssynkronisering och tillbakaskrivning av lösenord

**Flytta anpassade Synkroniseringsregler**  
Flytta anpassade Synkroniseringsregler genom att göra följande:

1. Öppna **Synchronization regler Editor** på din aktiva server.
2. Välj en anpassad regel. Klicka på **exportera**. Detta öppnar ett fönster för anteckningar. Spara den temporära filen med tillägget PS1. Detta gör att det PowerShell-skript. Kopiera filen PS1 till testservern.  
   ![Synkronisera regeln export](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Kopplingen GUID skiljer sig åt på testservern och du måste ändra den. GUID får starta **Synchronization regler Editor**, väljer du något av out-of-box-regler som representerar samma anslutna system och klicka på **exportera**. Ersätt GUID i PS1-fil med GUID från testservern.
4. Kör PS1-fil i en PowerShell-kommandotolk. Detta skapar anpassade synkroniseringsregeln på testservern.
5. Upprepa detta för alla anpassade regler.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Hur ska skjutas upp fullständig synkronisering efter uppgradering
Under uppgradering på plats, kanske ändringar som kräver specifika synkronisering aktiviteter (inklusive fullständig Import steg och fullständig synkronisering) för att köra. Exempelvis kräver connector schemaändringar **fullständig import** steg och out-of-box synkronisering regeln kräver **fullständig synkronisering** steg som ska utföras vid berörda kopplingar. Under uppgraderingen, Azure AD Connect avgör vilka synkronisering aktiviteter som krävs och registrerar dem som *åsidosätter*. I följande synkroniseringscykel Schemaläggaren synkronisering hämtar dessa åsidosättningar och kör dem. När en åsidosättning har körts tas bort.

Det kan finnas situationer där du inte vill att dessa åsidosättningar omedelbart efter uppgraderingen. Till exempel du har ett stort antal synkroniserade objekt och du vill att dessa synkronisering steg efter kontorstid. Ta bort dessa åsidosättningar:

1. Under uppgraderingen **avmarkera** alternativet **starta synkroniseringsprocessen när konfigurationen är klar**. Detta inaktiverar schemaläggare för synkronisering och förhindrar att synkroniseringscykel äger rum automatiskt innan åsidosättningarna som tas bort.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync01.png)

2. När uppgraderingen är klar kör du följande cmdlet ta reda på vilka åsidosättningar har lagts till: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Åsidosättningarna som är specifika för anslutningen. I följande exempel har fullständig Import steg och fullständig synkronisering lagts till både lokalerna AD-koppling och Azure AD-koppling.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync02.png)

3. Notera de befintliga åsidosättningar som lagts till.
   
4. Om du vill ta bort åsidosättningar för både fullständig import och fullständig synkronisering på en godtycklig koppling, kör du följande cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Kör följande PowerShell-skript för att ta bort åsidosättningar för alla kopplingar:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Om du vill återuppta Schemaläggaren, kör du följande cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Kom ihåg att köra nödvändiga synkroniseringssteg tidigaste dataskyddsstatistik. Du kan utföra dessa steg med hjälp av hanteraren för synkroniseringstjänsten eller manuellt lägga till åsidosättningarna tillbaka med cmdlet Set-ADSyncSchedulerConnectorOverride.

Kör följande cmdlet för att lägga till åsidosättningar för både fullständig import och fullständig synkronisering på en godtycklig koppling:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
