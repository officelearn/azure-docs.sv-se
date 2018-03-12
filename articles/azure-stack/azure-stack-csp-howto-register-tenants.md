---
title: "Lägg till klienter för användning och fakturering till Azure-stacken | Microsoft Docs"
description: "De steg som krävs för att lägga till en slutanvändare till Azure-stacken som hanteras av en Molntjänstleverantör."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 67e5a67d7cd5caf6bd4d2625969b139411d62696
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Lägg till klient för användning och fakturering till Azure-stacken

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskrivs vad du behöver lägga till en användare i Azure stapel som hanteras av en molntjänst-providers (CSP). När den nya innehavaren använder resurser, rapporterar Azure Stack användning till CSP prenumerationen.

Kryptografiproviders erbjuda ofta tjänster till flera kunder (klienter) på deras Azure Stack-distribution. Lägger till klienter i Azure Stack-registrering säkerställer att varje klientanvändning ska rapporteras och debiteras den motsvarande CSP-prenumerationen. Om du inte slutför stegen i den här artikeln debiteras klientanvändning till registreringen av Azure Stack-prenumerationen. Innan du kan lägga till en end-kund till Azure-stacken för spårning av användning och för att hantera klienten, behöver du konfigurera Azure-stacken som en CSP. Steg och resurser finns [Hantera användning och fakturering för Azure-stacken som en Molntjänstleverantör](azure-stack-add-manage-billing-as-a-csp.md).

I följande diagram visas de steg som en Kryptografiprovider måste följa för att aktivera en ny kund att använda Azure-stacken och konfigurera spårning för kundens användning. Lägger till slut-kund kan vara du också att hantera resurser i Azure-stacken. Har du två alternativ för att hantera sina resurser:

1. Du kan underhålla slutet kunden klient och ange autentiseringsuppgifter till den lokala Azure Stack-prenumerationen till slutkunden.  
2. Eller end-kund kan arbeta med prenumerationen lokalt och lägga till CSP: N som gäst med ägare.  

**Steg för att lägga till en end-kund**

![Ställa in Molntjänstleverantören för spårning av användning och hantera kundkontot slut](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Skapa en ny kund i Partner Center

Skapa en ny Azure-prenumeration för kunden Partner Center. Instruktioner finns i [lägga till en ny kund](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Skapa en Azure-prenumeration för end-kund

När du har skapat en post till kunden Partner Center kan sälja du dem prenumerationer produkter i katalogen. Instruktioner finns i [skapa, pausa eller avsluta kundprenumerationer](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Skapa gästanvändare i katalogen slutet kunder

Om slutkunden ska hantera sina egna konton, skapa gästanvändare i sina kataloger och skicka dem till informationen. Slutanvändaren sedan lägger till gästen och höjer behörigheten Gäst till **ägare** till Azure-stacken CSP-konto.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Uppdatera registreringen med kundprenumerationen slut

Uppdatera registreringen med nya kundens prenumeration. Azure rapporterar kundens användning med identiteten kunden från Central till Partner. Det här steget säkerställer att varje kunds användning rapporteras under den enskilda CSP kundprenumeration. Detta gör att spåra användaren användning och fakturering är mycket enklare.

> [!Note]  
> Om du vill utföra det här steget måste du ha [registrerade Azure Stack](azure-stack-register.md).

1. Öppna Windows PowerShell med en förhöjd behörighet och kör:  
    `Login-AzureRmAccount`
2. Ange dina autentiseringsuppgifter för Azure.
3. Kör i PowerShell-session:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```
### <a name="new-azurermresource-powershell-parameters"></a>Nya AzureRmResource PowerShell-parametrar
| Parameter | Beskrivning |
| --- | --- | 
|registrationSubscriptionID | Azure-prenumerationen som användes för registreringen av Azure-stacken. |
| customerSubscriptionID | Azure-prenumerationen (inte Azure-Stack) som hör till kunden ska registreras. Måste vara skapas i CSP erbjudandet; i praktiken innebär detta via Partnercenter. Om en kund har mer än en Azure Active Directory-klient, måste den här prenumerationen skapas i innehavaren som ska användas för att logga in på Azure-stacken.
| resourceGroup | Resursgrupp i Azure som är lagrade i din registrering. 
| registrationName | Namnet på registrering av Azure-stacken. Det är ett objekt som lagras i Azure. | 


> [!Note]  
> Klienter måste vara registrerad med varje Azure-stacken som de använder. Om du har två Azure Stack-distributioner och en klient kommer att använda båda, måste du uppdatera de inledande registreringarna för varje distribution med klientprenumeration.

## <a name="onboard-tenant-to-azure-stack"></a>Publicera klient till Azure-stacken

Konfigurera Azure-grupp om du vill hantera användare från flera Azure AD-klienter att använda tjänster i Azure-stacken. Instruktioner finns i [aktivera multitenans i Azure-stacken](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Skapa en lokal resurs i slutet kunden klienten i Azure-stacken

När du har lagt till den nya kunden till Azure-stacken, eller slutet kunden innehavaren har aktiverat gästkonto med privilegier som ägare, kontrollera att du kan skapa en resurs i klientorganisationen. De kan till exempel [skapa en virtuell Windows-dator med Azure Stack-portalen](user\azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Nästa steg

 - Felmeddelanden om de har utlösts i din registreringsprocessen finns [klient registrering felmeddelanden](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Läs mer om hur du hämtar information om användning från Azure-stacken i [användnings- och fakturering i Azure-stacken](/azure-stack-billing-and-chargeback.md).
 - Om du vill granska hur en end-kund kan lägga till dig, som CSP: N som hanterare för sina Azure Stack-klient, se [aktivera en Molntjänstleverantör att hantera din prenumeration på Azure-stacken](user\azure-stack-csp-enable-billing-usage-tracking.md).