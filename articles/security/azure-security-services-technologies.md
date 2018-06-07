---
title: Säkerheten i Azure-tjänster och teknik | Microsoft Docs
description: Artikeln innehåller en granskad lista över Azure-säkerhet tjänster och teknik.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2018
ms.author: barclayn
ms.openlocfilehash: e52cee2cb642de6e54270c597e6ed99f7162d0ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641466"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Säkerhetstjänster och tekniker som är tillgängliga på Azure

I vår diskussioner med aktuella och framtida Azure-kunder tillfrågas vi ofta ”har du en lista över alla säkerhetsrelaterade tjänster och teknik som Azure har att erbjuda”?

När du utvärderar molnalternativ service provider är det bra att ha denna information. Därför har vi angett listan för att komma igång.

Över tiden, den här listan ändrar och växer, precis som Azure. Se till att markera den här sidan med jämna mellanrum att hålla dig uppdaterad på vår säkerhetsrelaterade tjänster och teknik.

## <a name="general-azure-security"></a>Allmänna Azure-säkerhet
|Tjänst|Beskrivning|
|--------|--------|
|[Azure&nbsp;säkerhet&nbsp;Center](../security-center/security-center-intro.md)| En arbetsbelastning skydd molnlösning som möjliggör säkerhetshantering och avancerade skydd via hybrid cloud arbetsbelastningar.|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| En säker hemligheter store för lösenord, anslutningssträngar och annan information som du behöver för att hålla dina appar fungerar. |
|[Log Analytics](../log-analytics/log-analytics-overview.md)|En övervakningstjänsten som samlar in telemetri och andra data och ger en språk och analytics frågemotor för att leverera åtgärdsinformation för dina appar och resurser. Kan användas fristående eller med andra tjänster, till exempel Security Center. |
|[Utveckling och testning i Azure-labb](../devtest-lab/devtest-lab-overview.md)|En tjänst som hjälper utvecklare och testare att snabbt skapa miljöer i Azure under minimera skräp och kontrollera kostnaden.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Lagringssäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;lagring&nbsp;Service&nbsp;kryptering](../storage/common/storage-service-encryption.md)|En säkerhetsfunktion som automatiskt krypterar dina data i Azure-lagring.   |
|[StorSimple krypterade Hybrid lagring](../storsimple/storsimple-ova-overview.md)| En integrerad lagringslösning som hanterar lagringsuppgifter mellan lokala enheter och lagring i Azure-molnet.|
|[Azure klientsidan kryptering](../storage/common/storage-client-side-encryption.md)| En lösning för kryptering på klientsidan som krypterar data i klientprogram före överföringen till Azure Storage; dekrypterar också data när laddades ned. |
| [Azure Storage signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto.  |
|[Azure Lagringskontonycklar](../storage/common/storage-create-storage-account.md)| En kontroll åtkomstmetod för Azure storage som används för autentisering när lagringskontot används. |
|[Azure-filresurser med SMB 3.0-kryptering](../storage/files/storage-files-introduction.md)|En säkerhetsteknik för nätverket som aktiverar automatisk nätverkskryptering för Server Message Block (SMB)-protokoll för fildelning. |
|[Azure Storage Analytics](https://docs.microsoft.com/en-us/rest/api/storageservices/Storage-Analytics)| Loggning och mått av teknik för data i ditt lagringskonto. |

<!------>

## <a name="database-security"></a>Databassäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;SQL&nbsp;brandväggen](../sql-database/sql-database-firewall-configure.md)|Ett nätverk funktionen för åtkomstkontroll som skyddar mot nätverksbaserade attacker i databasen. |
|[Azure&nbsp;SQL&nbsp;Cell&nbsp;nivå kryptering](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| En säkerhetsteknik för databasen som erbjuder kryptering på en detaljerad nivå.  |
| [Azure&nbsp;SQL&nbsp;krypterad anslutning](../sql-database/sql-database-control-access.md)|För att erbjuda säkerhet, kontrollerar SQL Database åtkomsten med hjälp av brandväggsregler som begränsar anslutning efter IP-adress, autentiseringsmekanismer kräver att användare bevisar sin identitet och auktoriseringsmekanismer begränsar användare till specifika åtgärder och data. |
| [Azure SQL alltid kryptering](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Skyddar känslig data, till exempel kreditkortsnummer eller nationella ID-nummer (till exempel USA personnummer), lagras i Azure SQL Database eller SQL Server-databaser.  |
| [Azure&nbsp;SQL&nbsp;Transparent datakryptering](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| En databas säkerhetsfunktion som krypterar lagring av en hel databas. |
| [Azure SQL Database Auditing](../sql-database/sql-database-auditing.md)|En databas granskning funktion som spårar databashändelser och skriver dem till en granskningslogg logga i Azure storage-konto.  |


## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;rollen&nbsp;baserat&nbsp;åtkomstkontroll](../active-directory/role-based-access-control-configure.md)|En funktionen för åtkomstkontroll som utformats för att ge användare åtkomst till de resurser som de som krävs för att få åtkomst till baserat på deras roller i organisationen.  |
| [Azure Active Directory](../active-directory/active-directory-whatis.md)|En molnbaserad autentisering-databas som stöder en flera innehavare, molnbaserad katalog och flera identity management-tjänster i Azure.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|En identity management-tjänsten som möjliggör kontroll över hur kunder registrering, inloggning, och hantera sina profiler när du använder Azure-baserade program.   |
| [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)| En molnbaserad och hanterade version av Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| Etablera en säkerhet som använder flera olika former av autentisering och verifiering innan åtkomst till skyddad information. |

## <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och katastrofåterställning
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;säkerhetskopiering](../backup/backup-introduction-to-azure-backup.md)| En Azure-baserad tjänst som används för att säkerhetskopiera och återställa data i Azure-molnet. |
| [Azure&nbsp;plats&nbsp;återställning](../site-recovery/site-recovery-overview.md)|En onlinetjänst som replikerar arbetsbelastningar som körs på fysiska och virtuella datorer (VM) från en primär plats till en sekundär plats för att aktivera återställning av tjänster efter ett fel. |

## <a name="networking"></a>Nätverk
|Tjänst|Beskrivning|
|------|--------|
| [Nätverket&nbsp;säkerhet&nbsp;grupper](../virtual-network/virtual-networks-nsg.md)| En nätverksbaserad åtkomstfunktion använda 5-tuppel för att tillåta eller neka beslut.  |
| [Azure VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| En nätverksenhet som används som en VPN-slutpunkten för att tillåta anslutningar mellan lokala åtkomst till virtuella Azure-nätverk.  |
| [Azure Programgateway](../application-gateway/application-gateway-introduction.md)|Ett avancerat webbprogram belastningsutjämnare som kan vidarebefordra baserat på URL: en och utföra SSL-avlastning. |
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|En TCP/UDP-programmet Utjämning av nätverksbelastning. |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| En dedikerad WAN-länken mellan lokala nätverk och virtuella Azure-nätverk. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| Globala DNS belastningsutjämning.|
| [Azure Application Proxy](../active-directory/active-directory-application-proxy-get-started.md)| En autentisering frontend används för att säkra fjärråtkomst för webbprogram finns lokalt. |