---
title: Så här konfigurerar du åtkomst till Azure Active Directory
description: Så här konfigurerar du Azure Blockchain-tjänsten med Azure Active Directory-åtkomst
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028222"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Så här konfigurerar du åtkomst till Azure Active Directory

I den här artikeln får lära du att bevilja åtkomst och ansluta till Azure Blockchain Service-noder som använder Azure Active Directory (Azure AD)-användare, grupp eller program-ID: N.

Azure AD tillhandahåller molnbaserade Identitetshantering och kan du använda en enda identitet i en hela företaget och åtkomst i Azure. Azure Blockchain-Service är integrerat med Azure AD och ger fördelar, till exempel ID federation, enkel inloggning och Multi-Factor authentication.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Skapa en blockchain-medlem med Azure portal](create-member.md)

## <a name="grant-access"></a>Bevilja åtkomst

Du kan ge åtkomst på medlemsnivå såväl nivån. Bevilja behörigheter på medlemsnivå kommer i sin tur att bevilja åtkomst till alla noder under medlemmen.

### <a name="grant-member-level-access"></a>Bevilja medlemsåtkomst

Att bevilja behörighet på medlemsnivå.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till **åtkomstkontroll (IAM) > Lägg till > Lägg till rolltilldelning**.
1. Välj den **Blockchain medlem noden åtkomst (förhandsgranskning)** rollen och Lägg till Azure AD-ID-objektet som du vill bevilja åtkomst till. Azure AD-ID-objekt kan vara:

    | Azure AD-objekt | Exempel |
    |-----------------|---------|
    | Azure AD-användare   | `frank@contoso.onmicrosoft.com` |
    | Azure AD-grupp  | `sales@contoso.onmicrosoft.com` |
    | Program-ID:t  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Lägg till rolltilldelning](./media/configure-aad/add-role-assignment.png)

1. Välj **Spara**.

### <a name="grant-node-level-access"></a>Bevilja noden åtkomst

1. Du kan ge åtkomst till nivån genom att gå till nod-säkerhet och klicka på namnet på noden som du vill bevilja åtkomst.
1. Välj rollen Blockchain medlem noden åtkomst (förhandsgranskning) och Lägg till Azure AD-ID-objektet som du vill bevilja åtkomst till. 

## <a name="connect-using-azure-blockchain-connector"></a>Ansluta med Azure Blockchain-kopplingen

Ladda ned eller klona den [Azure Blockchain Connector från GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Följ snabbstarten i avsnittet den **readme** att skapa anslutningen från källkoden.

### <a name="connect-using-an-azure-ad-user-account"></a>Ansluta med hjälp av en Azure AD-användarkonto

1. Kör följande kommando för att autentisera med hjälp av en Azure AD-användarkonto. Ersätt \<myAADDirectory\> med en Azure AD-domän. Till exempel `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD frågar efter autentiseringsuppgifter.
1. Logga in med ditt användarnamn och lösenord.
1. Efter lyckad autentisering ansluter din lokala proxy till blockchain-nod. Nu kan du koppla Geth klienten med den lokala slutpunkten.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Ansluta med hjälp av ett program-ID

Många program autentisera med Azure AD med hjälp av ett program-ID i stället för en Azure AD-användarkonto.

För att ansluta till noden med hjälp av ett program-ID, Ersätt **aadauthcode** med **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parameter | Beskrivning |
|-----------|-------------|
| klient-id | Azure AD-domän, till exempel `yourdomain.onmicrosoft.com`
| klient-id | Klient-ID för det registrerade programmet i Azure AD
| client-secret | Klienthemlighet för det registrerade programmet i Azure AD

Mer information om hur du registrerar ett program i Azure AD finns i [så här: Använda portalen för att skapa en Azure AD-program och tjänstens huvudnamn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Ansluta en mobil enhet eller text-webbläsare

För en mobil enhet eller en textbaserad webbläsare där Azure AD authentication popup-skärmen inte är möjligt, genererar ett engångslösenord i Azure AD. Du kan kopiera lösenordet och fortsätta med Azure AD-autentisering i en annan miljö.

Om du vill generera lösenordet, Ersätt **aadauthcode** med **aaddevice**. Ersätt \<myAADDirectory\> med en Azure AD-domän. Till exempel `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Nästa steg

Mer information om säkerhet i Azure Blockchain Service finns:

> [!div class="nextstepaction"]
> [Azure Blockchain-Service-säkerhet](data-security.md)