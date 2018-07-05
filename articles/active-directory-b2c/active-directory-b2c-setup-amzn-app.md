---
title: Amazon konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Ge registrera dig och logga in till konsumenter med Amazon konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e3b3d66b913b595e68c03b68990d1a4806952579
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443718"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med Amazon-konton
## <a name="create-an-amazon-application"></a>Skapa ett Amazon-program
För att använda Amazon som en identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett Amazon-program och ange rätt parametrar. Du behöver en Amazon-konto för att göra detta. Om du inte har någon kan du hämta den på [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Gå till den [Amazon Developer Center](https://login.amazon.com/) och logga in med autentiseringsuppgifterna för ditt Amazon.
2. Om du inte redan har gjort det, klickar du på **registrera dig**, av utvecklare registrering anvisningarna och acceptera principen.
3. Klicka på **registrera nya program**.
   
    ![Registrera ett nytt program på Amazon-webbplatsen](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Ange programinformation (**namn**, **beskrivning**, och **meddelande Sekretesswebbadress**) och klicka på **spara**.
   
    ![Att tillhandahålla programinformationen för att registrera ett nytt program på Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. I den **webbinställningar** och kopiera värdena för **klient-ID** och **Klienthemlighet**. (Du måste klicka på den **visa hemligheten** för att se detta.) Du måste båda för att konfigurera Amazon som en identitetsprovider i din klient. Klicka på **redigera** längst ned i avsnittet. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
   
    ![Med klient-ID och Klienthemlighet för det nya programmet på Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Ange `https://login.microsoftonline.com` i den **tillåtna ursprung för JavaScript** fält och `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **tillåtna returnerar URL: er** fält. Ersätt **{klient}** med klientens namn (till exempel contoso.onmicrosoft.com). Klicka på **Spara**. Den **{klient}** värdet är skiftlägeskänsligt.
   
    ![Med JavaScript ursprung och returnera URL: er för det nya programmet på Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Konfigurera Amazon som en identitetsprovider i din klient
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för konfigurationen av identity-providern. Till exempel ange ”Amzn”.
5. Klicka på **identifiera providertyp**väljer **Amazon**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern** och ange klient-ID och klienthemlighet för Amazon-programmet som du skapade tidigare.
7. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Amazon.

