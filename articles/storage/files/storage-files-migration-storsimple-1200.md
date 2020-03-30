---
title: StorSimple 1200 migrering till Azure File Sync
description: Lär dig hur du migrerar en virtuell storsimple 1200-serie till Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502361"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 migrering till Azure File Sync

StorSimple 1200-serien är en virtuell installation som körs i ett lokalt datacenter. Det är möjligt att migrera data från den här installationen till en Azure File Sync-miljö. Azure File Sync är standard- och strategisk azure-tjänst som StorSimple-enheter kan migreras till.

StorSimple 1200-serien kommer att nå sin [uttjänt](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) i december 2022.  Det är viktigt att börja planera din migrering så snart som möjligt. Den här artikeln innehåller nödvändiga steg i bakgrundskunskap och migreringar för en lyckad migrering till Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft har åtagit sig att hjälpa kunder i deras migrering. E-post AzureFilesMigration@microsoft .com för en anpassad migreringsplan samt hjälp under migreringen.

Azure File Sync är en Microsoft-molntjänst som baseras på två huvudkomponenter:

* Filsynkronisering och molnnivådelning.
* Filresurser som inbyggt lagringsutrymme i Azure, som kan nås via flera protokoll som SMB och file REST. En Azure-filresurs kan jämföras med en filresurs på en Windows Server som du kan montera som en nätverksenhet. Den stöder viktiga filåtergivningsaspekter som attribut, behörigheter och tidsstämplar. Till skillnad från StorSimple krävs inget program/tjänst för att tolka de filer och mappar som lagras i molnet. Den idealiska och mest flexibla metoden för att lagra filserverdata för allmänt bruk samt vissa programdata i molnet.

Den här artikeln fokuserar på migreringsstegen. Om du innan du migrerar vill veta mer om Azure File Sync rekommenderar vi följande artiklar:

* [Synkronisering av Azure-filer – översikt](https://aka.ms/AFS "Översikt")
* [Azure File Sync - distributionsguide](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migreringsmål

Målet är att garantera produktionsdatas integritet och garantera tillgänglighet. Den senare kräver att hålla stilleståndstiden till ett minimum, så att den kan passa in i eller bara något överstiga vanliga underhållsfönster.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Migreringssökvägen för StorSimple 1200 till Azure File Sync

En lokal Windows-server krävs för att köra en Azure File Sync-agent. Windows Server kan vara minst en 2012R2-server men är helst en Windows Server 2019.

Det finns många alternativa migreringsvägar och det skulle skapa för lång tid av en artikel för att dokumentera dem alla och illustrera varför de bär risk eller nackdelar över den väg vi rekommenderar som bästa praxis i den här artikeln.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Översikt över migreringsvägen för stegen längre nedan i den här artikeln.":::

Den föregående bilden visar steg som motsvarar avsnitt i den här artikeln.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Steg 1: Etablera din lokala Windows Server och lagring

1. Skapa en Windows Server 2019 – minst 2012R2 – som en virtuell dator eller en fysisk server. Ett Windows Server-felväxlingskluster stöds också.
2. Etablera eller lägg till direktansluten lagring (DAS jämfört med NAS, som inte stöds). Storleken på Windows Server-lagringen måste vara lika med eller större än storleken på den tillgängliga kapaciteten för den virtuella StorSimple 1200-installationen.

### <a name="step-2-configure-your-windows-server-storage"></a>Steg 2: Konfigurera windows server-lagring

I det här steget mappar du lagringsstrukturen i StorSimple (volymer och resurser) till windows server-lagringsstrukturen.
Om du planerar att göra ändringar i lagringsstrukturen, vilket innebär antalet volymer, sammanslutning av datamappar till volymer eller undermappsstrukturen ovanför eller under dina aktuella SMB/NFS-resurser, är det nu dags att ta hänsyn till dessa ändringar.
Det går besvärligt och bör undvikas att ändra fil- och mappstrukturen när Azure File Sync har konfigurerats.
Den här artikeln förutsätter att du mappar 1:1, så du måste ta hänsyn till mappningsändringarna när du följer stegen i den här artikeln.

* Ingen av dina produktionsdata bör hamna på Windows Server-systemvolymen. Molnnivådelning stöds inte på systemvolymer. Den här funktionen krävs dock för migreringen samt kontinuerliga åtgärder som en StorSimple-ersättning.
* Etablera samma antal volymer på din Windows Server som du har på den virtuella storsimple 1200-installationen.
* Konfigurera alla Windows Server-roller, funktioner och inställningar som du behöver. Vi rekommenderar att du väljer Windows Server-uppdateringar för att hålla ditt operativsystem säkert och uppdaterat. På samma sätt rekommenderar vi att du väljer Microsoft Update för att hålla Microsoft-program uppdaterade, inklusive Azure File Sync-agenten.
* Konfigurera inga mappar eller resurser innan du läser följande steg.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Steg 3: Distribuera den första molnresursen för Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Steg 4: Matcha din lokala volym- och mappstruktur med Azure File Sync och Azure-filresursresurser

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Steg 5: Etablera Azure-filresurser

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Steg 6: Konfigurera målmappar för Windows Server

I tidigare steg har du övervägt alla aspekter som avgör komponenterna i dina synkroniseringstopologier. Det är nu dags att förbereda servern för att ta emot filer för uppladdning.

Skapa **alla** mappar som synkroniserar var och en till sin egen Azure-filresurs.
Det är viktigt att du följer mappstrukturen som du har dokumenterat tidigare. Om du till exempel har valt att synkronisera flera, lokala SMB-resurser tillsammans till en enda Azure-filresurs, måste du placera dem under en gemensam rotmapp på volymen. Skapa den här målrotmappen på volymen nu.

Antalet Azure-filresurser som du har etablerat bör matcha antalet mappar som du har skapat i det här steget + antalet volymer som du synkroniserar på rotnivå.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Steg 7: Distribuera Azure File Sync-agenten

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Steg 8: Konfigurera synkronisering

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Var noga med att aktivera molnnivåhantering!** Detta krävs om den lokala servern inte har tillräckligt med utrymme för att lagra den totala storleken på dina data i StorSimple-molnlagringen. Ange din nivåytningsprincip, tillfälligt för migreringen, till 99 % volymfritt utrymme.

Upprepa stegen för att skapa synkroniseringsgrupp och lägga till den matchande servermappen som en serverslutpunkt för alla Azure-filresurser/serverplatser som måste konfigureras för synkronisering.

### <a name="step-9-copy-your-files"></a>Steg 9: Kopiera dina filer

Den grundläggande migreringsmetoden är en RoboCopy från den virtuella StorSimple-installationen till windowsservern och Azure File Sync till Azure-filresurser.

Kör den första lokala kopian till målmappen för Windows Server:

* Identifiera den första platsen på din virtuella StorSimple-apparat.
* Identifiera matchningsmappen på Windows Server, som redan har Azure File Sync konfigurerad på den.
* Starta kopian med RoboCopy

Följande RoboCopy-kommando återkallar filer från Din StorSimple Azure-lagring till din lokala StorSimple och flyttar dem sedan över till målmappen för Windows Server. Windows Server synkroniserar den med Azure-filresursen.Windows Server will sync it to the Azure file share(s. När den lokala Windows Server-volymen blir full kommer molnnivådelningen att starta och nivåfiler som redan har synkroniserats. Molnnivådelning genererar tillräckligt med utrymme för att fortsätta kopian från den virtuella storsimple-installationen. Molnnivådelning kontrollerar en gång i timmen för att se vad som har synkroniserats och för att frigöra diskutrymme för att nå det 99 % volymfria utrymmet.

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
      /UNILOG:<file name>
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
      Gör det möjligt att köra detta RoboCopy-kommando flera gånger, sekventiellt på samma mål / mål. Den identifierar vad som har kopierats tidigare och utelämnar det. Endast ändringar, tillägg och "*borttagningar*" kommer att bearbetas, som inträffade sedan den senaste körningen. Om kommandot inte kördes tidigare utelämnas ingenting. Detta är ett utmärkt alternativ för källplatser som fortfarande används aktivt och förändras.
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

När du kör kommandot RoboCopy för första gången kommer användarna och programmen fortfarande åt StorSimple-filerna och -mapparna och kan eventuellt ändra det. Det är möjligt att RoboCopy har bearbetat en katalog, går vidare till nästa och sedan en användare på källplatsen (StorSimple) lägger till, ändrar eller tar bort en fil som nu inte kommer att bearbetas i den aktuella RoboCopy-körningen. Det är bra.

Den första körningen handlar om att flytta tillbaka huvuddelen av data till lokalt, över till din Windows Server och säkerhetskopiera till molnet via Azure File Sync. Detta kan ta lång tid, beroende på:

* din nedladdningsbandbredd
* återkallningshastigheten för StorSimple-molntjänsten
* uppladdningsbandbredden
* antalet objekt (filer och mappar), som måste bearbetas av någon av tjänsterna

När den första körningen är klar kör du kommandot igen.

Andra gången kommer det att avslutas snabbare, eftersom det bara behöver transportera ändringar som hände sedan den senaste körningen. Dessa ändringar är sannolikt lokala för StorSimple redan, eftersom de är nya. Det minskar ytterligare tiden eftersom behovet av återkallande från molnet minskar. Under den här andra körningen kan nya ändringar ackumuleras.

Upprepa den här processen tills du är övertygad om att den tid det tar att slutföra är ett godtagbart driftstopp.

När du anser att driftstoppet är acceptabelt och du är beredd att ta StorSimple-platsen offline, gör det då nu: Ta till exempel bort SMB-resursen så att ingen användare kan komma åt mappen eller ta något annat lämpligt steg som förhindrar att innehållet ändras i den här mappen på StorSimple.

Kör en sista RoboCopy runda. Detta kommer att plocka upp alla förändringar, som kan ha missats.
Hur lång tid det sista steget tar beror på hastigheten på RoboCopy-genomsökningen. Du kan uppskatta tiden (vilket är lika med driftstopp) genom att mäta hur lång tid föregående körning tog.

Skapa en resurs i Windows Server-mappen och eventuellt justera DFS-N-distributionen så att den pekar på den. Var noga med att ange samma behörigheter på resursnivå som på din StorSimple SMB-resurs.

Du har migrerat en aktie/grupp av aktier till en gemensam rot eller volym. (Beroende på vad du mappade och bestämde dig för att gå in i samma Azure-filresurs.)

Du kan försöka köra några av dessa kopior parallellt. Vi rekommenderar att du bearbetar omfattningen av en Azure-filresurs i taget.

> [!WARNING]
> När du har flyttat alla data från dig StorSimple till Windows Server, och din migrering är klar: Återgå till ***alla*** synkroniseringsgrupper i Azure-portalen och justera värdet för molnnivånivåfri utrymme procent till något som är bättre lämpat för cacheanvändning, säg 20%. 

Principen för ledigt utrymme för molnnivå för volym fungerar på en volymnivå med potentiellt flera serverslutpunkter som synkroniseras från den. Om du glömmer att justera det lediga utrymmet på ens en serverslutpunkt fortsätter synkroniseringen att tillämpa den mest restriktiva regeln och försöker hålla 99 % ledigt diskutrymme, vilket gör att den lokala cachen inte fungerar som förväntat. Om det inte är ditt mål att bara ha namnområdet för en volym som bara innehåller sällan nås, arkivdata.

## <a name="troubleshoot"></a>Felsöka

Det mest sannolika problemet du kan stöta på är att RoboCopy kommandot misslyckas med *"Volym full"* på Windows Server-sidan. Om så är fallet, då din nedladdningshastighet är sannolikt bättre än din uppladdningshastighet. Molnnivådelning fungerar en gång i timmen för att evakuera innehåll från den lokala Windows Server-disken som har synkroniserats.

Låt synkroniseringen gå framåt och molnnivådelning frigöra diskutrymme. Observera det i Utforskaren på Windows Server.

När Windows Server har tillräcklig kapacitet löser kommandot problemet genom att köra kommandot igen. Ingenting går sönder när man kommer in i den här situationen och man kan gå vidare med tillförsikt. Olägenhet att köra kommandot igen är den enda konsekvensen.

Du kan också stöta på andra Azure File Sync-problem.
Så osannolikt som de kan vara, om det händer, ta en titt på **felsökningsguiden för LINK Azure File Sync**.

## <a name="relevant-links"></a>Relevanta länkar

Migreringsinnehåll:

* [Migreringsguide för StorSimple 8000-serien](storage-files-migration-storsimple-8000.md)

Azure File Sync-innehåll:

* [AFS översikt](https://aka.ms/AFS)
* [AFS-distributionsguide](storage-files-deployment-guide.md)
* [AFS-felsökning](storage-sync-files-troubleshoot.md)
