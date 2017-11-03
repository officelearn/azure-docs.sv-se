---
title: "Anslut Azure SSIS-integrering runtime till ett virtuellt nätverk | Microsoft Docs"
description: "Lär dig hur du ansluter till Azure-SSIS-integrering runtime till ett Azure virtual network."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: 8a58f55bd627594145661e1c8d5c1da360cd1e30
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Ansluta till en Azure-SSIS-integrering körning till ett virtuellt nätverk
Du måste ansluta Azure SSIS-integrering runtime (IR) till ett Azure virtual network (VNet) om någon av följande villkor är uppfyllda: 

- Du är värd för SSIS-katalogdatabasen på en SQL Server-hanterad instans (privat förhandsversion) som är en del av ett VNet.
- Du vill ansluta till lokala datalager från SSIS-paket som körs på Azure-SSIS Integration Runtime.

 Med Azure Data Factory version 2 (förhandsversion) kan du ansluta Azure-SSIS Integration Runtime till ett klassiskt virtuellt nätverk. För närvarande stöds Azure Resource Manager VNet inte ännu. Du kan dock arbeta runt som visas i följande avsnitt. 

 > [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen om Data Factory version 1](v1/data-factory-introduction.md).

Om SSIS-paket åtkomst till endast de offentliga moln datalager, behöver du inte ansluta till Azure-SSIS IR till ett virtuellt nätverk. Om SSIS-paket åtkomst till lokala datalager, måste du ansluta Azure SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket. Om SSIS-katalogen finns i Azure SQL-databas som inte är i VNet, måste du öppna lämpliga portar. Om SSIS-katalogen finns i Azure SQL hanteras-instans som är i ett klassiskt virtuellt nätverk, kan du delta i Azure-SSIS IR till samma klassiska virtuella nätverk (eller) en annan klassiska virtuella nätverk som har en anslutning med klassen till klassiska VNet med Azure SQL hanteras instansen. Följande avsnitt innehåller mer information.  

## <a name="access-on-premises-data-stores"></a>Åtkomst till lokala datalager
Om SSIS-paket åtkomst till lokala datalager, Anslut din Azure-SSIS-integrering runtime till ett virtuellt nätverk som är anslutet till det lokala nätverket. Här följer några viktiga saker att Observera: 

- Om det finns inga befintliga virtuella nätverk som är anslutna till ditt lokala nätverk måste du först skapa en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering runtime att ansluta till. Konfigurera sedan en plats-till-plats [VPN-gatewayanslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) anslutning från det virtuella nätverket till ditt lokala nätverk.
- Om det finns ett existerande klassiska VNet som är anslutna till ditt lokala nätverk på samma plats som din Azure-SSIS-integrering runtime, kan du delta i din Azure-SSIS-integrering runtime till den.
- Om det finns en befintlig klassiska virtuella nätverk som är ansluten till nätverket lokalt på en annan plats från din Azure-SSIS-integrering Runtime måste du först skapa en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering Runtime att ansluta till. Konfigurera sedan en [klassisk till klassiska VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) anslutning.
- Om det finns en befintlig Azure Resource Manager VNet som är ansluten till nätverket på plats måste du först skapa en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering runtime att ansluta till. Konfigurera sedan en [klassisk till Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning.

## <a name="domain-name-services-server"></a>Domain Name Services-server 
Om du behöver använda din egen Services DNS (Domain Name)-server i ett VNet kopplas med din Azure-SSIS-integrering runtime följer vägledning för att [säkerställa att noderna i Azure-SSIS-integrering-körningsmiljön i VNet kan matcha Azure-slutpunkter](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Nätverkssäkerhetsgrupp
Om du behöver implementera Nätverkssäkerhetsgrupp (NSG) i ett VNet kopplas med din Azure-SSIS-integrering Runtime tillåta inkommande/utgående traffics via följande portar:

| Portar | Riktning | Transportprotokoll | Syfte | Inkommande källa för utgående mål |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | Inkommande | TCP | Azure-tjänster använder dessa portar för att kommunicera med noderna i Azure-SSIS-integrering-körningsmiljön i VNet. | Internet | 
| 443 | Utgående | TCP | Noderna i din Azure-SSIS-integrering körning i virtuella nätverk använda den här porten för att komma åt Azure-tjänster, till exempel Azure Storage, Event Hub osv. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Utgående | TCP | Noderna i Azure-SSIS-integrering-körningsmiljön i VNet använda dessa portar för att komma åt SSISDB hos dina Azure SQL Database-server (gäller inte för SSISDB Azure SQL-instans som hanteras som värd). | Internet | 

## <a name="script-to-configure-vnet"></a>Skript för att konfigurera virtuella nätverk 
Du kan använda den interaktiva PowerShell-skriptet från [i den här artikeln](create-azure-ssis-integration-runtime.md) att etablera en Azure-SSIS-integrering körning i ett VNet. Skriptet konfigurerar automatiskt VNet behörigheter och inställningar så att du kan gå du Azure SSIS-integrering runtime till VNet.  


## <a name="use-portal-to-configure-vnet"></a>Använda portalen för att konfigurera virtuella nätverk
Kör skriptet är det enklaste sättet att konfigurera virtuella nätverk. Om du inte har åtkomst till att konfigurera virtuella nätverk för den automatiska konfigurationen misslyckas, ägaren av det virtuella nätverket / du kan försöka att konfigurera dem manuellt i följande steg:

### <a name="find-the-resource-id-for-your-azure-vnet"></a>Hitta resurs-ID för ditt Azure VNet.
 
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **fler tjänster**. Filtrera efter och välj **virtuella nätverk (klassiskt)**.
3. Filtrera efter och välj din **virtuellt nätverk** i listan. 
4. På sidan virtuella nätverk (klassiska) Välj **egenskaper**. 

    ![klassiska VNet-resurs-ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Klicka på kopieringsknappen för den **resurs-ID** Kopiera resurs-ID för det klassiska nätverket till Urklipp. Spara ID från Urklipp i OneNote eller en fil.
6. Klicka på **undernät** på den vänstra menyn och kontrollera att antalet **tillgängliga adresser** är större än noderna i din Azure-SSIS-integrering runtime.

    ![Antal tillgängliga adresser i virtuella nätverk](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Anslut **MicrosoftAzureBatch** till **klassiska Virtual Machine-deltagare** roll för VNet. 
    1. Åtkomstkontroll (IAM) på den vänstra menyn och klicka på **Lägg till** i verktygsfältet.
    
        ![Åtkomstkontroll -> Lägg till](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. I **lägga till behörigheter** väljer **klassiska Virtual Machine-deltagare** för **rollen**. Typen **MicrosoftAzureBatch** i den **Välj** textruta och välj sedan **MicrosoftAzureBatch** från listan över sökresultat. 
    
        ![Lägg till behörigheter – Sök](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Klicka på Spara för att spara inställningarna och stänga sidan.
    
        ![Spara inställningar](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Bekräfta att du ser **MicrosoftAzureBatch** i listan över deltagare.
    
        ![Bekräfta AzureBatch åtkomst](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Kontrollera att Azure Batch-provider är registrerad i Azure-prenumeration som har VNet eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration är prenumerationen registrerad för Azure Batch.
    1. I Azure-portalen klickar du på **prenumerationer** på den vänstra menyn. 
    2. Välj din **prenumeration**. 
    3. Klicka på **resursproviders** till vänster och kontrollera att `Microsoft.Batch` är en registrerad provider. 
    
        ![bekräftelse-batch-registrerade](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Om du inte ser `Microsoft.Batch` i listan för att registrera den, [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 
         


## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS runtime finns i följande avsnitt: 

- [Azure-SSIS-integrering Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integration körningar i allmänhet inklusive Azure SSIS-IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-deploy-ssis-packages-azure.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln kan utökas med självstudien och innehåller instruktioner om att använda Azure SQL-hanterade instanser (privat förhandsgranskning) och ansluta IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
