---
title: 'Azure Active Directory Domain Services: Felsöka nätverks säkerhets grupper | Microsoft Docs'
description: Felsöka nätverks säkerhets grupps konfigurationen för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 450ee5635b378ed7c4d4e4bedc1c4245f6b52d70
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "70743445"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Felsök ogiltig nätverks konfiguration för din hanterade domän
Den här artikeln hjälper dig att felsöka och lösa nätverksrelaterade konfigurations fel som resulterar i följande aviserings meddelande:

## <a name="alert-aadds104-network-error"></a>Aviserings AADDS104: Nätverksfel
**Aviserings meddelande:** *Microsoft kan inte komma åt domän kontrol Lanterna för den här hanterade domänen. Detta kan inträffa om en nätverks säkerhets grupp (NSG) som kon figurer ATS i ditt virtuella nätverk blockerar åtkomsten till den hanterade domänen. En annan möjlig orsak är om det finns en användardefinierad väg som blockerar inkommande trafik från Internet.*

Ogiltiga NSG-konfigurationer är den vanligaste orsaken till nätverks fel för Azure AD Domain Services. Nätverks säkerhets gruppen (NSG) som har kon figurer ATS för ditt virtuella nätverk måste tillåta åtkomst till [vissa portar](network-considerations.md#network-security-groups-and-required-ports). Om dessa portar är blockerade kan inte Microsoft övervaka eller uppdatera din hanterade domän. Dessutom påverkas synkroniseringen mellan din Azure AD-katalog och din hanterade domän. När du skapar din NSG bör du se till att portarna är öppna för att undvika avbrott i tjänsten.

### <a name="checking-your-nsg-for-compliance"></a>Kontrollerar NSG för efterlevnad

1. Gå till sidan [nätverks säkerhets grupper](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) i Azure Portal
2. I tabellen väljer du den NSG som är kopplad till under nätet där din hanterade domän är aktive rad.
3. Under **Inställningar** i den vänstra panelen klickar du på **inkommande säkerhets regler**
4. Granska reglerna på plats och identifiera vilka regler som blockerar åtkomsten till [dessa portar](network-considerations.md#network-security-groups-and-required-ports)
5. Redigera NSG för att säkerställa efterlevnad genom att antingen ta bort regeln, lägga till en regel eller skapa en ny NSG helt. Steg för att [lägga till en regel](#add-a-rule-to-a-network-security-group-using-the-azure-portal) eller skapa en ny, kompatibel NSG finns under

## <a name="sample-nsg"></a>Exempel på NSG
I följande tabell visas en exempel-NSG som behåller din hanterade domän samtidigt som Microsoft kan övervaka, hantera och uppdatera information.

![exempel på NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services kräver obegränsad utgående åtkomst från det virtuella nätverket. Vi rekommenderar att du inte skapar någon ytterligare NSG-regel som begränsar utgående åtkomst för det virtuella nätverket.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Lägg till en regel i en nätverks säkerhets grupp med hjälp av Azure Portal
Om du inte vill använda PowerShell kan du manuellt lägga till enskilda regler i NSG: er med hjälp av Azure Portal. Utför följande steg för att skapa regler i nätverks säkerhets gruppen:

1. Gå till sidan [nätverks säkerhets grupper](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) i Azure Portal.
2. I tabellen väljer du den NSG som är kopplad till under nätet där din hanterade domän är aktive rad.
3. Under **Inställningar** i den vänstra panelen klickar du på antingen **inkommande säkerhets regler** eller **utgående säkerhets regler**.
4. Skapa regeln genom att klicka på **Lägg till** och fylla i informationen. Klicka på **OK**.
5. Kontrol lera att regeln har skapats genom att leta upp den i regel tabellen.


## <a name="need-help"></a>Behöver du hjälp?
Kontakta Azure Active Directory Domain Services produkt teamet för att [dela feedback eller för support](contact-us.md).
