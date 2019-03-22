---
title: Säkerhetskopiera App Service i Azure Stack | Microsoft Docs
description: Detaljerad vägledning för Azure Stack App Service-säkerhetskopiering.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340554"
---
# <a name="back-up-app-service-on-azure-stack"></a>Säkerhetskopiera App Service på Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*  

Det här dokumentet innehåller anvisningar om hur du säkerhetskopierar App Service på Azure Stack.

> [!IMPORTANT]
> App Service i Azure Stack säkerhetskopieras inte som en del av [säkerhetskopiering i Azure Stack-infrastruktur](azure-stack-backup-infrastructure-backup.md). Som operatör Azure Stack måste du vidta åtgärder för att se till att App Service har återställas om det behövs.

Azure App Service i Azure Stack består av fyra huvudsakliga komponenter att tänka på när du planerar för haveriberedskap:
1. Resource provider-infrastruktur. serverroller, arbetarnivåer osv. 
2. App Service-hemligheter
3. Som är värd för App Service SQL Server- och mätning databaser
4. App Service användaren arbetsbelastningen innehåll lagras i App Service-filresurs   

## <a name="back-up-app-service-secrets"></a>Säkerhetskopiera App Service-hemligheter
När du återställer App Service från en säkerhetskopia, måste du ange App Service-nycklar som används av den första distributionen. Den här informationen ska sparas när App Service har distribueras och lagras i en säker plats. Resurskonfigurationen providern infrastrukturen kommer att återskapas från en säkerhetskopia under återställning med hjälp av App Service-recovery PowerShell-cmdletar.

Använd administrationsportalen för säkerhetskopiering av app service-hemligheter genom att följa dessa steg: 

1. Logga in på Azure Stack-administrationsportalen som tjänstadministratör.

2. Bläddra till **Apptjänst** -> **hemligheter**. 

3. Välj **hämta hemligheter**.

   ![Hämta hemligheter](./media/app-service-back-up/download-secrets.png)

4. När hemligheter är redo för att ladda ned, klickar du på **spara** och lagra hemligheter för App Service (**SystemSecrets.JSON**) filen på en säker plats. 

   ![Spara hemligheter](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Upprepa dessa steg varje gång App Service-hemligheter roteras.

## <a name="back-up-the-app-service-databases"></a>Säkerhetskopiera databaserna App Service
Om du vill återställa App Service, måste den **Appservice_hosting** och **Appservice_metering** säkerhetskopior. Vi rekommenderar att du använder underhållsplaner för SQL Server eller Azure Backup Server så att dessa databaser säkerhetskopieras och sparas på ett säkert sätt med jämna mellanrum. Dock valfri metod för att säkerställa en vanlig SQL-säkerhetskopior har skapats kan användas.

Du kan använda följande PowerShell-kommandon för att säkerhetskopiera databaserna medan du är inloggad i SQL Server manuellt:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Om du vill säkerhetskopiera SQL AlwaysOn-databaser kan följa [instruktionerna](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

När alla databaser har säkerhetskopierats, kopierar du bak-filer på en säker plats tillsammans med information för App Service-hemligheter.

## <a name="back-up-the-app-service-file-share"></a>Säkerhetskopiera filresursen App Service
App Service butiker app klientinformation i filresursen. Detta måste säkerhetskopieras regelbundet tillsammans med App Service-databaser så att så lite som möjligt av informationen går förlorad om en återställning krävs. 

Om du vill säkerhetskopiera till App Service-filresursen innehåll, du kan använda Azure Backup Server eller någon annan metod för att regelbundet kopiera filresursen innehåll till platsen du har sparat alla tidigare återställningsinformation. 

Du kan till exempel använda de här stegen för att använda robocopy från en konsolsession för Windows PowerShell (inte PowerShell ISE):

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Nästa steg
[Återställ App Service i Azure Stack](app-service-recover.md)