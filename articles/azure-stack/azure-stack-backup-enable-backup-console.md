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
ms.date: 09/05/2018
ms.author: jeffgilb
ms.openlocfilehash: fdcc4f51f49e8eb133deeeecd070c55511cda464
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985804"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Aktivera säkerhetskopiering för Azure Stack från administrationsportalen
Aktivera infrastruktur Backup-tjänsten via administrationsportalen så att Azure Stack kan generera säkerhetskopieringar. Du kan använda dessa säkerhetskopior för att återskapa din miljö med hjälp av molnåterställning i händelse av [ett oåterkalleligt fel](.\azure-stack-backup-recover-data.md). Syftet med molnet är att säkerställa att dina operatörer och användare kan logga in igen på portalen när återställningen är klar. Användarna har sina prenumerationer som återställts, inklusive behörigheter för rollbaserad åtkomst och roller, ursprungliga planer, erbjudanden, och tidigare definierad beräkning, lagring och nätverkskvoter.

Men säkerhetskopieringstjänsten infrastruktur kan du inte säkerhetskopiera virtuella IaaS-datorer, konfigurationer för nätverk och lagringsresurser, t.ex storage-konton, blobbar, tabeller och så vidare, så att användare som loggar in efter molnet återställningen har slutförts kommer inte se några av sina tidigare befintliga resurser. Plattform som en tjänst (PaaS) resurser och data också säkerhetskopieras inte av tjänsten. 

Administratörer och användare ansvarar för att säkerhetskopiera och återställa IaaS och PaaS-resurser separat från säkerhetskopiering infrastrukturprocesser. Information om hur du säkerhetskopierar IaaS och PaaS-resurser finns i följande länkar:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Aktivera eller konfigurera om backup

1. Öppna den [Azure Stack-administrationsportalen](azure-stack-manage-portals.md).
2. Välj **alla tjänster**, och sedan under den **ADMINISTRATION** kategori väljer **infrastruktur för säkerhetskopiering**. Välj **Configuration** i den **infrastruktur för säkerhetskopiering** bladet.
3. Ange sökvägen till den **lagringsplats för säkerhetskopiering**. Använd en Universal Naming Convention (UNC)-sträng för sökväg till en filresurs på en separat enhet. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter. Du kan använda en IP-adress för tjänsten. Enheten måste vara i en separat plats för att säkerställa tillgängligheten för säkerhetskopierade data efter en katastrof.

    > [!Note]  
    > Om miljön stöder namnmatchning från nätverket för Azure Stack-infrastruktur för att din företagsmiljö kan använda du ett fullständigt domännamn i stället för IP-Adressen.
    
4. Skriv den **användarnamn** med domänen och användarnamnet har tillräcklig behörighet att läsa och skriva filer. Till exempel `Contoso\backupshareuser`.
5. Skriv den **lösenord** för användaren.
6. Ange lösenordet igen till **Bekräfta lösenord**.
7. Den **frekvens i timmar** bestämmer hur ofta säkerhetskopiering skapas. Standardvärdet är 12. Scheduler har stöd för upp till 12 och minst 4. 
8. Den **kvarhållningsperiod i dagar** avgör hur många dagars säkerhetskopior bevaras på den externa platsen. Standardvärdet är 7. Scheduler har stöd för upp till 14 och minst 2. Säkerhetskopieringar som är äldre än kvarhållningsperioden tas automatiskt bort från extern plats.

    > [!Note]  
    > Om du vill arkivera säkerhetskopior som är äldre än kvarhållningsperioden, se till att säkerhetskopiera filerna innan scheduler tar bort säkerhetskopiorna. Om du minskar kvarhållningsperiod för säkerhetskopiering (t.ex. från sju till 5 dagar) raderas Schemaläggaren alla säkerhetskopieringar som är äldre än den nya kvarhållningsperioden. Kontrollera att du är ok med säkerhetskopior tas bort innan du uppdaterar det här värdet. 

9. Tillhandahålla en i förväg delad nyckel i den **krypteringsnyckeln** box. Säkerhetskopiorna krypteras med hjälp av den här nyckeln. Se till att lagra den här nyckeln på en säker plats. När du anger den här nyckeln för första gången eller rotera nyckeln i framtiden ska visa du inte nyckeln från det här gränssnittet. Kör följande Azure Stack PowerShell-kommandon för att skapa nyckeln:
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. Välj **OK** att spara dina inställningar för säkerhetskopiering controller.

    ![Azure Stack - inställningarna för säkerhetskopiering-domänkontrollanter](media\azure-stack-backup\backup-controller-settings.png)

## <a name="start-backup"></a>Starta Säkerhetskopiering
Om du vill starta en säkerhetskopiering klickar du på **Säkerhetskopiera nu** att starta en säkerhetskopiering på begäran. En säkerhetskopiering på begäran kan inte ändra tiden för nästa schemalagda säkerhetskopiering. När uppgiften har slutförts kan du bekräfta inställningarna i **Essentials**:

![Azure Stack - säkerhetskopiering på begäran](media\azure-stack-backup\scheduled-backup.png)

Du kan också köra PowerShell-cmdleten **Start AzsBackup** på din dator för administration av Azure Stack. Mer information finns i [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Aktivera eller inaktivera automatisk säkerhetskopiering
Säkerhetskopieringarna schemaläggs automatiskt när du aktiverar säkerhetskopiering. Du kan kontrollera nästa schemalagda säkerhetskopiering tid i **Essentials**. 

![Azure Stack - säkerhetskopiering på begäran](media\azure-stack-backup\on-demand-backup.png)

Om du vill inaktivera framtida schemalagda säkerhetskopieringar klickar du på **inaktivera automatiska säkerhetskopieringar**. Inaktivera automatisk säkerhetskopiering behålls konfigurerade inställningar för säkerhetskopiering och behåller schemat för säkerhetskopiering. Den här åtgärden anger bara scheduler för att hoppa över framtida säkerhetskopieringar. 

![Azure Stack – inaktiverar schemalagda säkerhetskopieringar](media\azure-stack-backup\disable-auto-backup.png)

Bekräfta att framtida schemalagda säkerhetskopieringar har inaktiverats i **Essentials**:

![Azure Stack - bekräfta säkerhetskopior har inaktiverats](media\azure-stack-backup\confirm-disable.png)

Klicka på **aktivera automatiska säkerhetskopieringar** att informera scheduler för att starta framtida säkerhetskopieringar på den schemalagda tiden. 

![Azure Stack – aktivera schemalagda säkerhetskopieringar](media\azure-stack-backup\enable-auto-backup.png)


> [!Note]  
> Om du har konfigurerat infrastrukturen backup innan du uppdaterar till 1807 kommer automatiska säkerhetskopieringar att inaktiveras. På så sätt de säkerhetskopior som startas av Azure Stack inte står i konflikt med säkerhetskopior som startats av en extern aktivitet schemaläggning av motorn. När du inaktiverar eventuella externa Schemaläggaren, klickar du på **aktivera automatiska säkerhetskopieringar**.


## <a name="next-steps"></a>Nästa steg

- Lär dig att köra en säkerhetskopiering. Se [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Lär dig att verifiera att säkerhetskopieringen har körts. Se [bekräfta säkerhetskopieringen slutfördes i administrationsportalen](azure-stack-backup-back-up-azure-stack.md).
