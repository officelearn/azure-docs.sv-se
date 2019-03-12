---
title: Prenumerera på ett erbjudande i Azure Stack | Microsoft Docs
description: Skapa prenumerationer för erbjudanden i Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: ba1232bc28d638d1e4293b5e6bf30b84b23e4951
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761131"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Skapa prenumerationer för erbjudanden i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

När du [skapa ett erbjudande](azure-stack-create-offer.md), användarna behöver en prenumeration på erbjudandet innan de kan använda. Det finns två sätt att användare kan få prenumererar på ett erbjudande:

- Du kan skapa en prenumeration för en användare från i administratörsportalen som en cloud-operator. Prenumerationer som du skapar kan vara både offentliga och privata erbjudanden.
- Som en klientanvändare prenumererar du på ett erbjudande till allmänheten när du använder användarportalen.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Skapa en prenumeration som en cloud-operator

Molnoperatörer kan använda administrationsportalen för att skapa en prenumeration på ett erbjudande om en användare. Du kan skapa prenumerationer för medlemmar i din egen directory-klient. När [multitenans](azure-stack-enable-multitenancy.md) är aktiverad, kan du också skapa prenumerationer för användare i ytterligare directory-klienter.

Om inte vill att klienterna kommer att skapa sina egna prenumerationer, gör dina erbjudanden privat och sedan skapa prenumerationer för dina klienter. Den här metoden är vanlig när du integrerar Azure Stack med externa fakturerings- eller tjänsten katalogsystem.

När du skapar en prenumeration för en användare kan de logga in på användarportalen och se att de prenumererar på erbjudandet.  

### <a name="to-create-a-subscription-for-a-user"></a>Skapa en prenumeration för en användare

1. I Admin portal går du till **användarprenumerationer.**
2. Välj **Lägg till**. Under **ny användarprenumeration**, anger du följande information:  

   - **Visningsnamn** – ett eget namn för att identifiera prenumerationen som visas i den *namn på användarens prenumeration*.
   - **Användaren** – ange en användare från en tillgänglig directory-klient för den här prenumerationen. Användarnamnet som visas som *ägare*.  Formatet på användarnamnet är beroende av ID-lösning. Exempel:

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Directory-klient** – Välj directory-klient som användaren tillhör. Om du inte har aktiverat flera innehavare, finns bara i din lokala katalog-klient.

3. Välj **erbjuder**. Under **erbjuder**, Välj en **erbjuder** för den här prenumerationen. Eftersom du skapar prenumerationen för en användare kan välja **privata** som tillstånd för hjälpmedel.

4. Välj **skapa** att skapa prenumerationen. Den nya prenumerationen visas under **användarprenumeration**. När användaren loggar in på användarportalen, kan de se i prenumerationsinformationen.

### <a name="to-make-an-add-on-plan-available"></a>Att tillgängliggöra en tilläggsplan

En molnoperator kan lägga till en plan till en tidigare skapad prenumeration när som helst:

1. Välj i administrationsportalen **alla tjänster** och sedan under den **ADMINISTRATIONSRESURSER** kategori, väljer **användarprenumerationer**. Välj den prenumeration som du vill ändra.

2. Välj **tillägg** och välj sedan **+ Lägg till**.  

3. Under **Lägg till plan**, Välj den plan som du vill som ett tillägg.

## <a name="create-a-subscription-as-a-user"></a>Skapa en prenumeration som en användare

Som en användare kan du logga in på användarportalen för att hitta och prenumerera på offentliga erbjudanden och tilläggsplaner för directory-klienten (organisation).

>[!NOTE]
>Om Azure Stack-miljön stöder [multitenans](azure-stack-enable-multitenancy.md), du kan även prenumerera erbjudanden från en fjärrkatalog-klient.

### <a name="to-subscribe-to-an-offer"></a>Prenumerera på ett erbjudande

1. [Logga in](azure-stack-connect-azure-stack.md) till den [användarportalen för Azure Stack](https://portal.local.azurestack.external) och välj **skaffa en prenumeration**.

   ![Skaffa en prenumeration](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Under **skaffa en prenumeration**, ange det egna namnet för prenumerationen i **visningsnamn**. Välj **erbjuder** och under **Välj ett erbjudande**, Välj ett erbjudande. Välj **skapa** att skapa prenumerationen.

   ![Skapa ett erbjudande](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. När du prenumererar på ett erbjudande kan du uppdatera portalen för att se vilka tjänster som en del av den nya prenumerationen.

4. Om du vill se prenumerationen du skapade **alla tjänster** och sedan under den **Allmänt** kategori väljer **prenumerationer**. Välj prenumerationen du vill ha prenumerationsinformation.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Prenumerera på en tilläggsplan

Om ett erbjudande har en tilläggsplan, kan du lägga till den här planen prenumerationen när som helst.  

1. Välj i användarportalen, **alla tjänster**. Sedan under den **Allmänt** kategori, väljer **prenumerationer**, och välj sedan den prenumeration du vill ändra. Om det finns några tilläggsplaner, **+ Lägg till plan** är aktiv och det finns en panel för **tilläggsplaner**. 

   Om **+ Lägg till plan** är inte aktiv, så det finns inga tilläggsplaner för erbjudande som är associerade med den aktuella prenumerationen.

1. Välj **+ Lägg till plan** eller **tilläggsplaner** panelen. Under **tilläggsplaner**, Välj den plan som du vill lägga till.

## <a name="next-steps"></a>Nästa steg

- [Etablera en virtuell dator](azure-stack-provision-vm.md)
