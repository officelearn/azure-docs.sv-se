---
title: Konfigurera transaktionsnoder för Azure Blockchain-tjänst
description: Konfigurera Azure Blockchain Service-transaktionsnoder
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 4a9a4f660dd171e65b600ec4cd66714ca476b091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252264"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurera transaktionsnoder för Azure Blockchain-tjänst

Transaktionsnoder används för att skicka blockchain-transaktioner till Azure Blockchain Service via en offentlig slutpunkt. Standardtransaktionsnoden innehåller den privata nyckeln för Ethereum-kontot som är registrerat på blockchain- -och kan därför inte tas bort.

Så här visar du standardinformation för transaktionsnod:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till din Azure Blockchain Service-medlem. Välj **Transaktionsnoder**.

    ![Välj standardtransaktionsnod](./media/configure-transaction-nodes/nodes.png)

    Översiktsinformation inkluderar offentliga slutpunktsadresser och offentlig nyckel.

## <a name="create-transaction-node"></a>Skapa transaktionsnod

Du kan lägga till upp till nio ytterligare transaktionsnoder till din blockchain-medlem för totalt 10 transaktionsnoder. Genom att lägga till transaktionsnoder kan du öka skalbarheten eller distribuera belastningen. Du kan till exempel ha en slutpunkt för transaktionsnod för olika klientprogram.

Så här lägger du till en transaktionsnod:

1. I Azure-portalen navigerar du till din Azure Blockchain-tjänstmedlem och väljer **Transaktionsnoder > Lägg till**.
1. Slutför inställningarna för den nya transaktionsnoden.

    ![Lägg till transaktionsnod](./media/configure-transaction-nodes/add-node.png)

    | Inställning | Beskrivning |
    |---------|-------------|
    | Namn | Namn på transaktionsnod. Namnet används för att skapa DNS-adressen för slutpunkten för transaktionsnoden. Till exempel `newnode-myblockchainmember.blockchain.azure.com`. Nodnamnet kan inte ändras när det har skapats. |
    | lösenord | Ange ett starkt lösenord. Använd lösenordet för att komma åt slutpunkten för transaktionsnod med grundläggande autentisering.

1. Välj **Skapa**.

    Det tar cirka 10 minuter att etablera en ny transaktionsnod. Ytterligare transaktionsnoder medför kostnad. Mer information om kostnader finns i [Azure-priser](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Slutpunkter

Transaktionsnoder har ett unikt DNS-namn och offentliga slutpunkter.

Så här visar du slutpunktsinformation för en transaktionsnod:

1. I Azure-portalen navigerar du till en av dina Azure Blockchain-tjänstmedlemstransaktionsnoder och väljer **Översikt**.

    ![Slutpunkter](./media/configure-transaction-nodes/endpoints.png)

Slutpunkter för transaktionsnoder är säkra och kräver autentisering. Du kan ansluta till en transaktionsslutpunkt med Azure AD-autentisering, HTTPS-grundläggande autentisering och använda en åtkomstnyckel via HTTPS eller Websocket via SSL.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory-åtkomstkontroll

Azure Blockchain Service-transaktionsnodslutpunkter stöder Azure Active Directory -autentisering (Azure AD). Du kan bevilja Azure AD-användare, grupp och tjänsthuvudnamn åtkomst till din slutpunkt.

Så här beviljar du Azure AD-åtkomstkontroll till slutpunkten:

1. I Azure-portalen navigerar du till din Azure Blockchain-tjänstmedlem och väljer **Transaktionsnoder > IAM -kontroll (Access Control) > Lägg till > Lägg till rolltilldelning**.
1. Skapa en ny rolltilldelning för en användare, grupp eller tjänsthuvudnamn (programroller).

    ![Lägg till IAM-roll](./media/configure-transaction-nodes/add-role.png)

    | Inställning | Åtgärd |
    |---------|-------------|
    | Roll | Välj **Ägare,** **Deltagare**eller **Läsare**.
    | Tilldela åtkomst till | Välj **Azure AD-användare, grupp eller tjänsthuvudnamn**.
    | Välj | Sök efter användaren, gruppen eller tjänsthuvudhuvudet som du vill lägga till.

1. Välj **Spara** om du vill lägga till rolltilldelningen.

Mer information om Azure AD-åtkomstkontroll finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md)

Mer information om hur du ansluter med Azure AD-autentisering finns i [Ansluta till noden med AAD-autentisering](configure-aad.md).

### <a name="basic-authentication"></a>Grundläggande autentisering

För HTTPS grundläggande autentisering skickas användarnamn och lösenordsuppgifter i HTTPS-huvudet för begäran till slutpunkten.

Du kan visa en transaktionsnods grundläggande autentiseringsslutpunktsinformation i Azure-portalen. Navigera till en av dina Azure Blockchain-tjänstmedlemstransaktionsnoder och välj **Grundläggande autentisering** i inställningar.

![Grundläggande autentisering](./media/configure-transaction-nodes/basic.png)

Användarnamnet är namnet på noden och kan inte ändras.

Om du vill \<använda\> URL:en ersätter du lösenordet med lösenordsuppsättningen när noden etablerades. Du kan uppdatera lösenordet genom att välja **Återställ lösenord**.

### <a name="access-keys"></a>Åtkomstnycklar

För autentisering av åtkomstnyckel ingår åtkomstnyckeln i slutpunkts-URL:en. När transaktionsnoden etableras genereras två åtkomstnycklar. Båda åtkomstnyckeln kan användas för autentisering. Med två tangenter kan du ändra och rotera tangenter.

Du kan visa åtkomstnyckelinformation för en transaktionsnod och kopiera slutpunktsadresser som innehåller åtkomstnycklarna. Navigera till en av dina Azure Blockchain-tjänstmedlemstransaktionsnoder och välj **Åtkomstnycklar** i inställningarna.

### <a name="firewall-rules"></a>Brandväggsregler

Med brandväggsregler kan du begränsa de IP-adresser som kan försöka autentisera till transaktionsnoden.  Om inga brandväggsregler har konfigurerats för transaktionsnoden kan den inte nås av någon part.  

Om du vill visa en transaktionsnods brandväggsregler navigerar du till en av dina Azure Blockchain-tjänstmedlemstransaktionsnoder och väljer **Brandväggsregler** i inställningar.

Du kan lägga till brandväggsregler genom att ange ett regelnamn, starta IP-adress och en slut-IP-adress i rutnätet **Brandväggsregler.**

![Brandväggsregler](./media/configure-transaction-nodes/firewall-rules.png)

Så här aktiverar du:

* **Enda IP-adress:** Konfigurera samma IP-adress för start- och slut-IP-adresser.
* **IP-adressintervall:** Konfigurera start- och slut-IP-adressintervallet. Ett intervall som börjar på 10.221.34.0 och slutar vid 10.221.34.255 aktiverar till exempel hela 10.221.34.xxx undernät.
* **Tillåt alla IP-adresser:** Konfigurera start-IP-adressen till 0.0.0.0 och den avslutande IP-adressen till 255.255.255.255.

## <a name="connection-strings"></a>Anslutningssträngar

Syntax för anslutningssträng för transaktionsnoden tillhandahålls för grundläggande autentisering eller med hjälp av åtkomstnycklar. Anslutningssträngar inklusive åtkomstnycklar via HTTPS och WebSockets tillhandahålls.

Du kan visa en transaktionsnods anslutningssträngar och kopiera slutpunktsadresser. Navigera till en av dina Azure Blockchain-tjänstmedlemstransaktionsnoder och välj **Anslutningssträngar** i inställningar.

![Anslutningssträngar](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Exempelkod

Exempelkod tillhandahålls för att snabbt aktivera anslutning till transaktionsnoden via Web3, Nethereum, Web3js och Tryffel.

Du kan visa en transaktionsnods exempelanslutningskod och kopiera den till populära utvecklarverktyg. Gå till en av dina Azure Blockchain-tjänstmedlemstransaktionsnoder och välj **Exempelkod** i inställningarna.

Välj fliken Web3, Nethereum, Tryffel eller Web3j om du vill visa det kodexempel som du vill använda.

![Exempelkod](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera transaktionsnoder med Azure CLI](manage-cli.md)
