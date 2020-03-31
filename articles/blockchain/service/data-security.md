---
title: Azure Blockchain-tjänst säkerhet
description: Azure Blockchain Service-dataåtkomst och säkerhetsbegrepp
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982228"
---
# <a name="azure-blockchain-service-security"></a>Azure Blockchain-tjänst säkerhet

Azure Blockchain Service använder flera Azure-funktioner för att skydda dina data och tillgängliga. Data skyddas med isolering, kryptering och autentisering.

## <a name="isolation"></a>Isolering

Azure Blockchain Service-resurser är isolerade i ett privat virtuellt nätverk. Varje transaktions- och valideringsnod är en virtuell dator (VM). Virtuella datorer i ett virtuellt nätverk kan inte kommunicera direkt till virtuella datorer i ett annat virtuellt nätverk. Isolering säkerställer att kommunikationen förblir privat i det virtuella nätverket. Mer information om Azure-isolering av virtuella nätverk finns [i isolering i Azure Public Cloud](../../security/fundamentals/isolation-choices.md#networking-isolation).

![VNET-diagram](./media/data-security/vnet.png)

## <a name="encryption"></a>Kryptering

Användardata lagras i Azure-lagring. Användardata krypteras i rörelse och i vila för säkerhet och sekretess. Mer information finns i: [Azure Storage-säkerhetsguide .](../../storage/blobs/security-recommendations.md)

## <a name="authentication"></a>Autentisering

Transaktioner kan skickas till blockchain-noder via en RPC-slutpunkt. Klienter kommunicerar med en transaktionsnod med hjälp av en omvänd proxyserver som hanterar användarautentisering och krypterar data via SSL.

![Diagram över autentisering](./media/data-security/authentication.png)

Det finns tre autentiseringslägen för RPC-åtkomst.

### <a name="basic-authentication"></a>Grundläggande autentisering

Grundläggande autentisering använder ett HTTP-autentiseringshuvud som innehåller användarnamn och lösenord. Användarnamn är namnet på blockchain-noden. Lösenordet anges under etableringen av en medlem eller nod. Lösenordet kan ändras med hjälp av Azure-portalen eller CLI.

### <a name="access-keys"></a>Åtkomstnycklar

Access-nycklar använder en slumpmässigt genererad sträng som ingår i slutpunkts-URL:en. Två åtkomstnycklar hjälper till att aktivera tangentrotation. Nycklar kan återskapas från Azure-portalen och CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) använder en anspråksbaserad autentiseringsmekanism där användaren autentiseras av Azure AD med Hjälp av Azure AD-användarautentiseringsuppgifter. Azure AD tillhandahåller molnbaserad identitetshantering och gör det möjligt för kunder att använda en enda identitet i ett helt företag och komma åt program i molnet. Azure Blockchain-tjänsten integreras med Azure AD som möjliggör ID-federation, enkel inloggning och multifaktorautentisering. Du kan tilldela användare, grupper och programroller i organisationen för åtkomst till blockchain-medlemmar och nod.

Azure AD-klientproxyn är tillgänglig på [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Klientproxyn dirigerar användaren till inloggningssidan för Azure AD och hämtar en innehavartoken vid lyckad autentisering. Därefter ansluter användaren ett Ethereum-klientprogram som Geth eller Tryffel till klientproxyns slutpunkt. Slutligen, när en transaktion skickas, intgjuter klientproxyn innehavartoken i http-huvudet och den omvända proxyn validerar token med OAuth-protokollet.

## <a name="keys-and-ethereum-accounts"></a>Keys- och Ethereum-konton

När du etablerar en Azure Blockchain Service-medlem genereras ett Ethereum-konto och ett offentligt och privat nyckelpar. Den privata nyckeln används för att skicka transaktioner till blockkedjan. Ethereum-kontot är de sista 20 byteen av den offentliga nyckelns hash. Ethereum-kontot kallas också för plånbok.

Det privata och offentliga nyckelparet lagras som en nyckelfil i JSON-format. Den privata nyckeln krypteras med det lösenord som anges när blockchain-redovisningstjänsten skapas.

Privata nycklar används för att signera transaktioner digitalt. I privata blockkedjor representerar ett smart kontrakt som undertecknats av en privat nyckel undertecknarens identitet. För att verifiera signaturens giltighet kan mottagaren jämföra den offentliga nyckeln för undertecknaren med adressen som beräknas från signaturen.

Konstellationsnycklar används för att unikt identifiera en kvorumnod. Konstellationsnycklar genereras vid tidpunkten för nodetablering och anges i parametern privateFor för en privat transaktion i Kvorum.

## <a name="next-steps"></a>Nästa steg

Se [Så här konfigurerar du Azure Active Directory-åtkomst för Azure Blockchain Service](configure-aad.md).
