---
title: Självstudiekurs för att förbereda Azure Portal, datacentermiljö för att distribuera Azure Data Box Edge | Microsoft-dokument
description: Den första självstudien om hur du distribuerar Azure Data Box Edge handlar om hur du förbereder Azure-portalen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 90ed4bf8f0389619f130e998ed76c720442092b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474483"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Självstudiekurs: Förbered distribution för Azure Data Box Edge  

Det här är den första självstudien i serien med distributionssjälvstudier som krävs för en fullständigt distribuera Azure Data Box Edge. Den här självstudien beskriver hur du förbereder Azure-portalen för att distribuera en Data Box Edge-resurs.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Skapa en ny resurs
> * Hämta aktiveringsnyckeln

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

### <a name="get-started"></a>Kom igång

Gå igenom följande självstudier i angiven ordning för att distribuera Data Box Edge.

| **#** | **I det här steget** | **Använd de här dokumenten** |
| --- | --- | --- | 
| 1. |**[Förbereda Azure-portalen för Data Box Edge](data-box-edge-deploy-prep.md)** |Skapa och konfigurera din Data Box Edge-resurs innan du installerar en fysisk Data Box Edge-enhet. |
| 2. |**[Kant för installation av databox](data-box-edge-deploy-install.md)**|Packa upp, rackmontera och kabelanslut den fysiska Data Box Edge-enheten.  |
| 3. |**[Ansluta, konfigurera och aktivera Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Anslut till det lokala webbgränssnittet, slutför installationen av enheten och aktivera enheten. Enheten är redo att konfigurera SMB- eller NFS-resurser.  |
| 4. |**[Överföra data med Data Box Edge](data-box-edge-deploy-add-shares.md)** |Lägg till resurser och anslut till resurser via SMB eller NFS. |
| 5. |**[Transformera data med Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Konfigurera beräkningsmoduler på enheten för att omvandla data när de flyttas till Azure. |

Nu kan du börja konfigurera Azure-portalen.

## <a name="prerequisites"></a>Krav

Följande är konfigurationskraven för din Data Box Edge-resurs, din Data Box Edge-enheten och datacenternätverket.

### <a name="for-the-data-box-edge-resource"></a>För Data Box Edge-resursen

Innan du börjar bör du kontrollera att:

* Din Microsoft Azure-prenumeration är aktiverad för en Azure Stack Edge-resurs. Kontrollera att du har använt en prenumeration som stöds, till exempel [Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/) [CSP (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/azure-plan-lp)eller [Microsoft Azure Sponsoren](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Du har åtkomst till ägare eller deltagare på resursgruppsnivå för databoxens edge/databoxgateway, IoT Hub och Azure Storage-resurser.
    - Om du vill skapa en databox edge/ databoxgatewayresurs bör du ha behörigheter som deltagare (eller högre) på resursgruppsnivå. Du måste också se `Microsoft.DataBoxEdge` till att leverantören är registrerad. Information om hur du registrerar dig finns [i Registrera resursprovidern](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Om du vill skapa en IoT Hub-resurs kontrollerar du att Microsoft.Devices-providern är registrerad. Information om hur du registrerar dig finns [i Registrera resursprovidern](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Om du vill skapa en lagringskontoresurs behöver du återigen deltagare eller högre åtkomstomfattning på resursgruppsnivå. Azure Storage är som standard en registrerad resursprovider.
- Du har administratörs- eller användaråtkomst till Microsoft Graph API. Mer information finns i [Microsoft Graph-behörighetsreferens](https://docs.microsoft.com/graph/permissions-reference).
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.

### <a name="for-the-data-box-edge-device"></a>För Data Box Edge-enheten

Innan du distribuerar en fysisk enhet kontrollerar du att:

- Du har granskat säkerhetsinformationen som ingick i försändelsepaketet.
- Du har en 1U-kortplats som finns i ett standard 19-tumsställ i ditt datacenter för rackmontering av enheten.
- Du har tillgång till en plan, stabil och jämn arbetsyta där enheten kan stå på ett säkert sätt.
- Platsen där du tänker konfigurera enheten har standardnätström från en oberoende källa eller en strömfördelare på racket (PDU) med en avbrottsfri kraftfälla (UPS).
- Du har tillgång till en fysisk enhet.


### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar bör du kontrollera att:

- Nätverket i ditt datacenter konfigureras enligt nätverkskraven för din Data Box Edge-enhet. Mer information finns i [Systemkrav för Data Box Edge](data-box-edge-system-requirements.md).

- För normala driftsförhållanden för din Data Box Edge har du:

    - Minst 10 Mbit/s hämtar bandbredd för att säkerställa att enheten förblir uppdaterad.
    - Minst 20 Mbit/s dedikerad uppladdning och nedladdning av bandbredd för att överföra filer.

## <a name="create-a-new-resource"></a>Skapa en ny resurs

Om du har en befintlig Data Box Edge-resurs för hantering av virtuella enheter hoppar du över det här steget och går till [Hämta aktiveringsnyckeln](#get-the-activation-key).

Skapa en Data Box Edge-resurs genom att utföra följande steg på Azure-portalen.

1. Använda dina Microsoft Azure-autentiseringsuppgifter för att logga in på 
    
    - Azure-portalen på [https://portal.azure.com](https://portal.azure.com)den här URL:en: .
    - Eller Azure Government-portalen på [https://portal.azure.us](https://portal.azure.us)den här URL:en: . Mer information finns i [Anslut till Azure Government med hjälp av portalen](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. Välj **+ Skapa en resurs**i den vänstra rutan . Sök efter **Data Box Edge / Data Box Gateway**. Välj **Data Box Edge / Data Box Gateway**. Välj **Skapa**.
3. Välj den prenumeration som du vill använda för Data Box Edge-enheten. Välj den region där du vill distribuera Data Box Edge-resursen. En lista över alla regioner där Azure Stack Edge-resursen är tillgänglig finns i [Azure-produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Välj den plats som är närmast den geografiska region där du vill distribuera enheten. Regionen lagrar endast metadata för enhetshantering. De faktiska data kan lagras i alla lagringskonto.
    
    Klicka på alternativet **Data Box Edge** och välj **Skapa**.

    ![Söka efter Data Box Edge-tjänst](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Ange eller välj följande **Projektinformation**på fliken **Grunderna** .
    
    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration    |Detta fylls i automatiskt baserat på den tidigare markeringen. Prenumerationen är kopplad till ditt faktureringskonto. |
    |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Läs mer om [Azure Resource Groups](../azure-resource-manager/management/overview.md).     |

4. Ange eller välj följande **instansinformation**.

    |Inställning  |Värde  |
    |---------|---------|
    |Namn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken som består av bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |
    |Region     |En lista över alla regioner där Azure Stack Edge-resursen är tillgänglig finns i [Azure-produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Om du använder Azure Government är alla myndighetsregioner tillgängliga enligt [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/).<br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|

    ![Information om projekt och instans](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Välj **Nästa: Leveransadress**.

    - Om du redan har en enhet väljer du kombinationsrutan för **Jag har en Data Box Edge-enhet**.
    - Om det här är den nya enheten som du beställer anger du kontaktnamn, företag, adress för att leverera enheten och kontaktinformation.

    ![Leveransadress för ny enhet](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Välj **Nästa: Granska + skapa**.

7. På fliken **Granska + skapa** läser du **prisinformationen,** **användningsvillkoren**och informationen för din resurs. Välj kombinationsrutan för **jag har granskat sekretessvillkoren**.

    ![Granska information om datarutekantsresursinformation och sekretessvillkor](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Välj **Skapa**.

Det tar några minuter att skapa resursen. När resursen har skapats och distribuerats meddelas du. Välj **Gå till resurs**.

![Gå till databoxkantsresursen](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

När beställningen har gjorts granskar Microsoft beställningen och når ut till dig (via e-post) med leveransinformation.

![Meddelande för granskning av databoxens kantordning](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När Data Box Edge-resursen är igång så måste du hämta aktiveringsnyckeln. Den här nyckeln används för att aktivera och ansluta Data Box Edge-enheten med resursen. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Välj den resurs som du har skapat. Välj **Översikt** och välj sedan **Enhetskonfiguration**.

    ![Välj enhetskonfiguration](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. På panelen **Aktivera** väljer du **Generera för** att skapa en aktiveringsnyckel. Välj kopieringsikonen för att kopiera nyckeln och spara den för senare användning.

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



