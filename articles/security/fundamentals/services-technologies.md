---
title: Azure Security Services och Technologies | Microsoft Docs
description: Artikeln innehåller en granskad lista över Azures säkerhets tjänster och tekniker.
services: security
documentationcenter: na
author: terrylanfear
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
ms.author: terrylan
ms.openlocfilehash: 83548063a796401792bd19e7ec27be36a0efa4e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80549211"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Säkerhets tjänster och tekniker som är tillgängliga i Azure

I våra diskussioner med aktuella och framtida Azure-kunder brukar vi ofta bli "vill du ha en lista över alla säkerhetsrelaterade tjänster och tekniker som Azure måste erbjuda?"

När du utvärderar alternativ för moln tjänst leverantörer är det bra att ha den här informationen. Vi har angett den här listan för att komma igång.

Med tiden kommer den här listan att ändras och växa, precis som Azure gör. Se till att kontrol lera den här sidan regelbundet för att hålla dig uppdaterad om våra säkerhetsrelaterade tjänster och tekniker.

## <a name="general-azure-security"></a>Allmän Azure-säkerhet
|Tjänst|Beskrivning|
|--------|--------|
|[Azure &nbsp; Security &nbsp; Center](/azure/security-center/security-center-intro)| En skydds lösning för moln skydd som tillhandahåller säkerhets hantering och Avancerat skydd för arbets belastningar i hybrid moln.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| En säker hemligheter för lösen ord, anslutnings strängar och annan information som du behöver för att hålla dina appar igång. |
|[Azure Monitor loggar](/azure/log-analytics/log-analytics-overview)|En övervaknings tjänst som samlar in telemetri och andra data och tillhandahåller ett frågespråk och analys motor för att leverera Operational Insights för dina appar och resurser. Kan användas separat eller med andra tjänster som Security Center. |
|[Azure dev/test Labs](/azure/lab-services/devtest-lab-overview)|En tjänst som hjälper utvecklare och testare att snabbt skapa miljöer i Azure och minimera spill och kontrol lera kostnader.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Lagringssäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Kryptering av Azure &nbsp; Storage &nbsp; -tjänsten &nbsp;](/azure/storage/common/storage-service-encryption)|En säkerhetsfunktion som automatiskt krypterar dina data i Azure Storage.   |
|[StorSimple-krypterad hybrid lagring](/azure/storsimple/storsimple-ova-overview)| En integrerad lagrings lösning som hanterar lagrings aktiviteter mellan lokala enheter och Azures moln lagring.|
|[Azure Client-Side-kryptering](/azure/storage/common/storage-client-side-encryption)| En krypterings lösning på klient sidan som krypterar data i klient program innan de överförs till Azure Storage. dekrypterar också data vid hämtning. |
| [Azure Storage signaturer för delad åtkomst](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagrings konto.  |
|[Azure Storage konto nycklar](/azure/storage/common/storage-create-storage-account)| En åtkomst kontroll metod för Azure Storage som används för autentisering när lagrings kontot nås. |
|[Azure-filresurser med SMB 3,0-kryptering](/azure/storage/files/storage-files-introduction)|En nätverks säkerhets teknik som aktiverar automatisk nätverks kryptering för fildelnings protokollet SMB (Server Message Block). |
|[Azure-lagringsanalys](/rest/api/storageservices/Storage-Analytics)| En loggning och statistik som genererar teknik för data i ditt lagrings konto. |

<!------>

## <a name="database-security"></a>Databassäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Azure &nbsp; SQL- &nbsp; brandvägg](/azure/sql-database/sql-database-firewall-configure)|En funktion för nätverks åtkomst kontroll som skyddar mot nätverksbaserade attacker till databasen. |
|[Kryptering av Azure &nbsp; SQL- &nbsp; cellnivå &nbsp;](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| En databas säkerhets teknik som tillhandahåller kryptering på en detaljerad nivå.  |
| [Kryptering av Azure &nbsp; SQL- &nbsp; anslutning](/azure/sql-database/sql-database-control-access)|För att erbjuda säkerhet, kontrollerar SQL Database åtkomsten med hjälp av brandväggsregler som begränsar anslutning efter IP-adress, autentiseringsmekanismer kräver att användare bevisar sin identitet och auktoriseringsmekanismer begränsar användare till specifika åtgärder och data. |
| [Azure SQL Always Encryption](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Skyddar känsliga data, t. ex. kreditkorts nummer eller nationella identifierings nummer (t. ex. amerikanskt socialförsäkrings nummer) som lagras i Azure SQL Database-eller SQL Server-databaser.  |
| [Azure &nbsp; SQL- &nbsp; Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| En databas säkerhets funktion som krypterar lagringen av en hel databas. |
| [Azure SQL Database granskning](/azure/sql-database/sql-database-auditing)|En databas gransknings funktion som spårar databas händelser och skriver dem till en Gransknings logg på ditt Azure Storage-konto.  |


## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering
|Tjänst|Beskrivning|
|------|--------|
| [Azure- &nbsp; roll &nbsp; baserad &nbsp; Access Control](/azure/active-directory/role-based-access-control-configure)|En åtkomst kontroll funktion som är utformad för att tillåta användare att få åtkomst till de resurser som krävs för att få åtkomst baserat på deras roller i organisationen.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|En molnbaserad databas för autentisering som stöder en molnbaserad katalog med flera klienter och flera identitets hanterings tjänster i Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|En identitets hanterings tjänst som gör det möjligt att styra hur kunder registrerar, loggar in och hanterar sina profiler när de använder Azure-baserade program.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| En molnbaserad och hanterad version av Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)| En säkerhets bestämmelse som använder flera olika former av autentisering och verifiering innan åtkomst till skyddad information tillåts. |

## <a name="backup-and-disaster-recovery"></a>Säkerhets kopiering och haveri beredskap
|Tjänst|Beskrivning|
|------|--------|
| [Azure &nbsp; Backup](/azure/backup/backup-introduction-to-azure-backup)| En Azure-baserad tjänst som används för att säkerhetskopiera och återställa data i Azure-molnet. |
| [Azure &nbsp; Site &nbsp; Recovery](/azure/site-recovery/site-recovery-overview)|En online tjänst som replikerar arbets belastningar som körs på fysiska och virtuella datorer (VM) från en primär plats till en sekundär plats för att aktivera återställning av tjänster efter ett haveri. |

## <a name="networking"></a>Nätverk
|Tjänst|Beskrivning|
|------|--------|
| [Nätverks &nbsp; säkerhets &nbsp; grupper](/azure/virtual-network/virtual-networks-nsg)| En nätverks baserad åtkomst kontroll funktion som använder en 5-tupel för att tillåta eller neka beslut.  |
| [Azure VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| En nätverks enhet som används som en VPN-slutpunkt för att tillåta åtkomst mellan platser till virtuella Azure-nätverk.  |
| [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|En avancerad webb programs belastningsutjämnare som kan dirigeras baserat på URL och utföra SSL-avlastning. |
|[Brand vägg för webbaserade program](/azure/frontdoor/waf-overview) (WAF)|En funktion i Application Gateway som ger centraliserat skydd för dina webb program mot vanliga sårbarheter och sårbarheter|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|En TCP/UDP-program för nätverks belastnings utjämning. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| En dedikerad WAN-länk mellan lokala nätverk och virtuella Azure-nätverk. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| En global DNS-belastningsutjämnare.|
| [Azure Application Proxy](/azure/active-directory/active-directory-application-proxy-get-started)| En autentiserande klient del som används för att skydda fjärråtkomst för webb program som finns lokalt. |
|[Azure Firewall](/azure/firewall/overview)|En hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser.|
|[Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview)|Kombinerat med bästa praxis för program design, ger skydd mot DDoS-attacker.|
|[Virtual Network tjänst slut punkter](/azure/virtual-network/virtual-network-service-endpoints-overview)|Utökar det virtuella nätverkets privata adress utrymme och identiteten för ditt VNet till Azure-tjänsterna, via en direkt anslutning.|