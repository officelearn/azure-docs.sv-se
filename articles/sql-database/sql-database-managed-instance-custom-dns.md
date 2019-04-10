---
title: Azure SQL Database Managed Instance anpassad DNS | Microsoft Docs
description: Det här avsnittet beskriver konfigurationsalternativ för en anpassad DNS med en Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: bb5890b883b6062d834b928bff28a26a3664fb64
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361699"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurera en anpassad DNS för Azure SQL Database Managed Instance

En Azure SQL Database Managed Instance måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Det finns ett par scenarier (till exempel db e-post, länkade servrar till andra SQL-instanser i miljön moln eller hybrid) som kräver privata värdnamn som matchas från den hanterade instansen. I så fall måste du konfigurera en anpassad DNS i Azure. Eftersom Managed Instance använder samma DNS för dess informationsresurser, måste DNS-konfiguration för virtuellt nätverk vara kompatibel med Managed Instance.

   > [!IMPORTANT]
   > Använd alltid fullständigt kvalificerade domännamn (FQDN) för e-postservrar, SQL-servrar och andra tjänster även om de är i privat DNS-zonen. Till exempel använda `smtp.contoso.com` för e-postserver eftersom enkel `smtp` löses inte korrekt.

Om du vill göra en anpassad DNS-konfiguration är kompatibel med den hanterade instansen måste du:

- Konfigurera anpassad DNS-server så att den är kan matcha offentliga domännamn
- Placera Azures rekursiva matchare DNS-IP-adressen 168.63.129.16 i slutet av listan över DNS-virtuella nätverk

## <a name="setting-up-custom-dns-servers-configuration"></a>Anpassad konfiguration av DNS-servrar

1. Hitta den anpassade DNS-alternativet för ditt virtuella nätverk i Azure-portalen.

   ![anpassad dns-alternativ](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png)

2. Växla till anpassade och ange din anpassade DNS-server IP-adress som Azures rekursiva matchare IP-adressen 168.63.129.16.

   ![anpassad dns-alternativ](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png)

   > [!IMPORTANT]
   > Inte kan Azures rekursiva matchare i listan över DNS ge den hanterade instansen att ange ett felaktigt tillstånd där de anpassade DNS-servrarna inte kan användas av någon anledning. Återställa från att tillståndet kan kräva att du vill skapa en ny instans i ett virtuellt nätverk med efterlevnadsprinciperna för nätverk, skapa nivå instansdata och återställa databaserna. Ställa in Azures rekursiva matchare som säkerställer att den sista posten i listan över DNS, även om det misslyckas alla anpassade DNS-servrar, offentliga namn kan fortfarande matchas.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- En självstudiekurs som visar hur du skapar en ny hanterad instans finns i [skapar en hanterad instans](sql-database-managed-instance-get-started.md).
- Information om hur du konfigurerar ett virtuellt nätverk för en hanterad instans finns i [konfiguration av virtuellt nätverk för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md)
