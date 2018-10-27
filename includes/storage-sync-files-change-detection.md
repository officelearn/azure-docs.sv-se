---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166490"
---
Ändringar som görs till Azure-filresurs med hjälp av Azure-portalen eller SMB upptäcks inte omedelbart och replikerade ändringar Serverslutpunkten. Azure Files ännu inte har ändringsmeddelanden eller journaler, så det finns inget sätt att automatiskt att initiera en synkroniseringssession när filer ändras. På Windows Server, Azure File Sync använder [Windows USN-journal](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) att automatiskt starta en synkroniseringssessionen när filer ändras.<br /><br /> För att identifiera ändringar i Azure-filresursen Azure File Sync har ett schemalagt jobb som kallas en *ändra identifiering av jobbet*. Ett jobb för identifiering av ändring räknar upp varje fil i filresursen och jämför den med sync-versionen för filen. När ändringen identifiering av jobbet anger att filer som har ändrats, initierar Azure File Sync synkroniseringssession. Ändra identifiering av jobbet initieras var 24: e timme. Eftersom ändringen identifiering av jobbet fungerar genom att räkna upp varje fil i Azure-filresursen, tar identifiering av ändring av längre tid i större namnområden än i mindre namnområden. För stora namnområden, kan det ta längre tid än en gång per dygn för att avgöra vilka filer som har ändrats.<br /><br />
Observera att ändringar som gjorts i en Azure-filresurs med hjälp av REST gör inte uppdateringen SMB senast ändrad och kan inte ses som en ändring av synkronisering. <br /><br />
Vi utforskar att lägga till identifiering av ändring av för en Azure-filresurs som liknar USN för volymer på Windows Server. Hjälp oss att prioritera den här funktionen för framtida utveckling genom att rösta fram den på [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
