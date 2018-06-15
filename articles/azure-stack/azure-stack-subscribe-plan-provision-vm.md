---
title: Prenumerera på ett erbjudande i Azure-stacken | Microsoft Docs
description: Skapa prenumerationer för erbjudanden i Azure-stacken
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295218"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Skapa prenumerationer erbjudanden i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

När du [skapa ett erbjudande](azure-stack-create-offer.md), användare behöver en prenumeration på den erbjudanden innan de kan använda den. Det finns två sätt att användare kan hämta prenumererar på ett erbjudande:

- Du kan skapa en prenumeration för en användare från i administratörsportalen som en moln-operator. Prenumerationer som du skapar kan vara för både offentliga och privata erbjudanden.
- Som en klient-användare kan du prenumerera på offentliga erbjudandet när du använder användarportalen.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Skapa en prenumeration som molntjänster operator

Molnoperatörer kan använda administrationsportal för att skapa en prenumeration på ett erbjudande om en användare.  Du kan skapa prenumerationer för medlemmar i en egen directory-klient.  När [multitenans](azure-stack-enable-multitenancy.md) är aktiverad, kan du också skapa prenumerationer för användare i ytterligare directory-klienter.

Om inte vill att klienterna att skapa sina egna prenumerationer, göra dina erbjudanden privata och sedan skapa prenumerationer för dina klienter. Den här metoden är vanliga när du integrerar Azure stacken med externa fakturerings- eller tjänsten katalogen system.

När du skapar en prenumeration för en användare kan de logga in på användarportalen och se att de är prenumererar på erbjudandet.  

### <a name="to-create-a-subscription-for-a-user"></a>Skapa en prenumeration för en användare

1. Gå till i administrationsportal **användaren prenumerationer.**
2. Välj **Lägg till**. Under **användaren prenumeration**, anger du följande information:  

   - **Visningsnamn** – ett eget namn för att identifiera prenumerationen som visas som den *prenumeration användarnamn*.
   - **Användaren** – ange en användare från en tillgänglig directory-klient för den här prenumerationen. Användarnamnet visas som *ägare*.  Formatet på användarnamnet beror på din identitetslösning. Exempel:

     - **Azure AD:***&lt;Användare1 > @&lt;contoso.onmicrosoft.com >* 

     - **AD FS:***&lt;Användare1 > @&lt;azurestack.local >* 

   - **Directory-klient** – Välj directory-klient där användarkontot tillhör. Om du inte har aktiverat multitenans är bara din lokala katalog-klient tillgänglig.

3. Välj **erbjuder**. Under **erbjuder**, Välj en **erbjuder** för den här prenumerationen. Eftersom du skapar prenumerationen för en användare kan välja **privata** som hjälpmedel tillstånd.

4. Välj **skapa** att skapa prenumerationen. Du ser den nya prenumerationen under **användarens prenumeration**. När användaren loggar in på användarportalen kan de se prenumerationsinformation.

### <a name="to-make-an-add-on-plan-available"></a>Att tillhandahålla en plan för tillägg

En operator i molnet kan lägga till en plan för tillägg till en tidigare skapad prenumeration när som helst:

1. Välj i administrationsportal **fler tjänster** > **användaren prenumerationer**. Välj den prenumeration som du vill ändra.

2. Välj **tillägg** och välj sedan **+ Lägg till**.  

3. Under **Lägg till plan**, Välj den plan som du vill använda som ett tillägg.

## <a name="create-a-subscription-as-a-user"></a>Skapa en prenumeration som en användare

Som en användare kan du logga in på användarportalen för att leta upp och prenumerera på offentliga erbjudanden och tillägg planer för directory-klient (organisationen).

>[!NOTE]
>Om Azure Stack-miljön stöder [multitenans](azure-stack-enable-multitenancy.md) du kan även prenumerera på erbjudanden från en fjärrkatalog-klient.

### <a name="to-subscribe-to-an-offer"></a>Prenumerera på ett erbjudande

1. [Logga in](azure-stack-connect-azure-stack.md) till användarportalen Azure Stack (https://portal.local.azurestack.external) och välj **skaffa en prenumeration**.

   ![Skaffa en prenumeration](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Under **skaffa en prenumeration**, ange det egna namnet för prenumerationen i **visningsnamn**. Välj **erbjuder** och under **väljer du ett erbjudande**, Välj ett erbjudande. Välj **skapa** att skapa prenumerationen.

   ![Skapa ett erbjudande](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. När du prenumererar på ett erbjudande uppdatera portalen om du vill se vilka tjänster som är en del av den nya prenumerationen.
4. Om du vill se den prenumeration som du har skapat, Välj **fler tjänster** och välj sedan **prenumerationer**. Välj prenumerationen visas prenumerationsinformation.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Prenumerera på en plan för tillägg

Om ett erbjudande har en plan för tillägg, kan du lägga till planen prenumerationen när som helst.  

1. Välj i användarportalen, **fler tjänster** > **prenumerationer**, och sedan väljer du den prenumeration som du vill ändra. Om alla tillägg planer är tillgängliga **+ Lägg till plan** är aktiv och det finns en panel för **tillägg planer**.

   >[!NOTE]
   >Om **+ Lägg till plan** är inte aktiv, och det inte alla tillägg planer för erbjudande som är kopplade till den prenumerationen.

1. Välj **+ Lägg till plan** eller **tillägg planer** panelen. Under **tillägg planer**, Välj den plan som du vill lägga till.

## <a name="next-steps"></a>Nästa steg

[Etablera en virtuell dator](azure-stack-provision-vm.md)
