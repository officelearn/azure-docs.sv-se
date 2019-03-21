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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 98f793b7d94cd554d426a0eec30d8bb4553d3d81
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105411"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Aktivera säkerhetskopiering för Azure Stack från administrationsportalen
Aktivera infrastruktur Backup-tjänsten via administrationsportalen så att Azure Stack kan generera infrastruktur säkerhetskopieringar. Maskinvara-partner kan använda dessa säkerhetskopior för att återställa din miljö med molnåterställning i händelse av [ett oåterkalleligt fel](./azure-stack-backup-recover-data.md). Syftet med molnet är att säkerställa att dina operatörer och användare kan logga in igen på portalen när återställningen är klar. Användarna har sina prenumerationer som återställts, inklusive behörigheter för rollbaserad åtkomst och roller, ursprungliga planer, erbjudanden, och tidigare definierad beräkning, lagring, nätverkskvoter och Key Vault-hemligheter.

Men infrastruktur Backup-tjänsten inte säkerhetskopiera virtuella IaaS-datorer, konfiguration och lagringsresurser, till exempel lagringskonton, blobbar, tabeller och så vidare, så att användare som loggar in när molnet är klar inte ser någon av sina tidigare befintliga resurser. Plattform som en tjänst (PaaS) resurser och data också säkerhetskopieras inte av tjänsten. 

Administratörer och användare ansvarar för att säkerhetskopiera och återställa IaaS och PaaS-resurser separat från säkerhetskopiering infrastrukturprocesser. Information om hur du säkerhetskopierar IaaS och PaaS-resurser finns i följande länkar:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
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

9. Ange ett certifikat i rutan certifikat .cer-filen i krypteringsinställningar. Säkerhetskopiorna krypteras med hjälp av den offentliga nyckeln i certifikatet. Du bör ange ett certifikat som endast innehåller den offentliga nyckeldelen när du konfigurerar inställningar för säkerhetskopiering. När du anger det här certifikatet för första gången eller rotera certifikatet i framtiden ska kan du bara visa tumavtrycket för certifikatet. Du kan inte ladda ned eller visa filen överförda certifikat. Kör följande PowerShell-kommando för att skapa ett självsignerat certifikat med offentliga och privata nycklar och exportera certifikat med endast den offentliga nyckeln delen för att skapa certifikatfilen.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 och senare**: Azure Stack accepterar ett certifikat för att kryptera säkerhetskopierade data för infrastruktur. Se till att lagra certifikatet med den offentliga och privata nyckeln på en säker plats. Av säkerhetsskäl rekommenderas inte att du använder certifikatet med de offentliga och privata nycklarna så här konfigurerar du inställningar för säkerhetskopiering. Mer information om hur du hanterar livscykeln för det här certifikatet finns i [infrastruktur Backup-tjänsten metodtips](azure-stack-backup-best-practices.md).
   > 
   > **1811 eller tidigare**: Azure Stack accepterar en symmetrisk nyckel för att kryptera säkerhetskopierade data för infrastruktur. Använd den [New-AzsEncryptionKey64 cmdlet för att skapa en nyckel](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). När du har uppgraderat från 1811 till 1901 behåller säkerhetskopieringsinställningar krypteringsnyckeln. Rekommendationen är att uppdatera inställningar för att använda ett certifikat. Viktiga stöd för kryptering är nu föråldrad. Du har minst 3 versioner att uppdatera inställningarna för att använda ett certifikat. 

10. Välj **OK** att spara dina inställningar för säkerhetskopiering controller.

![Azure Stack - inställningarna för säkerhetskopiering-domänkontrollanter](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Starta Säkerhetskopiering
Om du vill starta en säkerhetskopiering klickar du på **Säkerhetskopiera nu** att starta en säkerhetskopiering på begäran. En säkerhetskopiering på begäran kan inte ändra tiden för nästa schemalagda säkerhetskopiering. När uppgiften har slutförts kan du bekräfta inställningarna i **Essentials**:

![Azure Stack - säkerhetskopiering på begäran](media/azure-stack-backup/scheduled-backup.png)

Du kan också köra PowerShell-cmdleten **Start AzsBackup** på din dator för administration av Azure Stack. Mer information finns i [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Aktivera eller inaktivera automatisk säkerhetskopiering
Säkerhetskopieringarna schemaläggs automatiskt när du aktiverar säkerhetskopiering. Du kan kontrollera nästa schemalagda säkerhetskopiering tid i **Essentials**. 

![Azure Stack - säkerhetskopiering på begäran](media/azure-stack-backup/on-demand-backup.png)

Om du vill inaktivera framtida schemalagda säkerhetskopieringar klickar du på **inaktivera automatiska säkerhetskopieringar**. Inaktivera automatisk säkerhetskopiering behålls konfigurerade inställningar för säkerhetskopiering och behåller schemat för säkerhetskopiering. Den här åtgärden anger bara scheduler för att hoppa över framtida säkerhetskopieringar. 

![Azure Stack – inaktiverar schemalagda säkerhetskopieringar](media/azure-stack-backup/disable-auto-backup.png)

Bekräfta att framtida schemalagda säkerhetskopieringar har inaktiverats i **Essentials**:

![Azure Stack - bekräfta säkerhetskopior har inaktiverats](media/azure-stack-backup/confirm-disable.png)

Klicka på **aktivera automatiska säkerhetskopieringar** att informera scheduler för att starta framtida säkerhetskopieringar på den schemalagda tiden. 

![Azure Stack – aktivera schemalagda säkerhetskopieringar](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Om du har konfigurerat infrastrukturen backup innan du uppdaterar till 1807 kommer automatiska säkerhetskopieringar att inaktiveras. På så sätt de säkerhetskopior som startas av Azure Stack inte står i konflikt med säkerhetskopior som startats av en extern aktivitet schemaläggning av motorn. När du inaktiverar eventuella externa Schemaläggaren, klickar du på **aktivera automatiska säkerhetskopieringar**.

## <a name="update-backup-settings"></a>Uppdatera inställningar för säkerhetskopiering
Från och med 1901, stöd för krypteringsnyckeln är inaktuell. Om du konfigurerar säkerhetskopiering för första gången på 1901, måste du använda ett certifikat. Azure Stack stöder krypteringsnyckeln endast om nyckeln är konfigurerad innan du uppdaterar till 1901. Läge för bakåtkompatibilitet kommer att fortsätta i tre versioner. Efter det kommer du inte längre stöd krypteringsnycklar. 

### <a name="default-mode"></a>Standardläge
I krypteringsinställningarna för, om du konfigurerar säkerhetskopiering av infrastruktur för första gången efter installation eller uppdatering till 1901, måste du konfigurera säkerhetskopiering med ett certifikat. Det stöds inte längre att använda en krypteringsnyckel. 

Om du vill uppdatera det certifikat som används för att kryptera säkerhetskopierade data, kan du överföra en ny. CER filen med den offentliga nyckeln delen och välj OK om du vill spara inställningarna. 

Nya säkerhetskopior börjar använda den offentliga nyckeln i det nya certifikatet. Det finns ingen inverkan på alla befintliga säkerhetskopior som har skapats med det tidigare certifikatet. Se till att lagra äldre certifikat runt på en säker plats om du behöver den för i molnet.

![Azure Stack – visa certifikatets tumavtryck](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Bakåtkompatibilitet kompatibilitetsläge
Om du har konfigurerat säkerhetskopiering innan du uppdaterar till 1901 överförs inställningarna med densamma. I det här fallet krypteringsnyckeln stöds för bakåtkompatibilitet kompatibilitet. Du har alternativet Uppdatera krypteringsnyckeln eller växlar för att använda ett certifikat. Du har minst tre versioner att fortsätta uppdatera krypteringsnyckeln. Använd den här gången med övergången till ett certifikat. Skapa en ny nyckel kryptering med den [cmdlet New-AzsEncryptionKeyBase64](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Azure Stack – med hjälp av krypteringsnyckeln i läget för bakåtkompatibilitet](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> Uppdaterar från krypteringsnyckeln till certifikat är en enkel åtgärd. När du har gjort den här ändringen kan du gå tillbaka till krypteringsnyckeln. Alla befintliga säkerhetskopior förblir krypterade med den tidigare krypteringsnyckeln. 

![Azure Stack - krypteringscertifikat för användning i läget för bakåtkompatibilitet](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Nästa steg

Lär dig att köra en säkerhetskopiering. Se [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Lär dig att verifiera att säkerhetskopieringen har körts. Se [bekräfta säkerhetskopieringen slutfördes i administrationsportalen](azure-stack-backup-back-up-azure-stack.md)
