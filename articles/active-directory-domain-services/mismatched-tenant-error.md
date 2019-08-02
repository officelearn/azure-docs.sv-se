---
title: Lös felmatchade katalog fel i Azure AD Domain Services | Microsoft Docs
description: Förstå och åtgärda felmatchade katalog fel för befintliga Azure AD Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 676efa155c85ab371ec41c49ad0c15eb2bd5a24a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233996"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Lös felmatchade katalog fel för befintliga Azure AD Domain Services hanterade domäner
Du har en befintlig Azure AD Domain Services hanterad domän. När du navigerar till Azure Portal och visar den hanterade domänen visas följande fel meddelande:

![Katalog fel som inte matchar](./media/getting-started/mismatched-tenant-error.png)

Du kan inte administrera den här hanterade domänen förrän felet har åtgärd ATS.


## <a name="whats-causing-this-error"></a>Vad är orsaken till det här felet?
Det här felet beror på att den hanterade domänen och det virtuella nätverk som den är aktive rad för tillhör två olika Azure AD-klienter. Du kan till exempel ha en hanterad domän med namnet "contoso.com" och den har Aktiver ATS för Contosos Azure AD-klient. Men det virtuella Azure-nätverket där den hanterade domänen aktiverades tillhör Fabrikam-en annan Azure AD-klient.

Den nya Azure Portal (och särskilt Azure AD Domain Services tillägget) bygger på Azure Resource Manager. I modern Azure Resource Manager-miljö tillämpas vissa begränsningar för att leverera större säkerhet och för rollbaserad åtkomst kontroll (RBAC) till resurser. Att aktivera Azure AD Domain Services för en Azure AD-klient är en känslig åtgärd eftersom det gör att hash-värden för autentiseringsuppgifter synkroniseras med den hanterade domänen. Den här åtgärden kräver att du är en klient administratör för katalogen. Dessutom måste du ha administratörs behörighet för det virtuella nätverk där du aktiverar den hanterade domänen. För att RBAC-kontrollerna ska fungera konsekvent bör den hanterade domänen och det virtuella nätverket tillhöra samma Azure AD-klient.

I korthet kan du inte aktivera en hanterad domän för en Azure AD-klient ' contoso.com ' i ett virtuellt nätverk som tillhör en Azure-prenumeration som ägs av en annan Azure AD-klient ' fabrikam.com '. 

**Giltig konfiguration**: I det här distributions scenariot har Contosos hanterade domän Aktiver ATS för Contoso Azure AD-klienten. Den hanterade domänen exponeras i ett virtuellt nätverk som tillhör en Azure-prenumeration som ägs av contoso Azure AD-klienten. Därför tillhör både den hanterade domänen och det virtuella nätverket samma Azure AD-klient. Den här konfigurationen är giltig och stöds fullt ut.

![Giltig klient konfiguration](./media/getting-started/valid-tenant-config.png)

**Felaktig matchning av klient konfiguration**: I det här distributions scenariot har Contosos hanterade domän Aktiver ATS för Contoso Azure AD-klienten. Den hanterade domänen exponeras dock i ett virtuellt nätverk som tillhör en Azure-prenumeration som ägs av den Fabrikam Azure AD-klienten. Därför tillhör den hanterade domänen och det virtuella nätverket två olika Azure AD-klienter. Den här konfigurationen är den felmatchade klient konfigurationen och stöds inte. Det virtuella nätverket måste flyttas till samma Azure AD-klient (det vill säga contoso) som den hanterade domänen. Mer information [](#resolution) finns i lösnings avsnittet.

![Felaktig matchning av klient konfiguration](./media/getting-started/mismatched-tenant-config.png)

Därför visas det här felet när den hanterade domänen och det virtuella nätverk som den är aktive rad för tillhör två olika Azure AD-klienter.

Följande regler gäller i Resource Manager-miljön:
- En Azure AD-katalog kan ha flera Azure-prenumerationer.
- En Azure-prenumeration kan ha flera resurser, till exempel virtuella nätverk.
- En enda Azure AD Domain Services hanterad domän är aktive rad för en Azure AD-katalog.
- En Azure AD Domain Services hanterad domän kan aktive ras på ett virtuellt nätverk som tillhör någon av Azure-prenumerationerna inom samma Azure AD-klient.


## <a name="resolution"></a>Lösning
Det finns två alternativ för att lösa det felmatchade katalog felet. Du kan:

- Klicka på **ta bort** om du vill ta bort den befintliga hanterade domänen. Återskapa med hjälp av [Azure Portal](https://portal.azure.com), så att den hanterade domänen och det virtuella nätverk som den är tillgänglig i tillhör Azure AD-katalogen. Anslut alla datorer som tidigare var anslutna till den borttagna domänen till den nyligen skapade hanterade domänen.

- Flytta Azure-prenumerationen som innehåller det virtuella nätverket till den Azure AD-katalog som din hanterade domän tillhör. Följ stegen i den [överföra ägarskapet för en Azure-prenumeration till en annan konto](../billing/billing-subscription-transfer.md) artikel.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](create-instance.md)
* [Fel söknings guide – Azure AD Domain Services](troubleshoot.md)
