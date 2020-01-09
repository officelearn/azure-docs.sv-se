---
title: Säkerhets rekommendationer för Queue Storage
titleSuffix: Azure Storage
description: Lär dig mer om säkerhets rekommendationer för Queue Storage. Genom att implementera den här vägledningen kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 1315e1f81ee32a544b623b7f3ffad61a7e7275d8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486292"
---
# <a name="security-recommendations-for-queue-storage"></a>Säkerhets rekommendationer för Queue Storage

Den här artikeln innehåller säkerhets rekommendationer för Queue Storage. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell. Mer information om vad Microsoft gör för att uppfylla ansvaret för service leverantörer finns i [delade ansvars områden för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Några av rekommendationerna som ingår i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvars linjen i att skydda dina resurser i Azure. Information om Azure Security Center finns i [Azure Security Center vad är?](../../security-center/security-center-intro.md).

Azure Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Sedan får du rekommendationer om hur du kan åtgärda dem. Mer information om Azure Security Center rekommendationer finns [i säkerhets rekommendationer i Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer | Säkerhetscenter |
|-|----|--|
| Använd Azure Resource Manager distributions modell | Skapa nya lagrings konton med hjälp av Azure Resource Manager distributions modell för viktiga säkerhets förbättringar, inklusive överlägsen åtkomst kontroll (RBAC) och granskning, Resource Manager-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst för att Azure Key Vault för hemligheter och Azure AD-baserad autentisering och auktorisering för åtkomst till Azure Storage data och resurser. Migrera om möjligt Befintliga lagrings konton som använder den klassiska distributions modellen för att använda Azure Resource Manager. Mer information om Azure Resource Manager finns i [Azure Resource Manager översikt](/azure/azure-resource-manager/resource-group-overview). | - |
| Aktivera alternativet för **säker överföring som krävs** på alla dina lagrings konton | När du aktiverar alternativet för **säker överföring krävs** måste alla begär Anden som görs mot lagrings kontot äga rum via säkra anslutningar. Förfrågningar som görs över HTTP kommer att Miss klar. Mer information finns i [Kräv säker överföring i Azure Storage](../common/storage-require-secure-transfer.md). | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Aktivera avancerat skydd för alla dina lagrings konton | Avancerat skydd för Azure Storage ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Säkerhets aviseringar utlöses i Azure Security Center när avvikelser i aktivitet sker och också skickas via e-post till prenumerations administratörer, med information om misstänkt aktivitet och rekommendationer för att undersöka och åtgärda hot. Mer information finns i [Avancerat skydd mot Azure Storage](../common/storage-advanced-threat-protection.md). | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Begränsa endast SAS-token (signatur för delad åtkomst) till HTTPS-anslutningar | Att kräva HTTPS när en klient använder en SAS-token för att komma åt Queue data bidrar till att minimera risken för avlyssning. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentarer | Säkerhetscenter |
|-|----|--|
| Använd Azure Active Directory (Azure AD) för att ge åtkomst till köade data | Azure AD ger överlägsen säkerhet och enkel användning över delad nyckel för auktorisering av förfrågningar till Queue Storage. Mer information finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md). | - |
| Kom ihåg huvud kontot för minsta behörighet när du tilldelar behörigheter till ett säkerhets objekt för Azure AD via RBAC | När du tilldelar en roll till en användare, grupp eller ett program, så beviljar du detta säkerhets objekt endast de behörigheter som krävs för att utföra sina uppgifter. Genom att begränsa åtkomsten till resurser kan du förhindra både oavsiktlig och skadlig missbruk av dina data. | - |
| Skydda konto åtkomst nycklar med Azure Key Vault | Microsoft rekommenderar att Azure AD används för att auktorisera begär anden till Azure Storage. Men om du måste använda autentisering med delad nyckel kan du skydda dina konto nycklar med Azure Key Vault. Du kan hämta nycklarna från nyckel valvet vid körning i stället för att spara dem i ditt program. | - |
| Återskapa dina konto nycklar regelbundet | Genom att rotera konto nycklarna minskar du regelbundet risken för att exponera dina data till skadliga aktörer. | - |
| Kom ihåg huvud kontot för minsta behörighet när du tilldelar behörigheter till en SAS | När du skapar en SAS anger du endast de behörigheter som krävs av klienten för att utföra sin funktion. Genom att begränsa åtkomsten till resurser kan du förhindra både oavsiktlig och skadlig missbruk av dina data. | - |
| Ha en återkallnings plan för alla SAS som du utfärdar till klienter | Om en SAS komprometteras, vill du återkalla SAS så snart som möjligt. Om du vill återkalla en användar Delegerings-SAS ska du återkalla användar Delegerings nyckeln så att alla signaturer som är associerade med den här nyckeln blir snabbt Om du vill återkalla en tjänst-SAS som är associerad med en lagrad åtkomst princip kan du ta bort den lagrade åtkomst principen, byta namn på principen eller ändra förfallo tiden till en tid som redan har passerat. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).  | - |
| Om en tjänst-SAS inte är associerad med en lagrad åtkomst princip anger du förfallo tiden till en timme eller mindre | En tjänst-SAS som inte är associerad med en lagrad åtkomst princip kan inte återkallas. Därför bör du begränsa förfallo tiden så att SAS är giltig i en timme eller mindre. | - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Säkerhetscenter |
|-|----|--|
| Aktivera brand Väggs regler | Konfigurera brand Väggs regler för att begränsa åtkomsten till ditt lagrings konto till begär Anden som kommer från angivna IP-adresser eller intervall, eller från en lista över undernät i ett Azure-Virtual Network (VNet). Mer information om hur du konfigurerar brand Väggs regler finns i [Azure File Sync proxy-och brand Väggs inställningar](../files/storage-sync-files-firewall-and-proxy.md). | - |
| Tillåt att betrodda Microsoft-tjänster har åtkomst till lagrings kontot | Att aktivera brand Väggs regler för ditt lagrings konto blockerar inkommande begär Anden om data som standard, om inte begär Anden kommer från en tjänst som körs i ett Azure-Virtual Network (VNet) eller från tillåtna offentliga IP-adresser. Begäranden som blockeras är de från andra Azure-tjänster från Azure-portalen från loggning och mått tjänster, och så vidare. Du kan tillåta förfrågningar från andra Azure-tjänster genom att lägga till ett undantag för att tillåta betrodda Microsoft-tjänster att komma åt lagrings kontot. Mer information om hur du lägger till ett undantag för betrodda Microsoft-tjänster finns [Azure File Sync proxy-och brand Väggs inställningar](../files/storage-sync-files-firewall-and-proxy.md).| - |
| Använd privata slut punkter | En privat slut punkt tilldelar en privat IP-adress från ditt Azure-Virtual Network (VNet) till lagrings kontot. Den skyddar all trafik mellan ditt VNet och lagrings kontot via en privat länk. Mer information om privata slut punkter finns i [ansluta privat till ett lagrings konto med hjälp av den privata Azure-slutpunkten](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Begränsa nätverks åtkomst till vissa nätverk | Att begränsa nätverks åtkomsten till nätverk som är värd för klienter som kräver åtkomst minskar exponeringen för dina resurser till nätverks attacker. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Loggning/övervakning

| Rekommendation | Kommentarer | Säkerhetscenter |
|-|----|--|
| Spåra hur begär Anden auktoriseras | Aktivera Azure Storage loggning för att spåra hur varje begäran som görs mot Azure Storage auktoriserades. Loggarna anger om en begäran har gjorts anonymt, genom att använda en OAuth 2,0-token med hjälp av delad nyckel eller genom att använda en signatur för delad åtkomst (SAS). Mer information finns i [Azure Storage Analytics-loggning](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-säkerhet](https://docs.microsoft.com//azure/security/)
- [Skydda utvecklings dokumentationen](https://docs.microsoft.com/azure/security/develop/).
