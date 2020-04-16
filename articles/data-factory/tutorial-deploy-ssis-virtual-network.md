---
title: Självstudiekurs för att konfigurera en Azure-SSIS-integreringskörning för att ansluta till ett virtuellt nätverk
description: Lär dig hur du ansluter till en Azure-SSIS-integreringskörning till ett virtuellt Azure-nätverk.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 7470af23059ccd07e48050e6dc34521e299a986f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418617"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Konfigurera en SSIS-integrationskörning (Azure-SQL Server Integration Services) för att ansluta till ett virtuellt nätverk

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här självstudien innehåller grundläggande steg för att använda Azure-portalen för att konfigurera en SSIS-programkörning (Azure-SQL Server Integration Services) för att ansluta till ett virtuellt nätverk.

Stegen omfattar:

- Konfigurera ett virtuellt nätverk.
- Gå med i Azure-SSIS IR till ett virtuellt nätverk från Azure Data Factory-portalen.

## <a name="prerequisites"></a>Krav

- **Azure-SSIS-integreringskörning**. Om du inte har en Azure-SSIS-integreringskörning [etablerar du en Azure-SSIS-integreringskörning i Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) innan du börjar.

- **Användarbehörighet**. Användaren som skapar Azure-SSIS IR måste ha [rolltilldelningen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) minst på Azure Data Factory-resursen med något av alternativen nedan:

    - Använd den inbyggda nätverksdeltagarerollen. Den här rollen levereras med _behörigheten Microsoft.Network/\* _ som har ett mycket större utrymme än nödvändigt.
    - Skapa en anpassad roll som bara innehåller nödvändig _microsoft.network/virtualNetworks/\*/join/action-behörighet._ Om du också vill ta med egna offentliga IP-adresser för Azure-SSIS IR när du ansluter till ett virtuellt Azure Resource Manager-nätverk, inkludera även _behörigheten Microsoft.Network/publicIPAddresses/*/join/action_ i rollen.

- **Virtuellt nätverk**.

    - Om du inte har ett virtuellt nätverk [skapar du ett virtuellt nätverk med Azure-portalen](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - Kontrollera att det virtuella nätverkets resursgrupp kan skapa och ta bort vissa Azure-nätverksresurser.
    
        Azure-SSIS IR måste skapa vissa nätverksresurser under samma resursgrupp som det virtuella nätverket. Dessa resurser omfattar:
        - En Azure-belastningsutjämnare med namnet * \<Guid>-azurebatch-cloudserviceloadbalancer*
        - En nätverkssäkerhetsgrupp med\<namnet * Guid>-azurebatch-cloudservicenetworksecuritygroup
        - En offentlig Azure-IP-adress med namnet -azurebatch-cloudservicepublicip
    
        Dessa resurser skapas när din Azure-SSIS IR startar. De tas bort när din Azure-SSIS IR stoppas. Använd inte dessa nätverksresurser i dina andra resurser för att undvika att blockera din Azure-SSIS IR från att stoppas.

    - Kontrollera att du inte har något [resurslås](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) på resursgruppen/prenumerationen som det virtuella nätverket tillhör. Om du konfigurerar ett skrivskyddat/borttagningslås misslyckas och stoppar azure-SSIS IR, eller så slutar det svara.

    - Se till att du inte har en Azure-princip som förhindrar att följande resurser skapas under den resursgrupp/prenumeration som det virtuella nätverket tillhör:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Nedan **omfattas inte scenarier för nätverkskonfiguration** i den här självstudien:
    - Om du tar med egna offentliga IP-adresser för Azure-SSIS IR.
    - Om du använder din egen DNS-server (Domain Name System).
    - Om du använder en nätverkssäkerhetsgrupp (NSG) i undernätet.
    - Om du använder Azure ExpressRoute eller en användardefinierad väg (UDR).
    - Om du använder anpassad Azure-SSIS IR.
    
    Mer information finns i [konfigurationen av virtuella nätverk](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Använd Azure-portalen för att konfigurera ett virtuellt nätverk innan du försöker ansluta till en Azure-SSIS IR till den.

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare datafabrikens användargränssnitt.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Fler tjänster**. Filtrera efter och välj **Virtuella nätverk**.

1. Filtrera efter och välj ditt virtuella nätverk i listan.

1. På sidan **Virtuellt nätverk** väljer du **Egenskaper**.

1. Välj kopieringsknappen för **RESURS-ID** om du vill kopiera resurs-ID:et för det virtuella nätverket till Urklipp. Spara ID:t från Urklipp i OneNote eller en fil.

1. Välj **Undernät**på den vänstra menyn .

    - Kontrollera att det undernät du väljer har tillräckligt med ledigt adressutrymme för Azure-SSIS IR att använda. Lämna tillgängliga IP-adresser för minst två gånger IR-nodnumret. Azure reserverar vissa IP-adresser i varje undernät. Dessa adresser kan inte användas. De första och sista IP-adresserna för undernäten är reserverade för protokollkonformans och ytterligare tre adresser används för Azure-tjänster. Mer information finns i [Finns det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Välj inte GatewaySubnet för att distribuera en Azure-SSIS IR. Den är avsedd för virtuella nätverksgateways.
    - Använd inte ett undernät som uteslutande används av andra Azure-tjänster (till exempel SQL Database-hanterad instans, AppTjänst och så vidare).

1. Kontrollera att Azure Batch-providern är registrerad i Azure-prenumerationen som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory-portalen registreras Azure Batch-providern automatiskt åt dig.)

   1. Välj **Prenumerationer**på den vänstra menyn i Azure-portalen .

   1. Välj din prenumeration.

   1. Till vänster väljer du **Resursleverantörer**och bekräftar att **Microsoft.Batch** är en registrerad leverantör.

   ![Bekräftelse av statusen "Registrerad"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.Batch** i listan skapar du [ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration om du vill registrera det. Du kan ta bort den senare.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Ansluta till Azure-SSIS IR till ett virtuellt nätverk

När du har konfigurerat det virtuella nätverket Azure Resource Manager eller det klassiska virtuella nätverket kan du ansluta till Azure-SSIS IR till det virtuella nätverket:

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare datafabrikens användargränssnitt.

1. Välj **Datafabriker**på den vänstra menyn i [Azure-portalen](https://portal.azure.com). Om du inte ser **Datafabriker** på menyn väljer du **Fler tjänster**och väljer sedan **Datafabriker**i avsnittet **INTELLIGENS + ANALYTICS** .

   ![Lista över datafabriker](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Välj din datafabrik med Azure-SSIS IR i listan. Du ser hemsidan för din datafabrik. Välj panelen **Författare & Bildskärm.** Du ser datafabriksgränssnittet på en separat flik.

   ![Datafabrikens startsida](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Växla till fliken **Redigera** i datafabriken, välj **Anslutningar**och växla till fliken **Integrationskörningar.**

   ![Fliken "Integrationskörning"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Om din Azure-SSIS IR körs väljer du **stoppknappen** för din Azure-SSIS IR i listan **Integration runtimes** i kolumnen **Åtgärder.** Du kan inte redigera din Azure-SSIS IR förrän du stoppar den.

   ![Stoppa IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Välj knappen **Redigera** för din Azure-SSIS IR i kolumnen **Åtgärder** i listan **Integrationkörningstider.**

   ![Redigera integrationskörningen](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. På inställningspanelen för integreringskörning går du vidare genom avsnitten **Allmänna inställningar** och **SQL-inställningar** genom att välja knappen **Nästa.**

1. I avsnittet **Avancerade inställningar:**
   1. Markera kryssrutan **Välj ett virtuella nätverk för din Azure-SSIS-integrationskörning för att gå med, tillåt ADF att skapa vissa nätverksresurser och eventuellt ta med egna statiska offentliga IP-adresser.**

   1. För **prenumeration**väljer du den Azure-prenumeration som har ditt virtuella nätverk.

   1. För **Plats**väljs samma plats för integrationskörningen.

   1. För **Typ**väljer du typ av virtuellt nätverk: klassiskt eller Azure Resource Manager. Vi rekommenderar att du väljer ett virtuellt Azure Resource Manager-nätverk, eftersom klassiska virtuella nätverk snart kommer att inaktuell.

   1. För **VNet-namn**väljer du namnet på det virtuella nätverket. Det bör vara samma som används för din Azure SQL Database-server med slutpunkter för virtuella nätverkstjänster eller hanterad instans med privat slutpunkt som värd för SSISDB. Eller så ska den vara samma som är ansluten till ditt lokala nätverk. Annars kan det vara valfritt virtuellt nätverk för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

   1. För **Undernätsnamn**väljer du namnet på undernätet för det virtuella nätverket. Det bör vara samma som används för din Azure SQL Database-server med virtuella nätverkstjänstslutpunkter som värd för SSISDB. Eller så ska det vara ett annat undernät än det som används för din hanterade instans med privat slutpunkt som värd för SSISDB. Annars kan det vara vilket undernät som helst för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

   1. Välj **VNet-validering**. Om valideringen lyckas väljer du **Fortsätt**.

   ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Granska alla inställningar för din Azure-SSIS IR i avsnittet **Sammanfattning.** Välj sedan **Uppdatera**.

1. Starta din Azure-SSIS IR genom att välja **Start-knappen** i kolumnen **Åtgärder** för din Azure-SSIS IR. Det tar cirka 20 till 30 minuter att starta Azure-SSIS IR som ansluter till ett virtuellt nätverk.

## <a name="next-steps"></a>Efterföljande moment

Läs mer om [hur du går med i Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).
