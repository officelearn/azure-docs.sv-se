---
title: "Prenumerera på ett erbjudande | Microsoft Docs"
description: "Lär dig hur du prenumererar på ett erbjudande som en användare."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/03/2017
ms.author: erikje
ms.openlocfilehash: f70815b5e89753a4b0083ffbe10d9920062d1ff0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="subscribe-to-an-offer"></a>Prenumerera på ett erbjudande

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Nu när du har [skapas ett erbjudande](azure-stack-create-offer.md), testa att användarna kan skapa en prenumeration.

1. [Logga in](azure-stack-connect-azure-stack.md) på Azure-stacken användarportalen (https://portal.local.azurestack.external) och klicka på **skaffa en prenumeration**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. I den **visningsnamn** fältet, Skriv ett namn för din prenumeration, klickar du på **erbjuder**, klicka på någon av erbjudanden i den **väljer du ett erbjudande** bladet och klicka sedan på  **Skapa**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Så här visar du skapade prenumerationen **fler tjänster**, klickar du på **prenumerationer**, klicka på den nya prenumerationen.  

När du prenumererar på ett erbjudande uppdatera portalen om du vill se vilka tjänster som är en del av den nya prenumerationen.

## <a name="subscribe-to-an-add-on-plan"></a>Prenumerera på en plan för tillägg
Om erbjudandet har en plan för tillägg, kan användare lägga till prenumerationen när som helst.  

1. Välj i användarportalen, **fler tjänster** > **prenumerationer**.

2. Klicka på prenumerationen > **lägga till Plan** och välj tillägg planen.



## <a name="next-steps"></a>Nästa steg
[Etablera en virtuell dator](azure-stack-provision-vm.md)
