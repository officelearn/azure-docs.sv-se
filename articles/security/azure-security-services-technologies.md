---
title: Azure säkerhetstjänster och teknologier | Microsoft Docs
description: Artikeln innehåller en granskad lista över Azure säkerhetstjänster och teknologier.
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
ms.openlocfilehash: 0addad1db4d9f897736a7793c5489897bca2cd38
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093434"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Säkerhetstjänster och teknologier som är tillgängliga på Azure

I vår diskussioner med aktuella och kommande Azure-kunder uppmanas vi ofta ”har du en lista över alla säkerhetsrelaterade tjänster och tekniker som Azure har att erbjuda”?

När du utvärderar molnalternativ service provider är det bra att ha den här informationen. Därför har vi lagt till den här listan för att komma igång.

Framöver kommer den här listan ändrar och växa, precis som Azure gör. Se till att kontrollera den här sidan med jämna mellanrum du hålla koll på vår säkerhetsrelaterade tjänster och tekniker.

## <a name="general-azure-security"></a>Allmänna Azure-säkerhet
|Tjänst|Beskrivning|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](../security-center/security-center-intro.md)| En arbetsbelastning protection molnlösning som tillhandahåller säkerhetshantering och Avancerat skydd mot hot i olika hybridmolnarbetsbelastningar.|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| En säker hemligheter lagring för lösenord, anslutningssträngar och annan information som du behöver för att hålla dina appar fungerar. |
|[Log Analytics](../log-analytics/log-analytics-overview.md)|En övervakningstjänst som samlar in telemetri och övriga data och ger en fråga frågespråk och en analytisk motor för att leverera operativa insikter för dina appar och resurser. Kan användas fristående eller med andra tjänster, till exempel Security Center. |
|[Azure Dev/Test Lab](../devtest-lab/devtest-lab-overview.md)|En tjänst som hjälper utvecklare och testare snabbt skapa miljöer i Azure samtidigt som du minimerar avbrott och kontrollerar kostnaderna.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Lagringssäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;Storage&nbsp;Service&nbsp;kryptering](../storage/common/storage-service-encryption.md)|En säkerhetsfunktion som krypteras automatiskt dina data i Azure storage.   |
|[StorSimple krypterad Hybridlagring](../storsimple/storsimple-ova-overview.md)| En integrerad lagringslösning som hanterar lagringsuppgifter mellan lokala enheter och Azure-molnlagring.|
|[Azure Client Side Encryption](../storage/common/storage-client-side-encryption.md)| En client side encryption-lösningen som krypterar data i klientprogram före överföringen till Azure Storage; även dekrypterar data under nedladdningen. |
| [Azure Storage signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto.  |
|[Azure Storage-Kontonycklar](../storage/common/storage-create-storage-account.md)| En kontroll åtkomstmetod för Azure-lagring som används för autentisering när lagringskontot används. |
|[Azure-filresurser med SMB 3.0-kryptering](../storage/files/storage-files-introduction.md)|Ett nätverk säkerhetsteknik som möjliggör automatisk nätverkskryptering för Server Message Block (SMB)-protokoll för fildelning. |
|[Azure Storage Analytics](https://docs.microsoft.com/en-us/rest/api/storageservices/Storage-Analytics)| Loggning och mått generera teknik för data i ditt storage-konto. |

<!------>

## <a name="database-security"></a>Databassäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;SQL&nbsp;brandvägg](../sql-database/sql-database-firewall-configure.md)|En nätverks-åtkomstfunktion som skyddar mot nätverksbaserade attacker i databasen. |
|[Azure&nbsp;SQL&nbsp;Cell&nbsp;nivå kryptering](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| En databas säkerhetsteknik som erbjuder kryptering på en detaljerad nivå.  |
| [Azure&nbsp;SQL&nbsp;krypterad anslutning](../sql-database/sql-database-control-access.md)|För att erbjuda säkerhet, kontrollerar SQL Database åtkomsten med hjälp av brandväggsregler som begränsar anslutning efter IP-adress, autentiseringsmekanismer kräver att användare bevisar sin identitet och auktoriseringsmekanismer begränsar användare till specifika åtgärder och data. |
| [Azure SQL alltid kryptering](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Skyddar känsliga data, till exempel kreditkortsnummer eller nationella identifikationsnummer (till exempel amerikanska personnummer), lagras i Azure SQL Database eller SQL Server-databaser.  |
| [Azure&nbsp;SQL&nbsp;Transparent datakryptering](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| En databas säkerhetsfunktion som krypterar lagring av en hel databas. |
| [Azure SQL Database-granskning](../sql-database/sql-database-auditing.md)|En databas med granskning funktion som spårar databashändelser och skriver dem till en granskningslogg i ditt Azure storage-konto.  |


## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;rollen&nbsp;baserat&nbsp;åtkomstkontroll](../active-directory/role-based-access-control-configure.md)|En funktion för kontroll av åtkomst som utformats för att ge användare åtkomst till de resurser som de som krävs för att åtkomst baserat på deras roller i organisationen.  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|En molnbaserad autentisering-databas som har stöd för en katalog med flera innehavare, molnbaserade och flera identity management-tjänster i Azure.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|En identitetshanteringstjänst som möjliggör kontroll över hur kunder registrering, inloggning, och hanterar sina profiler när de använder Azure-baserade program.   |
| [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)| En molnbaserad och hanterade version av Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| Etablera en säkerhet som använder flera olika former av autentisering och verifiering innan åtkomst beviljas till skyddad information. |

## <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och haveriberedskap
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;säkerhetskopiering](../backup/backup-introduction-to-azure-backup.md)| En Azure-baserad tjänst som används för att säkerhetskopiera och återställa data i Azure-molnet. |
| [Azure&nbsp;plats&nbsp;återställning](../site-recovery/site-recovery-overview.md)|En onlinetjänst som replikerar arbetsbelastningar som körs på fysiska och virtuella datorer (VM) från en primär plats till en sekundär plats för att aktivera återställning av tjänster efter ett fel. |

## <a name="networking"></a>Nätverk
|Tjänst|Beskrivning|
|------|--------|
| [Nätverk&nbsp;Security&nbsp;grupper](../virtual-network/virtual-networks-nsg.md)| En nätverksbaserad åtkomstfunktion med hjälp av en 5-tuppel att tillåta eller neka beslut.  |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| En nätverksenhet som används som en VPN-slutpunkt för att tillåta mellan lokala åtkomst till virtuella Azure-nätverk.  |
| [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)|Ett avancerat webbprogram belastningsutjämnare som kan dirigera baserat på URL: en och utför SSL-avlastning. |
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|TCP/UDP programmet Utjämning av nätverksbelastning. |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| En dedikerad WAN-länken mellan lokala nätverk och virtuella Azure-nätverk. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| En global DNS-belastningsutjämnare.|
| [Azure Application Proxy](../active-directory/active-directory-application-proxy-get-started.md)| En autentisera klient används för säker fjärråtkomst för webbprogram finns lokalt. |