---
title: Självstudie om hur du förbereder Azure-portalen för att distribuera Data Box Gateway | Microsoft Docs
description: Den första självstudien om hur du distribuerar Azure Data Box Gateway handlar om hur du förbereder Azure-portalen.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 74fec059bdffb91f5a7774d430e2f1897f0e863c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474466"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Självstudiekurs: Förbereda distributionen av Azure Data Box Gateway

Det här är den första självstudien i serien med distributionssjälvstudier som krävs för en fullständig Azure Data Box Gateway-distribution. Den här självstudien beskriver hur du förbereder Azure-portalen för att distribuera Data Box Gateway-resursen.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Skapa en ny resurs
> * Ladda ned avbildningen av den virtuella enheten
> * Hämta aktiveringsnyckeln

## <a name="get-started"></a>Kom igång

Gå igenom följande självstudier i angiven ordning när du ska distribuera Data Box Gateway.

| **#** | **I det här steget** | **Använd de här dokumenten** |
| --- | --- | --- | 
| 1. |**[Förbereda Azure-portalen för Data Box Gateway](data-box-gateway-deploy-prep.md)** |Skapa och konfigurera Data Box Gateway-resursen innan du etablerar en virtuell Data Box Gateway-enhet. |
| 2. |**[Etablera Data Box Gateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Etablera Data Box Gateway i VMware](data-box-gateway-deploy-provision-vmware.md)**|För Hyper-V etablerar du och ansluter till en virtuell Data Box Gateway-enhet i ett värdsystem som kör Hyper-V i Windows Server 2016 eller Windows Server 2012 R2. <br><br><br> För VMware etablerar du och ansluter till en virtuell Data Box Gateway-enhet i ett värdsystem som kör VMware ESXi 6.0, 6.5 eller 6.7.<br></br> |
| 3. |**[Ansluta, konfigurera och aktivera Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Anslut till det lokala webbgränssnittet, slutför installationen av enheten och aktivera enheten. Sedan kan du etablera SMB-resurser.  |
| 4. |**[Överföra data med Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Lägg till resurser och anslut till resurser via SMB eller NFS. |

Nu kan du börja konfigurera Azure-portalen.

## <a name="prerequisites"></a>Krav

Här hittar du konfigurationskraven för Data Box Gateway-resursen, Data Box Gateway-enheten och datacenternätverket.

### <a name="for-the-data-box-gateway-resource"></a>För Data Box Gateway-resursen

Innan du börjar bör du kontrollera att:

* Din Microsoft Azure-prenumeration är aktiverad för en Azure Stack Edge-resurs. Kontrollera att du har använt en prenumeration som stöds, till exempel [Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/) [CSP (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/azure-plan-lp)eller [Microsoft Azure Sponsoren](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Du har åtkomst till ägare eller deltagare på resursgruppsnivå för databoxens edge/databoxgateway, IoT Hub och Azure Storage-resurser.
    - Om du vill skapa en databox edge/ databoxgatewayresurs bör du ha behörigheter som deltagare (eller högre) på resursgruppsnivå. Du måste också se `Microsoft.DataBoxEdge` till att leverantören är registrerad. Information om hur du registrerar dig finns [i Registrera resursprovidern](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Om du vill skapa en lagringskontoresurs behöver du återigen deltagare eller högre åtkomstomfattning på resursgruppsnivå. Azure Storage är som standard en registrerad resursprovider.
- Du har administratörs- eller användaråtkomst till Microsoft Graph API. Mer information finns i [Microsoft Graph-behörighetsreferens](https://docs.microsoft.com/graph/permissions-reference).
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.

### <a name="for-the-data-box-gateway-device"></a>För Data Box Gateway-enheten

Innan du distribuerar en virtuell enhet kontrollerar du att:

- Du har åtkomst till ett värdsystem som kör Hyper-V i Windows Server 2012 R2 eller senare eller VMware (ESXi 6.0, 6.5 eller 6.7) som kan användas för att etablera en enhet.
- Värdsystemet kan dedikera följande resurser för att etablera den virtuella Data Box-enheten:
  
  - Minst 4 virtuella processorer.
  - Minst 8 GB RAM.
  - Ett nätverksgränssnitt.
  - En operativsystemdisk på 250 GB.
  - En virtuell disk på 2 TB för systemdata.

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar bör du kontrollera att:

- Nätverket i datacentret konfigureras enligt nätverkskraven för din Data Box Gateway-enhet. Mer information finns i [systemkraven för Data Box Gateway](data-box-gateway-system-requirements.md).

- För normala driftsförhållanden för din Data Box Gateway bör du ha en:

    - Minst 10 Mbit/s hämtar bandbredd för att säkerställa att enheten förblir uppdaterad.
    - Minst 20 Mbit/s dedikerad uppladdning och nedladdning av bandbredd för att överföra filer.

## <a name="create-a-new-resource"></a>Skapa en ny resurs

Om du har en befintlig Data Box Gateway-resurs för hantering av virtuella enheter hoppar du över det här steget och går till [Hämta aktiveringsnyckeln](#get-the-activation-key).

Om du vill skapa en Data Box Gateway-resurs gör du följande steg i Azure-portalen.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på:

    - Azure-portalen på [https://portal.azure.com](https://portal.azure.com)den här URL:en: .
    - Eller Azure Government-portalen på [https://portal.azure.us](https://portal.azure.us)den här URL:en: . Mer information finns i [Anslut till Azure Government med hjälp av portalen](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. Välj **+ Skapa en resurs**i den vänstra rutan . Sök efter **Data Box Edge / Data Box Gateway**. Välj Data Box Edge / Data Box Gateway. Välj **Skapa**.
3. Välj den prenumeration som du vill använda för Data Box Gateway-enhet. Välj den region där du vill distribuera databoxgatewayresursen. En lista över alla regioner där Azure Stack Edge-resursen är tillgänglig finns i [Azure-produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Välj den plats som är närmast den geografiska region där du vill distribuera enheten. Välj **Skapa**i alternativet **DataBox gateway** .

    ![Leta upp Data Box Gateway-tjänsten](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Ange eller välj följande **Projektinformation**på fliken **Grunderna** .
    
    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration    |Detta fylls i automatiskt baserat på den tidigare markeringen. Prenumerationen är kopplad till ditt faktureringskonto. |
    |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Läs mer om [Azure Resource Groups](../azure-resource-manager/management/overview.md).     |

5. Ange eller välj följande **instansinformation**.

    |Inställning  |Värde  |
    |---------|---------|
    |Namn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken som består av bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |   
    |Region     |En lista över alla regioner där Azure Stack Edge-resursen är tillgänglig finns i [Azure-produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). För Azure Government är alla myndighetsregioner som anges i [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/) tillgängliga. <br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|
    
    ![Skapa Data Box Gateway-resursen](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Välj **Granska + skapa**.
 
7. På fliken **Granska + skapa** läser du **prisinformationen,** **användningsvillkoren**och informationen för din resurs. Välj **Skapa**.

    ![Granska resursinformation för Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Det tar några minuter att skapa resursen. När resursen har skapats och distribuerats meddelas du. Välj **Gå till resurs**.

![Granska resursinformation för Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Ladda ned avbildningen av den virtuella enheten

När Data Box Gateway-resursen har skapats laddar du ned lämplig avbildning av den virtuella enheten för att etablera en virtuell enhet i värdsystemet. De virtuella enhetsavbildningarna är specifika för ett operativsystem.

> [!IMPORTANT]
> Programvaran som körs på Data Box Gateway kan bara användas med Data Box Gateway-resursen.

Följ dessa steg i [Azure-portalen](https://portal.azure.com/) för att hämta en virtuell enhetsavbildning.

1. I resursen som du skapade och välj sedan **Översikt**. Om du har en befintlig Azure Data Box Gateway-resurs väljer du resursen och går till **Översikt**. Välj **Enhetsinställning**.

    ![Ny Data Box Gateway-resurs](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. På panelen **Hämta avbildning** väljer du den virtuella enhetsavbildning som motsvarar operativsystemet på värdservern som används för att etablera den virtuella datorn. Bildfilerna är cirka 5,6 GB.
   
   * [VHDX för Hyper-V i Windows Server 2012 R2 och senare](https://aka.ms/dbe-vhdx-2012).
   * [VMDK för VMWare ESXi 6.0, 6.5 eller 6.7](https://aka.ms/dbe-vmdk).

    ![Hämta virtuell enhetsbild för Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Ladda ned och packa upp filen till en lokal enhet och skriv ned sökvägen till de uppackade filerna.


## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När databoxgatewayresursen är igång måste du hämta aktiveringsnyckeln. Den här nyckeln används för att aktivera och ansluta Data Box Gateway-enheten med resursen. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Välj den resurs som du skapade och välj sedan **Översikt**. Gå till panelen Konfigurera **och aktivera** i **enhetsinställningarna.**

    ![Konfigurera och aktivera panel](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Välj **Generera nyckel** för att skapa en aktiveringsnyckel. Välj kopieringsikonen för att kopiera nyckeln och spara den för senare användning.

    ![Hämta aktiveringsnyckeln](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Aktiveringsnyckeln upphör att gälla tre dagar efter att den skapats.
> - Om nyckeln har upphört att gälla genererar du en ny nyckel. Den äldre nyckeln är inte giltig.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Gateway-ämnen som att:

> [!div class="checklist"]
> * Skapa en ny resurs
> * Ladda ned avbildningen av den virtuella enheten
> * Hämta aktiveringsnyckeln

Gå vidare till nästa självstudie och lär dig hur du etablerar en virtuell dator för Data Box Gateway. Läs de detaljerade instruktionerna i följande avsnitt, beroende på ditt värdoperativsystem:

> [!div class="nextstepaction"]
> [Etablera en Data Box Gateway i Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

ELLER

> [!div class="nextstepaction"]
> [Etablera en Data Box Gateway i VMware](./data-box-gateway-deploy-provision-vmware.md)


