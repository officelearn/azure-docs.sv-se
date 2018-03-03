---
title: Delegera erbjudanden i Azure-stacken | Microsoft Docs
description: "Lär dig mer om att placera andra personer som ansvarar för att skapa erbjudanden och logga in användare du."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 287bc04660664facbe99d2cb80ae6c92e41c4111
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="delegate-offers-in-azure-stack"></a>Delegera erbjudanden i Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Som Azure Stack-operator vill du ofta placera andra personer som ansvarar för att skapa erbjudanden och logga in användare. Om du är en tjänstprovider kan kanske du vill återförsäljare att registrera kunder och hantera dem å dina vägnar. Eller om du är en del av en central IT-grupp i ett företag, dotterbolag registrera användare igen.

Delegering hjälper dig med dessa uppgifter genom att göra det möjligt att nå och hantera fler användare än kan du direkt. Följande bild visar en nivå med delegering, men Azure stacken stöder flera nivåer. Delegerad providers (DP) kan i sin tur delegera till andra leverantörer, upp till fem nivåer.

![Nivåer av delegering](media/azure-stack-delegated-provider/image1.png)

Azure Stack-operatörer kan delegera skapandet av erbjudanden och användare till andra användare med hjälp av funktionen för delegering.

## <a name="roles-and-steps-in-delegation"></a>Roller och steg i delegering
Tänk på att det finns tre roller som används för att förstå delegering:

* Den *Azure Stack operatorn* hanterar Azure Stack-infrastruktur, skapar en mall för erbjudandet och delegerar andra erbjuder för sina användare.

* Delegerad Azure Stack-operatorer kallas *delegerad providers*. De kan tillhöra andra organisationer, t.ex andra användare i Azure Active Directory (AD Azure).

* *Användare* registrera sig för erbjudandena och använda dem för att hantera sina arbetsbelastningar, hur du skapar virtuella datorer kan lagra data, och så vidare.

I följande bild visas det finns två steg för att skapa delegeringen.

1. *Identifiera delegerad providers*. Det gör du genom att prenumerera ett erbjudande baserat på en plan som innehåller endast tjänsten prenumerationer. Användare som prenumererar på det här erbjudandet får du några av operatorn Azure Stack-funktioner, inklusive möjligheten att utöka erbjudanden och loggar användarna in för dem.

2. *Delegera ett erbjudande till delegerad providern*. Det här erbjudandet fungerar som en mall för vad delegerad providern kan erbjuda. Delegerad providern kan nu ta erbjudandet. Välj ett namn för den (men inte ändra dess tjänster och kvoter) och erbjuda kunder.

![Skapa delegerad providern och aktivera dem att logga in användare](media/azure-stack-delegated-provider/image2.png)

För att agera som delegerade providers, behöver användarna skapar en relation med den huvudsakliga providern. Med andra ord behöver de för att skapa en prenumeration. I det här fallet identifierar den här prenumerationen delegerad leverantörer som har rätt till finns erbjudanden för den huvudsakliga providern.

När den här relationen har upprättats kan Azure Stack-operatorn Delegera ett erbjudande till delegerad-providern. Delegerad providern kan nu ta erbjudandet, byta namn på den (men inte ändra dess ämne) och erbjuda sina kunder.

I följande avsnitt beskrivs hur du upprättar en delegerad provider, delegera ett erbjudande, och kontrollera att användare kan registrera dig för den.

## <a name="set-up-roles"></a>Konfigurera roller

Om du vill se en delegerad provider på arbetet, behöver du ytterligare Azure AD-konton förutom ditt konto för Azure Stack-operator. Skapa dem om du inte har dessa två konton. Konton kan höra till Azure AD-användare och kallas delegerad providern och användaren.

| **Roll** | **Organisationens rättigheter** |
| --- | --- |
| Delegerad provider |Användare |
| Användare |Användare |

## <a name="identify-the-delegated-providers"></a>Identifiera delegerad providers
1. Logga in som en Azure-Stack-operator.

2. Skapa erbjudande som gör att användarna ska bli delegerad providers:
   
   a.  [Skapa en plan](azure-stack-create-plan.md).
       Planen bör innefatta prenumerationer tjänsten. Den här artikeln använder en plan som heter **PlanForDelegation**.
   
   b.  [Skapa ett erbjudande](azure-stack-create-offer.md) baserat på den här planen. Den här artikeln används ett erbjudande som kallas **OfferToDP**.
   
   c.  När den har skapats av erbjudandet delegerad providern lägga till som en prenumerant till det här erbjudandet. Gör detta genom att markera **prenumerationer** > **Lägg till** > **nya Klientprenumeration**.
   
   ![Lägg till delegerad provider som prenumerant](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> Som med alla Azure Stack-erbjudanden som du har möjlighet att erbjudandet offentliga och innebär att användare att registrera dig för det, eller att hålla den privata och att låta Azure Stack-operatorn hantera registreringen. Delegerad providers är vanligtvis en liten grupp. Du vill styra vem som är upptagna, så att det här erbjudandet privata klokt i de flesta fall.
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack-operatorn skapar delegerad erbjudandet

Du har nu skapat din delegerade leverantör. Nästa steg är att skapa planen och erbjudande som du kommer att delegera och som används av dina kunder. Det är en bra idé att definiera det här erbjudandet precis som du vill att kunderna ska se den eftersom delegerad providern inte kan ändra planer och kvoter som den innehåller.

1. Som operatör Azure Stack [skapar en plan](azure-stack-create-plan.md) och [ett erbjudande](azure-stack-create-offer.md) baserat på den. Den här artikeln används ett erbjudande som kallas **DelegatedOffer.**
   
   > [!NOTE]
   > Det här erbjudandet behöver inte vara offentlig. Om du väljer du den offentliga. I de flesta fall, men vill du bara delegerad leverantörer att ha åtkomst till den. När du delegerar privata erbjudande enligt beskrivningen i följande steg har delegerad providern åtkomst till den.
   > 
   > 
1. Delegera erbjudandet. Gå till **DelegatedOffer.** I den **inställningar** väljer **delegerad Providers** > **Lägg till**.

2. Välj prenumerationen för delegerad providern från den nedrullningsbara listrutan och välj sedan **ombud**.

> ![Lägga till en delegerad provider](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>Delegerad providern anpassar erbjudandet

Logga in på användarportalen som delegerad provider och sedan skapa ett nytt erbjudande med hjälp av delegerade erbjudandet som en mall.

1. Välj **nya** > **klient erbjuder + planer** > **erbjuder**.

    ![Skapa ett nytt erbjudande](media/azure-stack-delegated-provider/image5.png)


1. Tilldela ett namn till erbjudandet. Den här artikeln använder **ResellerOffer**. Välj delegerad erbjudande som du vill basera den och välj sedan **skapa**.
   
   ![Tilldela ett namn](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > Observera skillnaden jämfört med för att erbjuda skapas som erfarenhet av Azure Stack-operatorn. Delegerad providern inte att konstruera erbjudandet från basen planer och tillägg planer. De kan bara välja från erbjudanden som har delegerats till dem och det går inte att göra ändringar i dessa erbjudanden.

1. Offentliggöra erbjudandet genom att välja **Bläddra**, och sedan **erbjuder**. Välj erbjudandet och välj sedan **ändring av tillstånd**.

2. Delegerad providern visar dessa erbjudanden via sina egna portal URL. Dessa erbjudanden visas endast via delegerad portalen. Hitta och ändra den här URL:
   
    a.  Välj **Bläddra** > **fler tjänster** > **prenumerationer**. Välj delegerad provider-prenumeration. Till exempel **DPSubscription** > **egenskaper**.
   
    b.  Kopiera portalen URL till en annan plats, till exempel Anteckningar.
   
    ![Välj den delegerade provider-prenumerationen](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   Nu har du skapat ett erbjudande om delegerad som en delegerad provider. Logga ut som den delegerade providern. Stäng webbläsarfönstret som du har använt.

## <a name="sign-up-for-the-offer"></a>Registrera dig för erbjudande
1. Gå till den delegerade portalen i ett nytt webbläsarfönster URL som du sparade i föregående steg. Logga in på portalen som en användare. 
   
   >[!NOTE]
   >Delegerad erbjudanden visas inte om du inte använder den delegerade portalen. 

2. I instrumentpanelen, väljer **skaffa en prenumeration**. Du kan se att de delegerade erbjudanden som har skapats av delegerade providern är visas för användaren:

> ![Visa och välja erbjudanden](media/azure-stack-delegated-provider/image8.png)
> 
> 

Processen för erbjudande delegering har slutförts. Användaren kan nu registrera dig för detta erbjudande genom att få en prenumeration för den.

## <a name="multiple-tier-delegation"></a>Flera nivåer delegering

Flera nivåer delegering aktiverar delegerad providern att delegera erbjudandet till andra enheter. Detta kan exempelvis skapas djupare återförsäljarkanaler, där den provider som hanterar Azure Stack delegerar ett erbjudande till en återförsäljare. Som distributör i sin tur delegerar till en återförsäljare. Azure-stacken stöder upp till fem nivåer av delegering.

Om du vill skapa flera nivåer av erbjudandet delegering delegerar delegerad providern i sin tur erbjudandet till nästa provider. Processen är densamma för den delegerade providern som den var för Azure Stack-operatorn (se [Azure Stack operatorn skapar delegerad erbjudandet](#cloud-operator-creates-the-delegated-offer)).

## <a name="next-steps"></a>Nästa steg
[Etablera en virtuell dator](azure-stack-provision-vm.md)

