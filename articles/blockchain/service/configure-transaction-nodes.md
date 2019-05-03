---
title: Konfigurera Azure Blockchain Service transaktion noder
description: Så här konfigurerar du Azure Blockchain Service transaktion noder
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027967"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurera Azure Blockchain Service transaktion noder

För att interagera med Azure Blockchain-tjänsten, göra du det genom att ansluta till en eller flera transaktion noder i blockchain-medlemmen.  Interagera med transaktionen noder ska du kunna konfigurera noderna för åtkomst.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Skapa en medlem i Azure Blockchain](create-member.md)

## <a name="transaction-node-overview"></a>Transaktionsöversikt för noden

Transaktionen noder används för att skicka blockchain transaktioner på Azure Blockchain-tjänsten via en offentlig slutpunkt. Transaktionen standardnoden innehåller den privata nyckeln för Ethereum-kontot som registrerats på blockkedja och därför kan inte tas bort.

Visa standard noden transaktionsinformation:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till din Azure Blockchain Service medlem. Välj **transaktion noder**.

    ![Välj standard transaktion nod](./media/configure-transaction-nodes/nodes.png)

    Översikt över informationen innehåller offentliga slutpunktsadresser och en offentlig nyckel.

## <a name="create-transaction-node"></a>Skapa transaktion nod

Du kan lägga till upp till nio ytterligare transaktionsloggar noder din blockchain-medlem, totalt tio transaktion noder. Genom att lägga till transaktion noder, kan du öka skalbarheten eller distribuera belastning. Du kan till exempel ha en transaktion noden slutpunkt för olika program.

Lägga till en transaktion nod:

1. I Azure-portalen, navigera till din Azure Blockchain Service-medlem och välj **transaktion noder > Lägg till**.
1. Slutföra inställningarna för den nya noden i transaktionen.

    ![Lägg till nod för transaktion](./media/configure-transaction-nodes/add-node.png)

    | Inställning | Beskrivning |
    |---------|-------------|
    | Namn | Nodnamnet för transaktionen. Namnet används för att skapa DNS-adressen för transaktionen noden slutpunkten. Till exempel `newnode-myblockchainmember.blockchain.azure.com`. Namnet på noden kan inte ändras när den har skapats. |
    | Lösenord | Ange ett starkt lösenord. Använd lösenordet för att komma åt noden transaktion slutpunkten med grundläggande autentisering.

1. Välj **Skapa**.

    Etablera en ny transaktion nod tar cirka 10 minuter. Ytterligare transaktionsloggar noder resultera i kostnader. Mer information om kostnader finns i [prissättning för Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Slutpunkter

Transaktionen noder har ett unikt DNS-namn och offentliga slutpunkter.

Visa information om slutpunkten för en transaktion nod:

1. Navigera till en av dina Azure Blockchain Service medlem transaktion noder i Azure-portalen och välj **översikt**.

    ![Slutpunkter](./media/configure-transaction-nodes/endpoints.png)

Transaktionen noden slutpunkter är säkra och kräver autentisering. Du kan ansluta till en transaktion slutpunkt med hjälp av Azure AD-autentisering, grundläggande autentisering för HTTPS, och med en åtkomstnyckel över HTTPS eller Websocket via SSL.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory Access Control

Azure Blockchain Service transaktion noden slutpunkterna stöder Azure Active Directory (Azure AD)-autentisering. Du kan bevilja Azure AD-användare, grupp och tjänstens huvudnamn åtkomst till slutpunkten.

För att bevilja Azure AD åtkomstkontroll till din slutpunkt:

1. I Azure-portalen går du till din Azure Blockchain Service-medlem och välj **transaktion noder > åtkomstkontroll (IAM) > Lägg till > Lägg till rolltilldelning**.
1. Skapa en ny rolltilldelning för en användare, grupp eller tjänstens huvudnamn (programroller).

    ![Lägg till IAM-roll](./media/configure-transaction-nodes/add-role.png)

    | Inställning | Åtgärd |
    |---------|-------------|
    | Roll | Välj **ägare**, **deltagare**, eller **läsare**.
    | Tilldela behörighet till | Välj **Azure AD-användare, grupp eller tjänstens huvudnamn**.
    | Välj | Sök efter användaren, gruppen eller tjänstens huvudnamn som du vill lägga till.

1. Välj **spara** att lägga till rolltilldelningen.

Läs mer om Azure AD-behörighet, [hantera åtkomst till Azure-resurser med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md)

Mer information om hur du ansluter med hjälp av Azure AD-autentisering finns i [ansluta till noden med hjälp av AAD-autentisering](configure-aad.md).

### <a name="basic-authentication"></a>Grundläggande autentisering

För grundläggande HTTPS-autentisering skickas användarens namn och lösenord för autentiseringsuppgifter i HTTPS-huvudet i begäran till slutpunkten.

Du kan visa information om slutpunkten för en transaktion nod grundläggande autentisering i Azure-portalen. Navigera till en av dina Azure Blockchain Service medlem transaktion noder och markera **grundläggande autentisering** i inställningar.

![Grundläggande autentisering](./media/configure-transaction-nodes/basic.png)

Användarnamnet är namnet på noden och kan inte ändras.

Om du vill använda URL: en \<lösenord\> med det lösenord anges när noden har tillhandahållits. Du kan uppdatera lösenordet genom att välja **Återställ lösenord**.

### <a name="access-keys"></a>Åtkomstnycklar

För åtkomst-nyckelautentisering ingår åtkomstnyckeln i slutpunkts-URL. När transaktionen noden etableras, genereras två åtkomstnycklar. Antingen åtkomstnyckel kan användas för autentisering. Två nycklar kan du ändra och rotera nycklar.

Du kan visa information om en transaktion nod åtkomst nyckel och kopiera slutpunktsadresser som innehåller åtkomstnycklarna. Navigera till en av dina Azure Blockchain Service medlem transaktion noder och markera **åtkomstnycklar** i inställningar.

### <a name="firewall-rules"></a>Brandväggsregler

Brandväggsregler kan du begränsa IP-adresser som kan försöka att autentisera till din nod för transaktionen.  Om inga brandväggsregler har konfigurerats för din nod för transaktionen kan inte nås av part.  

Om du vill visa en transaktion nod brandväggsregler, navigera till en av dina Azure Blockchain Service medlem transaktion noder och välj **brandväggsregler** i inställningar.

Du kan lägga till brandväggsregler genom att ange ett regelnamn, startar IP-adress och en avslutande IP-adressen i den **brandväggsregler** rutnätet.

![Brandväggsregler](./media/configure-transaction-nodes/firewall-rules.png)

För att aktivera:

* **Enskild IP-adress:** Konfigurera samma IP-adress för första och sista IP-adresser.
* **IP-adressintervall:** Konfigurera första och sista IP-adressintervall. Ett intervall med början vid 10.221.34.0 och slutar på 10.221.34.255 skulle till exempel aktivera hela 10.221.34.xxx undernätet.
* **Tillåt alla IP-adresser:** Konfigurera 0.0.0.0 från IP-adressen och 255.255.255.255 avslutande IP-adressen.

## <a name="connection-strings"></a>Anslutningssträngar

Anslutningssträngar för din nod för transaktionen har angetts för grundläggande autentisering eller med åtkomstnycklar. Anslutningssträngar, inklusive åtkomst över HTTPS och WebSockets tillhandahålls.

Du kan visa anslutningssträngar för en transaktion nod och kopiera slutpunktsadresser. Navigera till en av dina Azure Blockchain Service medlem transaktion noder och markera **anslutningssträngar** i inställningar.

![Anslutningssträngar](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Exempelkod

Exempelkod är tillgänglig för att snabbt ansluta till din transaktion nod via Web3, Nethereum, Web3js och Truffle.

Du kan visa en transaktion nod exempelkod för anslutning och kopiera den till att använda med populära utvecklingsverktyg. Navigera till en av dina Azure Blockchain Service medlem transaktion noder och markera **exempelkoden** i inställningar.

Välj fliken Web3 eller Nethereum för att visa kodexempel som du vill använda.

![Exempelkod](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera transaktion noder med Azure CLI](manage-cli.md)
