---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391802"
---
Ändringar som görs i Azure-filresursen med hjälp av Azure-portalen eller SMB identifieras inte omedelbart och replikeras som ändringar i serverslutpunkten. Azure Files har ännu inte ändringsmeddelanden eller journalering, så det finns inget sätt att automatiskt initiera en synkroniseringssession när filer ändras. På Windows Server använder Azure File Sync [Windows USN-journaler](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) för att automatiskt initiera en synkroniseringssession när filerna ändras.

Azure File Sync för att identifiera ändringar i Azure-filresursen har ett schemalagt jobb som kallas *ett ändringsidentifieringsjobb*. Ett ändringsidentifieringsjobb räknar upp varje fil i filresursen och jämför den sedan med synkroniseringsversionen för filen. När ändringsidentifieringsjobbet avgör att filerna har ändrats initierar Azure File Sync en synkroniseringssession. Jobbet för ändringsidentifiering initieras var 24:e timme. Eftersom ändringsidentifieringsjobbet fungerar genom att räkna upp alla filer i Azure-filresursen tar ändringsidentifieringen längre i större namnområden än i mindre namnområden. För stora namnområden kan det ta längre tid än en gång var 24:e timme att avgöra vilka filer som har ändrats.

För att omedelbart synkronisera filer som ändras i Azure-filresursen kan **PowerShell-cmdletn Invoke-AzStorageSyncChangeDetection** användas för att manuellt initiera identifiering av ändringar i Azure-filresursen. Den här cmdleten är avsedd för scenarier där någon typ av automatiserad process gör ändringar i Azure-filresursen eller om ändringarna görs av en administratör (som att flytta filer och kataloger till resursen). För ändringar av slutanvändaren är rekommendationen att installera Azure File Sync-agenten i en virtuell IaaS-dator och låta slutanvändare komma åt filresursen via IaaS VM. På så sätt synkroniseras alla ändringar snabbt med andra agenter utan att du behöver använda cmdleten Invoke-AzStorageSyncChangeDetection. Mer information finns i dokumentationen [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)

>[!NOTE]
>Ändringar som görs i en Azure-filresurs med REST uppdaterar inte den senast ändrade tiden för SMB och ses inte som en ändring av synkronisering.

Vi undersöker att lägga till ändringsidentifiering för en Azure-filresurs som liknar USN för volymer på Windows Server. Hjälp oss att prioritera den här funktionen för framtida utveckling genom att rösta på den på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
