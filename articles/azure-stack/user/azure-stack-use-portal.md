---
title: "Med hjälp av Azure Stack-portalen | Microsoft Docs"
description: "Lär dig hur du använder användarportalen i Azure-stacken."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 7c34d7a225be63da95f664525b0366ff89b28838
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="using-the-azure-stack-portal"></a>Med hjälp av Azure Stack-portalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda Azure Stack-portalen för att prenumerera på offentliga erbjudanden och använda de tjänster som är tillgängliga via dessa erbjudanden som en konsument Azure Stack-tjänster. Om du har använt Azure-portalen innan är du redan bekant med användargränssnittet.

## <a name="access-the-portal"></a>Åtkomst till portalen

Azure Stack-operatorn (en tjänsteleverantör eller en administratör i din organisation), informerar dig om att få åtkomst till portalen rätt URL. 

- URL: en för ett integrerat system varierar beroende på din operatorn region och externa domännamn och kommer att vara i formatet https://portal. &lt; *region*&gt;.&lt; *FQDN*&gt;.
- Om du använder Azure Stack Development Kit portal adressen är https://portal.local.azurestack.external.

![Skärmbild av Azure-stacken användarportalen](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Instrumentpanelen innehåller en rad standardpaneler. Du kan klicka på **redigera instrumentpanel** ändra standardinstrumentpanelen, eller klicka på **ny instrumentpanel** att lägga till anpassade instrumentpaneler. Du kan enkelt lägga till paneler på instrumentpanelen. Du kan till exempel klicka **ny**, högerklicka på **Compute**, och klicka sedan på **fäst på instrumentpanelen**.

## <a name="create-subscription-and-browse-available-resources"></a>Skapa prenumeration och bläddra bland tillgängliga resurser
 
Om du inte redan har en prenumeration, är det första du behöver göra prenumererar på ett erbjudande. Sedan kan bläddra du vilka resurser som är tillgängliga för dig. För att bläddra och skapa resurser, gör du något av följande:

- Klicka på den **Marketplace** panelen på instrumentpanelen. 
- På den **alla resurser** panelen, klickar du på **skapa resurser**.
- I det vänstra navigeringsfönstret klickar du på **ny**.

## <a name="learn-how-to-use-available-services"></a>Lär dig hur du använder tillgängliga tjänster

Om du behöver vägledning för hur du använder tillgängliga tjänster kan finnas det olika alternativ tillgängliga för dig.

- Din organisation eller en tjänsteleverantör får sin egen dokumentation. Detta gäller särskilt om de erbjuder anpassade tjänster eller program.
- Appar från tredje part har sin egen dokumentation.
- Konsekvent Azure services rekommenderar vi att du först granska dokumentationen för Azure-stacken. Klicka på hjälpikonen för att komma åt Azure Stack användardokumentation, och klicka sedan på **hjälp + support**.
 
    ![Skärmbild av hjälp och support alternativ i Användargränssnittet](media/azure-stack-use-portal/HelpAndSupport.png)

    I synnerhet föreslår vi att du läser igenom följande artiklar för att komma igång:

    - [Nyckeln överväganden: använda tjänster eller utveckla appar för Azure-Stack](azure-stack-considerations.md)
    - Visas varje Azure-konsekvent tjänst som visas i avsnittet ”använda services” i dokumentationen. Det finns en ”överväganden” avsnittet för varje tjänst, som beskriver går mellan tjänsten erbjuds i Azure och samma tjänst som erbjuds i Azure-stacken. Ett exempel finns [VM överväganden](azure-stack-vm-considerations.md). Det kan finnas andra informationen i avsnittet ”använda services” som är unik för Azure-stacken. 
     
      Du kan använda i Azure-dokumentationen som allmän referens för en tjänst, men du måste vara medveten om dessa skillnader. Förstå att dokumentationen länkar på den **Quickstart självstudier** panelen pekar på Azure-dokumentationen.

## <a name="get-support"></a>Få support

Om du behöver ytterligare support, kontakta din organisation eller service-leverantör om du behöver hjälp. 

Om du använder Azure Stack Development Kit den [Azure Stack-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) är den enda metoden för support.

## <a name="next-steps"></a>Nästa steg

[Nyckeln överväganden: använda tjänster eller utveckla appar för Azure-Stack](azure-stack-considerations.md)
