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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79474466"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Självstudie: förbereda för att distribuera Azure Data Box Gateway

Det här är den första självstudien i serien med distributionssjälvstudier som krävs för en fullständig Azure Data Box Gateway-distribution. Den här självstudien beskriver hur du förbereder Azure-portalen för att distribuera Data Box Gateway-resursen.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.

I den här guiden får du lära dig att:

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

Innan du börjar ska du kontrollera att:

* Din Microsoft Azure prenumeration är aktive rad för en Azure Stack Edge-resurs. Se till att du har använt en prenumeration som stöds, till exempel [Microsoft Enterprise-avtal (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)eller [Microsoft Azure-sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Du har ägar-eller deltagar åtkomst på resurs grupps nivå för Data Box Edge/Data Box Gateway, IoT Hub och Azure Storage resurser.
    - Om du vill skapa en Data Box Edge-/Data Box Gateway-resurs, bör du ha behörighet som deltagare (eller högre) som är begränsade till resurs grupps nivå. Du måste också kontrol lera att `Microsoft.DataBoxEdge` providern är registrerad. Information om hur du registrerar finns i [Registrera resurs leverantör](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Om du vill skapa en lagrings konto resurs måste du igen med deltagar-eller högre åtkomst omfång på resurs grupps nivå. Azure Storage är som standard en registrerad resurs leverantör.
- Du har administratörs-eller användar åtkomst till Microsoft Graph API. Mer information finns i [referens för Microsoft Graph-behörigheter](https://docs.microsoft.com/graph/permissions-reference).
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

Innan du börjar ska du kontrollera att:

- Nätverket i datacentret konfigureras enligt nätverkskraven för din Data Box Gateway-enhet. Mer information finns i [system kraven för data Box Gateway](data-box-gateway-system-requirements.md).

- För normala drift villkor för din Data Box Gateway bör du ha ett:

    - Minst 10 Mbit/s Ladda ned bandbredd för att se till att enheten förblir uppdaterad.
    - Minst 20 Mbit/s dedikerad överföring och nedladdning av bandbredd för överföring av filer.

## <a name="create-a-new-resource"></a>Skapa en ny resurs

Om du har en befintlig Data Box Gateway-resurs för hantering av virtuella enheter hoppar du över det här steget och går till [Hämta aktiveringsnyckeln](#get-the-activation-key).

För att skapa en Data Box Gateway resurs, utför följande steg i Azure Portal.

1. Använd dina Microsoft Azure autentiseringsuppgifter för att logga in på:

    - Azure Portal på denna URL: [https://portal.azure.com](https://portal.azure.com).
    - Eller, Azure Government portalen på denna URL: [https://portal.azure.us](https://portal.azure.us). Mer information finns i [ansluta till Azure Government med hjälp av portalen](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. I den vänstra rutan väljer du **+ skapa en resurs**. Sök efter **data Box Edge/data Box Gateway**. Välj Data Box Edge/Data Box Gateway. Välj **Skapa**.
3. Välj den prenumeration som du vill använda för Data Box Gateway enhet. Välj den region där du vill distribuera Data Box Gateway resursen. För en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig, se [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Välj den plats som är närmast den geografiska region där du vill distribuera enheten. I alternativet **data Box Gateway** väljer du **skapa**.

    ![Leta upp Data Box Gateway-tjänsten](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. På fliken **grundläggande** anger eller väljer du följande **projekt information**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration    |Detta fylls i automatiskt baserat på den tidigare markeringen. Prenumerationen är kopplad till ditt faktureringskonto. |
    |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Lär dig mer om [Azures resurs grupper](../azure-resource-manager/management/overview.md).     |

5. Ange eller Välj följande **instans information**.

    |Inställning  |Värde  |
    |---------|---------|
    |Namn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken som består av bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |   
    |Region     |För en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig, se [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). För Azure Government är alla de myndigheter som anges i Azure- [regionerna](https://azure.microsoft.com/global-infrastructure/regions/) tillgängliga. <br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|
    
    ![Skapa Data Box Gateway-resursen](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Välj **Granska + skapa**.
 
7. På fliken **Granska + skapa** granskar du **pris informationen**, **användningsvillkor**och informationen för resursen. Välj **Skapa**.

    ![Granska Data Box Gateway resurs information](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Det tar några minuter att skapa resursen. När resursen har skapats och distribuerats får du ett meddelande. Välj **Gå till resurs**.

![Granska Data Box Gateway resurs information](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Ladda ned avbildningen av den virtuella enheten

När Data Box Gateway-resursen har skapats laddar du ned lämplig avbildning av den virtuella enheten för att etablera en virtuell enhet i värdsystemet. De virtuella enhets avbildningarna är bara för ett operativ system.

> [!IMPORTANT]
> Programvaran som körs på Data Box Gateway kan bara användas med Data Box Gateway-resursen.

Följ de här stegen i [Azure Portal](https://portal.azure.com/) för att ladda ned en virtuell enhets avbildning.

1. I resursen som du skapade och välj sedan **Översikt**. Om du har en befintlig Azure Data Box Gateway resurs väljer du resursen och går till **Översikt**. Välj **enhets konfiguration**.

    ![Ny Data Box Gateway-resurs](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. På panelen **Ladda ned avbildning** väljer du den virtuella enhets avbildning som motsvarar operativ systemet på den värd server som används för att etablera den virtuella datorn. Bildfilerna är ungefär 5,6 GB.
   
   * [VHDX för Hyper-V i Windows Server 2012 R2 och senare](https://aka.ms/dbe-vhdx-2012).
   * [VMDK för VMWare ESXi 6.0, 6.5 eller 6.7](https://aka.ms/dbe-vmdk).

    ![Ladda ned Data Box Gateway virtuell enhets avbildning](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Ladda ned och packa upp filen till en lokal enhet och skriv ned sökvägen till de uppackade filerna.


## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När Data Box Gateway-resursen är igång måste du hämta aktiverings nyckeln. Den här nyckeln används för att aktivera och ansluta Data Box Gateway-enheten med resursen. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Välj den resurs som du skapade och välj sedan **Översikt**. I **enhets konfigurationen**går du till panelen **Konfigurera och aktivera** .

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


