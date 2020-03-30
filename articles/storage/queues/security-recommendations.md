---
title: Säkerhetsrekommendationer för kölagring
titleSuffix: Azure Storage
description: Läs mer om säkerhetsrekommendationer för kölagring. Genom att implementera den här vägledningen kan du uppfylla dina säkerhetsskyldigheter enligt beskrivningen i vår modell för delat ansvar.
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060903"
---
# <a name="security-recommendations-for-queue-storage"></a>Säkerhetsrekommendationer för kölagring

Den här artikeln innehåller säkerhetsrekommendationer för kölagring. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhetsskyldigheter enligt beskrivningen i vår modell för delat ansvar. Mer information om vad Microsoft gör för att uppfylla tjänsteleverantörens ansvar finns i [Delat ansvar för molnbaserad databehandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Några av rekommendationerna i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvarslinjen för att skydda dina resurser i Azure. Information om Azure Security Center finns i [Vad är Azure Security Center?](../../security-center/security-center-intro.md).

Azure Security Center analyserar regelbundet säkerhetstillståndet för dina Azure-resurser för att identifiera potentiella säkerhetsproblem. Den ger dig sedan rekommendationer om hur du ska hantera dem. Mer information om Azure Security Center-rekommendationer finns [i Säkerhetsrekommendationer i Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Använda distributionsmodellen för Azure Resource Manager | Skapa nya lagringskonton med hjälp av distributionsmodellen för Azure Resource Manager för viktiga säkerhetsförbättringar, inklusive bättre åtkomstkontroll (RBAC) och granskning, Resurshanteraren-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till hanterade identiteter, åtkomst till Azure Key Vault för hemligheter och Azure AD-baserad autentisering och auktorisering för åtkomst till Azure Storage-data och resurser. Om möjligt migrera befintliga lagringskonton som använder den klassiska distributionsmodellen för att använda Azure Resource Manager. Mer information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview). | - |
| Aktivera alternativet **Säker överföring som krävs** på alla dina lagringskonton | När du aktiverar alternativet **Säker överföring måste** alla begäranden som görs mot lagringskontot ske via säkra anslutningar. Alla begäranden som görs via HTTP misslyckas. Mer information finns [i Kräv säker överföring i Azure Storage](../common/storage-require-secure-transfer.md). | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Aktivera avancerat hotskydd för alla dina lagringskonton | Avancerat hotskydd för Azure Storage ger ett extra lager av säkerhetsinformation som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Säkerhetsaviseringar utlöses i Azure Security Center när avvikelser i aktivitet inträffar och skickas även via e-post till prenumerationsadministratörer, med information om misstänkt aktivitet och rekommendationer om hur du undersöker och åtgärdar hot. Mer information finns i [Avancerat hotskydd för Azure Storage](../common/storage-advanced-threat-protection.md). | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Begränsa SAS-token (Shared Access Signature) till endast HTTPS-anslutningar | Att kräva HTTPS när en klient använder en SAS-token för att komma åt ködata hjälper till att minimera risken för avlyssning. Mer information finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../common/storage-sas-overview.md) | - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Använda Azure Active Directory (Azure AD) för att auktorisera åtkomst till ködata | Azure AD ger överlägsen säkerhet och användarvänlighet över delad nyckel för att godkänna begäranden till kölagring. Mer information finns i [Auktorisera åtkomst till Azure-blobbar och köer med Azure Active Directory](../common/storage-auth-aad.md). | - |
| Tänk på huvudmannen för minst behörighet när du tilldelar behörigheter till ett Azure AD-säkerhetsobjekt via RBAC | När du tilldelar en roll till en användare, grupp eller ett program, bevilja säkerhetsobjektet endast de behörigheter som krävs för att de ska kunna utföra sina uppgifter. Genom att begränsa åtkomsten till resurser förhindras både oavsiktligt och skadligt missbruk av dina data. | - |
| Skydda dina kontoåtkomstnycklar med Azure Key Vault | Microsoft rekommenderar att du använder Azure AD för att auktorisera begäranden till Azure Storage. Men om du måste använda auktorisering för delad nyckel, sedan säkra dina kontonycklar med Azure Key Vault. Du kan hämta nycklarna från nyckelvalvet vid körning i stället för att spara dem med ditt program. | - |
| Återskapa dina kontonycklar med jämna mellanrum | Om du roterar kontonycklarna med jämna mellanrum minskar risken för att dina data exponeras för skadliga aktörer. | - |
| Tänk på huvudmannen för minst behörighet när du tilldelar behörigheter till en SAS | När du skapar en SAS anger du bara de behörigheter som krävs av klienten för att utföra dess funktion. Genom att begränsa åtkomsten till resurser förhindras både oavsiktligt och skadligt missbruk av dina data. | - |
| Ha en återkallningsplan på plats för alla SAS som du utfärdar till kunder | Om en SAS äventyras, kommer du att vilja återkalla att SAS så snart som möjligt. Om du vill återkalla en SAS för användardelegering återkallar du användardelegeringsnyckeln för att snabbt ogiltigförklara alla signaturer som är associerade med nyckeln. Om du vill återkalla en SERVICE-SAS som är associerad med en lagrad åtkomstprincip kan du ta bort den lagrade åtkomstprincipen, byta namn på principen eller ändra dess utgångstid till en tid som tidigare. Mer information finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../common/storage-sas-overview.md)  | - |
| Om en service-SAS inte är associerad med en lagrad åtkomstprincip ställer du in utgångstiden till en timme eller mindre | En service-SAS som inte är associerad med en lagrad åtkomstprincip kan inte återkallas. Därför rekommenderas att begränsa utgångstiden så att SAS är giltigt i en timme eller mindre. | - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Aktivera brandväggsregler | Konfigurera brandväggsregler för att begränsa åtkomsten till ditt lagringskonto till begäranden som kommer från angivna IP-adresser eller intervall, eller från en lista över undernät i ett Virtuellt Azure-nätverk (VNet). Mer information om hur du konfigurerar brandväggsregler finns i [Azure File Sync proxy och brandväggsinställningar](../files/storage-sync-files-firewall-and-proxy.md). | - |
| Tillåt betrodda Microsoft-tjänster att komma åt lagringskontot | Om du aktiverar brandväggsregler för ditt lagringskonto blockeras inkommande begäranden om data som standard, såvida inte begäranden kommer från en tjänst som arbetar inom ett Virtuellt Azure-nätverk (VNet) eller från tillåtna offentliga IP-adresser. Begäranden som blockeras inkluderar de från andra Azure-tjänster, från Azure-portalen, från loggning och måtttjänster och så vidare. Du kan tillåta begäranden från andra Azure-tjänster genom att lägga till ett undantag för att tillåta betrodda Microsoft-tjänster att komma åt lagringskontot. Mer information om hur du lägger till ett undantag för betrodda Microsoft-tjänster finns i [Proxy- och brandväggsinställningar för Azure File Sync](../files/storage-sync-files-firewall-and-proxy.md).| - |
| Använda privata slutpunkter | En privat slutpunkt tilldelar en privat IP-adress från ditt Virtuella Azure-nätverk (VNet) till lagringskontot. Det säkrar all trafik mellan ditt virtuella nätverk och lagringskontot via en privat länk. Mer information om privata slutpunkter finns i [Ansluta privat till ett lagringskonto med Azure Private Endpoint](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Begränsa nätverksåtkomsten till specifika nätverk | Om du begränsar nätverksåtkomsten till nätverk som är värdar för klienter som behöver åtkomst minskar exponeringen för dina resurser för nätverksattacker. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Loggning/övervakning

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Spåra hur begäranden auktoriseras | Aktivera Azure Storage-loggning för att spåra hur varje begäran som gjorts mot Azure Storage har auktoriserats. Loggarna anger om en begäran gjordes anonymt, med hjälp av en OAuth 2.0-token, med delad nyckel eller med hjälp av en signatur för delad åtkomst (SAS). Mer information finns i [Loggning av Azure Storage analytics](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Nästa steg

- [Azure-säkerhetsdokumentation](https://docs.microsoft.com//azure/security/)
- [Säker utvecklingsdokumentation](https://docs.microsoft.com/azure/security/develop/).
