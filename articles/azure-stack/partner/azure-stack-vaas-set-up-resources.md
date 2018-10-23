---
title: Självstudie – konfigurera resurser för verifiering som en tjänst | Microsoft Docs
description: I de här självstudierna lär du dig hur du ställer in resurser för verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: d9406032a55c0bbd73bf16ae2f0fa272dddd7698
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651739"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Självstudie: Konfigurera resurser för verifiering som en tjänst

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Du måste skapa en lösning. En verifiering som en tjänstlösning (VaaS) representerar en Azure Stack-lösning med en viss maskinvara struktur. Du kommer använda lösningen för att kontrollera om maskinvaran har stöd för kör Azure Stack. Följ den här kursen och gör dig redo att använda tjänsten med din lösning.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Gör dig redo att använda VaaS genom att konfigurera Azure AD (Azure AD)-instans.
> * Skapa ett lagringskonto.

## <a name="configure-an-azure-ad-tenant"></a>Konfigurera en Azure AD-klient

En Azure AD-klient krävs för autentisering och registrera dig hos VaaS. Rollbaserad åtkomstkontroll (RBAC) funktioner för klienten används av partnern för att hantera vilka i partnerorganisationen kan använda VaaS.

Registrera din organisations Azure AD-klient katalog (i stället för Azure AD-klient-katalog som används för Azure Stack) och upprätta en princip för att hantera användarkonton i den. Mer information finns i [hantera Azure AD-katalogen](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

### <a name="create-a-tenant"></a>Skapa en klient

Skapa en klient som ska användas med VaaS med ett beskrivande namn, till exempel `ContosoVaaS@onmicrosoft.com`.

1. Skapa en Azure AD-klient i den [Azure-portalen](https://portal.azure.com), eller Använd en befintlig klient. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Lägg till medlemmar i din organisation för klienten. Dessa användare är ansvarig för att använda tjänsten för att visa eller schemalägga testerna. När du är klar med registreringen, definierar du användarnas åtkomstnivåer.
 
    Auktorisera användare i din klient för att köra åtgärder i VaaS genom att tilldela någon av följande roller:

    | Rollnamn | Beskrivning |
    |---------------------|------------------------------------------|
    | Ägare | Har fullständig åtkomst till alla resurser. |
    | Läsare | Kan visa alla resurser men inte skapa eller hantera. |
    | Test-deltagare | Kan skapa och hantera test-resurser. |

    Tilldela roller i den **Azure Stack-verifiering Service** program:

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Välj **alla tjänster** > **Azure Active Directory** under den **identitet** avsnittet.
    3. Välj **företagsprogram** > **Azure Stack-verifiering Service** program.
    4. Välj **Användare och grupper**. Den **Azure Stack-verifiering Service - användare och grupper** bladet visar en lista över användare med behörighet att använda programmet.
    5. Välj **+ Lägg till användare** lägga till en användare från din klient och tilldela en roll.
   
    Om du vill isolera VaaS resurser och åtgärder mellan olika grupper inom en organisation kan du skapa flera kataloger i Azure AD-klient.

### <a name="register-your-tenant"></a>Registrera din klient

Den här processen tillåter din klient med den **Azure Stack-verifiering Service** Azure AD-program.

1. Skicka följande information om klienten till Microsoft på [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

    | Data | Beskrivning |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Organisationens namn | Det officiella organisationsnamnet. |
    | Katalognamnet för Azure AD-klient | Azure AD-klient katalognamnet håller på att registreras. |
    | Katalog-ID för Azure AD-klient | Katalogen Azure AD-klient GUID som är kopplade till katalogen. Information om hur du hittar din katalog-ID för Azure AD-klient finns i [hämta klient-ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). |

2. Vänta på bekräftelse från Azure Stack-verifieringsteamet att kontrollera att din klient kan använda VaaS-portalen.

### <a name="consent-to-the-vaas-application"></a>Samtyck till VaaS-program

Azure AD-administratör ge VaaS Azure AD-programmet behörigheterna som krävs för din klient:

1. Använd de autentiseringsuppgifter som global administratör för klienten för att logga in på den [VaaS portal](https://azurestackvalidation.com/). 

2. Välj **mitt konto**.

3 acceptera villkoren för att gå vidare när du uppmanas att bevilja VaaS listade Azure AD.

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Under testkörning av VaaS matar du ut diagnostikloggar till ett Azure Storage-konto. Förutom loggar testet, kan storage-konto också användas för att ladda upp OEM-tilläggspaket för paketet valideringsarbetsflödet.

Azure Storage-konto finns i Azure offentligt moln, inte på Azure Stack-miljön.

1. I Azure-portalen väljer du **alla tjänster** > **Storage** > **lagringskonton**. På den **Lagringskonton** bladet väljer **Lägg till**.

2. Välj den prenumeration där du vill skapa lagringskontot.

3. Under **resursgrupp**väljer **Skapa ny**. Ange ett namn för din nya resursgrupp.

4. Ange ett namn för lagringskontot. Det namn du väljer måste vara:
    - Unikt i Azure
    - Mellan 3 och 24 tecken
    - Endast innehålla siffror och gemener

5. Välj den **västra USA** region för ditt lagringskonto.

    För att säkerställa att nätverkets inte debitering för att lagra loggar kan Azure Storage-kontot kan konfigureras att bara använda den **västra USA** region. Replikering av data och funktionen frekvent nivå krävs inte för dessa data. Antingen funktionen aktiveras ökar dramatiskt dina kostnader.

6. Lämna inställningarna till standardvärden undantag för **typ av konto**:

    - Den **distributionsmodellen** anges till **Resource Manager** som standard.
    - Fältet **Prestanda** är inställt på **Standard** som standard.
    - Välj **typ av konto** fältet **Blob-lagring**.
    - Den **replikering fältet** är inställd på **lokalt redundant lagring (LRS)** som standard.
    - **Åtkomstnivå** är inställt på **Frekvent** som standard.

7. Klicka på **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.

## <a name="next-steps"></a>Nästa steg

Om din miljö inte tillåter inkommande anslutningar, följ självstudien om hur du distribuerar lokal agent om du vill köra ett test på din maskinvara.

> [!div class="nextstepaction"]
> [Distribuera lokal agent](azure-stack-vaas-local-agent.md)