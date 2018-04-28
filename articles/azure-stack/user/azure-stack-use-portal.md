---
title: Med hjälp av Azure Stack-portalen | Microsoft Docs
description: Lär dig hur du använder användarportalen i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: mabrigg
ms.openlocfilehash: 279722cc53889cb0a261fcffde0c7e0f86be6dc5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="using-the-azure-stack-portal"></a>Med hjälp av Azure Stack-portalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda Azure Stack-portalen för att prenumerera på offentliga erbjudanden och använda de tjänster som dessa erbjudanden tillhandahåller som en konsument Azure Stack-tjänster. Om du har använt Azure-portalen innan är du redan bekant med användargränssnittet.

## <a name="access-the-portal"></a>Åtkomst till portalen

Azure Stack-operatorn (en tjänsteleverantör eller en administratör i din organisation), informerar dig om att få åtkomst till portalen rätt URL.

- För ett integrerat system URL: en varierar beroende på din operatorn region och externa domännamn och ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
- Om du använder Azure Stack Development Kit portal adressen är https://portal.local.azurestack.external.

![Skärmbild av Azure-stacken användarportalen](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Instrumentpanelen innehåller en standarduppsättning paneler. Du kan klicka på **redigera instrumentpanel** ändra standardinstrumentpanelen, eller klicka på **ny instrumentpanel** att skapa en anpassad instrumentpanel. Du kan enkelt anpassa en instrumentpanel genom att lägga till eller ta bort paneler. Till exempel om du vill lägga till en beräkning panel, klickar du på **ny**. Högerklicka på **Compute**, och klicka sedan på **fäst på instrumentpanelen**.

## <a name="create-subscription-and-browse-available-resources"></a>Skapa prenumeration och bläddra bland tillgängliga resurser
 
Om du inte redan har en prenumeration, är det första du behöver göra prenumererar på ett erbjudande. Sedan kan bläddra du tillgängliga resurser. Bläddra och skapa resurser genom att använda någon av följande metoder:

- Klicka på den **Marketplace** panelen på instrumentpanelen.
- På den **alla resurser** panelen, klickar du på **skapa resurser**.
- I det vänstra navigeringsfönstret klickar du på **ny**.

## <a name="learn-how-to-use-available-services"></a>Lär dig hur du använder tillgängliga tjänster

Om du behöver vägledning för hur du använder tillgängliga tjänster kan finnas det olika alternativ tillgängliga för dig.

- Din organisation eller en tjänsteleverantör får sin egen dokumentation som normalt är fallet om de erbjuder anpassade tjänster eller program.
- Appar från tredje part har sin egen dokumentation.
- Konsekvent Azure services rekommenderar vi att du först granska dokumentationen för Azure-stacken. Klicka på hjälpikonen för att komma åt Azure Stack användardokumentation, och klicka sedan på **hjälp + support**.
 
    ![Skärmbild av hjälp och support alternativ i Användargränssnittet](media/azure-stack-use-portal/HelpAndSupport.png)

    I synnerhet föreslår vi att du läser igenom följande artiklar för att komma igång:

    - [Nyckeln överväganden: använda tjänster eller utveckla appar för Azure-Stack](azure-stack-considerations.md)
    - I avsnittet ”använda services” i dokumentationen finns en ”överväganden” artikel för varje tjänst. Sidan ”överväganden” beskrivs skillnaderna mellan tjänsten erbjuds i Azure och samma tjänst som erbjuds i Azure-stacken. Ett exempel finns [VM överväganden](azure-stack-vm-considerations.md). Det kan finnas andra informationen i avsnittet ”använda services” som är unik för Azure-stacken.
     
      Du kan använda i Azure-dokumentationen som allmän referens för en tjänst, men du måste vara medveten om dessa skillnader. Förstå att dokumentationen länkar på den **Quickstart självstudier** panelen pekar på Azure-dokumentationen.

## <a name="get-support"></a>Få support

Om du behöver ytterligare hjälp kan du kontakta din organisation eller service-leverantör för hjälp.

Om du använder Azure Stack Development Kit den [Azure Stack-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) är den enda källan för support.

## <a name="next-steps"></a>Nästa steg

[Nyckeln överväganden: använda tjänster eller utveckla appar för Azure-Stack](azure-stack-considerations.md)
