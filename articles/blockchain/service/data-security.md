---
title: Azure Blockchain-Service-säkerhet
description: Azure Blockchain Service data åtkomst och säkerhet-koncept
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028207"
---
# <a name="azure-blockchain-service-security"></a>Azure Blockchain-Service-säkerhet

Azure Blockchain Service använder flera Azure-funktioner för att förvara dina data säkra och tillgängliga. Data är skyddade med isolering, kryptering och autentisering.

## <a name="isolation"></a>Isolering

Azure Blockchain Service-resurser är isolerade i ett privat virtuellt nätverk. Varje transaktion och verifiering nod är en virtuell dator (VM). Virtuella datorer i ett virtuellt nätverk kan inte kommunicera direkt till virtuella datorer i ett annat virtuellt nätverk. Isolering säkerställer att kommunikation förblir privata i det virtuella nätverket. Läs mer på Azure-nätverksisolering [isolering i Azures offentliga moln](../../security/azure-isolation.md#networking-isolation).

![VNET-diagram](./media/data-security/vnet.png)

## <a name="encryption"></a>Kryptering

Användarinformationen är lagrad i Azure storage. Användarens data krypteras vid överföring och i vila för säkerhet och sekretess. Mer information finns i: [Säkerhetsguiden för Azure Storage](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Autentisering

Transaktioner kan skickas till blockchain noder via en RPC-slutpunkt. Klienter kommunicerar med en transaktion nod med hjälp av en omvänd proxy-server som hanterar användarautentisering och krypterar data via SSL.

![Autentiseringsdiagram](./media/data-security/authentication.png)

Det finns tre lägen för autentisering av RPC-åtkomst.

### <a name="basic-authentication"></a>Grundläggande autentisering

Grundläggande autentisering använder ett HTTP-autentisering-huvud som innehåller användarnamn och lösenord. Användarnamnet är namnet på den blockchain-noden. Lösenordet anges vid etablering av en medlem eller en nod. Lösenordet kan ändras med hjälp av Azure-portalen eller CLI.

### <a name="access-keys"></a>Åtkomstnycklar

Åtkomstnycklar använder en slumpmässigt genererad sträng som ingår i slutpunkts-URL. Två åtkomstnycklar möjliggör rotation av. Nycklar kan återskapas från Azure portal och CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) använder en anspråksbaserad autentiseringsmekanism där användaren har autentiserats av Azure AD med hjälp av autentiseringsuppgifter för Azure AD-användare. Azure AD tillhandahåller molnbaserade Identitetshantering och kan kunderna använda en enda identitet för en hel enterprise och åtkomst till program i molnet. Azure Blockchain Service kan integreras med Azure AD aktiverar ID federation, enkel inloggning och Multi-Factor authentication. Du kan tilldela användare, grupper och roller för programmet i din organisation för blockchain medlem och noden åtkomst.

Proxy för Azure AD-klienten finns på [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Klient-proxy leder användaren till inloggningssidan för Azure AD och hämtar en ägartoken efter lyckad autentisering. Användaren ansluter därefter ett Ethereum-klientprogram, till exempel Geth eller Truffle till slutpunkten för klient-proxy. Slutligen när en transaktion har skickats, klient-proxy lägger in ägartoken i http-huvud och omvänd proxy verifierar token med hjälp av OAuth-protokollet.

## <a name="keys-and-ethereum-accounts"></a>Nycklar och Ethereum-konton

När du etablerar en medlem i Azure Blockchain Service, genereras ett Ethereum-konto och en offentlig och privat nyckel. Den privata nyckeln används för att skicka transaktioner till blockchain. Ethereum-kontot är de senaste 20 byte av den offentliga nyckeln hash. En wallet är en förkortning av Ethereum-konto.

Privata och offentliga nyckelpar lagras som en keyfile i JSON-format. Den privata nyckeln är krypterad med lösenord anges när tjänsten blockchain transaktionsregister skapas.

Privata nycklar används för att signera transaktioner. I privata blockchains representerar ett smarta kontrakt som signerats av en privat nyckel undertecknarens identitet. Mottagaren kan jämföra den offentliga nyckeln undertecknarens med adressen beräknas från signaturen för att kontrollera giltigheten för signaturen.

Constellation nycklar används för att unikt identifiera en kvorum-nod. Constellation nycklar genereras vid tidpunkten för noden etablering och anges i parametern privateFor för en privat transaktion i kvorum.

## <a name="next-steps"></a>Nästa steg

[Konfigurera Azure Blockchain Service transaktion noder](configure-transaction-nodes.md)
