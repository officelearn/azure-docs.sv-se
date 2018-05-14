---
title: Säkerhetskopiering och återställa data för Azure-stacken med Backup-tjänsten infrastruktur | Microsoft Docs
description: Du kan säkerhetskopiera och återställa konfiguration och tjänstdata med hjälp av tjänsten infrastruktur för säkerhetskopiering.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 8c8037fe3936485082299250e603b2f3ea3859b9
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Säkerhetskopiering och återställa data för Azure-stacken med infrastruktur Backup-tjänsten

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan säkerhetskopiera och återställa konfiguration och tjänstdata med hjälp av tjänsten infrastruktur för säkerhetskopiering. Varje Azure Stack-installationen innehåller en instans av tjänsten. Du kan använda säkerhetskopior som har skapats av tjänsten för Omdistributionen av Azure Stack-molnet för att återställa identitet, säkerhet och Azure Resource Manager-data.

Du kan aktivera säkerhetskopiering när du är redo att placera ditt moln i produktion. Aktivera inte säkerhetskopia om du planerar att utföra tester och verifieringen av en längre tidsperiod.

Innan du aktiverar din säkerhetskopieringstjänsten, kontrollera att du har [krav på plats](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Infrastruktur Backup-tjänsten innehåller inte användardata och program. Se följande artiklar för instruktioner om säkerhetskopiering och återställning [Apptjänster](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), och [MySQL](https://aka.ms/azure-stack-mysql) resursproviders och associerade användardata...

## <a name="the-infrastructure-backup-service"></a>Tjänsten infrastrukturen för säkerhetskopiering

Tjänsten innehåller följande funktioner.

| Funktion                                            | Beskrivning                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Säkerhetskopiering infrastrukturtjänster                     | Samordna säkerhetskopiering över en delmängd av infrastrukturtjänster i Azure-stacken. Om det finns en katastrof, kan data återställas som en del av omdistributionen. |
| Komprimering och kryptering av exporterade säkerhetskopierade Data | Säkerhetskopierade data komprimeras och krypteras av systemet innan den exporteras till extern lagringsplats som tillhandahålls av administratören.                |
| Säkerhetskopieringsjobbet övervakning                              | När säkerhetskopieringen jobb misslyckas och reparation steg visas ett meddelande.                                                                                                |
| Säkerhetskopiering hanteringen                       | Säkerhetskopiering RP stöd för att aktivera säkerhetskopiering.                                                                                                                         |
| Molnet återställning                                     | Om det finns en oåterkallelig dataförlust, användas säkerhetskopieringar för att återställa core Azure Stackinformation som en del av distributionen.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Kontrollera krav för infrastruktur Backup-tjänsten

- **Lagringsplats**  
  Behöver du en filresurs tillgänglig från Azure-grupp som kan innehålla sju säkerhetskopieringar. Varje säkerhetskopiering är cirka 10 GB. Resursen ska kunna lagra säkerhetskopior 140 GB. Mer information om hur du väljer en lagringsplats för tjänsten Azure Stack infrastruktur säkerhetskopiering finns [kraven för säkerhetskopiering domänkontrollanter](azure-stack-backup-reference.md#backup-controller-requirements).
- **Autentiseringsuppgifter**  
  Du behöver ett domänanvändarkonto och autentiseringsuppgifter, du kan till exempel använda Azure Stack-administratörsautentiseringsuppgifter.
- **Krypteringsnyckel**  
  Säkerhetskopiorna krypteras med den här nyckeln. Se till att lagra den här nyckeln på en säker plats. När du anger den här nyckeln för första gången eller rotera nyckeln i framtiden ska visa du inte den här nyckeln från det här gränssnittet. Mer information att generera en i förväg delad nyckel, följ skript på [Aktivera säkerhetskopiering för Azure-stacken med PowerShell](http://azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [Aktivera säkerhetskopiering för Azure-stacken från administrationsportalen](azure-stack-backup-enable-backup-console.md).
- Lär dig hur du [Aktivera säkerhetskopiering för Azure-stacken med PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Lär dig hur du [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md )
- Lär dig hur du [återställa från oåterkallelig dataförlust](azure-stack-backup-recover-data.md)
