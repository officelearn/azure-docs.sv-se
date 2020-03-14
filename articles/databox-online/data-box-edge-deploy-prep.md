---
title: Självstudie för att förbereda Azure Portal Data Center miljö för att distribuera Azure Data Box Edge | Microsoft Docs
description: Den första självstudien om hur du distribuerar Azure Data Box Edge handlar om hur du förbereder Azure-portalen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 5192816ca7be47ecb5e602e46dff16951f7475ee
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370278"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Självstudie: förbereda för att distribuera Azure Data Box Edge  

Det här är den första självstudien i serien med distributionssjälvstudier som krävs för en fullständigt distribuera Azure Data Box Edge. Den här självstudien beskriver hur du förbereder Azure-portalen för att distribuera en Data Box Edge-resurs.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
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
| 5. |**[Transformera data med Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Konfigurera Compute-moduler på enheten för att transformera data när de flyttas till Azure. |

Nu kan du börja konfigurera Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Följande är konfigurationskraven för din Data Box Edge-resurs, din Data Box Edge-enheten och datacenternätverket.

### <a name="for-the-data-box-edge-resource"></a>För Data Box Edge-resursen

Innan du börjar ska du kontrollera att:

* Din Microsoft Azure prenumeration är aktive rad för en Azure Stack Edge-resurs. Se till att du har använt en prenumeration som stöds, till exempel [Microsoft Enterprise-avtal (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)eller [Microsoft Azure-sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Du har ägar-eller deltagar åtkomst på resurs grupps nivå för Data Box Edge/Data Box Gateway, IoT Hub och Azure Storage resurser.

  * Om du vill skapa en Data Box Edge-/Data Box Gateway-resurs, bör du ha behörighet som deltagare (eller högre) som är begränsade till resurs grupps nivå. Du måste också se till att `Microsoft.DataBoxEdge`-providern är registrerad. Information om hur du registrerar finns i [Registrera resurs leverantör](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Om du vill skapa en IoT Hub resurs måste du kontrol lera att Microsoft. providers-providern är registrerad. Information om hur du registrerar finns i [Registrera resurs leverantör](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Om du vill skapa en lagrings konto resurs måste du igen med deltagar-eller högre åtkomst omfång på resurs grupps nivå. Azure Storage är som standard en registrerad resurs leverantör.
* Du har administratörs-eller användar åtkomst till Azure Active Directory Graph API. Mer information finns i [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.

### <a name="for-the-data-box-edge-device"></a>För Data Box Edge-enheten

Innan du distribuerar en fysisk enhet kontrollerar du att:

- Du har granskat säkerhets informationen som ingick i försändelse paketet.
- Du har en 1U-plats som är tillgänglig i ett standardiserat 19-tums rack i ditt data Center för rack montering av enheten.
- Du har tillgång till en plan, stabil och jämn arbetsyta där enheten kan stå på ett säkert sätt.
- Platsen där du tänker konfigurera enheten har standardnätström från en oberoende källa eller en strömfördelare på racket (PDU) med en avbrottsfri kraftfälla (UPS).
- Du har tillgång till en fysisk enhet.


### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar ska du kontrollera att:

- Nätverket i ditt datacenter konfigureras enligt nätverkskraven för din Data Box Edge-enhet. Mer information finns i [Systemkrav för Data Box Edge](data-box-edge-system-requirements.md).

- För normala drift villkor för din Data Box Edge har du:

    - Minst 10 Mbit/s Ladda ned bandbredd för att se till att enheten förblir uppdaterad.
    - Minst 20 Mbit/s dedikerad överföring och nedladdning av bandbredd för överföring av filer.

## <a name="create-a-new-resource"></a>Skapa en ny resurs

Om du har en befintlig Data Box Edge-resurs för hantering av virtuella enheter hoppar du över det här steget och går till [Hämta aktiveringsnyckeln](#get-the-activation-key).

Skapa en Data Box Edge-resurs genom att utföra följande steg på Azure-portalen.

1. Använd dina Microsoft Azure autentiseringsuppgifter för att logga in på 
    
    - Azure Portal på denna URL: [https://portal.azure.com](https://portal.azure.com).
    - Eller, Azure Government portalen på denna URL: [https://portal.azure.us](https://portal.azure.us). Mer information finns i [ansluta till Azure Government med hjälp av portalen](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. I den vänstra rutan väljer du **+ skapa en resurs**. Sök efter **data Box Edge/data Box Gateway**. Välj **data Box Edge/data Box Gateway**. Välj **Skapa**.
3. Välj den prenumeration som du vill använda för den Data Box Edge enheten. Välj den region där du vill distribuera Data Box Edge-resursen. För en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig, se [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Välj den plats som är närmast den geografiska region där du vill distribuera enheten. Regionen lagrar bara metadata för enhets hantering. Faktiska data kan lagras i valfritt lagrings konto.
    
    Klicka på alternativet **Data Box Edge** och välj **Skapa**.

    ![Söka efter Data Box Edge-tjänst](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. På fliken **grundläggande** anger eller väljer du följande **projekt information**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration    |Detta fylls i automatiskt baserat på den tidigare markeringen. Prenumerationen är kopplad till ditt faktureringskonto. |
    |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Läs mer om [Azure-resursgrupper](../azure-resource-manager/management/overview.md).     |

4. Ange eller Välj följande **instans information**.

    |Inställning  |Värde  |
    |---------|---------|
    |Namn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken som består av bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |
    |Region     |För en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig, se [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Om du använder Azure Government är alla myndigheter tillgängliga som de visas i Azure- [regionerna](https://azure.microsoft.com/global-infrastructure/regions/).<br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|

    ![Projekt-och instans information](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Välj **Nästa: leverans adress**.

    - Om du redan har en enhet väljer du kombinations rutan för **Jag har en data Box Edge enhet**.
    - Om det här är den nya enhet som du beställer anger du kontakt namn, företag, adress för att leverera enheten och kontakt information.

    ![Leverans adress för ny enhet](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Välj **Nästa: granska + skapa**.

7. På fliken **Granska + skapa** granskar du **pris informationen**, **användningsvillkor**och informationen för resursen. Välj kombinations rutan för **Jag har granskat sekretess villkoren**.

    ![Granska Data Box Edge resurs information och sekretess villkor](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Välj **Skapa**.

Det tar några minuter att skapa resursen. När resursen har skapats och distribuerats får du ett meddelande. Välj **Gå till resurs**.

![Gå till Data Box Edge resursen](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

När ordern har placerats, granskar Microsoft ordern och når dig (via e-post) med leverans information.

![Meddelande om granskning av Data Box Edges ordningen](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När Data Box Edge-resursen är igång så måste du hämta aktiveringsnyckeln. Den här nyckeln används för att aktivera och ansluta Data Box Edge-enheten med resursen. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Välj den resurs som du har skapat. Välj **Översikt** och välj sedan **enhets konfiguration**.

    ![Välj enhets konfiguration](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. På panelen **Aktivera** väljer du **generera nyckel** för att skapa en aktiverings nyckel. Välj kopieringsikonen för att kopiera nyckeln och spara den för senare användning.

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



