---
title: 'Azure Active Directory Domain Services: Komma igång | Microsoft Docs'
description: Aktivera Azure Active Directory Domain Services med Azure-portalen
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: 607ca1900200f3b163d69a7aa4cb700b842f9334
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586839"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivera Azure Active Directory Domain Services med Azure-portalen


## <a name="before-you-begin"></a>Innan du börjar
Se [Nätverksrelaterade aspekter att tänka på med Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Uppgift 2: konfigurera nätverksinställningar
Nästa konfigurationsåtgärd är att skapa ett virtuellt Azure-nätverk och en dedikerad undernät i den. Du aktiverar Azure Active Directory Domain Services i detta undernät inom ditt virtuella nätverk. Du kan också välja ett befintligt virtuellt nätverk och skapa dedikerade undernätet i den.

1. Klicka på **för virtuella nätverk** att välja ett virtuellt nätverk.
    > [!NOTE]
    > **Klassiska virtuella nätverk stöds inte för nya distributioner.** Klassiska virtuella nätverk stöds inte för nya distributioner. Befintliga hanterade domäner som har distribuerats i klassiska virtuella nätverk fortfarande användas. Microsoft gör att du kan migrera en befintlig hanterade domän från ett klassiskt virtuellt nätverk till ett virtuellt nätverk för Resource Manager inom en snar framtid.
    >

2. På den **Välj virtuellt nätverk** kan du se alla befintliga virtuella nätverk. Du ser bara de virtuella nätverk som tillhör resursgruppen och Azure-plats som du har valt på den **grunderna** sidan i guiden.
3. Välj det virtuella nätverket som Azure AD Domain Services ska aktiveras. Du kan välja ett befintligt virtuellt nätverk eller skapa en ny.

  > [!TIP]
  > **Du kan inte flytta din hanterade domän till ett annat virtuellt nätverk när du har aktiverat Azure AD Domain Services.** Välj rätt virtuellt nätverk för att aktivera din hanterade domän. När du har skapat en hanterad domän kan flytta du inte den till ett annat virtuellt nätverk utan att ta bort den hanterade domänen. Vi rekommenderar att du granskar den [nätverk överväganden för Azure Active Directory Domain Services](active-directory-ds-networking.md) innan du fortsätter.  
  >

4. **Skapa virtuellt nätverk:** klickar du på **Skapa nytt** att skapa ett nytt virtuellt nätverk. Använd en dedikerad undernät för Azure AD Domain Services. Till exempel skapa ett undernät med namnet DomainServices, vilket gör det lättare för andra administratörer att förstå vad som har distribuerats i undernätet. Klicka på **OK** när du är klar.

    ![Välj virtuella nätverk](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > Se till att välja ett adressutrymme som ligger inom det privata IP-adressutrymmet. IP-adresser som du inte äger som är offentligt adressutrymme orsaka fel i Azure AD Domain Services.

5. **Befintligt virtuellt nätverk:** om du planerar att välja ett befintligt virtuellt nätverk [skapa ett dedikerat undernät med virtuella nätverk](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), och välj sedan det undernätet. Klicka på **virtuellt nätverk** att välja det befintliga virtuella nätverket. Klicka på **undernät** att välja dedikerad undernät i det befintliga virtuella nätverket att aktivera din nya hanterade domän. Klicka på **OK** när du är klar.

    ![Välj undernät i det virtuella nätverket](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Riktlinjer för att välja ett undernät**
  > 1. Använd en dedikerad undernät för Azure AD Domain Services. Distribuera inte eventuella andra virtuella datorer till det här undernätet. Den här konfigurationen kan du konfigurera nätverkssäkerhetsgrupper (NSG: er) för dina arbetsbelastningar för virtuella datorer utan att avbryta din hanterade domän. Mer information finns i [nätverk överväganden för Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Välj inte Gateway-undernätet för att distribuera Azure AD Domain Services, eftersom den inte är en konfiguration som stöds.
  3. Det undernät som du har valt måste ha minst 3-5 tillgängliga IP-adresser i dess adressutrymme.
  >

6. När du är klar klickar du på **OK** för att komma till den **administratörsgruppen** sidan i guiden.


## <a name="next-step"></a>Nästa steg
[Uppgift 3: Konfigurera administrativ grupp och aktivera Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
