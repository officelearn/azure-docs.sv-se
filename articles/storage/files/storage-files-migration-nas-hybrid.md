---
title: Lokal NAS-migrering till Azure File Sync
description: Lär dig hur du migrerar filer från en lokal NAS-plats (Network Attached Storage) till en hybridmolndistribution med Azure File Sync och Azure-filresurser.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247358"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrera från NAS (Network Attached Storage) till en hybridmolndistribution med Azure File Sync

Azure File Sync fungerar på DAS-platser (Direct Attached Storage) och stöder inte synkronisering till NAS-platser (Network Attached Storage).
Detta faktum gör en migrering av dina filer nödvändigt och den här artikeln guidar dig genom planering och genomförande av en sådan migrering.

## <a name="migration-goals"></a>Migreringsmål

Målet är att flytta de resurser som du har på NAS-apparaten till en Windows Server. Använd sedan Azure File Sync för en hybridmolndistribution. Den här migreringen måste göras på ett sätt som garanterar produktionsdatas integritet och tillgänglighet under migreringen. Den senare kräver att hålla stilleståndstiden till ett minimum, så att den kan passa in i eller bara något överstiga vanliga underhållsfönster.

## <a name="migration-overview"></a>Översikt över migrering

Som nämns i [översiktsartikeln](storage-files-migration-overview.md)för Azure Files migration är det viktigt att använda rätt kopieringsverktyg och tillvägagångssätt. NAS-apparaten exponerar SMB-resurser direkt i ditt lokala nätverk. RoboCopy, inbyggd i Windows Server, är det bästa sättet att flytta dina filer i det här migreringsscenariot.

- Fas 1: [Identifiera hur många Azure-filresurser du behöver](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fas 2: [Etablera en lämplig Windows Server lokalt](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fas 3: [Distribuera molnresursen Azure File Sync](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fas 4: [Distribuera Azure-lagringsresurser](#phase-4-deploy-azure-storage-resources)
- Fas 5: [Distribuera Azure File Sync-agenten](#phase-5-deploy-the-azure-file-sync-agent)
- Fas 6: [Konfigurera Synkronisering av Azure-filer på Windows Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fas 7: [RoboCopy](#phase-7-robocopy)
- Fas 8: [Överskärning av användare](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fas 1: Identifiera hur många Azure-filresurser du behöver

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fas 2: Etablera en lämplig Windows Server lokalt

* Skapa en Windows Server 2019 – minst 2012R2 – som en virtuell dator eller en fysisk server. Ett Windows Server-felväxlingskluster stöds också.
* Etablera eller lägg till direktansluten lagring (DAS jämfört med NAS, som inte stöds).

    Mängden lagringsutrymme som du etablerar kan vara mindre än vad du för närvarande använder på din NAS-installation, om du använder azure file [syncs-molnnivådelningsfunktionen.](storage-sync-cloud-tiering.md)
    Men när du kopierar dina filer från det större NAS-utrymmet till den mindre Windows Server-volymen i ett senare skede måste du arbeta i batchar:

    1. Flytta en uppsättning filer som passar till disken
    2. låta filsynkronisering och molnnivådelning engagera
    3. När mer ledigt utrymme skapas på volymen fortsätter du med nästa grupp filer. 
    
    Du kan undvika den här batching-metoden genom att etablera motsvarande utrymme på Windows Server som dina filer upptar på NAS-apparaten. Överväg deduplicering på NAS / Windows. Om du inte vill permanent spara den här stora mängden lagringsutrymme på Din Windows Server kan du minska volymstorleken efter migreringen och innan du justerar molnnivåprinciperna. Som skapar en mindre lokal cache av dina Azure-filresurser.

Resurskonfigurationen (beräkning och RAM) för den Windows Server som du distribuerar beror främst på antalet objekt (filer och mappar) som du ska synkronisera. Vi rekommenderar att du går med en konfiguration med högre prestanda om du har några problem.

[Lär dig hur du ã¶r storlek på en Windows Server baserat på antalet objekt (filer och mappar) som du behöver synkronisera.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Den tidigare länkade artikeln visar en tabell med ett intervall för serverminne (RAM). Du kan orientera dig mot det mindre numret för servern, men förvänta dig att den första synkroniseringen kan ta betydligt längre tid.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fas 3: Distribuera molnresursen Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fas 4: Distribuera Azure-lagringsresurser

I den här fasen kan du läsa mappningstabellen från fas 1 och använda den för att etablera rätt antal Azure-lagringskonton och filresurser i dem.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fas 5: Distribuera Azure File Sync-agenten

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fas 6: Konfigurera Synkronisering av Azure-filer på Windows Server

Din registrerade lokala Windows Server måste vara klar och ansluten till internet för den här processen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Molnnivådelning är AFS-funktionen som gör att den lokala servern har mindre lagringskapacitet än vad som lagras i molnet, men som ändå har det fullständiga namnområdet tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb åtkomstprestanda. Molnnivådelning är en valfri funktion per Azure File Sync "serverslutpunkt".

> [!WARNING]
> Om du har etablerat mindre lagringsutrymme på windowsservervolymen än dina data som används på NAS-apparaten är molnnivådelning obligatorisk. Om du inte aktiverar molnnivådelning frigör servern inte utrymme för att lagra alla filer. Ange din nivåytningsprincip, tillfälligt för migreringen, till 99 % volymfritt utrymme. Var noga med att återgå till dina inställningar för molnnivådelning när migreringen är klar och ställ in den på en mer långsiktig användbar nivå.

Upprepa stegen för att skapa synkroniseringsgrupp och lägga till den matchande servermappen som en serverslutpunkt för alla Azure-filresurser/serverplatser som måste konfigureras för synkronisering.

När alla serverslutpunkter har skapats fungerar synkroniseringen. Du kan skapa en testfil och se den synkroniseras från serverplatsen till den anslutna Azure-filresursen (enligt beskrivningen av molnslutpunkten i synkroniseringsgruppen).

Båda platserna, servermapparna och Azure-filresurserna är annars tomma och väntar på data på någon av platserna. I nästa steg börjar du kopiera filer till Windows Server för Azure File Sync för att flytta dem till molnet. Om du har aktiverat molnnivådelning börjar servern sedan att nivåfiler, om du får på kapacitet på de lokala volymerna.

## <a name="phase-7-robocopy"></a>Fas 7: RoboCopy

Den grundläggande migreringsmetoden är en RoboCopy från NAS-enheten till din Windows Server och Azure File Sync till Azure-filresurser.

Kör den första lokala kopian till målmappen för Windows Server:

* Identifiera den första platsen på DIN NAS-apparat.
* Identifiera matchningsmappen på Windows Server, som redan har Azure File Sync konfigurerad på den.
* Starta kopian med RoboCopy

Följande RoboCopy-kommando kopierar filer från NAS-lagringen till målmappen för Windows Server. Windows Server synkroniserar den med Azure-filresursen.Windows Server will sync it to the Azure file share(s. 

Om du har etablerat mindre lagringsutrymme på Din Windows Server än vad dina filer tar upp på NAS-apparaten har du konfigurerat molnnivåindelning. När den lokala Windows Server-volymen blir full kommer [molnnivådelningen](storage-sync-cloud-tiering.md) att starta och nivåfiler som redan har synkroniserats. Molnnivådelning genererar tillräckligt med utrymme för att fortsätta kopian från NAS-enheten. Molnnivådelning kontrollerar en gång i timmen för att se vad som har synkroniserats och för att frigöra diskutrymme för att nå det 99 % volymfria utrymmet.
Det är möjligt att RoboCopy flyttar filer snabbare än du kan synkronisera till molnet och nivå lokalt, vilket börjar få på lokalt diskutrymme. RoboCopy misslyckas. Vi rekommenderar att du arbetar genom aktierna i en sekvens som förhindrar det. Till exempel inte starta RoboCopy jobb för alla resurser samtidigt, eller bara flytta resurser som passar på den aktuella mängden ledigt utrymme på Windows Server, för att nämna några.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Bakgrund:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Gör det möjligt för RoboCopy att köra flertrådade. Standard är 8, max är 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<filnamn\>
   :::column-end:::
   :::column span="1":::
      Utdatastatus till LOG-fil som UNICODE (skriver över befintlig logg).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Utdata till konsolfönstret. Används tillsammans med utdata till en loggfil.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Kör RoboCopy i samma läge som ett säkerhetskopieringsprogram skulle använda. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR (MIR)
   :::column-end:::
   :::column span="1":::
      Gör det möjligt att köra detta RoboCopy-kommando flera gånger, sekventiellt på samma mål / mål. Den identifierar vad som har kopierats tidigare och utelämnar det. Endast ändringar, tillägg och "*borttagningar*" kommer att bearbetas, som inträffade sedan den senaste körningen. Om kommandot inte kördes tidigare utelämnas ingenting. */MIR-flaggan* är ett utmärkt alternativ för källplatser som fortfarande används aktivt och ändras.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      återgivning av filkopian (standard är /COPY:DAT), kopiera flaggor: D=Data, A=Attribut, T=Tidsstämplar, S=Security=NTFS-ACL:er, O=Ägarinformation, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      KOPIERA ALL filinformation (motsvarande /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      återgivning för kopian av kataloger (standard är /DCOPY:DA), kopiera flaggor: D=Data, A=Attribut, T=Tidsstämplar
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>Fas 8: Överskärning av användare

När du kör kommandot RoboCopy för första gången kommer användarna och programmen fortfarande åt filer på NAS-enheten och kan eventuellt ändra dem. Det är möjligt att RoboCopy har bearbetat en katalog, går vidare till nästa och sedan en användare på källplatsen (NAS) lägger till, ändrar eller tar bort en fil som nu inte kommer att bearbetas i den aktuella RoboCopy-körningen. Det här beteendet är förväntat.

Den första körningen handlar om att flytta huvuddelen av data till din Windows Server och till molnet via Azure File Sync. Den här första kopian kan ta lång tid, beroende på:

* din nedladdningsbandbredd
* uppladdningsbandbredden
* den lokala nätverkshastigheten och antalet hur optimalt antalet RoboCopy trådar matchar det
* antalet objekt (filer och mappar), som måste bearbetas av RoboCopy och Azure File Sync

När den första körningen är klar kör du kommandot igen.

Andra gången kommer det att avslutas snabbare, eftersom det bara behöver transportera ändringar som hände sedan den senaste körningen. Under den här andra körningen kan nya ändringar ackumuleras.

Upprepa den här processen tills du är övertygad om att den tid det tar att slutföra en RoboCopy för en viss plats ligger inom ett acceptabelt fönster för driftstopp.

När du anser att driftstoppet är acceptabelt och du är beredd att koppla från NAS-platsen: För att ta användaråtkomst offline har du möjlighet att ändra åtkomstkontrollanter på resursroten så att användarna inte längre kan komma åt platsen eller ta något annat lämpligt steg som förhindrar att innehåll ändras i den här mappen på din NAS.

Kör en sista RoboCopy runda. Det kommer att plocka upp alla förändringar, som kan ha missats.
Hur lång tid det sista steget tar beror på hastigheten på RoboCopy-genomsökningen. Du kan uppskatta tiden (vilket är lika med driftstopp) genom att mäta hur lång tid föregående körning tog.

Skapa en resurs i Windows Server-mappen och eventuellt justera DFS-N-distributionen så att den pekar på den. Var noga med att ange samma behörigheter på resursnivå som på nas SMB-resursen. Om du hade en domänansluten NAS i företagsklass matchar användarens SID automatiskt när användarna finns i Active Directory och RoboCopy kopierar filer och metadata med full återgivning. Om du har använt lokala användare på din NAS måste du återskapa dessa användare som lokala Windows Server-användare och mappa befintliga SID-sid roboCopy som flyttas över till din Windows Server till SID för dina nya lokala Windows Server-användare.

Du har migrerat en aktie/grupp av aktier till en gemensam rot eller volym. (Beroende på din mappning från fas 1)

Du kan försöka köra några av dessa kopior parallellt. Vi rekommenderar att du bearbetar omfattningen av en Azure-filresurs i taget.

> [!WARNING]
> När du har flyttat alla data från din NAS till Windows Server och din migrering är klar: Återgå till ***alla*** synkroniseringsgrupper i Azure-portalen och justera värdet för molnnivåreglering av volymfritt utrymme till något som är bättre lämpat för cacheanvändning, säg 20 %. 

Principen för ledigt utrymme för molnnivå för volym fungerar på en volymnivå med potentiellt flera serverslutpunkter som synkroniseras från den. Om du glömmer att justera det lediga utrymmet på ens en serverslutpunkt fortsätter synkroniseringen att tillämpa den mest restriktiva regeln och försöker hålla 99 % ledigt diskutrymme, vilket gör att den lokala cachen inte fungerar som förväntat. Om det inte är ditt mål att bara ha namnområdet för en volym som bara innehåller sällan nås, arkiveringsdata och du reserverar resten av lagringsutrymmet för ett annat scenario.

## <a name="troubleshoot"></a>Felsöka

Det mest sannolika problemet du kan stöta på är att RoboCopy kommandot misslyckas med *"Volym full"* på Windows Server-sidan. Molnnivådelning fungerar en gång i timmen för att evakuera innehåll från den lokala Windows Server-disken som har synkroniserats. Dess mål är att nå din 99% ledigt utrymme på volymen.

Låt synkroniseringen gå framåt och molnnivådelning frigöra diskutrymme. Observera det i Utforskaren på Windows Server.

När Windows Server har tillräcklig kapacitet löser kommandot problemet genom att köra kommandot igen. Ingenting går sönder när man kommer in i den här situationen och man kan gå vidare med tillförsikt. Olägenhet att köra kommandot igen är den enda konsekvensen.

Kontrollera länken i följande avsnitt för felsökning av Azure File Sync-problem.

## <a name="next-steps"></a>Nästa steg

Det finns mer att upptäcka om Azure-filresurser och Azure File Sync. Följande artiklar hjälper till att förstå avancerade alternativ, metodtips och innehåller även felsökningshjälp. De här artiklarna länkar till [Azure-filresursdokumentationen](storage-files-introduction.md) efter behov.

* [AFS översikt](https://aka.ms/AFS)
* [AFS-distributionsguide](storage-files-deployment-guide.md)
* [AFS-felsökning](storage-sync-files-troubleshoot.md)
