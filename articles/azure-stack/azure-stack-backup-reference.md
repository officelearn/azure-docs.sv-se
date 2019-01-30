---
title: Referens för Azure Stack-infrastruktur Backup-tjänsten | Microsoft Docs
description: Den här artikeln innehåller information om Azure Stack-infrastruktur Backup-tjänsten.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 80cca629ea49d8843580c23f74d1d2076aa623e3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246719"
---
# <a name="infrastructure-backup-service-reference"></a>Referens för Backup-tjänsten för infrastruktur

## <a name="azure-backup-infrastructure"></a>Azure backup-infrastrukturen

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack består av många tjänster som utgör portal, Azure Resource Manager och drabbas av infrastrukturhantering. Av installation-liknande hanteringsupplevelse av Azure Stack fokuserar på att minska komplexiteten som är exponerade för operatorn i lösningen.

Infrastruktur för säkerhetskopiering har utformats för att internalize komplexiteten med att säkerhetskopiera och återställa data för infrastrukturtjänster, säkerställer operatörer kan fokusera på hantering lösningen och underhåll av ett serviceavtal för användare.

Exportera säkerhetskopierade data till en extern resurs krävs för att undvika lagring av säkerhetskopior på samma system. Kräver en extern resurs ger administratören möjlighet att avgöra var du vill lagra dina data baserat på befintliga företagsprinciper BC/DR. 

### <a name="infrastructure-backup-components"></a>Infrastrukturkomponenter för säkerhetskopiering

Säkerhetskopiering av infrastruktur innehåller följande komponenter:

 - **Infrastruktur för säkerhetskopiering Controller**  
 Infrastruktur för säkerhetskopiering Controller instantieras med och ligger i varje Azure Stack-moln.
 - **Backup-Resursprovidern**  
 Resursprovidern Backup (säkerhetskopiering RP) består av den användaren gränssnittet och programmet programmet gränssnitt (API) s exponera grundläggande säkerhetskopiering funktioner för Azure Stack-infrastruktur.

#### <a name="infrastructure-backup-controller"></a>Infrastruktur för säkerhetskopiering Controller

Infrastruktur för säkerhetskopiering styrenheten är ett Service Fabric service hämtar instans för ett Azure Stack-molnet. Säkerhetskopiering resurser skapas vid en regionala nivå och avbilda regionspecifika service från AD, CA, Azure Resource Manager, CRP, SRP, NRP, KeyVault, RBAC. 

### <a name="backup-resource-provider"></a>Backup-Resursprovidern

Säkerhetskopiering Resursprovidern anger användargränssnittet i Azure Stack-portalen för grundläggande konfiguration och listning av säkerhetskopiering resurser. Operatör kan utföra följande åtgärder i användargränssnittet:

 - Aktivera säkerhetskopiering för första gången genom att tillhandahålla extern lagringsplats, autentiseringsuppgifter och krypteringsnyckeln
 - Visa slutförda skapade säkerhetskopiering resurser och status resurser skapas
 - Ändra lagringsplats där säkerhetskopiering Controller placerar säkerhetskopierade data
 - Ändra autentiseringsuppgifterna som säkerhetskopiering Controller använder för att få åtkomst till extern lagringsplats
 - Ändra krypteringsnyckeln som domänkontrollant för säkerhetskopiering använder för att kryptera säkerhetskopiorna 


## <a name="backup-controller-requirements"></a>Säkerhetskopiera kraven för domänkontrollanter

Det här avsnittet beskrivs viktiga krav för infrastruktur för säkerhetskopiering. Vi rekommenderar att du läser informationen noggrant innan du aktiverar säkerhetskopiering för Azure Stack-instans och sedan refererar tillbaka till det som behövs under distributionen och efterföljande driften.

Kraven är:

  - **Programvarukrav** – beskriver stöds lagringsplatser och storlek vägledning. 
  - **Krav på** – beskrivs nätverkskraven för olika lagringsplatser.  

### <a name="software-requirements"></a>Programvarukrav

#### <a name="supported-storage-locations"></a>Stöds lagringsplatser

| Lagringsplats                                                                 | Information                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-filresurs som finns på en enhet i miljön betrott nätverk | SMB-resurs i samma datacenter där Azure Stack har distribuerats eller i ett annat datacenter. Flera instanser av Azure Stack kan använda samma filresurs. |
| SMB-filresurs på Azure                                                          | Stöds för närvarande inte.                                                                                                                                 |
| BLOB-lagring på Azure                                                            | Stöds för närvarande inte.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>SMB-versioner som stöds

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Storage plats storlek 

Infrastruktur för säkerhetskopiering Controller säkerhetskopieras data på begäran. Rekommendationen är att säkerhetskopiera minst två gånger en dag och hålla högst sju dagar säkerhetskopieringar. 

**1811 och senare**
| Miljö-skala | Planerade storleken på säkerhetskopia | Totala mängden utrymme som krävs |
|-------------------|--------------------------|--------------------------------|
| 4 – 16 noder        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**Pre-1811**
| Miljö-skala | Planerade storleken på säkerhetskopia | Totala mängden utrymme som krävs |
|-------------------|--------------------------|--------------------------------|
| 4 – 16 noder, ASDK  | 10 GB                     | 140 GB                        |

### <a name="network-requirements"></a>Nätverkskrav
| Lagringsplats                                                                 | Information                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SMB-filresurs som finns på en enhet i miljön betrott nätverk | Port 445 krävs om Azure Stack-instans finns i en brandväggsskyddad miljö. Infrastruktur för säkerhetskopiering Controller initierar en anslutning till SMB-filservern via port 445. |
| För att använda FQDN för servern, måste namnet matchas från detta program             |                                                                                                                                                                                         |

> [!Note]  
> Inga ingående portar måste du öppna.


## <a name="infrastructure-backup-limits"></a>Begränsningar för säkerhetskopiering av infrastruktur

Överväg att dessa begränsningar när du planerar, distribuerar och driva dina Microsoft Azure Stack-instanser. I följande tabell beskrivs dessa gränser.

### <a name="infrastructure-backup-limits"></a>Begränsningar för säkerhetskopiering av infrastruktur
| Gräns för identifierare                                                 | Gräns        | Kommentarer                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ av säkerhetskopiering                                                      | Endast fullständig    | Infrastruktur för säkerhetskopiering styrenhet stöder endast fullständiga säkerhetskopieringar. Inkrementell säkerhetskopiering stöds inte.                                          |
| Schemalagda säkerhetskopieringar                                                | Schemalagda och manuellt  | Backup-styrenhet stöder schemalagda säkerhetskopieringar och på begäran säkerhetskopieringar                                                                                 |
| Maximalt antal samtidiga säkerhetskopiering jobb                                   | 1            | Endast en aktiv säkerhetskopiering stöds per instans av säkerhetskopiering Controller.                                                                  |
| Konfiguration av nätverksväxel                                     | Inte i omfånget | Administratören måste säkerhetskopiera konfiguration av nätverksväxel med hjälp av OEM-verktyg. I dokumentationen för Azure Stack som tillhandahålls av varje OEM-leverantör. |
| Maskinvara livscykel värd                                          | Inte i omfånget | Administratören måste säkerhetskopiera maskinvara livscykel värden med hjälp av OEM-verktyg. I dokumentationen för Azure Stack som tillhandahålls av varje OEM-leverantör.      |
| Maximalt antal filresurser                                    | 1            | Endast en filresurs som kan användas för att lagra säkerhetskopierade data                                                                                        |
| Säkerhetskopiera App Services, funktion, SQL, mysql-providern resursdata | Inte i omfånget | Se vägledningen publicerad för att distribuera och hantera mervärde RPs som skapats av Microsoft.                                                  |
| Säkerhetskopiering från tredje part resursprovidrar                              | Inte i omfånget | Se vägledningen publicerad för att distribuera och hantera mervärde RPs som skapats av andra leverantörer.                                          |

## <a name="next-steps"></a>Nästa steg

 - Läs mer om Backup-tjänsten infrastruktur i [säkerhetskopiering och dataåterställning för Azure Stack med Backup-tjänsten infrastruktur](azure-stack-backup-infrastructure-backup.md).
