---
title: Konfigurera Azure Active Directory-åtkomst - Azure Blockchain-tjänst
description: Konfigurera Azure Blockchain-tjänsten med Azure Active Directory-åtkomst
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455866"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Konfigurera Azure Active Directory-åtkomst för Azure Blockchain-tjänsten

I den här artikeln får du lära dig hur du beviljar åtkomst och ansluter till Azure Blockchain Service-noder med Azure Active Directory (Azure AD) användare, grupp eller program-ID: er.

Azure AD tillhandahåller molnbaserad identitetshantering och låter dig använda en enda identitet i ett helt företag och komma åt program i Azure. Azure Blockchain Service är integrerad med Azure AD och erbjuder fördelar som ID-federation, enkel inloggning och multifaktorautentisering.

## <a name="prerequisites"></a>Krav

* [Skapa en blockchain-medlem med Azure-portalen](create-member.md)

## <a name="grant-access"></a>Bevilja åtkomst

Du kan bevilja åtkomst på både medlemsnivå och nodnivå. Att bevilja åtkomsträttigheter på medlemsnivå ger i sin tur åtkomst till alla noder under medlemmen.

### <a name="grant-member-level-access"></a>Bevilja åtkomst på medlemsnivå

Så här beviljar du åtkomstbehörighet på medlemsnivå.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till **Access Control (IAM) > Lägg till > Lägg till rolltilldelning**.
1. Välj rollen **Blockchain Member Node Access (Preview)** och lägg till Azure AD ID-objektet som du vill bevilja åtkomst till. Azure AD ID-objekt kan vara:

    | Azure AD-objekt | Exempel |
    |-----------------|---------|
    | Azure AD-användare   | `kim@contoso.onmicrosoft.com` |
    | Azure AD-grupp  | `sales@contoso.onmicrosoft.com` |
    | Program-ID:t  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Lägg till rolltilldelning](./media/configure-aad/add-role-assignment.png)

1. Välj **Spara**.

### <a name="grant-node-level-access"></a>Åtkomst på tilldelning av nodnivå

Du kan bevilja åtkomst på nodnivå genom att navigera till nodsäkerhet och klicka på nodnamnet som du vill bevilja åtkomst.

Välj rollen Blockchain Member Node Access (Preview) och lägg till Azure AD ID-objektet som du vill bevilja åtkomst till.

Mer information finns i [Konfigurera Azure Blockchain Service-transaktionsnoder](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Anslut med Azure Blockchain Connector

Hämta eller klona [Azure Blockchain Connector från GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Följ snabbstartsavsnittet i **readme för** att skapa kopplingen från källkoden.

### <a name="connect-using-an-azure-ad-user-account"></a>Ansluta med ett Azure AD-användarkonto

1. Kör följande kommando för att autentisera med ett Azure AD-användarkonto. Ersätt \<myAADDirectory\> med en Azure AD-domän. Till exempel `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD-frågor om autentiseringsuppgifter.
1. Logga in med ditt användarnamn och lösenord.
1. När din lokala proxy har framgångsrikt anslutits till blockchain-noden. Du kan nu koppla geth-klienten till den lokala slutpunkten.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Ansluta med hjälp av ett program-ID

Många program autentiserar med Azure AD med hjälp av ett program-ID i stället för ett Azure AD-användarkonto.

Om du vill ansluta till noden med hjälp av ett program-ID ersätter du **aadauthcode** med **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parameter | Beskrivning |
|-----------|-------------|
| klient-id | Azure AD-domän, till exempel`yourdomain.onmicrosoft.com`
| klient-id | Klient-ID för det registrerade programmet i Azure AD
| klienthemlighet | Klienthemlighet för det registrerade programmet i Azure AD

Mer information om hur du registrerar ett program i Azure AD finns i [Så här: Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Ansluta en mobil enhet eller textwebbläsare

För en mobil enhet eller textbaserad webbläsare där popup-skärmen Azure AD-autentisering inte är möjlig genererar Azure AD en engångslösenkod. Du kan kopiera lösenkoden och fortsätta med Azure AD-autentisering i en annan miljö.

Om du vill generera lösenkoden ersätter du **aadauthcode** med **aaddevice**. Ersätt \<myAADDirectory\> med en Azure AD-domän. Till exempel `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Nästa steg

Mer information om datasäkerhet i Azure Blockchain Service finns i [Azure Blockchain Service security](data-security.md).