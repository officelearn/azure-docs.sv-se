---
title: "Azure Active Directory Domain Services: Uppdatera DNS-inställningarna för det virtuella Azure-nätverket | Microsoft Docs"
description: "Komma igång med Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 8bee2a25f196d645b27f30f21305b1550e44e07a
ms.contentlocale: sv-se
ms.lasthandoff: 06/30/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Uppdatera DNS-inställningarna för det virtuella Azure-nätverket
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Uppgift 4 – Uppdatera DNS-inställningarna för det virtuella Azure-nätverket
I de tidigare konfigurationsuppgifterna har du aktiverat Azure Active Directory Domain Services för katalogen. Härnäst ska du se till att datorerna i det virtuella nätverket kan ansluta och använda tjänsterna. I den här artikeln får du uppdatera DNS-serverinställningarna för det virtuella nätverket så att de pekar på de bägge IP-adresserna där Azure Active Directory Domain Services är tillgängliga i det virtuella nätverket.

> [!NOTE]
> Skriv ner IP-adresserna för Azure Active Directory Domain Services som visas på fliken **Konfigurera** för din katalog när du har aktiverat Azure Active Directory Domain Services för katalogen.
>
>

Utför följande konfigurationssteg för att uppdatera DNS-serverinställningen för det virtuella nätverket där Azure Active Directory Domain Services har aktiverats:

1. Gå till den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj **Nätverk** i den vänstra rutan.  
    Fönstret **Nätverk** öppnas.

    ![Fönstret virtuellt nätverk](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. På fliken **Virtuella nätverk** väljer du det virtuella nätverket som du har aktiverat Azure Active Directory Domain Services i för att visa dess egenskaper.
4. Klicka på fliken **Konfigurera**.

    ![Fönstret virtuellt nätverk](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. Se till att du anger båda IP-adresserna avsnittet **DNS-servrar** som visades i avsnittet **Domäntjänster** på fliken **Konfigurera** för katalogen.
6. Klicka på **Spara** i åtgärdsfönstret längst ned på sidan för att spara DNS-serverinställningarna för det här virtuella nätverket.

   ![Uppdatera DNS-serverinställningarna för det virtuella nätverket](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
>  Virtuella datorer i nätverket kan bara hämta de nya DNS-inställningarna efter en omstart. Om du vill att de ska hämta de uppdaterade DNS-inställningarna direkt kan du utlösa en omstart genom portalen, PowerShell eller CLI.
>
>

## <a name="next-steps"></a>Nästa steg
Uppgift 5: [Aktivera lösenordssynkronisering med Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

