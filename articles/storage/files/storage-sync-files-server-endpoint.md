---
title: Lägga till/ta bort en serverslut för Azure File Sync | Microsoft-dokument
description: Lär dig vad du bör tänka på när du planerar för en Azure Files-distribution.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 684b30a24e049722cb531cbc84e3a2cd90912ec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255111"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Lägga till/ta bort en serverslut för Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Detta gör detta genom att omvandla dina Windows-servrar till en snabb cache av din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

En *serverslutpunkt* representerar en viss plats på en *registrerad server,* till exempel en mapp på en servervolym eller volymens rot. Det kan finnas flera serverslutpunkter på samma volym om deras namnområden inte överlappar varandra (till exempel F:\sync1 och F:\sync2). Du kan konfigurera principer för molnnivådelning individuellt för varje serverslutpunkt. Om du lägger till en serverplats med en befintlig uppsättning filer som en serverslutpunkt i en synkroniseringsgrupp, slås dessa filer samman med alla andra filer som redan finns på andra slutpunkter i synkroniseringsgruppen.

Se [Så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md) för information om hur du distribuerar Azure File Sync från på nytt.

## <a name="prerequisites"></a>Krav
Om du vill skapa en serverslutpunkt måste du först se till att följande villkor uppfylls: 
- Servern har Azure File Sync-agenten installerad och har registrerats. Instruktioner för installation av Azure File Sync Agent finns i artikeln [Registrera/avregistrera en server med Azure File Sync.](storage-sync-files-server-registration.md) 
- Kontrollera att en lagringssynkroniseringstjänst har distribuerats. Mer information om hur [du distribuerar Azure File Sync](storage-sync-files-deployment-guide.md) finns i Hur du distribuerar en lagringssynkroniseringstjänst. 
- Kontrollera att en synkroniseringsgrupp har distribuerats. Läs om hur du [skapar en synkroniseringsgrupp](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Kontrollera att servern är ansluten till internet och att Azure är tillgängligt. Vi använder port 443 för all kommunikation mellan servern och vår tjänst.

## <a name="add-a-server-endpoint"></a>Lägga till en serverslutpunkt
Om du vill lägga till en serverslutpunkt navigerar du till önskad synkroniseringsgrupp och väljer "Lägg till serverslutpunkt".

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Följande information krävs under **Lägg till serverslutpunkt:**

- **Registrerad server:** Namnet på servern eller klustret som serverns slutpunkt ska skapas på.
- **Sökväg:** Sökvägen till den Windows Server som ska synkroniseras som en del av synkroniseringsgruppen.
- **Molnnivådelning**: En växel för att aktivera eller inaktivera molnnivåhantering. När det är aktiverat kommer molnnivådelning *att nivåa* filer till dina Azure-filresurser. Detta konverterar lokala filresurser till en cache, snarare än en fullständig kopia av datauppsättningen, för att hjälpa dig att hantera utrymmeseffektivitet på servern.
- **Ledigt utrymme för volym:** mängden ledigt utrymme för att reservera på den volym som serverslutpunkten finns. Om till exempel det volymfria utrymmet är inställt på 50 % på en volym med en enda serverslutpunkt, kommer ungefär hälften av mängden data att nivåindelad till Azure-filer. Oavsett om molnnivådelning är aktiverat har din Azure-filresurs alltid en fullständig kopia av data i synkroniseringsgruppen.

Välj **Skapa** om du vill lägga till serverslutpunkten. Filerna inom ett namnområde för en synkroniseringsgrupp kommer nu att synkroniseras. 

## <a name="remove-a-server-endpoint"></a>Ta bort en serverslutpunkt
Om du vill sluta använda Azure File Sync för en viss serverslutpunkt kan du ta bort serverslutpunkten. 

> [!Warning]  
> Försök inte felsöka problem med synkronisering, molnnivådelning eller någon annan aspekt av Azure File Sync genom att ta bort och återskapa serverslutpunkten om inte uttryckligen instrueras av en Microsoft-tekniker. Att ta bort en serverslutpunkt är en destruktiv åtgärd, och nivåindelada filer i serverslutpunkten kommer inte att "återanslutas" till sina platser på Azure-filresursen när serverslutpunkten har återskapats, vilket resulterar i synkroniseringsfel. Observera också att nivåindelada filer som finns utanför serverslutpunktens namnområde kan gå förlorade permanent. Nivåindelad filer kan finnas i serverslutpunkten även om molnnivådelning aldrig har aktiverats.

Om du vill vara kontrollerar att alla nivåindelade filer återkallas innan serverslutpunkten tas bort inaktiverar du molnnivådelning på serverslutpunkten och kör sedan följande PowerShell-cmdlet för att återkalla alla nivåindelade filer i serverslutpunktsnamnområdet:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Om `-Order CloudTieringPolicy` du anger kommer de senast ändrade filerna att återkallas först.
Andra valfria men användbara parametrar att tänka på är:
* `-ThreadCount`avgör hur många filer som kan återkallas parallellt.
* `-PerFileRetryCount`avgör hur ofta ett återkallande görs av en fil som för närvarande är blockerad.
* `-PerFileRetryDelaySeconds`bestämmer tiden i sekunder mellan återförsök för att återkalla försök och bör alltid användas i kombination med den tidigare parametern.

> [!Note]  
> Om den lokala volymen som är värd för servern inte har `Invoke-StorageSyncFileRecall` tillräckligt med ledigt utrymme för att återkalla alla nivåindelade data, misslyckas cmdleten.  

Så här tar du bort serverslutpunkten:

1. Navigera till tjänsten Lagringssynkronisering där servern är registrerad.
2. Navigera till önskad synkroniseringsgrupp.
3. Ta bort den serverslutpunkt som du vill ha i synkroniseringsgruppen i tjänsten Lagringssynkronisering. Detta kan åstadkommas genom att högerklicka på relevant serverslutpunkt i synkroniseringsgruppfönstret.

    ![Ta bort en serverslutpunkt från en synkroniseringsgrupp](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Nästa steg
- [Registrera/avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md)
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
