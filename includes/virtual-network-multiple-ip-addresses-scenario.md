---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52272377"
---
## <a name="scenario"></a>Scenario
En virtuell dator med ett enda nätverkskort skapas och ansluts till ett virtuellt nätverk. Den virtuella datorn kräver tre olika *privata* IP-adresser och två *offentliga* IP-adresser. IP-adresser tilldelas till följande IP-konfigurationer:

* **IPConfig-1:** tilldelar en *statiska* privat IP-adress och en *statiska* offentlig IP-adress.
* **IPConfig-2:** tilldelar en *statiska* privat IP-adress och en *statiska* offentlig IP-adress.
* **IPConfig-3:** tilldelar en *statiska* privata IP-adress och ingen offentlig IP-adress.
  
    ![Flera IP-adresser](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

IP-konfigurationer har associerats med nätverkskortet när nätverkskortet har skapats och nätverkskortet är kopplat till den virtuella datorn när den virtuella datorn skapas. Typer av IP-adresser som används för scenariot är illustration. Du kan tilldela IP-adress och tilldelning av typer som du behöver.

> [!NOTE]
> Även om stegen i den här artikeln tilldelar ett enda nätverkskort alla IP-konfigurationer, kan du också tilldela flera IP-konfigurationer till alla NIC i en multi-NIC-VM. Om du vill veta hur du skapar en virtuell dator med flera nätverkskort kan du läsa den [skapa en virtuell dator med flera nätverkskort](../articles/virtual-machines/windows/multiple-nics.md) artikeln.