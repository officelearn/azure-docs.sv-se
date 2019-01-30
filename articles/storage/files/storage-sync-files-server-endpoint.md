---
title: Lägg till/ta bort en serverslutpunkt för Azure File Sync | Microsoft Docs
description: Lär dig att tänka på när du planerar för distribution av Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 118ecc08604cbe05181dd3ba943fd73a34fd47f1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212485"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Lägg till/ta bort en serverslutpunkt för Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla dina Windows-servrar till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

En *serverslutpunkt* representerar en specifik plats på en *registrerad server*, till exempel en mapp på en server-volym eller i roten på volymen. Flera serverslutpunkter kan finnas på samma volym om deras namnområden överlappar inte varandra (till exempel F:\sync1 och F:\sync2). Du kan konfigurera molnet lagringsnivåer principer separat för varje serverslutpunkt. Om du lägger till en plats på servern med en befintlig uppsättning filer som en serverslutpunkt till en synkroniseringsgrupp slås filerna samman med andra filer som redan finns på andra slutpunkter i synkroniseringsgruppen.

Se [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md) information om hur du distribuerar Azure File Sync slutpunkt till slutpunkt.

## <a name="prerequisites"></a>Förutsättningar
Om du vill skapa en serverslutpunkt, måste du först se till att följande villkor är uppfyllda: 
- Servern har Azure File Sync-agenten installerad och har registrerats. Instruktioner för installation av Azure File Sync-agenten finns i den [registrera/avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md) artikeln. 
- Se till att en Storage Sync-tjänsten har distribuerats. Se [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md) mer information om hur du distribuerar en Lagringssynkroniseringstjänst. 
- Se till att en synkroniseringsgrupp har distribuerats. Lär dig hur du [skapar en synkroniseringsgrupp](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Kontrollera att servern är ansluten till internet och att Azure är tillgänglig. Vi använder port 443 för all kommunikation mellan servern och vår tjänst.

## <a name="add-a-server-endpoint"></a>Lägga till en serverslutpunkt
Gå till den önskade synkroniseringsgruppen för att lägga till en serverslutpunkt och välj ”Lägg till serverslutpunkt”.

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Följande information krävs **Lägg till serverslutpunkt**:

- **Registrerad server**: Namnet på servern eller klustret att skapa Serverslutpunkten på.
- **Sökväg**: Sökvägen på den Windows-Server som ska synkroniseras som en del av synkroniseringsgruppen.
- **Molnnivå**: En växel för att aktivera eller inaktivera lagringsnivåer. När aktiverad cloud lagringsnivåer kommer *nivå* filer till Azure-filresurser. Det konverterar lokala filresurser till en cache i stället för en fullständig kopia av datauppsättningen för att hjälpa dig att hantera utrymmeseffektivitet på servern.
- **Ledigt utrymme på volym**: mängden ledigt utrymme för att reservera på volymen som Serverslutpunkten finns. Till exempel om volymens lediga utrymme är inställt på 50% på en volym med en enskild server-slutpunkt, ungefär hälften av data kommer att vara nivåindelad till Azure Files. Oavsett om molnet lagringsnivåer har aktiverats kan din Azure-filresurs har alltid en fullständig kopia av data i synkroniseringsgruppen.

Välj **skapa** att lägga till Serverslutpunkten. Filerna i ett namnområde för en synkroniseringsgrupp kommer nu hålls synkroniserade. 

## <a name="remove-a-server-endpoint"></a>Ta bort en serverslutpunkt
Om du vill sluta använda Azure File Sync för en viss server-slutpunkt, kan du ta bort Serverslutpunkten. 

> [!Warning]  
> Försök inte att felsöka problem med synkronisering, lagringsnivåer för moln eller några andra aspekter av Azure File Sync genom att ta bort och återskapa Serverslutpunkten om inte explicit ger instruktioner till en Microsoft-tekniker. Det är en destruktiv åtgärd för att ta bort en serverslutpunkt och nivåindelade filer inom Serverslutpunkten kommer inte att ”återansluta” till deras platser på Azure-filresursen när Serverslutpunkten återskapas, vilket innebär synkroniserade fel. Observera också, nivåindelade filer som finns utanför den slutpunkten namnrymden kan gå förlorade permanent. Nivåindelade filer kan finnas samtidigt i din server-slutpunkt, även om molnet molnlagringsnivåer aldrig har aktiverats.

Inaktivera molnlagringsnivåer på serverslutpunkt för att säkerställa att alla nivåindelade filer hämtas innan du tar bort Serverslutpunkten, och sedan köra följande PowerShell-cmdlet för att återkalla alla nivåindelade filer i din slutpunkt namnrymd:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Om den lokala volymen som är värd för servern inte har tillräckligt med ledigt utrymme för att återställa alla nivåindelade data i `Invoke-StorageSyncFileRecall` cmdlet misslyckas.  

Ta bort Serverslutpunkten:

1. Gå till Storage Sync-tjänsten där servern är registrerad.
2. Navigera till önskad synkroniseringsgruppen.
3. Ta bort Serverslutpunkten du önskar i synkroniseringsgruppen i Storage Sync-tjänsten. Detta kan åstadkommas genom att högerklicka på relevant Serverslutpunkten i fönstret sync grupp.

    ![Ta bort en serverslutpunkt från en synkroniseringsgrupp](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Nästa steg
- [Registrera/avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md)
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
