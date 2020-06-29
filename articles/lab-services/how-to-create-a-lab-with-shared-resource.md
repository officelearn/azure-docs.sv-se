---
title: Så här skapar du ett labb med en delad resurs | Azure Lab Services
description: Lär dig hur du skapar ett labb som kräver en resurs som delas mellan eleverna.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9cb5698f95aa220208fb02a35a52ff5363a173ac
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85443374"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Så här skapar du ett labb med en delad resurs i Azure Lab Services

Ibland när du skapar ett klass rums labb kan det finnas vissa resurser som behöver delas mellan alla studenter i ett labb.  Du kan till exempel ha en licens Server eller SQL Server för en databas klass.  Den här artikeln beskriver stegen för att aktivera den delade resursen för ett labb.  Vi pratar också om hur du begränsar åtkomsten till den delade resursen.

## <a name="architecture"></a>Arkitektur

Som du ser i diagrammet nedan kommer vi att ha ett labb konto med ett labb.  Labb kontot kommer att ha inställningarna för VNet-peering så att det virtuella nätverket för labbet är anslutet till nätverket i den delade resursen.  I diagrammet nedan finns det två virtuella nätverk med icke överlappande IP-intervall.  Dessa IP-intervall är bara exempel intervall.  Observera också att det virtuella nätverket för den delade resursen finns i samma prenumeration som labb kontot.

![Labb tjänster med delad resurs arkitektur](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Konfigurera delad resurs

Det virtuella nätverket för den delade resursen måste skapas innan labbet skapas.  Mer information om hur du skapar ett virtuellt nätverk finns i [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md).  Planera ut virtuella nätverks intervall så att de inte överlappar test datorernas IP-adress är viktigt.  Mer information om hur du planerar ditt nätverk finns i artikeln [Planera virtuella nätverk](../virtual-network/virtual-network-vnet-plan-design-arm.md) . I vårt exempel finns den delade resursen i ett virtuellt nätverk med intervallet 10.2.0.0/16.  Om du inte redan gjort det kan du [skapa ett undernät](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) som ska innehålla den delade resursen.  I exemplet använder vi intervallet 10.2.0.0/24, men intervallet kan vara olika beroende på nätverkets behov.

Den delade resursen kan vara program vara som körs på en virtuell dator eller en Azure-tjänst. Den delade resursen ska vara tillgänglig via den privata IP-adressen.  Genom att endast göra den delade resursen tillgänglig via privat IP begränsar du åtkomsten till den delade resursen.

Diagrammet visar också en nätverks säkerhets grupp (NSG) som kan användas för att begränsa trafik som kommer från den virtuella student datorn.  Du kan till exempel skriva en säkerhets regel som anger trafik från den virtuella elevens IP-adresser kan bara komma åt en delad resurs och inget annat.  Mer information om hur du ställer in säkerhets regler finns i [Hantera nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md#work-with-security-rules). Om du vill begränsa åtkomsten till en delad resurs till ett särskilt labb hämtar du IP-adressen för labbet från [labb inställningarna från labb kontot](manage-labs.md#view-labs-in-a-lab-account) och anger en regel för inkommande trafik som endast tillåter åtkomst från den IP-adressen.  Glöm inte att tillåta portarna 49152 till 65535 för den IP-adressen.  Om du vill kan du hitta den privata IP-adressen för elevens virtuella datorer med hjälp av [sidan för poolen för virtuella datorer](how-to-set-virtual-machine-passwords.md).

Om din delade resurs är en virtuell Azure-dator som kör nödvändig program vara kan du behöva ändra standard brand Väggs reglerna för den virtuella datorn.

## <a name="lab-account"></a>Labb konto

Om du vill använda en delad resurs måste labb kontot konfigureras att använda ett peer-kopplat [virtuellt nätverk](how-to-connect-peer-virtual-network.md).  I det här fallet kommer vi att peer-koppla till det virtuella nätverket som innehåller den delade resursen.

>[!WARNING]
>Labbet för klassen måste skapas **efter** att Lab-kontot har peer-kopplats till det virtuella nätverket i den delade resursen.  
Mall dator

När ditt labb konto har peer-kopplats till det virtuella nätverket bör mallen nu ha åtkomst till den delade resursen.  Du kan behöva uppdatera brand Väggs reglerna, beroende på vilken delad resurs som används.
