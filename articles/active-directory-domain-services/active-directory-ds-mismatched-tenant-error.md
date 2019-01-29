---
title: Lös Felmatchade katalogfel för befintliga Azure AD Domain Services hanterade domäner | Microsoft Docs
description: Förstå och lösa felmatchad katalogfel för befintliga Azure AD Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: ergreenl
ms.openlocfilehash: d3586293574cc71011ad96fece5e5a4fdfa0b70f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192000"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Lös Felmatchade katalogfel för befintliga Azure AD Domain Services hanterade domäner
Du har en befintlig Azure AD Domain Services-hanterad domän. När du navigerar du till Azure-portalen och visa den hanterade domänen, visas följande felmeddelande visas:

![Felmatchad katalog-fel](./media/getting-started/mismatched-tenant-error.png)

Du kan inte administrera den här hanterade domänen förrän felet är löst.


## <a name="whats-causing-this-error"></a>Vad som orsakar felet?
Det här felet beror på när din hanterade domän och det virtuella nätverket som den är aktiverad i tillhör två olika Azure AD-klienter. Exempelvis kan du ha en hanterad domän som heter ”contoso.com” och det har aktiverats för Contosos Azure AD-klient. Men Azure-nätverket där den hanterade domänen aktiverades hör till Fabrikam - en annan Azure AD-klient.

Nya Azure portal (och mer specifikt Azure AD Domain Services-tillägget) bygger på Azure Resource Manager. I den moderna Azure Resource Manager-miljön tillämpas vissa begränsningar för att leverera ökad säkerhet och kontroll (RBAC) för rollbaserad åtkomst till resurser. Aktivera Azure AD Domain Services för en Azure AD-klient är en åtgärd som känslig eftersom den gör att autentiseringshasherna som ska synkroniseras med den hanterade domänen. Den här åtgärden måste du vara Innehavaradministratör för för katalogen. Du måste dessutom ha administrativ behörighet över det virtuella nätverket där du aktiverar den hanterade domänen. För de RBAC-kontrollerna som arbetar konsekvent, måste den hanterade domänen och det virtuella nätverket tillhöra samma Azure AD-klienten.

Kort sagt så kan inte du aktivera en hanterad domän för en Azure AD-klient ”contoso.com” i ett virtuellt nätverk som hör till en Azure-prenumeration som ägs av en annan Azure AD-klient ”fabrikam.com”. 

**Giltiga konfigurationen**: I det här distributionsscenariot har hanterade Contoso-domänen aktiverats för Contoso Azure AD-klienten. Den hanterade domänen visas i ett virtuellt nätverk som hör till en Azure-prenumeration som ägs av Contoso Azure AD-klienten. Därför tillhör såväl den hanterade domänen som det virtuella nätverket samma Azure AD-klienten. Den här konfigurationen är giltig och stöds fullt ut.

![Giltigt klientkonfiguration](./media/getting-started/valid-tenant-config.png)

**Felmatchade configuration**: I det här distributionsscenariot har hanterade Contoso-domänen aktiverats för Contoso Azure AD-klienten. Dock exponeras den hanterade domänen i ett virtuellt nätverk som hör till en Azure-prenumeration som ägs av Fabrikam Azure AD-klient. Därför kan den hanterade domänen och det virtuella nätverket tillhör två olika Azure AD-klienter. Den här konfigurationen är felmatchade konfigurationen och stöds inte. Det virtuella nätverket måste flyttas till samma Azure AD-klient (det vill säga Contoso) som den hanterade domänen. Se den [lösning](#resolution) information.

![Felmatchade konfiguration](./media/getting-started/mismatched-tenant-config.png)

Därför när den hanterade domänen och det virtuella nätverket som den är aktiverad i tillhör två olika Azure AD-klienter som du ser det här felet.

Det gäller följande regler i Resource Manager-miljö:
- Azure AD-katalog kan ha flera Azure-prenumerationer.
- En Azure-prenumeration kan ha flera resurser, till exempel virtuella nätverk.
- En enda Azure AD Domain Services-hanterad domän har aktiverats för Azure AD-katalog.
- En Azure AD Domain Services-hanterad domän kan aktiveras i ett virtuellt nätverk som hör till någon av de Azure-prenumerationerna i samma Azure AD-klienten.


## <a name="resolution"></a>Lösning
Du har två alternativ för att lösa problemet felmatchad katalog. Du kan:

- Klicka på den **ta bort** för att ta bort den befintliga hanterade domän. Återskapa med hjälp av den [Azure-portalen](https://portal.azure.com), så att den hanterade domänen och virtuellt nätverk som den är tillgänglig i tillhör Azure AD-katalog. Ansluta alla datorer som tidigare ansluten till domänen som har tagits bort ska den nyligen skapade hanterade domänen.

- Flytta Azure-prenumerationen som innehåller det virtuella nätverket till Azure AD-katalog som den hanterade domänen tillhör. Följ stegen i den [överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md) artikeln.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](active-directory-ds-getting-started.md)
* [Felsökningsguide för – Azure AD Domain Services](active-directory-ds-troubleshooting.md)
