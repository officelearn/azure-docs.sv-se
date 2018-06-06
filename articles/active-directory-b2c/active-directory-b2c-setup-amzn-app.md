---
title: Konfigurationen för Amazon i Azure Active Directory B2C | Microsoft Docs
description: Ange registrering och inloggning för konsumenter med Amazon-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9b3464e246a6b672362583ee4446a6146cc3b3d8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709758"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med Amazon-konton
## <a name="create-an-amazon-application"></a>Skapa ett Amazon-program
Om du vill använda Amazon som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett Amazon-program och ange rätt parametrar. Du behöver ett Amazon-konto för att göra detta. Om du inte har något du kan hämta den på [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Gå till den [Amazon Developer Center](https://login.amazon.com/) och logga in med autentiseringsuppgifterna för ditt Amazon-konto.
2. Om du inte redan har gjort det, klickar du på **registrera dig**gör developer registrering och acceptera principen.
3. Klicka på **registrera nya program**.
   
    ![Registrera en ny App på Amazon-webbplatsen](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Ange programinformationen (**namn**, **beskrivning**, och **meddelande Sekretesswebbadress**) och klicka på **spara**.
   
    ![Tillhandahåller information om programmet för att registrera en ny App på Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. I den **webbinställningar** avsnittet, kopiera värdena för **klient-ID** och **Klienthemlighet**. (Du måste klicka på den **visa hemlighet** för att visa detta.) Du måste båda för att konfigurera Amazon som en identitetsleverantör i din klient. Klicka på **redigera** längst ned i avsnittet. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
   
    ![Med klient-ID och Klienthemlighet för det nya programmet på Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Ange `https://login.microsoftonline.com` i den **tillåtna JavaScript ursprung** fält och `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **tillåtna returnerar URL: er** fältet. Ersätt **{klient}** med din klient namn (till exempel contoso.onmicrosoft.com). Klicka på **Spara**. Den **{klient}** värdet är skiftlägeskänsligt.
   
    ![Med JavaScript ursprung och returnera URL: er för det nya programmet på Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Konfigurera Amazon som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Till exempel ange ”Amzn”.
5. Klicka på **identitet providertyp**väljer **Amazon**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantör** och ange klient-ID och klienthemlighet för Amazon-program som du skapade tidigare.
7. Klicka på **OK** och klicka sedan på **skapa** spara Amazon-konfigurationen.

