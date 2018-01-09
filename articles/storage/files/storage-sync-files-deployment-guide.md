---
title: "Distribuera Azure filsynkronisering (förhandsversion) | Microsoft Docs"
description: "Lär dig hur du distribuerar Azure filsynkronisering från början till slut."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: d5864b8df85a5b3cec086d4cb2edc6d288f1639a
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="deploy-azure-file-sync-preview"></a>Distribuera Azure filsynkronisering (förhandsgranskning)
Använda Azure filsynkronisering (förhandsgranskning) för att centralisera din organisations filresurser i Azure-filer, samtidigt som flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure filsynkronisering omvandlar Windows Server till en snabb cache med Azure-filresursen. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt data lokalt, inklusive SMB och NFS FTPS. Du kan ha valfritt antal cacheminnen som du behöver över hela världen.

Vi rekommenderar starkt att du läser [planera för distribution av en Azure-filer](storage-files-planning.md) och [planera för distribution av en Azure filsynkronisering](storage-sync-files-planning.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar
* Ett Azure Storage-konto och ett Azure filresurs i samma region som du vill distribuera Azure filsynkronisering. Mer information finns i:
    - [Regional tillgänglighet](storage-sync-files-planning.md#region-availability) för Azure filsynkronisering.
    - [Skapa ett lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för en stegvis beskrivning av hur du skapar ett lagringskonto.
    - [Skapa en filresurs](storage-how-to-create-file-share.md) för en stegvis beskrivning av hur du skapar en filresurs.
* Minst en stöds instans av Windows Server eller Windows Server-klustret för att synkronisera med synkronisering av Azure-filen. Mer information om vilka versioner av Windows Server finns [samverkan med Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).

## <a name="deploy-the-storage-sync-service"></a>Distribuera synkroniseringstjänsten för lagring 
Synkroniseringstjänsten lagring är översta Azure resurs för Azure filsynkronisering. Om du vill distribuera en tjänst för synkronisering av lagring, gå till den [Azure-portalen](https://portal.azure.com/), klickar du på *ny* och sök sedan efter Azure filsynkronisering. I sökresultaten väljer **filsynkronisering i Azure (förhandsversion)**, och välj sedan **skapa** att öppna den **distribuera lagring Sync** fliken.

I fönstret som öppnas anger du följande information:

- **Namnet**: ett unikt namn (per prenumeration) för lagring Sync-tjänsten.
- **Prenumerationen**: prenumerationen som du vill skapa synkroniseringstjänsten för lagring. Beroende på din organisations Konfigurationsstrategi kanske du har åtkomst till en eller flera prenumerationer. En Azure-prenumeration är den mest grundläggande behållaren för fakturering för varje tjänst i molnet (till exempel Azure-filer).
- **Resursgruppen**: en resursgrupp är en logisk grupp med Azure-resurser, till exempel ett lagringskonto eller en tjänst för synkronisering av lagring. Du kan skapa en ny resursgrupp eller Använd en befintlig resursgrupp för Azure filsynkronisering. (Vi rekommenderar att du använder resursgrupper som behållare för att isolera resurser logiskt för din organisation, till exempel gruppera HR resurser eller resurser för ett visst projekt.)
- **Plats**: den region där du vill distribuera Azure filsynkronisering. Endast regioner som stöds är tillgängliga i den här listan.

När du är klar väljer du **skapa** att distribuera lagring Sync-tjänsten.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Förbereda Windows Server till Azure filsynkronisering
Utför följande steg för varje server som du tänker använda med synkronisering av Azure-filen, inklusive servernoder i ett Failover-kluster:

1. Inaktivera **Förbättrad säkerhetskonfiguration i Internet Explorer**. Detta krävs endast för inledande serverregistreringsguiden. Du kan aktivera det igen när servern har registrerats.
    1. Öppna Serverhanteraren.
    2. Klicka på **lokal Server**:  
        ![”Lokal Server” på vänster sida av Server Manager-UI](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. På den **egenskaper** subpane, klicka på länken för **Förbättrad säkerhetskonfiguration**.  
        ![Fönstret ”Förbättrad säkerhetskonfiguration” i Server Manager-UI](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. I den **Förbättrad säkerhetskonfiguration i Internet Explorer** dialogrutan **av** för **administratörer** och **användare**:  
        ![Förbättrad säkerhetskonfiguration i Internet Explorer popup-fönster med ”av” markerad](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Se till att du kör minst PowerShell 5.1.\* (PowerShell 5.1 är standard i Windows Server 2016). Du kan kontrollera att du kör PowerShell 5.1. \* genom att titta på värdet för den **PSVersion** -egenskapen för den **$PSVersionTable** objekt:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Om din PSVersion-värdet är mindre än 5.1. \*, som kommer att vara fallet med de flesta installationer av Windows Server 2012 R2, kan du lätt uppgradera genom att hämta och installera [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Ladda ned och installera för Windows Server 2012 R2 är lämpligt paket **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

3. [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Vi rekommenderar att du använder den senaste versionen av Azure PowerShell-moduler.

## <a name="install-the-azure-file-sync-agent"></a>Installera Azure filsynkronisering agenten
Azure filsynkronisering agenten är hämtningsbara paket som gör det möjligt för Windows Server till att synkroniseras med en Azure-filresursen. Du kan ladda ned agenten från den [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). När hämtningen är klar dubbelklickar du på MSI-paketet för att starta installationen av Azure filsynkronisering.

> [!Important]  
> Om du tänker använda Azure filsynkronisering med ett redundanskluster måste filsynkronisering för Azure-agenten installeras på varje nod i klustret.


Installera installationspaketet Azure filsynkronisering agent relativt snabbt och utan för många ytterligare frågor. Vi rekommenderar att du gör följande:
- Lämna standardinstallationssökvägen (C:\Program Files\Azure\StorageSyncAgent) för att förenkla underhållet för felsökning och server.
- Aktivera Microsoft Update för att hålla Azure filsynkronisering uppdaterade. Alla uppdateringar sker till Azure filsynkronisering agenten, inklusive funktionsuppdateringar och snabbkorrigeringar, från Microsoft Update. Vi rekommenderar att du installerar den senaste uppdateringen ska synkroniseras för Azure-filen. Mer information finns i [Azure filsynkronisering uppdateringsprincip](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

När installationen av Azure filen Sync är klar öppnas automatiskt Användargränssnittet för servern registrering. Om du vill veta hur du registrerar den här servern med Azure filsynkronisering finns i nästa avsnitt.

## <a name="register-windows-server-with-storage-sync-service"></a>Registrera Windows Server med synkroniseringstjänsten för lagring
Windows-servern registreras med lagring-synkroniseringstjänsten upprättar en förtroenderelation mellan din server (eller ett kluster) och lagring-synkroniseringstjänsten. Användargränssnittet för servern registrering ska öppnas automatiskt efter installationen av Azure filsynkronisering agent. Om den inte kan du öppna det manuellt från dess filplats: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. När Användargränssnittet för servern registrering öppnas, Välj **inloggning** ska börja.

När du loggar in uppmanas du att ange följande information:

![En skärmbild av Användargränssnittet för servern registrering](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-prenumeration**: den prenumeration som innehåller synkroniseringstjänsten lagring (se [distribuera lagring synkroniseringstjänsten](#deploy-the-storage-sync-service)). 
- **Resursgruppen**: resursgruppen som innehåller synkroniseringstjänsten för lagring.
- **Storage-synkroniseringstjänsten**: namnet på synkroniseringsservern lagring som du vill registrera.

När du har valt rätt information, Välj **registrera** att slutföra registreringen av servern. Som en del av registreringen efterfrågas ett ytterligare inloggning.

## <a name="create-a-sync-group"></a>Skapa en grupp för synkronisering
En synkronisering grupp definierar sync-topologin för en uppsättning filer. Slutpunkter inom en synkronisering grupp hålls synkroniserade med varandra. En grupp för synkronisering måste innehålla minst en molnslutpunkt som representerar en Azure-filresurs, och slutpunkten för en server, som representerar en sökväg på Windows Server. Skapa en sync-grupp i den [Azure-portalen](https://portal.azure.com/)går du till lagring-synkroniseringstjänsten och välj sedan **+ Sync grupp**:

![Skapa en ny grupp för synkronisering i Azure-portalen](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Ange följande information för att skapa en grupp för synkronisering med en molnslutpunkt i fönstret som öppnas:

- **Synkronisera gruppnamn**: namnet på sync-grupp som ska skapas. Det här namnet måste vara unika inom synkroniseringstjänsten lagring, men kan vara vilket namn som är logiska för dig.
- **Prenumerationen**: prenumeration där du har distribuerat Storage Sync-tjänsten i [distribuera lagring synkroniseringstjänsten](#deploy-the-storage-sync-service).
- **Lagringskontot**: Om du väljer **Välj lagringskonto**, ett annat fönster visas där du väljer det lagringskonto som har Azure-filresursen som du vill synkronisera med.
- **Azure-filresurs**: namnet på Azure-filresursen som du vill synkronisera.

Om du vill lägga till en serverslutpunkt, gå till den nyligen skapade sync-gruppen och välj sedan **Lägg till serverslutpunkt**.

![Lägg till en ny serverslutpunkt i fönstret sync grupp](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

I den **Lägg till serverslutpunkt** rutan anger du följande information för att skapa en serverslutpunkt för:

- **Registrerad server**: namnet på servern eller klustret där du vill skapa serverslutpunkt.
- **Sökvägen**: I Windows Server-sökvägen till synkroniseras som en del av gruppen synkronisering.
- **Cloud Tiering**: en växel för att aktivera eller inaktivera molnet skiktning. Kan nivåindelas med molnet skiktning, sällan används eller filer till Azure Files.
- **Ledigt utrymme på volymen**: mängden ledigt utrymme som ska reserveras på volymen där serverns slutpunktens finns. Till exempel om ledigt utrymme på volymen anges till 50% på en volym som har en enda serverslutpunkt är ungefär hälften av data nivåer till Azure Files. Oavsett om molnet skiktning är aktiverat, Azure-filresursen har alltid en fullständig kopia av data i gruppen synkronisering.

Om du vill lägga till Serverslutpunkten, Välj **skapa**. Filerna är nu synkroniseras mellan din Azure-filresursen och Windows Server. 

> [!Important]  
> Du kan göra ändringar till en molnslutpunkt eller Serverslutpunkten i gruppen synkronisering och ha dina filer synkroniseras till de andra slutpunkterna i gruppen synkronisering. Om du gör en ändring i molnslutpunkt (Azure-filresursen) direkt måste ändringar först identifieras av en Azure-filen ändras identifiering synkroniseringsjobb. Ett jobb för identifiering av ändring initieras för en molnslutpunkt bara en gång per dygn. Mer information finns i [Azure Files vanliga frågor och](storage-files-faq.md#afs-change-detection).

## <a name="onboarding-with-azure-file-sync"></a>Onboarding med Azure File synkronisering
Rekommenderade åtgärder ska publiceras på Azure filsynkronisering för först med noll avbrottstid samtidigt som fullständiga återgivning och åtkomstkontrollistan (ACL) är följande:
 
1.  Distribuera en tjänst för synkronisering av lagring.
2.  Skapa en grupp för synkronisering.
3.  Installera Azure filsynkronisering agenten på servern med fullständig datauppsättning.
4.  Registrera servern och skapa en serverslutpunkt för resursen. 
5.  Kan synkronisera gör fullständig överföringen till Azure-filresursen (molnslutpunkt).  
6.  När den första överföringen är klar, installerar du Azure filsynkronisering agenten på de återstående servrarna.
7.  Skapa nya filresurser på var och en av de återstående servrarna.
8.  Skapa server-slutpunkter på nya filresurser med principer för molnet lagringsnivåer, om så önskas. (Det här steget kräver ytterligare lagringsutrymme ska vara tillgänglig för den första installationen.)
9.  Kan Azure filsynkronisering agent om du vill göra en snabb återställning av det fullständiga namnområdet utan faktiska dataöverföringen. Efter fullständig namnområde-synkronisering fylls Synkroniseringsmotorn det lokala diskutrymme baserat på principer för lagringsnivåer för molnet för Serverslutpunkten. 
10. Se till att synkroniseringen har slutförts och testa din topologi efter behov. 
11. Omdirigera användare och program till nya resursen.
12. Du kan eventuellt ta bort alla dubbla resurser på servrarna.
 
Om du inte har extra lagring för inledande onboarding och vill ansluta till de befintliga resurserna, kan du initiera data i Azure filresurser före. Den här metoden föreslås endast om du kan acceptera avbrott och absolut garantera att inga dataändringar för serverresurser under inledande onboarding-processen. 
 
1.  Se till att data på någon av servrarna inte kan ändras under onboarding-processen.
2.  Före utsäde Azure filresurser med data på servern med ett verktyg för överföring av data över SMB t.ex. Robocopy direkt SMB kopia. Eftersom AzCopy inte ladda upp data via SMB så den kan inte användas för före seeding.
3.  Skapa Azure filsynkronisering topologi med önskad server-slutpunkter som pekar på de befintliga resurserna.
4.  Kan synkronisera Slutför avstämning process på alla slutpunkter. 
5.  När avstämningen är klar kan öppna du resurser för ändringar.
 
Tänk på att för närvarande före seeding metod har några begränsningar- 
- Fullständig återgivning på filer sparas inte. Till exempel förlorar filer ACL: er och tidsstämplar.
- Dataändringar på servern innan sync topologin är helt upp och körs kan orsaka konflikter på server-slutpunkter.  
- När molnet slutpunkten har skapats körs Azure filsynkronisering en process för att identifiera filer i molnet innan du startar den första synkroniseringen. Den tid det tar att slutföra den här processen varierar beroende på de olika faktorerna som nätverkshastigheten, tillgänglig bandbredd och antalet filer och mappar. För grov uppskattning i förhandsversionen körs identifieringsprocessen ungefär vid 10 filer per sekund.  Därför måste även om seeding före körs snabbt vara den totala tid att hämta ett fullständigt system betydligt längre när data är förinstallerade i molnet.


## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrera en distribution av DFS Replication (DFS-R) till Azure filsynkronisering
Att migrera en DFS-R-distribution till Azure filsynkronisering:

1. Skapa en grupp för synkronisering för att representera DFS-R-topologi som du ersätter.
2. Starta på den server som har en fullständig uppsättning data i DFS-R-topologi för att migrera. Installera Azure filsynkronisering på servern.
3. Registrera servern och skapa en serverslutpunkt för den första servern som ska migreras. Aktivera inte molnet skiktning.
4. Låt alla datasynkronisering till din Azure-filresurs (molnslutpunkt).
5. Installera och registrera filsynkronisering för Azure-agenten på de återstående DFS-R-servrarna.
6. Inaktivera DFS-r 
7. Skapa en serverslutpunkt på DFS-R-servrarna. Aktivera inte molnet skiktning.
8. Se till att synkroniseringen har slutförts och testa din topologi efter behov.
9. Ta bort DFS-r
10. Molnet lagringsnivåer kan nu aktiveras på valfri serverslutpunkt efter behov.

Mer information finns i [Azure filsynkronisering interop med Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Nästa steg
- [Lägg till eller ta bort en Azure-filen Sync Serverslutpunkt](storage-sync-files-server-endpoint.md)
- [Registrera eller avregistrera en server med Azure filsynkronisering](storage-sync-files-server-registration.md)
