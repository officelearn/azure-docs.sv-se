---
title: Infrastruktur för Backup-tjänsten Metodtips för Azure Stack | Microsoft Docs
description: Du kan följa uppsättning rekommenderade metoder när du distribuerar och hanterar Azure Stack i datacentret för att minimera dataförlust om det finns ett oåterkalleligt fel.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 0b6153a863b0637f314b4f333b255ec8d24b5b0c
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136442"
---
# <a name="infrastructure-backup-service-best-practices"></a>Metodtips för infrastruktur Backup-tjänsten

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan följa bästa praxis när du distribuerar och hanterar Azure Stack i datacentret för att minska data går förlorade i händelse av ett oåterkalleligt fel.

Bästa praxis bör du granska med jämna mellanrum att verifiera att installationen är fortfarande i efterlevnad när ändringar görs i flödet igen. Bör du får problem när du implementerar dessa metodtips, kontakta Microsoft Support om du behöver hjälp.

## <a name="configuration-best-practices"></a>Metodtips för konfiguration

### <a name="deployment"></a>Distribution

Aktivera säkerhetskopiering av infrastruktur efter distributionen av varje Azure Stack-molnet. Du kan schemalägga säkerhetskopieringar från en klient/server med åtkomst till operatorn management API-slutpunkt med hjälp av Azure Stack PowerShell.

### <a name="networking"></a>Nätverk

Universal Naming Convention (UNC)-sträng för sökvägen måste använda ett fullständigt kvalificerat domännamn (FQDN). IP-adressen är möjligt om namnmatchning inte är möjligt. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter.

### <a name="encryption"></a>Kryptering

Krypteringsnyckeln används för att kryptera säkerhetskopierade data som exporteras till extern lagring. Nyckeln genereras som en del av [aktiverar säkerhetskopiering för Azure Stack med PowerShell](azure-stack-backup-enable-backup-powershell.md).

Nyckeln måste lagras på en säker plats (till exempel offentliga Azure Key Vault-hemlighet). Den här nyckeln måste användas under omdistribution av Azure Stack. 

![Lagrade nyckeln en säker plats.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Metodtips för fortlöpande

### <a name="backups"></a>Säkerhetskopior

 - Infrastruktur för måste säkerhetskopiering aktiveras på begäran. Rekommendationen är att säkerhetskopiera minst två gånger per dag.
 - Säkerhetskopieringsjobb körs medan systemet körs så att det finns inga driftstopp för hanteringsupplevelser eller användarprogram. Förvänta dig säkerhetskopieringsjobb ska börja 20 – 40 minuter efter en lösning som är rimligt belastning.
 - Med OEM tillhandahålls instruktion bör manuellt säkerhetskopiering nätverksväxlar och maskinvara livscykel värden (HLH) lagras på den samma säkerhetskopieringsresursen där-dataplaner kontrollen infrastruktur för säkerhetskopiering Controller lagrar säkerhetskopierade data. Överväg att lagra växel och HLH konfigurationer i mappen region. Om du har flera Azure Stack-instanser i samma region kan du använda en identifierare för varje konfiguration som tillhör en skalningsenhet.

### <a name="folder-names"></a>Mappnamn

 - Infrastruktur skapas MASBACKUP automatiskt. Det här är en Microsoft-hanterad resurs. Du kan skapa filresurser på samma nivå som MASBACKUP. Det rekommenderas inte skapa mappar eller lagringsdata inuti MASBACKUP som inte har skapat i Azure Stack. 
 -  Användaren FQDN och region i mappnamnet skilja säkerhetskopierade data från olika moln. Det fullständigt kvalificerade domännamnet (FQDN) Azure Stack-distributioner och slutpunkter är en kombination av Region parametrarna och externa domännamn. Mer information finns i [Azure Stack datacenter-integrering - DNS-](azure-stack-integrate-dns.md).

Till exempel är säkerhetskopieringsresursen AzSBackups fileserver01.contoso.com som värdplattform. I den filresursen kan det finnas en mapp per Azure Stack-distribution med externa domännamnet och en undermapp som använder samma region. 

FQDN: contoso.com  
Region: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup mappen är där Azure Stack lagrar säkerhetskopierade data. Du bör inte använda den här mappen för att lagra dina egna data. OEM bör inte använda den här mappen för att lagra alla säkerhetskopierade data antingen. 

Bör du också lagra säkerhetskopierade data för komponenter under mappen region. Varje nätverksväxlar, maskinvara livscykel värd (HLH) och så vidare kan lagras i en egen undermapp. Exempel:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Övervakning

Följande aviseringar som stöds av systemet:

| Varning                                                   | Beskrivning                                                                                     | Åtgärd                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Säkerhetskopieringen misslyckades eftersom filresursen är kapacitet | Filresursen är kapacitet och säkerhetskopiering domänkontrollant kan inte exportera säkerhetskopieringsfiler till platsen. | Lägg till mer lagringskapacitet och försök säkerhetskopiera igen. Ta bort befintliga säkerhetskopior (med början från äldsta först) för att frigöra utrymme.                    |
| Säkerhetskopieringen misslyckades på grund av problem med nätverksanslutningen.             | Nätverket mellan Azure Stack och filen resursen innehåller fel.                          | Åtgärda problem med nätverket och försök säkerhetskopieringen igen.                                                                                            |
| Säkerhetskopieringen misslyckades på grund av ett fel i sökvägen                | Sökvägen till filresursen kan inte lösas                                                          | Mappa resursen från en annan dator för att se till att resursen är tillgänglig. Du kan behöva uppdatera sökvägen om den inte längre giltig.       |
| Säkerhetskopieringen misslyckades på grund av problem med autentisering               | Det kan finnas ett problem med autentiseringsuppgifterna eller ett problem som påverkar autentisering.    | Mappa resursen från en annan dator för att se till att resursen är tillgänglig. Du kan behöva uppdatera autentiseringsuppgifter om de inte längre giltig. |
| Säkerhetskopieringen misslyckades på grund av ett allmänt fel                    | Misslyckade begäranden kan bero på ett tillfälligt problem. Försök säkerhetskopiera igen.                    | Kontakta supporten                                                                                                                               |

## <a name="next-steps"></a>Nästa steg

 - Granska referensmaterial för den [infrastruktur Backup-tjänsten](azure-stack-backup-reference.md).  
 - Aktivera den [infrastruktur säkerhetskopieringstjänsten](azure-stack-backup-enable-backup-console.md).
