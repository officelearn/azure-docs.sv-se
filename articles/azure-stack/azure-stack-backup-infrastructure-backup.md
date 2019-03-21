---
title: Säkerhetskopiering och dataåterställning för Azure Stack med infrastruktur Backup-tjänsten | Microsoft Docs
description: Du kan säkerhetskopiera och återställa konfiguration och tjänstdata med hjälp av Backup-tjänsten infrastruktur.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 080129ca1520dc2b1b085c69f6389508f11c7ba2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285929"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Säkerhetskopiering och dataåterställning för Azure Stack med Backup-tjänsten infrastruktur

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan säkerhetskopiera och återställa konfiguration och tjänstdata med hjälp av Backup-tjänsten infrastruktur. Varje Azure Stack-installation innehåller en instans av tjänsten. Du kan använda säkerhetskopior som har skapats av tjänsten för omdistribution av Azure Stack-molnet för att återställa identitet, säkerhet och Azure Resource Manager-data. 

Du kan aktivera säkerhetskopiering när du är redo att använda molnet i produktion. Aktivera inte säkerhetskopia om du planerar att utföra testning och validering för en längre tidsperiod.

Innan du aktiverar din backup-tjänsten, kontrollera att du har [krav uppfyllda](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Infrastruktur för Backup-tjänsten innehåller inte användardata och program. Referera till [skydda virtuella datorer som distribueras på Azure Stack](user/azure-stack-manage-vm-protect.md) mer information om hur du skyddar IaaS VM-baserade program. En grundläggande förståelse för hur du skyddar program på Azure Stack finns i den [Azure Stack onsiderations för företag affärskontinuitet och haveriberedskap recovery white paper om](http://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>Säkerhetskopieringstjänsten infrastruktur

Tjänsterna innehåller följande funktioner.

| Funktion                                            | Beskrivning                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Säkerhetskopiering infrastrukturtjänster                     | Samordna säkerhetskopiering för en delmängd av infrastrukturtjänster i Azure Stack. Om det finns en katastrof, kan data återställas som en del av omdistribution. |
| Komprimering och kryptering av exporterade säkerhetskopierade Data | Säkerhetskopierade data komprimeras och krypteras av systemet innan den exporteras till den externa lagringsplatsen som tillhandahålls av administratören.                |
| Säkerhetskopieringsjobbet övervakning                              | När säkerhetskopiering jobb misslyckas och reparation steg visas ett meddelande.                                                                                                |
| Upplevelse för hantering av säkerhetskopiering                       | Säkerhetskopiering RP har stöd för Aktivera säkerhetskopiering.                                                                                                                         |
| Cloud Recovery                                     | Om det finns en oåterkallelig dataförlust, användas säkerhetskopieringar för att återställa information om grundläggande Azure Stack som en del av distributionen.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Kontrollera krav för infrastruktur Backup-tjänsten

- **Lagringsplats**  
  Du behöver en filresurs kan nås från Azure Stack som kan innehålla sju säkerhetskopieringar. Varje säkerhetskopiering är ungefär 10 GB. Din resurs ska kunna lagra 140 GB säkerhetskopieringar. Mer information om hur du väljer en lagringsplats för Azure Stack-infrastruktur Backup-tjänsten finns i [kraven för säkerhetskopiering domänkontrollanter](azure-stack-backup-reference.md#backup-controller-requirements).
- **Autentiseringsuppgifter**  
  Du behöver ett domänanvändarkonto och autentiseringsuppgifter, du kan till exempel använda Azure Stack-administratörsautentiseringsuppgifter.
- **Krypteringscertifikat**  
  Säkerhetskopiorna krypteras med hjälp av den offentliga nyckeln i certifikatet. Se till att lagra certifikatet på en säker plats. 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [Aktivera säkerhetskopiering för Azure Stack från administrationsportalen](azure-stack-backup-enable-backup-console.md).

Lär dig hur du [Aktivera säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).

Lär dig hur du [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md )

Lär dig hur du [fort oåterkallelig dataförlust](azure-stack-backup-recover-data.md)
