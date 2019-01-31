---
title: Konfigurera namngivna platser i Azure Active Directory | Microsoft Docs
description: Lär dig att konfigurera namngivna platser.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7921e35158a78eca246932e9b3a1af46d4db8834
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190042"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Snabbstart: Konfigurera namngivna platser i Azure Active Directory

Du kan använda namngivna platser för att sätta etiketter på betrodda IP-adressintervall i din organisation. Azure AD använder namngivna platser för att:
- Identifiera falska positiva identifieringar i [riskhändelser](concept-risk-events.md). Inloggning från en betrodd plats minskar en användares inloggningsrisk.   
- Konfigurera [platsbaserad villkorlig åtkomst](../conditional-access/location-condition.md).

I den här snabbstarten får du lära dig att konfigurera namngivna platser i din miljö.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

* En Azure AD-klientorganisation. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/trial/get-started-active-directory/). 
* En användare som är en global administratör för klienten.
* Ett IP-adressintervall som är etablerat och betrott inom din organisation. IP-intervallet måste använda formatet **Classless Interdomain Routing (CIDR)**.

## <a name="configure-named-locations"></a>Konfigurera namngivna platser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I den vänstra rutan väljer du **Azure Active Directory** och sedan **Villkorlig åtkomst** från avsnittet **Säkerhet**.

    ![Fliken villkorlig åtkomst](./media/quickstart-configure-named-locations/entrypoint.png)

3. På sidan **Villkorlig åtkomst** väljer du **Namngivna platser** och **Ny plats**.

    ![Namngivna platser](./media/quickstart-configure-named-locations/namedlocation.png)

6. Fyll i formuläret på den nya sidan. 

    * I rutan **Namn** skriver du ett namn på din namngivna plats.
    * I rutan **IP-intervall** skriver du ett IP-adressintervall i CIDR-format.  
    * Klicka på **Skapa**.
    
    ![Det nya bladet](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

- [Azure AD villkorsstyrd åtkomst](../active-directory-conditional-access-azure-portal.md).
- [Platsvillkor i villkorsstyrd åtkomst i Azure AD](../conditional-access/location-condition.md)
- [Rapport över riskfyllda inloggningar](concept-risky-sign-ins.md).  
