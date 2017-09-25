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
ms.date: 09/20/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: c704ee189072ce8ed196d1ef0a23edd528a10025
ms.contentlocale: sv-se
ms.lasthandoff: 06/30/2017

---
# <a name="enable-azure-active-directory-domain-services-preview"></a>Aktivera Azure Active Directory Domain Services (förhandsversion)

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Uppgift 4 – uppdatera DNS-inställningarna för det virtuella Azure-nätverket
I de tidigare konfigurationsuppgifterna har du aktiverat Azure Active Directory Domain Services för katalogen. Härnäst ska du se till att datorerna i det virtuella nätverket kan ansluta och använda tjänsterna. I den här artikeln får du uppdatera DNS-serverinställningarna för det virtuella nätverket så att de pekar på de bägge IP-adresserna där Azure Active Directory Domain Services är tillgängliga i det virtuella nätverket.

Utför följande konfigurationssteg för att uppdatera DNS-serverinställningen för det virtuella nätverket där Azure Active Directory Domain Services har aktiverats:

1. Fliken **Översikt** visar **Nödvändiga konfigurationssteg** som ska utföras när din hanterade domän är helt etablerad. Det första konfigurationssteget är **Uppdatera DNS-serverinställningarna för det virtuella nätverket**.

    ![Domain Services - översiktsflik vid full etablering](./media/getting-started/domain-services-provisioned-overview.png)

2. När din domän är helt etablerad, visas två IP-adresser i den här panelen. IP-adresserna representerar en domänkontrollant för din hanterade domän.

3. Klicka på kopieringsknappen bredvid den för att kopiera den första IP-adressen till Urklipp. Klicka sedan på knappen **Konfigurera DNS-servrar**.

4. Klistra in den första IP-adressen i textrutan **Lägga till DNS-server** i bladet **DNS-servrar**. Rulla vågrätt till vänster för att kopiera den andra IP-adressen och klistra in de i textrutan **Lägga till DNS-server**.

    ![Domain Services – uppdatera DNS](./media/getting-started/domain-services-update-dns.png)

5. Klicka på **Spara** när du är klar för att uppdatera DNS-servrar för det virtuella nätverket.

> [!NOTE]
> Virtuella datorer i nätverket kan bara hämta de nya DNS-inställningarna efter en omstart. Om du vill att de ska hämta de uppdaterade DNS-inställningarna direkt kan du utlösa en omstart genom portalen, PowerShell eller CLI.
>
>

## <a name="next-step"></a>Nästa steg
Uppgift 5: [aktivera lösenordssynkronisering med Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

