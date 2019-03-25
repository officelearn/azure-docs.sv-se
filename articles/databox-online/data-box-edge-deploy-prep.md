---
title: Självstudie om att förbereda Azure-portalen för att distribuera Data Box Edge | Microsoft Docs
description: Den första självstudien om hur du distribuerar Azure Data Box Edge handlar om hur du förbereder Azure-portalen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 19c4fc96653f966ea5642149d944886e4b7f4483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401682"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Självstudier: Förbered att distribuera Azure Data Box Edge  


Det här är den första självstudien i serien med distributionssjälvstudier som krävs för en fullständigt distribuera Azure Data Box Edge. Den här självstudien beskriver hur du förbereder Azure-portalen för att distribuera en Data Box Edge-resurs.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny resurs
> * Hämta aktiveringsnyckeln

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


### <a name="get-started"></a>Kom igång

Gå igenom följande självstudier i angiven ordning för att distribuera Data Box Edge.

| **#** | **I det här steget** | **Använd de här dokumenten** |
| --- | --- | --- | 
| 1. |**[Förbereda Azure-portalen för Data Box Edge](data-box-edge-deploy-prep.md)** |Skapa och konfigurera din Data Box Edge-resurs innan du installerar en fysisk Data Box Edge-enhet. |
| 2. |**[Installera Data Box Edge](data-box-edge-deploy-install.md)**|Packa upp, rackmontera och kabelanslut den fysiska Data Box Edge-enheten.  |
| 3. |**[Ansluta, konfigurera och aktivera Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Anslut till det lokala webbgränssnittet, slutför installationen av enheten och aktivera enheten. Enheten är redo att konfigurera SMB- eller NFS-resurser.  |
| 4. |**[Överföra data med Data Box Edge](data-box-edge-deploy-add-shares.md)** |Lägg till resurser och anslut till resurser via SMB eller NFS. |
| 5. |**[Transformera data med Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Konfigurera Edge-moduler på enheten för att transformera data då de flyttas till Azure. |

Nu kan du börja konfigurera Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Följande är konfigurationskraven för din Data Box Edge-resurs, din Data Box Edge-enheten och datacenternätverket.

### <a name="for-the-data-box-edge-resource"></a>För Data Box Edge-resursen

Innan du börjar ska du kontrollera att:

- Din Microsoft Azure-prenumeration är aktiverad för en Data Box Edge-resurs. Prenumerationer med användningsbaserad betalning stöds inte.
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.

### <a name="for-the-data-box-edge-device"></a>För Data Box Edge-enheten

Innan du distribuerar en fysisk enhet kontrollerar du att:

- Du har granskat den säkerhetsinformation som ingick i paketets leverans.
- Du har en 1 U-plats tillgänglig i ett standardrack på 19 tum i ditt datacenter för att rackmontera enheten. 
- Du har tillgång till en plan, stabil och jämn arbetsyta där enheten kan stå på ett säkert sätt.
- Platsen där du tänker konfigurera enheten har standardnätström från en oberoende källa eller en strömfördelare på racket (PDU) med en avbrottsfri kraftfälla (UPS).
- Du har tillgång till en fysisk enhet.


### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar ska du kontrollera att:

- Nätverket i ditt datacenter konfigureras enligt nätverkskraven för din Data Box Edge-enhet. Mer information finns i [Systemkrav för Data Box Edge](data-box-edge-system-requirements.md).

- Under normala driftsförhållanden av din Data Box-Edge har du:

    - Minst 10 Mbit/s hämta bandbredd för att se till att enheten är uppdaterad.
    - Minst 20 Mbit/s dedikerade ladda upp och hämta bandbredd för att överföra filer.

## <a name="create-a-new-resource"></a>Skapa en ny resurs

Om du har en befintlig Data Box Edge-resurs för hantering av virtuella enheter hoppar du över det här steget och går till [Hämta aktiveringsnyckeln](#get-the-activation-key).

Skapa en Data Box Edge-resurs genom att utföra följande steg på Azure-portalen.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på 
    
    - Azure-portalen på den här URL: [ https://portal.azure.com ](http://portal.azure.com).
    - Eller Azure Government-portalen på denna URL: [https://portal.azure.us](https://portal.azure.us)

2. I den vänstra rutan väljer **+ skapa en resurs**. Sök efter **Data Box Edge / Data Box Gateway**. Välj **Data Box Edge / Data Box Gateway**. Välj **Skapa**.
3. Välj den prenumeration som du vill använda för Data Box Edge-enhet. Välj den region där du vill distribuera Data Box Edge-resursen. Den här versionen finns östra USA, Sydostasien och Västeuropa. Välj den plats som är närmast den geografiska region där du vill distribuera enheten. Klicka på alternativet **Data Box Edge** och välj **Skapa**.

    ![Söka efter Data Box Edge-tjänst](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. På den **grunderna** anger eller väljer du följande **projektet information**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration    |Det fylls i automatiskt baserat på den tidigare val. Prenumerationen är kopplad till ditt faktureringskonto. |
    |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Läs mer om [Azure-resursgrupper](../azure-resource-manager/resource-group-overview.md).     |

4. Ange eller Välj följande **instans information**.

    |Inställning  |Värde  |
    |---------|---------|
    |Namn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken som består av bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |
    |Region     |Den här versionen är är östra USA, Sydostasien och Västeuropa tillgängliga för att distribuera din resurs. Om du använder Azure Government government-regioner är tillgängliga enligt den [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).<br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|

    ![Information om projektet och instans](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Välj **Nästa: Leveransadress**.

    - Om du redan har en enhet väljer du kombinationsrutan för **jag har en Data Box Edge-enhet**.
    - Om detta är den nya enheten som du beställer, ange namn, företag, adress för att skicka enheten och kontaktinformation.

    ![Leveransadress för nya enheten](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Välj **Nästa: Granska + skapa**.

7. På den **granska + skapa** fliken kan du granska den **prisinformation**, **användningsvillkoren**, och information för din resurs. Välj kombinationsrutan för **jag har läst igenom sekretesspolicyn**.

    ![Granska information om Data Box Edge-resursen och sekretesspolicy](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Välj **Skapa**.

Det tar några minuter att skapa resursen. När resursen har skapas och distribueras, meddelas du. Välj **Gå till resurs**.

![Gå till Data Box Edge-resurs](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

När ordningen placeras, Microsoft granskar ordningen och når till dig (via e-post) med leveransinformation.

![Meddelande för granskning av Data Box Edge-order](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När Data Box Edge-resursen är igång så måste du hämta aktiveringsnyckeln. Den här nyckeln används för att aktivera och ansluta Data Box Edge-enheten med resursen. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Välj den resurs som du skapade. Välj **översikt** och välj sedan **Enhetsinställningar**.

    ![Välj Enhetsinställningar](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. På den **aktivera** panelen, väljer **skapa nycklar** att skapa en aktiveringsnyckel. Välj kopieringsikonen för att kopiera nyckeln och spara den för senare användning.

    ![Hämta aktiveringsnyckeln](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Aktiveringsnyckeln upphör att gälla tre dagar efter att den skapats.
> - Om nyckeln har upphört att gälla genererar du en ny nyckel. Den äldre nyckeln är inte giltig.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Box Edge-ämnen som att:

> [!div class="checklist"]
> * Skapa en ny resurs
> * Hämta aktiveringsnyckeln

Gå vidare till nästa självstudie för att lära dig hur du installerar Data Box Edge.

> [!div class="nextstepaction"]
> [Installera Data Box Edge](./data-box-edge-deploy-install.md)



