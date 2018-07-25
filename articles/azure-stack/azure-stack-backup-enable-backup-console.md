---
title: Aktivera säkerhetskopiering för Azure Stack från administrationsportalen | Microsoft Docs
description: Aktivera infrastruktur Backup-tjänsten via administrationsportalen så att Azure Stack kan återställas om det uppstår ett fel.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: 08bce6284b672ae092e2cee3c26140e8c6049a34
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242860"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Aktivera säkerhetskopiering för Azure Stack från administrationsportalen
Aktivera infrastruktur Backup-tjänsten via administrationsportalen så att Azure Stack kan generera säkerhetskopieringar. Du kan använda dessa säkerhetskopior för att återskapa din miljö med hjälp av molnåterställning i händelse av [ett oåterkalleligt fel](.\azure-stack-backup-recover-data.md). Syftet med molnet är att säkerställa att dina operatörer och användare kan logga in igen på portalen när återställningen är klar. Användarna har sina prenumerationer som återställts, inklusive behörigheter för rollbaserad åtkomst och roller, ursprungliga planer, erbjudanden, och tidigare definierad beräkning, lagring och nätverkskvoter.

Men säkerhetskopieringstjänsten infrastruktur kan du inte säkerhetskopiera virtuella IaaS-datorer, konfigurationer för nätverk och lagringsresurser, t.ex storage-konton, blobbar, tabeller och så vidare, så att användare som loggar in efter molnet återställningen har slutförts kommer inte se några av sina tidigare befintliga resurser. Plattform som en tjänst (PaaS) resurser och data också säkerhetskopieras inte av tjänsten. 

Administratörer och användare ansvarar för att säkerhetskopiera och återställa IaaS och PaaS-resurser separat från säkerhetskopiering infrastrukturprocesser. Information om hur du säkerhetskopierar IaaS och PaaS-resurser finns i följande länkar:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Innan du aktiverar säkerhetskopiering via konsolen kan behöva du konfigurera backup-tjänsten. Du kan konfigurera tjänsten backup med hjälp av PowerShell. Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Aktivera säkerhetskopiering

1. Öppna Azure Stack-administrationsportalen på [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Välj **fler tjänster** > **infrastruktur för säkerhetskopiering**. Välj **Configuration** i den **infrastruktur för säkerhetskopiering** bladet.

    ![Azure Stack - inställningarna för säkerhetskopiering-domänkontrollanter](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Ange sökvägen till den **lagringsplats för säkerhetskopiering**. Använd en Universal Naming Convention (UNC)-sträng för sökväg till en filresurs på en separat enhet. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter. Du kan använda en IP-adress för tjänsten. Enheten måste vara i en separat plats för att säkerställa tillgängligheten för säkerhetskopierade data efter en katastrof.
    > [!Note]  
    > Om miljön stöder namnmatchning från nätverket för Azure Stack-infrastruktur för att din företagsmiljö kan använda du ett fullständigt domännamn i stället för IP-Adressen.
4. Skriv den **användarnamn** med domänen och användarnamnet har tillräcklig behörighet att läsa och skriva filer. Till exempel `Contoso\backupshareuser`.
5. Skriv den **lösenord** för användaren.
5. Ange lösenordet igen till **Bekräfta lösenord**.
6. Tillhandahålla en i förväg delad nyckel i den **krypteringsnyckeln** box. Säkerhetskopiorna krypteras med hjälp av den här nyckeln. Se till att lagra den här nyckeln på en säker plats. När du anger den här nyckeln för första gången eller rotera nyckeln i framtiden ska visa du inte den här nyckeln från det här gränssnittet. För mer anvisningar för att generera en i förväg delad nyckel, följer du skript på [Aktivera säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).
7. Välj **OK** att spara dina inställningar för säkerhetskopiering controller.

För att köra en säkerhetskopia, måste du ladda ned Azure Stack-verktyg och kör sedan PowerShell-cmdleten **Start AzSBackup** på din nod för administration av Azure Stack. Mer information finns i [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Nästa steg

- Lär dig att köra en säkerhetskopiering. Se [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Lär dig att verifiera att säkerhetskopieringen har körts. Se [bekräfta säkerhetskopieringen slutfördes i administrationsportalen](azure-stack-backup-back-up-azure-stack.md).