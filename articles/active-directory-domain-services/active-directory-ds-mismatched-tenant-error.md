---
title: "Lös Felmatchade katalogfel för befintliga Azure AD Domain Services hanterade domäner | Microsoft Docs"
description: "Förstå och lösa Felmatchade katalogfel för befintliga Azure AD Domain Services hanterade domäner"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: maheshu
ms.openlocfilehash: 9c9a47e9b3050eb7f41202d6a4b9202ba0f379df
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Lös Felmatchade katalogfel för befintliga Azure AD Domain Services hanterade domäner
Du har en befintlig hanterad domän som har aktiverats med hjälp av den klassiska Azure-portalen. När du navigerar du till den nya Azure-portalen och visa den hanterade domänen visas följande felmeddelande:

![Fel vid Felmatchade directory](.\media\getting-started\mismatched-tenant-error.png)

Du kan inte administrera hanterade domänen förrän felet är löst.


## <a name="whats-causing-this-error"></a>Vad som orsakar felet?
Det här felet uppstår om din hanterade domän och det virtuella nätverket som den är aktiverad i tillhör två olika Azure AD-klienter. Till exempel du har en hanterad domän som heter ”contoso.com” och den har aktiverats för Contosos Azure AD-klient. Dock virtuella Azure-nätverket som den hanterade domänen har aktiverats tillhör Fabrikam - en annan Azure AD-klient.

Den nya Azure-portalen (och specifikt Azure AD Domain Services-tillägget) bygger på Azure Resource Manager. Vissa begränsningar tillämpas för att ge större säkerhet och styra (RBAC) för rollbaserad åtkomst till resurser i den moderna Azure Resource Manager-miljön. Aktivera Azure AD Domain Services för en Azure AD-klient är en åtgärd som känslig eftersom den gör att autentiseringshasherna som ska synkroniseras till den hanterade domänen. Den här åtgärden måste du vara klientadministratör för för katalogen. Dessutom måste du ha administratörsbehörighet över det virtuella nätverket där du aktiverar den hanterade domänen. RBAC kontroller arbetar konsekvent, ska den hanterade domänen och det virtuella nätverket tillhöra samma Azure AD-klienten.

Kort sagt: du kan inte aktivera en hanterad domän för en Azure AD-klient ”contoso.com” i ett virtuellt nätverk som hör till en Azure-prenumeration som ägs av en annan Azure AD-klient 'fabrikam.com'. Den klassiska Azure-portalen är byggda på plattformen Resource Manager och tillämpa inte dessa begränsningar.

**Giltig konfiguration**: I det här distributionsscenariot Contoso hanterad domän är aktiverat för Contoso Azure AD-klient. Den hanterade domänen exponeras i ett virtuellt nätverk som hör till en Azure-prenumeration som ägs av Contoso Azure AD-klienten. Därför tillhör både den hanterade domänen som det virtuella nätverket samma Azure AD-klienten. Den här konfigurationen är giltigt och stöds fullt ut.

![Giltig klientkonfiguration](./media/getting-started/valid-tenant-config.png)

**Felmatchade klientkonfiguration**: I det här distributionsscenariot Contoso hanterad domän är aktiverat för Contoso Azure AD-klient. Dock exponeras den hanterade domänen i ett virtuellt nätverk som hör till en Azure-prenumeration som ägs av Fabrikam Azure AD-klient. Därför kan den hanterade domänen och det virtuella nätverket tillhör två olika Azure AD-klienter. Den här konfigurationen är inkompatibla klientkonfiguration och stöds inte. Det virtuella nätverket måste flyttas till samma Azure AD-klient (det vill säga Contoso) som hanterade domän. Finns det [upplösning](#resolution) information.

![Felmatchade klientkonfiguration](./media/getting-started/mismatched-tenant-config.png)

Därför när den hanterade domänen och det virtuella nätverket som den är aktiverad i tillhör två olika Azure AD-klienter som du ser det här felet.

Det gäller följande regler i Resource Manager-miljön:
- Azure AD-katalog kan ha flera Azure-prenumerationer.
- En Azure-prenumeration kan ha flera resurser, till exempel virtuella nätverk.
- En enda hanterad Azure AD DS-domän har aktiverats för en Azure AD-katalog.
- En Azure AD Domain Services-hanterad domän kan aktiveras på ett virtuellt nätverk som hör till något Azure-prenumerationer inom samma Azure AD-klienten.


## <a name="resolution"></a>Lösning
Du har två alternativ för att åtgärda felet Felmatchade directory. Du kan:

- Klicka på den **ta bort** för att ta bort det befintliga hanterade domän. Skapa nytt med hjälp av den [Azure-portalen](https://portal.azure.com), så att den hanterade domänen och virtuella nätverk som den är tillgänglig i hör till Azure AD-katalog. Anslut alla datorer som tidigare ansluten till domänen borttagna till den nyligen skapade hanterade domänen.

- Flytta den Azure-prenumeration som innehåller det virtuella nätverket som Azure AD-katalog som den Hantera domänen tillhör. Följ stegen i den [överför ägarskapet för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md) artikel.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Felsökningsguide för - Azure AD Domain Services](active-directory-ds-troubleshooting.md)
