---
title: Säkerhetskopiering och dataåterställning för Azure Stack med infrastruktur Backup-tjänsten | Microsoft Docs
description: Du kan säkerhetskopiera och återställa konfiguration och tjänstdata med hjälp av Backup-tjänsten infrastruktur.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
mss.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4cb8ffe218ef1cd64b93201eddbbd09bb16026db
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087397"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Säkerhetskopiering och dataåterställning för Azure Stack med Backup-tjänsten infrastruktur

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan säkerhetskopiera och återställa konfiguration och tjänstdata med hjälp av Backup-tjänsten infrastruktur. Varje Azure Stack-installation innehåller en instans av tjänsten. Du kan använda säkerhetskopior som har skapats av tjänsten för omdistribution av Azure Stack-molnet för att återställa identitet, säkerhet och Azure Resource Manager-data.

Du kan aktivera säkerhetskopiering när du är redo att använda molnet i produktion. Aktivera inte säkerhetskopia om du planerar att utföra testning och validering för en längre tidsperiod.

Innan du aktiverar din backup-tjänsten, kontrollera att du har [krav uppfyllda](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Infrastruktur för Backup-tjänsten innehåller inte användardata och program. <!-- See the following articles for instructions on backing up and restore [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), and [MySQL](https://aka.ms/azure-stack-mysql) resource providers and associated user data. -->

## <a name="the-infrastructure-backup-service"></a>Säkerhetskopieringstjänsten infrastruktur

Tjänsterna innehåller följande funktioner.

| Funktion                                            | Beskrivning                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Säkerhetskopiering infrastrukturtjänster                     | Samordna säkerhetskopiering för en delmängd av infrastrukturtjänster i Azure Stack. Om det finns en katastrof, kan data återställas som en del av omdistribution. |
| Komprimering och kryptering av exporterade säkerhetskopierade Data | Säkerhetskopierade data komprimeras och krypteras av systemet innan den exporteras till den externa lagringsplatsen som tillhandahålls av administratören.                |
| Säkerhetskopieringsjobbet övervakning                              | När säkerhetskopiering jobb misslyckas och reparation steg visas ett meddelande.                                                                                                |
| Upplevelse för hantering av säkerhetskopiering                       | Säkerhetskopiering RP har stöd för Aktivera säkerhetskopiering.                                                                                                                         |
| Molnåterställning                                     | Om det finns en oåterkallelig dataförlust, användas säkerhetskopieringar för att återställa information om grundläggande Azure Stack som en del av distributionen.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Kontrollera krav för infrastruktur Backup-tjänsten

- **Lagringsplats**  
  Du behöver en filresurs kan nås från Azure Stack som kan innehålla sju säkerhetskopieringar. Varje säkerhetskopiering är ungefär 10 GB. Din resurs ska kunna lagra 140 GB säkerhetskopieringar. Mer information om hur du väljer en lagringsplats för Azure Stack-infrastruktur Backup-tjänsten finns i [kraven för säkerhetskopiering domänkontrollanter](azure-stack-backup-reference.md#backup-controller-requirements).
- **Autentiseringsuppgifter**  
  Du behöver ett domänanvändarkonto och autentiseringsuppgifter, du kan till exempel använda Azure Stack-administratörsautentiseringsuppgifter.
- **Krypteringsnyckel**  
  Säkerhetskopiorna krypteras med hjälp av den här nyckeln. Se till att lagra den här nyckeln på en säker plats. När du anger den här nyckeln för första gången eller rotera nyckeln i framtiden ska visa du inte den här nyckeln från det här gränssnittet. För mer anvisningar för att generera en i förväg delad nyckel, följer du skript på [Aktivera säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [Aktivera säkerhetskopiering för Azure Stack från administrationsportalen](azure-stack-backup-enable-backup-console.md).
- Lär dig hur du [Aktivera säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Lär dig hur du [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md )
- Lär dig hur du [fort oåterkallelig dataförlust](azure-stack-backup-recover-data.md)
