---
title: Azure Security Services and Technologies | Microsoft-dokument
description: Artikeln innehåller en detaljerad lista över Azure Security-tjänster och -tekniker.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 741e6b9cad20645fdfc085623fd9492a12ac1dfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726567"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Säkerhetstjänster och tekniker som är tillgängliga på Azure

I våra diskussioner med nuvarande och framtida Azure-kunder får vi ofta frågan "Har du en lista över alla säkerhetsrelaterade tjänster och tekniker som Azure har att erbjuda?"

När du utvärderar molntjänstleverantörsalternativ är det bra att ha den här informationen. Så vi har lämnat denna lista för att komma igång.

Med tiden kommer den här listan att ändras och växa, precis som Azure gör. Se till att regelbundet kontrollera den här sidan för att hålla dig uppdaterad om våra säkerhetsrelaterade tjänster och tekniker.

## <a name="general-azure-security"></a>Allmän Azure-säkerhet
|Tjänst|Beskrivning|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](/azure/security-center/security-center-intro)| En molnarbetsbelastningsskyddslösning som ger säkerhetshantering och avancerat hotskydd över hybridmolnarbetsbelastningar.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| En säker hemlighetsbutik för lösenord, anslutningssträngar och annan information som du behöver för att hålla dina appar i arbete. |
|[Azure Monitor-loggar](/azure/log-analytics/log-analytics-overview)|En övervakningstjänst som samlar in telemetri och andra data och tillhandahåller en frågespråk- och analysmotor för att leverera operativa insikter för dina appar och resurser. Kan användas ensamt eller med andra tjänster som Security Center. |
|[Azure-utvecklings-/testlabb](/azure/lab-services/devtest-lab-overview)|En tjänst som hjälper utvecklare och testare att snabbt skapa miljöer i Azure samtidigt som avfall minimeras och kostnaderna kontrolleras.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Lagringssäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Kryptering&nbsp;&nbsp;av&nbsp;Azure Storage-tjänsten](/azure/storage/common/storage-service-encryption)|En säkerhetsfunktion som automatiskt krypterar dina data i Azure-lagring.   |
|[StorSimple krypterad hybridlagring](/azure/storsimple/storsimple-ova-overview)| En integrerad lagringslösning som hanterar lagringsuppgifter mellan lokala enheter och Azure-molnlagring.|
|[Kryptering på Azure-klientsidan](/azure/storage/common/storage-client-side-encryption)| En krypteringslösning på klientsidan som krypterar data i klientprogram innan de överförs till Azure Storage. dekrypterar också data när du hämtar. |
| [Signaturer för delad åtkomst till Azure Storage](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto.  |
|[Azure Storage-kontonycklar](/azure/storage/common/storage-create-storage-account)| En åtkomstkontrollmetod för Azure-lagring som används för autentisering när lagringskontot används. |
|[Azure File delar med SMB 3.0-kryptering](/azure/storage/files/storage-files-introduction)|En nätverkssäkerhetsteknik som möjliggör automatisk nätverkskryptering för fildelningsprotokollet För Server Message Block (SMB). |
|[Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics)| En loggnings- och måttgenererande teknik för data i ditt lagringskonto. |

<!------>

## <a name="database-security"></a>Databassäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Azure&nbsp;&nbsp;SQL-brandvägg](/azure/sql-database/sql-database-firewall-configure)|En nätverksåtkomstkontrollfunktion som skyddar mot nätverksbaserade attacker till databasen. |
|[Kryptering&nbsp;&nbsp;på&nbsp;Azure SQL Cell-nivå](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| En databassäkerhetsteknik som tillhandahåller kryptering på detaljerad nivå.  |
| [Kryptering&nbsp;&nbsp;av Azure SQL-anslutning](/azure/sql-database/sql-database-control-access)|För att erbjuda säkerhet, kontrollerar SQL Database åtkomsten med hjälp av brandväggsregler som begränsar anslutning efter IP-adress, autentiseringsmekanismer kräver att användare bevisar sin identitet och auktoriseringsmekanismer begränsar användare till specifika åtgärder och data. |
| [Azure SQL-alltid kryptering](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Skyddar känsliga data, till exempel kreditkortsnummer eller nationella id-nummer (till exempel amerikanska personnummer), som lagras i Azure SQL Database- eller SQL Server-databaser.  |
| [Azure&nbsp;&nbsp;SQL Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| En databassäkerhetsfunktion som krypterar lagringen av en hel databas. |
| [Granskning av Azure SQL-databas](/azure/sql-database/sql-database-auditing)|En databasgranskningsfunktion som spårar databashändelser och skriver dem till en granskningslogg i ditt Azure-lagringskonto.  |


## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering
|Tjänst|Beskrivning|
|------|--------|
| [Azure-rollbaserad&nbsp;&nbsp;&nbsp;åtkomstkontroll](/azure/active-directory/role-based-access-control-configure)|En åtkomstkontrollfunktion som utformats för att tillåta användare att komma åt endast de resurser som de behöver komma åt baserat på deras roller inom organisationen.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|En molnbaserad autentiseringsdatabas som stöder en molnbaserad katalog med flera innehavare och flera identitetshanteringstjänster i Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|En identitetshanteringstjänst som gör det möjligt att kontrollera hur kunder registrerar sig, loggar in och hanterar sina profiler när de använder Azure-baserade program.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| En molnbaserad och hanterad version av Active Directory Domain Services. |
| [Azure multifaktorautentisering](/azure/active-directory/authentication/multi-factor-authentication)| En säkerhetsbestämmelse som använder flera olika former av autentisering och verifiering innan den ger tillgång till säker information. |

## <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och haveriberedskap
|Tjänst|Beskrivning|
|------|--------|
| [Azure-säkerhetskopiering&nbsp;](/azure/backup/backup-introduction-to-azure-backup)| En Azure-baserad tjänst som används för att säkerhetskopiera och återställa data i Azure-molnet. |
| [Återställning&nbsp;&nbsp;av Azure-webbplatser](/azure/site-recovery/site-recovery-overview)|En onlinetjänst som replikerar arbetsbelastningar som körs på fysiska och virtuella datorer (VMs) från en primär plats till en sekundär plats för att aktivera återställning av tjänster efter ett fel. |

## <a name="networking"></a>Nätverk
|Tjänst|Beskrivning|
|------|--------|
| [Säkerhetsgrupper&nbsp;för nätverk&nbsp;](/azure/virtual-network/virtual-networks-nsg)| En nätverksbaserad åtkomstkontrollfunktion med hjälp av en 5-tuppel för att fatta beslut om att tillåta eller neka beslut.  |
| [Azure VPN-gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| En nätverksenhet som används som vpn-slutpunkt för att tillåta korsintern åtkomst till Virtuella Azure-nätverk.  |
| [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|En avancerad belastningsutjämnare för webbprogram som kan cirkulera baserat på URL och utföra SSL-avlastning. |
|[Brandvägg för webbprogram](/azure/frontdoor/waf-overview) (WAF)|En funktion i Application Gateway som ger centraliserat skydd av dina webbprogram från vanliga bedrifter och sårbarheter|
| [Azure belastningsutjämning](/azure/load-balancer/load-balancer-overview)|En belastningsutjämnad TCP/UDP-programnätverk. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| En dedikerad WAN-länk mellan lokala nätverk och Virtuella Azure-nätverk. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| En global DNS-belastningsutjämnare.|
| [Proxy för Azure-program](/azure/active-directory/active-directory-application-proxy-get-started)| En autentisera frontend som används för att skydda fjärråtkomst för webbprogram som finns lokalt. |
|[Azure Firewall](/azure/firewall/overview)|En hanterad, molnbaserad nätverkssäkerhetstjänst som skyddar dina Azure Virtual Network-resurser.|
|[Azure DDoS-skydd](/azure/virtual-network/ddos-protection-overview)|Kombinerat med metodtips för programdesign ger skydd mot DDoS-attacker.|
|[Slutpunkter för tjänsten Virtuellt nätverk](/azure/virtual-network/virtual-network-service-endpoints-overview)|Utökar ditt privata adressutrymme för virtuella nätverk och ditt virtuella nätverks identitet till Azure-tjänsterna, via en direktanslutning.|