---
title: "Prenumerera på ett erbjudande i Azure-stacken | Microsoft Docs"
description: "Skapa prenumerationer för erbjudanden i Azure-stacken"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Skapa prenumerationer erbjudanden i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

När du [skapa ett erbjudande](azure-stack-create-offer.md), användare behöver en prenumeration på den erbjudanden innan de kan använda den.   
- Du kan skapa en prenumeration för en användare från i administratörsportalen som en moln-operator.  Prenumerationer som du skapar kan vara för både offentliga och privata erbjudanden.
- Som en klient-användare kan du prenumerera på offentliga erbjudandet när du använder användarportalen.  

Följande avsnitt innehåller riktlinjer för molnoperatörer när du skapar prenumerationer för användare och hur du prenumererar på ett erbjudande som en användare.

## <a name="create-a-subscription-as-a-cloud-operator"></a>Skapa en prenumeration som molntjänster operator
Molnoperatörer kan använda administrationsportal för att skapa en prenumeration på ett erbjudande om en användare.  Du kan skapa prenumerationer för medlemmar i en egen directory-klient.  När [multitenans](azure-stack-enable-multitenancy.md) är aktiverad, kan du också skapa prenumerationer för användare i ytterligare directory-klienter.

Du kan skapa prenumerationer för både offentliga och privata erbjudanden.  Om inte vill att klienterna att skapa sina egna prenumerationer, se alla dina privata erbjudanden och sedan skapa prenumerationer åt dina klienter. Den här metoden är vanliga när du integrerar Azure stacken med externa fakturerings- eller tjänsten katalogen system.

När du skapar en prenumeration för en användare som användaren kan logga in på användarportalen och hittar prenumererar på till erbjudandet.  

### <a name="to-create-the-subscription-for-a-user"></a>Skapa prenumeration för en användare
1.  Gå till i administrationsportal **användaren prenumerationer.**
2.  Välj **Lägg till** att öppna den **användaren prenumeration** fönstret. Här kan du ange följande information:  

  a. **Visningsnamn** – ett eget namn för att identifiera prenumerationen som visas som den *prenumeration användarnamn*.

  b. **Användaren** – ange en användare från en tillgänglig directory-klient för den här prenumerationen. Användarnamnet visas som *ägare*.  Formatet på användarnamnet beror på din identitetslösning. Exempel:   

     -  **Azure AD:***&lt;Användare1 > @&lt;contoso.onmicrosoft.com >* 

     -   **AD FS:**  *&lt;user1>@&lt;azurestack.local>*     

  c.    **Directory-klient** – Välj directory-klient där användarkontot tillhör. Om du inte har aktiverat multitenans är bara din lokala katalog-klient tillgänglig.

3.  Välj **erbjuder** att öppna den **erbjuder** fönstret och välj sedan en **erbjuder** för den här prenumerationen. Eftersom du skapar en prenumeration för en användare väljer du ett privat eller offentlig erbjudande.

4.  Välj **skapa** att skapa prenumerationen. Den **användaren prenumerationer** fönstret nu visas den nya prenumerationen.  Senare, när användaren loggar in på användarportalen, kan visa information om den här prenumerationen.

### <a name="to-make-an-add-on-plan-available"></a>Att tillhandahålla en plan för tillägg  
En operator i molnet kan lägga till en plan för tillägg till en tidigare skapad prenumeration när som helst:   
1.  I administrationsportal går du till **fler tjänster** > **användaren prenumerationer**, och sedan väljer du den prenumeration som du vill ändra.   

2.  Välj **tillägg** > **Lägg till** att öppna den **Lägg till plan** fönstret.  

3.  Välj den plan som du vill lägga till som ett tillägg.  Konsolen visar sedan planer som är associerade med prenumerationen.




## <a name="create-a-subscription-as-a-user"></a>Skapa en prenumeration som en användare
Som en användare kan du logga in på användarportalen för att leta upp och prenumerera på offentliga erbjudanden och tillägg planer från directory-klient (organisationen). När Azure Stack-miljön stöder [multitenans](azure-stack-enable-multitenancy.md) du kan prenumerera på erbjudanden från en fjärrkatalog-klient.

### <a name="to-subscribe-to-an-offer"></a>Prenumerera på ett erbjudande
1. [Logga in](azure-stack-connect-azure-stack.md) på Azure-stacken användarportalen (https://portal.local.azurestack.external) och klicka på **skaffa en prenumeration**.

   ![Skaffa en prenumeration](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. I den **visningsnamn** fältet, Skriv ett namn för din prenumeration, klickar du på **erbjuder**, klicka på någon av erbjudanden i den **väljer du ett erbjudande** rutan och klicka sedan på  **Skapa**.

   ![Skapa ett erbjudande](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Så här visar du skapade prenumerationen **fler tjänster**, klickar du på **prenumerationer**, klicka på den nya prenumerationen.  

När du prenumererar på ett erbjudande uppdatera portalen om du vill se vilka tjänster som är en del av den nya prenumerationen.

### <a name="to-subscribe-to-an-add-on-plan"></a>Prenumerera på en plan för tillägg
Om ett erbjudande har en plan för tillägg, kan du lägga till planen prenumerationen när som helst.  

1. Välj i användarportalen, **fler tjänster** > **prenumerationer**, och sedan väljer du den prenumeration som du vill ändra.

2. Välj **lägga till Plan** och välj tillägg planen som du vill använda. Om **Lägg till plan** är inte tillgänglig, det finns inga tillägg planer för erbjudande som är associerade med den prenumerationen.



## <a name="next-steps"></a>Nästa steg
[Etablera en virtuell dator](azure-stack-provision-vm.md)
