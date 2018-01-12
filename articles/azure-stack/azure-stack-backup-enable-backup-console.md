---
title: "Aktivera säkerhetskopiering för Azure-Stack från administrationsportalen | Microsoft Docs"
description: "Aktivera infrastruktur Backup-tjänsten via administrationsportalen så att Azure-stacken kan återställas om det uppstår ett fel."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 456a0db9771f5963c8d4375d54a22257f6ca1c56
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Aktivera säkerhetskopiering för Azure-Stack från administrationsportalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Aktivera infrastruktur Backup-tjänsten via administrationsportalen så att Azure-stacken kan generera säkerhetskopieringar. Du kan använda dessa säkerhetskopieringar för att återställa din miljö vid fel.

> [!Note]  
> Innan du aktiverar säkerhetskopiering via konsolen måste du konfigurera säkerhetskopieringstjänsten. Du kan konfigurera säkerhetskopieringstjänsten med hjälp av PowerShell. Mer information finns i [Aktivera säkerhetskopiering för Azure-stacken med PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Aktivera säkerhetskopiering

1. Öppna Azure Stack-administrationsportalen på [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Välj **fler tjänster** > **infrastruktur säkerhetskopiering**. Välj **Configuration** i den **infrastruktur säkerhetskopiering** bladet.

    ![Azure Stack - inställningarna för säkerhetskopiering domänkontrollanter](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Ange sökvägen till den **säkerhetskopiera lagringsplats**. Använd en Universal Naming Convention (UNC)-sträng för sökväg till en filresurs som finns på en separat enhet. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter. Du kan använda en IP-adress för tjänsten. Enheten måste vara i en annan plats för att säkerställa tillgängligheten för säkerhetskopierade data efter en katastrof.
    > [!Note]  
    > Om miljön har stöd för namnmatchning från nätverkets infrastruktur Azure Stack till din företagsmiljö kan använda du ett fullständigt domännamn i stället för IP-Adressen.
4. Typ av **användarnamn** med domänen och användarnamnet. Till exempel `Contoso\administrator`.
5. Typ av **lösenord** för användaren.
5. Skriv in lösenordet igen till **Bekräfta lösenord**.
6. Tillhandahålla en i förväg delad nyckel i den **krypteringsnyckeln** rutan. Säkerhetskopiorna krypteras med den här nyckeln. Se till att lagra den här nyckeln på en säker plats. När du anger den här nyckeln för första gången eller rotera nyckeln i framtiden ska visa du inte den här nyckeln från det här gränssnittet. Mer information att generera en i förväg delad nyckel, följ skript på [Aktivera säkerhetskopiering för Azure-stacken med PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Välj **OK** att spara inställningarna för säkerhetskopiering domänkontrollant.

Om du vill köra en säkerhetskopia, måste du hämtar verktyg för Azure-stacken och köra PowerShell-cmdlet **Start AzSBackup** på Azure-stacken administrationsnoden. Mer information finns i [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Nästa steg

 - Lär dig att köra en säkerhetskopiering. Se [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Lär dig att kontrollera att säkerhetskopieringen har körts. Se [bekräfta säkerhetskopieringen har slutförts i administrationsportalen](azure-stack-backup-back-up-azure-stack.md ).
