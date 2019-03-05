---
title: Delegera erbjudanden i Azure Stack | Microsoft Docs
description: Lär dig mer om att placera andra personer som ansvarar för att skapa erbjudanden och logga in användare för dig.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 1211edde43d802a4c48cbd936473b1a6f83af705
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342096"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegera erbjudanden i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Som Azure Stack-operatör vill du ofta att andra personer som ansvarar för att logga in användare och skapa prenumerationer. Till exempel om du är en tjänstleverantör kan du återförsäljare att registrera kunder och hantera dem åt dig. Eller, om du är en del av en central IT-grupp i ett företag kan du kanske vill delegera användarregistrering till annan IT-personal.

Delegering gör det enklare att nå och hantera fler användare än du själv, enligt följande bild:

![Nivåer av delegering](media/azure-stack-delegated-provider/image1.png)

Delegerad providern hanterar ett erbjudande (delegerade erbjudandet) med delegering, och slutkunder hämta prenumerationer under erbjudandet utan inblandning från systemadministratören.

## <a name="understand-delegation-roles-and-steps"></a>Förstå delegering roller och steg

### <a name="delegation-roles"></a>Delegering roller

Följande roller är en del av delegering:

* Den *Azure Stack-operatör* hanterar Azure Stack-infrastruktur och skapar en mall för erbjudandet. Operatorn delegerar andra tillhandahåller erbjudanden till sina klient.

* Delegerad Azure Stack-operatörer är användare med *ägare* eller *deltagare* rättigheter i prenumerationerna som kallas *delegerade providrar*. De kan tillhöra andra organisationer, t.ex andra klienter i Azure Active Directory (AD Azure).

* *Användare* registrera dig för dina erbjudanden och använda dem för att hantera sina arbetsbelastningar, skapa virtuella datorer, lagring av data, och så vidare.

### <a name="delegation-steps"></a>Steg för delegering

Det finns två grundläggande stegen för att ställa in delegering:

1. **Skapa en delegerad providerprenumeration**: Prenumerera på en användare till ett erbjudande som innehåller endast tjänsten prenumerationer. Användare som prenumererar på det här erbjudandet kan sedan utöka delegerade erbjudanden till andra användare genom att registrera dem för de erbjudandena.

2. **Delegera ett erbjudande till delegerade providern**: Det här erbjudandet kan den delegerade providern kan skapa prenumerationer eller utöka erbjudandet till sina användare. Delegerad providern kan nu ta erbjudandet och erbjuda de till andra användare.

Följande bild visar stegen för att ställa in delegering:

![Skapa delegerad providern och aktivera dem att logga in användare](media/azure-stack-delegated-provider/image2.png)

#### <a name="delegated-provider-requirements"></a>Delegerad provider-krav

För att fungera som en delegerad provider, relation en användare en med den huvudsakliga providern genom att skapa en prenumeration. Den här prenumerationen identifierar delegerade leverantören som har rätt att presentera de delegerade erbjudandena för den huvudsakliga providern.

När den här relationen har upprättats, kan Azure Stack-operatör Delegera ett erbjudande till delegerade-providern. Delegerad providern kan ta erbjudandet, Byt namn på den (men inte ändra sak) och erbjuda sina kunder.

## <a name="delegation-walkthrough"></a>Genomgång för delegering

Följande avsnitt innehåller en praktisk genomgång för hur du konfigurerar en delegerad provider, delegera ett erbjudande och verifiera att användarna kan registrera sig för det delegerade erbjudandet.

### <a name="set-up-roles"></a>Konfigurera roller

Om du vill använda den här genomgången behöver du två Azure AD-konton förutom ditt konto för Azure Stack-operator. Om du inte har dessa två konton, måste du skapa dem. Konton kan höra till alla Azure AD-användare och de kallas delegerade providern och användaren.

| **Roll** | **Organisationens rättigheter** |
| --- | --- |
| Delegerad provider |Användare |
| Användare |Användare |

 > [!NOTE]
 > När det gäller en CSP-återförsäljare kräver skapar den här delegerade providern dessa användare i klientkatalogen (användare AAD). Azure Stack-operator måste [först publicera](azure-stack-enable-multitenancy.md) som AAD-klient och sedan konfigurera användning och fakturering genom att följa [här](azure-stack-csp-howto-register-tenants.md).

### <a name="identify-the-delegated-provider"></a>Identifiera den delegerade providern

1. Logga in på administratörsportalen som Azure Stack-operatör.

1. Skapa ett erbjudande som används att bli en delegerad provider:

   a.  [Skapa en plan](azure-stack-create-plan.md).
       Den här planen bör innehålla endast prenumerationstjänsten. Den här artikeln använder ett schema med namnet **PlanForDelegation** som exempel.

   b.  [Skapa ett erbjudande](azure-stack-create-offer.md) baserat på den här planen. Den här artikeln används ett erbjudande med namnet **OfferToDP** som exempel.

   c.  Lägger till delegerad providern som prenumerant på det här erbjudandet genom att välja **prenumerationer**, sedan **Lägg till**, sedan **nya Klientprenumeration**.

   ![Lägg till den delegerade providern som en prenumerant](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Som med alla Azure Stack-erbjudanden som du har möjlighet att erbjudandet offentliga och slipper användare att registrera dig för den, eller att hålla det privata och att låta Azure Stack-operatör hantera registreringen. Delegerade providrar är vanligtvis en liten grupp. Du vill kontrollera vem som är upptagna till den, så att hålla det här erbjudandet privat passar i de flesta fall.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack-operatör skapar det delegerade erbjudandet

Nästa steg är att skapa plan och erbjudande som du kommer att delegera och som användarna ska använda. Det är en bra idé att definiera det här erbjudandet precis som du vill att användarna ska se den, eftersom den delegerade providern inte kan ändra den och kvoter som den innehåller.

1. Som en Azure Stack-operatör [skapa en plan](azure-stack-create-plan.md) och [ett erbjudande](azure-stack-create-offer.md) baserat på planen. Den här artikeln används ett erbjudande med namnet **DelegatedOffer** som exempel.

   > [!NOTE]
   > Det här erbjudandet har inte är offentliga men du kan göra offentliga. Men i de flesta fall vill du bara delegerade providrar ska ha åtkomst till erbjudandet. När du delegerar erbjudande om en privat enligt beskrivningen i följande steg bör har delegerade leverantören åtkomst till den.

2. Delegera erbjudandet. Gå till **DelegatedOffer**. Under **inställningar**väljer **delegerade providrar**och välj sedan **Lägg till**.

3. Välj prenumerationen för delegerade providern från den nedrullningsbara listan och välj sedan **ombud**.

   ![Lägg till en delegerad provider](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Delegerad providern anpassar erbjudandet

Logga in på användarportalen som delegerad provider och sedan skapa ett nytt erbjudande med hjälp av det delegerade erbjudandet som en mall.

1. Välj **+ skapa en resurs**, sedan **Klienterbjudanden och planer**och välj sedan **erbjuder**.

    ![Skapa ett nytt erbjudande](media/azure-stack-delegated-provider/image5.png)

2. Tilldela ett namn till erbjudandet. Det här exemplet används **ResellerOffer**. Välj det delegerade erbjudandet som du vill basera den och välj sedan **skapa**.

   ![Tilldela ett namn](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Det är viktigt att förstå att delegerade providrar kan endast välja erbjudanden som delegeras till dem. De kan inte ändra de erbjudandena. Endast Azure Stack-operatör kan ändra dessa erbjudanden, till exempel ändra sina och kvoter. En delegerad providern inte att konstruera ett erbjudande från basplaner och tilläggsplaner.

3. Delegerad leverantören kan göra dessa erbjudanden offentliga via sina egna portal URL: en. För att göra erbjudandet offentlig, Välj **Bläddra**, och sedan **erbjuder**. Välja erbjudandet och välj sedan **ändra tillstånd**.

4. Offentliga delegerade erbjudanden visas nu endast via delegerade-portalen. Hitta och ändra den här URL: en:

    a.  Välj **Bläddra**, sedan **alla tjänster**, och sedan under den **Allmänt** kategori, väljer **prenumerationer**. Välj delegerad providerprenumeration; till exempel **DPSubscription**, sedan **egenskaper**.

    b.  Kopiera portalen URL: en till en annan plats, till exempel Anteckningar.

    ![Välj delegerad Providerprenumeration](media/azure-stack-delegated-provider/dpportaluri.png)  

   Du har nu skapat ett delegerat erbjudande som en delegerad provider. Logga ut som den delegerade providern och stänga webbläsarfönstret.

### <a name="sign-up-for-the-offer"></a>Registrera dig för erbjudandet

1. I ett nytt webbläsarfönster går du till den delegerade portalen-URL som du sparade i föregående steg. Logga in på portalen som en användare.

   >[!NOTE]
   >Delegerade erbjudanden visas inte om du inte använder den delegerade portalen.

1. I instrumentpanelen, väljer **skaffa en prenumeration**. Du ser att de delegerade erbjudanden som har skapats av delegerade providern visas för användaren.

   ![Visa och välj erbjudanden](media/azure-stack-delegated-provider/image8.png)

Processen att delegera ett erbjudande är klar. Nu kan en användare registrera dig för det här erbjudandet genom att skaffa en prenumeration på den.

## <a name="move-subscriptions-between-delegated-providers"></a>Flytta prenumerationer mellan delegerade providrar

Om det behövs kan en prenumeration flyttas mellan ny eller befintlig delegerad providerprenumerationer som hör till samma directory-klient. Detta görs med hjälp av PowerShell-cmdleten [flytta AzsSubscription](/powershell/module/azs.subscriptions.admin).

Detta är användbart när:

* Publicera en ny gruppmedlem som kommer att utföras på den delegerade providerrollen och du tilldela det här teamet medlem användarprenumerationer som tidigare har skapats i standard-providerprenumeration.
* Du har flera prenumerationer för delegerade providrar i samma directory-klient (Azure Active Directory) och vill flytta användarprenumerationer mellan dem. Det här scenariot kan vara ett fall där en gruppmedlem flyttar mellan teamen och sin prenumeration behöver som ska allokeras till det nya teamet.

## <a name="next-steps"></a>Nästa steg

* [Etablera en virtuell dator](azure-stack-provision-vm.md)
