---
title: "Hur du distribuerar Azure filsynkronisering (förhandsversion) | Microsoft Docs"
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
ms.openlocfilehash: b31b6ae413f72c626e2601ba860aad44ddaa29cd
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>Hur du distribuerar Azure filsynkronisering (förhandsgranskning)
Med Azure File Sync (förhandsversionen) kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Detta möjliggörs genom att Windows-servern omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Vi rekommenderar starkt att läsa [planera för distribution av en Azure-filer](storage-files-planning.md) och [planera för distribution av en Azure filsynkronisering](storage-sync-files-planning.md) innan du följer stegen i den här guiden.

## <a name="prerequisites"></a>Krav
* Ett Lagringskonto och en Azure filresurs i samma region som du vill distribuera Azure filsynkronisering. Mer information finns i:
    - [Regional tillgänglighet](storage-sync-files-planning.md#region-availability) för Azure filsynkronisering
    - [Skapa ett Lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för stegvisa anvisningar om hur du skapar ett Lagringskonto och
    - [Skapa en filresurs](storage-how-to-create-file-share.md) för stegvisa anvisningar om hur du skapar en filresurs.
* Minst en stöd för Windows Server eller Windows Server-klustret för att synkronisera med synkronisering av Azure-filen. Se [samverkan med Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability) mer information om vilka versioner av Windows Server.

## <a name="deploy-the-storage-sync-service"></a>Distribuera synkroniseringstjänsten för lagring 
Synkroniseringstjänsten lagring är den översta Azure-resurs som representerar Azure filsynkronisering. Om du vill distribuera en tjänst för synkronisering av lagring, navigera till den [Azure-portalen](https://portal.azure.com/), och Sök efter Azure filsynkronisering. Välj ”skapa” pop öppna ”distribuera lagring Sync” fliken när du har valt ”Azure filen Sync (förhandsgranskning)” i sökresultatet.

Resulterande bladet uppmanas du att ange följande information:

- **Namnet**: ett unikt namn (per prenumeration) för lagring Sync-tjänsten.
- **Prenumerationen**: prenumerationen att skapa lagringstjänsten för synkronisering. Beroende på din organisations Konfigurationsstrategi kan du ha åtkomst till en eller flera prenumerationer. En Azure-prenumeration är den mest grundläggande behållaren för fakturering för varje tjänst i molnet (till exempel Azure-filer).
- **Resursgruppen**: en resursgrupp är en logisk grupp med Azure-resurser, till exempel ett Lagringskonto eller en tjänst för synkronisering av lagring. Du kan skapa en ny resursgrupp eller Använd en befintlig resursgrupp för Azure filsynkronisering (Vi rekommenderar att du använder resursgrupper som behållare som används för att isolera resurser logiskt för din organisation, till exempel gruppera HR resurser eller resurser för ett visst projekt) .
- **Plats**: den region där du vill distribuera Azure filsynkronisering. Endast regioner som stöds är tillgängliga i den här listan.

När formuläret ”distribuera lagring Sync” har slutförts, klickar du på ”Skapa” för att distribuera lagring Sync-tjänsten.

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Förbereda Windows-servrar för användning med Azure filsynkronisering
Till någonsin server som du tänker använda Azure filsynkronisering, inklusive servernoder i ett redundanskluster, Slutför följande steg:

Utför följande steg för varje server, inklusive servernoder i ett redundanskluster som du tänker använda med synkronisering av Azure-fil:

1. Inaktivera Förbättrad säkerhetskonfiguration i Internet Explorer. Detta är bara krävs för inledande server registrering och kan reenabled när servern har registrerats.
    1. Öppna Serverhanteraren.
    2. Klicka på **lokal Server**:  
        ![”Lokal Server” på vänster sida av Server Manager-UI](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Klicka på länken för **Förbättrad säkerhetskonfiguration** i fönstret Egenskaper för underordnad:  
        ![Den ”IE Förbättrad säkerhetskonfiguration” i Användargränssnittet för Serverhanteraren](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Välj **av** för både administratörer och användare i popup-fönstret Förbättrad säkerhetskonfiguration i Internet Explorer:  
        ![Förbättrad säkerhetskonfiguration i Internet Explorer popup-fönster med ”av” markerad](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Se till att du kör minst PowerShell 5.1.\* (PowerShell 5.1 är standard i Windows Server 2016). Du kan kontrollera att du kör PowerShell 5.1. \* genom att titta på värdet för egenskapen PSVersion för $PSVersionTable-objekt:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - Om din PSVersion är mindre än 5.1. \*, som kommer att vara fallet med de flesta installationer av Windows Server 2012 R2, kan du lätt uppgradera genom att hämta och installera [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Ladda ned och installera för Windows Server 2012 R2 är lämpligt paket **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

3. [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Du bör alltid använda den senaste versionen av Azure PowerShell-moduler.

## <a name="install-the-azure-file-sync-agent"></a>Installera Azure filsynkronisering agenten
Azure filsynkronisering agenten är hämtningsbara paket delar som aktiverar en Windows-Server som ska synkroniseras med en Azure-fil. Agenten kan hämtas från den [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). När du har hämtat, dubbelklicka på MSI-paketet för att starta installationen av Azure filsynkronisering.

> [!Important]  
> Om du tänker använda Azure filsynkronisering med ett redundanskluster måste filsynkronisering för Azure-agenten installeras på varje nod i klustret.

Installationspaketet för Azure filsynkronisering agenten ska installera relativt snabbt utan att för många fler meddelanden. Vi rekommenderar följande:
- Lämna standardinstallationssökvägen `C:\Program Files\Azure\StorageSyncAgent`) att förenkla underhållet för felsökning och server.
- Aktivera Microsoft Update för att hålla Azure filsynkronisering uppdaterade. Alla uppdateringar, inklusive funktionsuppdateringar och snabbkorrigeringar till Azure filsynkronisering agenten sker från Microsoft Update. Vi rekommenderar alltid tar den senaste uppdateringen till Azure filsynkronisering. Se [Azure filsynkronisering uppdateringsprincip](storage-sync-files-planning.md#azure-file-sync-agent-update-policy) för mer information.

Vid slutet av installationen av Azure filsynkronisering Användargränssnittet för servern registrering ska starta automatiskt. Se nästa avsnitt för att registrera den här servern med synkronisering av Azure-filen.

## <a name="register-windows-server-with-storage-sync-service"></a>Registrera Windows Server med synkroniseringstjänsten för lagring
Registrera din Windows-Server med en Storage-synkroniseringstjänsten upprättar en förtroenderelation mellan din server (eller ett kluster) och lagring-synkroniseringstjänsten. Användargränssnittet för servern registrering bör starta automatiskt efter installationen av agenten Azure filsynkronisering, men om den inte kan du öppna det manuellt från platsen: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`. När Användargränssnittet för servern registrering är öppen klickar du på **inloggning** ska börja.

Efter inloggning uppmanas du följande information:

![En skärmbild av Användargränssnittet för servern registrering](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-prenumeration**: prenumerationen som innehåller synkroniseringstjänsten lagring (se [distribuera lagring synkroniseringstjänsten](#deploy-the-storage-sync-service) ovan). 
- **Resursgruppen**: resursgruppen som innehåller synkroniseringstjänsten för lagring.
- **Storage-synkroniseringstjänsten**: namnet på synkroniseringsservern lagring som du vill registrera.

När du har valt rätt information från av listrutorna, klickar du på **registrera** att slutföra registreringen av servern. Som en del av registreringen efterfrågas ett ytterligare inloggning.

## <a name="create-a-sync-group"></a>Skapa en grupp för synkronisering
En synkronisering grupp definierar sync-topologin för en uppsättning filer. Slutpunkter inom en grupp för synkronisering kommer att synkroniseras med varandra. En grupp för synkronisering måste innehålla minst en Molnslutpunkt som representerar en Azure-filresurs, och en Server-slutpunkt som representerar en sökväg på en Windows-Server. Om du vill skapa en grupp för synkronisering, navigera till Storage Sync-tjänsten i den [Azure-portalen](https://portal.azure.com/), och klicka på **+ Sync grupp**:

![Skapa ny grupp för synkronisering i Azure-portalen](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Den resulterande rutan uppmanas du att ange följande information för att skapa en grupp för synkronisering med en Molnslutpunkt:

- **Synkronisera gruppnamn**: namnet på Sync-grupp som ska skapas. Det här namnet måste vara unika inom synkroniseringstjänsten lagring, men kan vara vilket namn som är logiska för dig.
- **Prenumerationen**: prenumerationen du distribuerat Storage Sync-tjänsten i [distribuera lagring synkroniseringstjänsten](#deploy-the-storage-sync-service) ovan.
- **Lagringskontot**: ”Välj lagringskonto” pop öppnas en ytterligare rutan så att du väljer det lagringskonto som innehåller Azure filresursen som du vill synkronisera.
- **Azure-filresursen**: namnet på den Azure-filresurs som du vill synkronisera.

Om du vill lägga till en slutpunkt för servern, navigerar du till den nyligen skapade gruppen för synkronisering och klicka på ”Lägg till server endpoint”.

![Lägg till en ny Server-slutpunkt i fönstret Sync-grupp](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Resulterande ”Lägg till server endpoint”-rutan kräver följande information för att skapa en slutpunkt för servern:

- **Registrerad Server**: namnet på servern eller klustret att skapa slutpunkten Server.
- **Sökvägen**: sökväg på Windows Server som ska synkroniseras som en del av gruppen synkronisering.
- **Cloud Tiering**: en växel som ska aktivera eller inaktivera molnet skiktning, vilket möjliggör sällan används eller komma åt filer för att nivåindelas till Azure Files.
- **Ledigt utrymme på volymen**: mängden ledigt utrymme som ska reserveras på volymen där serverns slutpunktens finns. Till exempel om volymens lediga utrymme anges till 50% på en volym med en enda Serverslutpunkt ska ungefär hälften av data vara nivåer till Azure Files. Observera att oavsett om molnet skiktning är aktiverad kan Azure filresursen har alltid en fullständig kopia av data i gruppen synkronisering.

Klicka på ”Skapa” för att lägga till slutpunkten för servern. Filerna kommer nu att synkroniseras i Azure-filresurs och Windows Server. 

> [!Important]  
> Du kan göra ändringar till molnet eller Serverslutpunkten i gruppen synkronisering och har dina filer synkroniseras till de andra slutpunkterna i gruppen synkronisering. Om du ändrar till slutpunkten för molnet (Azure-filresurs) direkt, Lägg märke till att ändringar först måste identifieras av en Azure-filen ändra identifiering synkroniseringsjobb, vilket är endast initatiated för en Molnslutpunkt var 24: e timme. Finns det [Azure filer FAQ](storage-files-faq.md#afs-change-detection) för mer information.

## <a name="next-steps"></a>Nästa steg
- [Lägg till/ta bort en Azure-filen Sync Serverslutpunkt](storage-sync-files-server-endpoint.md)
- [Registrera/avregistrera en server med Azure filsynkronisering](storage-sync-files-server-registration.md)