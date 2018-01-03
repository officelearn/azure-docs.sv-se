---
title: "Azure-stacken infrastruktur säkerhetskopiering tjänstereferens | Microsoft Docs"
description: "Den här artikeln innehåller referensmaterial för Azure-stacken infrastruktur Backup-tjänsten."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 4e6e0a52b2c55239e38757223f54e5e94dc98c42
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="infrastructure-backup-service-reference"></a>Infrastruktur för säkerhetskopiering tjänstereferens

## <a name="azure-backup-infrastructure"></a>Azure backup-infrastrukturen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure Stack består av flera tjänster som utgör portal, Azure Resource Manager och infrastrukturhantering upplevelse. Hanteringen av enhet som Azure-protokollstacken som fokuserar på minska komplexiteten exponeras för operatorn i lösningen.

Infrastruktur för säkerhetskopiering är utformat för att internalize komplexitet säkerhetskopiera och återställa data för infrastrukturtjänster säkerställt operatörer kan fokusera på hanterar lösningen och underhåller ett SLA för användare.

Exportera den säkerhetskopiera informationen till en extern resurs krävs för att undvika lagring av säkerhetskopior på samma system. Kräver en extern resurs ger administratören möjlighet att avgöra var du vill lagra data baserat på befintliga företagets BC/DR-principer. 

### <a name="infrastructure-backup-components"></a>Infrastrukturkomponenter för säkerhetskopiering

Säkerhetskopiering av infrastruktur innehåller följande komponenter:

 - **Infrastruktur för säkerhetskopiering domänkontrollant**  
 Infrastruktur för säkerhetskopiering Controller instansieras med och finns i varje Azure-molnet för stacken.
 - **Säkerhetskopiering Resource Provider**  
 Resursprovidern Backup (säkerhetskopiering RP) består av av användaren gränssnitt och programmet programmet gränssnitt (API) s exponera grundläggande säkerhetskopiering funktioner för Azure Stack-infrastruktur.

#### <a name="infrastructure-backup-controller"></a>Infrastruktur för säkerhetskopiering domänkontrollant

Infrastruktur för säkerhetskopiering domänkontrollanten är en Service Fabric service hämtar skapa en instans för ett moln för Azure-stacken. Säkerhetskopiering resurser har skapats på ett regionalt nivå och avbilda regionspecifika tjänstdata från AD, CA, Azure Resource Manager CRP, SRP, NRP, KeyVault, RBAC. 

### <a name="backup-resource-provider"></a>Säkerhetskopiering Resource Provider

Säkerhetskopiering Resursprovidern visar användargränssnittet i Azure Stack-portal för grundläggande konfiguration och lista över säkerhetskopiering resurser. Operatorn kan utföra följande åtgärder i användargränssnittet:

 - Aktivera säkerhetskopiering för första gången genom att tillhandahålla extern lagringsplats, autentiseringsuppgifter och krypteringsnyckeln
 - Visa slutförda skapade säkerhetskopiering och status resurser under skapandet
 - Ändra lagringsplats där säkerhetskopian Controller placerar säkerhetskopierade data
 - Ändra autentiseringsuppgifterna som domänkontrollant för säkerhetskopiering använder för åtkomst till extern lagringsplats
 - Ändra den krypteringsnyckel som använder Säkerhetskopiering domänkontrollanten att kryptera säkerhetskopiorna 


## <a name="backup-controller-requirements"></a>Säkerhetskopiera kraven för domänkontrollanter

Det här avsnittet beskrivs viktiga krav för infrastruktur-säkerhetskopiering. Vi rekommenderar att du har läst informationen noggrant innan du aktiverar säkerhetskopiering för din Azure Stack-instans och sedan refererar tillbaka till den vid behov under distributionen och efterföljande igen.

Kraven är:

  - **Programvarukrav** – beskriver stöds lagringsplatser och riktlinjer för storleksändring. 
  - **Krav på** – beskrivs nätverkskraven för olika lagringsplatser.  

### <a name="software-requirements"></a>Programvarukrav

#### <a name="supported-storage-locations"></a>Stöds lagringsplatser

| Lagringsplats                                                                 | Information                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-filresurs som finns på en lagringsenhet i miljön betrott nätverk | SMB-resurs i samma datacenter där Azure Stack distribueras eller i olika datacenter. Flera instanser av Azure-stacken kan använda samma filresurs. |
| SMB-filresurs på Azure                                                          | Stöds inte för närvarande.                                                                                                                                 |
| BLOB-lagring på Azure                                                            | Stöds inte för närvarande.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>SMB-versioner som stöds

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Storlek för plats för lagring 

Infrastruktur för säkerhetskopiering domänkontrollant kommer säkerhetskopiera data på begäran. Det rekommenderas att säkerhetskopiera minst två gånger en dag och behålla högst sju dagar säkerhetskopieringar. 

| Miljö skala | Planerade storleken på säkerhetskopiering | Utrymme som krävs |
|-------------------|--------------------------|--------------------------------|
| 4-12-noder        | 10 GB                     | 140 GB                          |

### <a name="network-requirements"></a>Nätverkskrav
| Lagringsplats                                                                 | Information                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-filresurs som finns på en lagringsenhet i miljön betrott nätverk | Port 445 krävs om Azure Stack-instansen finns i en brandväggsskyddad miljö. Infrastruktur för säkerhetskopiering Controller initierar en anslutning till SMB-filservern via port 445. |
| Om du vill använda filen serverns FQDN-namnet måste kunna lösas från detta program             |                                                                                                                                                                                         |

> [!Note]  
> Inga inkommande portar måste öppnas.


## <a name="infrastructure-backup-limits"></a>Infrastruktur för säkerhetskopiering gränser

Överväg att dessa gränser som du planera, distribuera och använda Microsoft Azure Stack-instanser. I följande tabell beskrivs dessa begränsningar.

### <a name="infrastructure-backup-limits"></a>Infrastruktur för säkerhetskopiering gränser
| Gräns för identifierare                                                 | Gräns        | Kommentarer                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ av säkerhetskopiering                                                      | Endast fullständig    | Infrastruktur för säkerhetskopiering styrenhet stöder bara fullständiga säkerhetskopieringar. Inkrementell säkerhetskopiering stöds inte.                                          |
| Schemalagda säkerhetskopieringar                                                | Manuell endast  | Säkerhetskopiering styrenhet stöder för närvarande endast säkerhetskopieringar på begäran                                                                                 |
| Maximal samtidiga säkerhetskopieringsjobb                                   | 1            | Endast en aktiv säkerhetskopiering stöds per instans av säkerhetskopiering domänkontrollant.                                                                  |
| Nätverksväxel                                     | Inte i omfånget | Administratören måste säkerhetskopiera nätverksväxel med hjälp av OEM-verktyg. Läs dokumentationen för Azure-Stack från varje OEM-leverantör för. |
| Maskinvara livscykel värden                                          | Inte i omfånget | Administratören måste säkerhetskopiera maskinvara livscykel värden med hjälp av OEM-verktyg. Läs dokumentationen för Azure-Stack från varje OEM-leverantör för.      |
| Maximalt antal filresurser                                    | 1            | Endast en filresurs som kan användas för att lagra säkerhetskopierade data                                                                                        |
| Säkerhetskopiera Apptjänster, funktion, SQL, mysql resursdata provider | Inte i omfånget | Se vägledning publicerade för att distribuera och hantera mervärde RPs som skapats av Microsoft.                                                  |
| Säkerhetskopiering från tredje part resursprovidrar                              | Inte i omfånget | Se vägledning publicerade för att distribuera och hantera mervärde RPs som skapats av andra leverantörer.                                          |

## <a name="next-steps"></a>Nästa steg

 - Mer information om tjänsten infrastruktur säkerhetskopiering finns [säkerhetskopia och återställa data för Azure-stacken med Backup-tjänsten infrastruktur](azure-stack-backup-infrastructure-backup.md).