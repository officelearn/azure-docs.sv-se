---
title: "Infrastruktur-säkerhetskopieringstjänsten bästa praxis för Azure-stacken | Microsoft Docs"
description: "Du kan följa uppsättning av bästa praxis när du distribuerar och hanterar Azure-stacken i ditt datacenter för att minimera dataförlust om ett oåterkalleligt fel."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2017
ms.author: mabrigg
ms.openlocfilehash: b9438f3bab92f40a5c79ce7b7a572195c182be45
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="infrastructure-backup-service-best-practices"></a>Metodtips för infrastruktur Backup-tjänsten

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan följa bästa praxis när du distribuerar och hanterar Azure-stacken i ditt datacenter för att minimera dataförlust om ett oåterkalleligt fel.

Bästa praxis bör du granska med jämna mellanrum att verifiera att installationen är fortfarande i kompatibilitet när ändringar görs till flödet för åtgärden. Ska det uppstår problem när du implementerar dessa bästa praxis, kontakta Microsofts Support för hjälp.

## <a name="configuration-best-practices"></a>Metodtips för konfiguration

### <a name="deployment"></a>Distribution

Aktivera infrastruktur säkerhetskopiering efter distributionen av varje moln för Azure-stacken. Med hjälp av AzureStack-verktyg som du kan också schemalägga säkerhetskopieringar från en klient/server med åtkomst till operatorn management API-slutpunkt.

### <a name="networking"></a>Nätverk

Universal Naming Convention (UNC)-sträng för sökväg måste använda ett fullständigt kvalificerat domännamn (FQDN). IP-adress är möjligt om namnmatchning inte är möjligt. En UNC-sträng Anger platsen för resurser, till exempel delade filer eller enheter.

### <a name="encryption"></a>Kryptering

Krypteringsnyckeln används för att kryptera säkerhetskopian som exporteras till extern lagring. Nyckeln kan genereras med hjälp av AzureStack-verktyg. 

![AzureStack-verktyg](media\azure-stack-backup\azure-stack-backup-encryption1.png)

Nyckeln måste vara lagrad i en säker plats (till exempel offentliga Azure Key Vault hemliga). Den här nyckeln måste användas under omdistributionen av Azure-stacken. 

![Lagrade nyckeln för en säker plats.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Metodtips för fortlöpande

### <a name="backups"></a>Säkerhetskopior

 - Infrastruktur för måste säkerhetskopiering aktiveras på begäran. Rekommendation är att säkerhetskopiera minst två gånger per dag.
 - Säkerhetskopieringsjobb köra när systemet körs så att det finns inget avbrott uppstår på hanteringsupplevelser eller program. Förvänta dig säkerhetskopieringsjobb ska börja 20 – 40 minuter efter en lösning som är under rimliga belastning.
 - Med OEM tillhandahålls instruktion bör manuellt säkerhetskopiering nätverksväxlar och maskinvara livscykel värden (HLH) lagras på samma säkerhetskopiering resurs där infrastruktur säkerhetskopiering Controller lagrar kontrollen plan säkerhetskopierade data. Överväg att lagra växeln och HLH konfigurationer i mappen region. Överväg att använda en identifierare för varje konfiguration som hör till en skalningsenhet om du har flera Azure Stack-instanser i samma region.

### <a name="folder-names"></a>Mappnamn

 - Infrastruktur skapar automatiskt MASBACKUP mapp. Det här är en Microsoft-hanterad resurs. Du kan skapa resurser på samma nivå som MASBACKUP. Det rekommenderas inte att skapa mappar eller storage-data i MASBACKUP Azure Stack inte skapar. 
 -  Användaren FQDN och region i mappnamnet för att skilja säkerhetskopierade data från olika moln. Fullständigt kvalificerade domännamn (FQDN) Azure Stack-distribution och slutpunkter är en kombination av Region parametrarna och externa domännamn. Mer information finns i [Azure Stack-integrering för datacenter - DNS-](azure-stack-integrate-dns.md).

Säkerhetskopiering resursen är till exempel AzSBackups finns på fileserver01.contoso.com. I den filresursen kan det finnas en mapp per Azure Stack-distribution med namnet på externa domänen och en undermapp med namnet region. 

FQDN: contoso.com  
Region: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup mappen är där Azure Stack lagrar säkerhetskopierade data. Du bör inte använda den här mappen för att lagra dina egna data. OEM bör inte använda den här mappen för att lagra säkerhetskopierade data antingen. 

Bör du lagra säkerhetskopierade data för komponenterna under mappen region. Varje nätverksväxlar, maskinvara livscykel värden (HLH) och så vidare kan lagras i sin egen undermapp. Exempel:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Övervakning

Följande aviseringar som stöds av systemet:

| Varning                                                   | Beskrivning                                                                                     | Åtgärd                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Säkerhetskopiering misslyckades eftersom filresursen är utanför kapacitet | Filresursen är utanför kapacitet och säkerhetskopiering domänkontrollant kan inte exportera säkerhetskopieringsfiler till platsen. | Lägg till mer lagringskapacitet och försök sedan säkerhetskopiera igen. Ta bort befintliga säkerhetskopior (med början från äldsta först) för att frigöra utrymme.                    |
| Säkerhetskopieringen misslyckades på grund av problem med nätverksanslutningen.             | Nätverket mellan Azure-stacken och filen resursen upplever problem.                          | Åtgärda nätverksproblemet och försök säkerhetskopieringen igen.                                                                                            |
| Säkerhetskopieringen misslyckades på grund av ett fel i sökvägen                | Sökvägen till filresursen kan inte lösas                                                          | Mappa resursen från en annan dator för att se till att resursen är tillgänglig. Du kan behöva uppdatera sökvägen om det är inte längre giltig.       |
| Säkerhetskopieringen misslyckades på grund av autentiseringsproblem               | Det kan finnas ett problem med autentiseringsuppgifterna eller ett nätverksproblem som påverkar autentisering.    | Mappa resursen från en annan dator för att se till att resursen är tillgänglig. Du kan behöva uppdatera autentiseringsuppgifterna, om de inte längre giltig. |
| Säkerhetskopieringen misslyckades på grund av ett allmänt fel                    | Den misslyckade begäranden kan bero på ett tillfälligt fel. Försök säkerhetskopiera igen.                    | Ring supporten                                                                                                                               |

## <a name="next-steps"></a>Nästa steg

 - Granska referensmaterial för den [infrastruktur Backup-tjänsten](azure-stack-backup-reference.md).  
 - Aktivera den [infrastruktur säkerhetskopieringstjänsten](azure-stack-backup-enable-backup-console.md).