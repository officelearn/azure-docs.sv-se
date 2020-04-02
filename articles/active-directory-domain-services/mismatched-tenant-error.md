---
title: Åtgärda fel i felmatchade katalogfel i Azure AD Domain Services | Microsoft-dokument
description: Lär dig vad ett felanpassat katalogfel innebär och hur du löser det i Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 7a74d5e609c615bb6b973eceebb144c58e46f7cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519462"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Lösa felmatchade katalogfel för befintliga Azure AD Domain Services-hanterade domäner

Om en Hanterad Azure Active Directory Domain Services (Azure AD DS) hanterad domän visar ett fel i överensstämmelse med klienten kan du inte administrera den hanterade domänen förrän den har lösts. Det här felet uppstår om det underliggande virtuella Azure-nätverket flyttas till en annan Azure AD-katalog.

I den här artikeln beskrivs varför felet uppstår och hur du löser det.

## <a name="what-causes-this-error"></a>Vad orsakar det här felet?

Ett felmatchat katalogfel inträffar när en Azure AD DS-hanterad domän och virtuellt nätverk tillhör två olika Azure AD-klienter. Du kan till exempel ha en Azure AD DS-hanterad domän som heter *aaddscontoso.com* som körs i Contosos Azure AD-klientorganisation. Azure-virtuella nätverket för hanterad domän är dock en del av Fabrikam Azure AD-klienten.

Azure använder rollbaserad åtkomstkontroll (RBAC) för att begränsa åtkomsten till resurser. När du aktiverar Azure AD DS i en Azure AD-klient synkroniseras autentiseringsuppgifterna till den hanterade domänen. Den här åtgärden kräver att du är klientadministratör för Azure AD-katalogen och åtkomst till autentiseringsuppgifterna måste kontrolleras. Om du vill distribuera resurser till ett virtuellt Azure-nätverk och styra trafik måste du ha administratörsbehörighet i det virtuella nätverket där du distribuerar Azure AD DS.

För att RBAC ska fungera konsekvent och skydda åtkomsten till alla resurser som Azure AD DS använder måste den hanterade domänen och det virtuella nätverket tillhöra samma Azure AD-klientorganisation.

Följande regler gäller i Resource Manager-miljön:

- En Azure AD-katalog kan ha flera Azure-prenumerationer.
- En Azure-prenumeration kan ha flera resurser, till exempel virtuella nätverk.
- En enda Azure AD Domain Services-hanterad domän är aktiverad för en Azure AD-katalog.
- En Hanterad Azure AD Domain Services-domän kan aktiveras i ett virtuellt nätverk som tillhör någon av Azure-prenumerationerna i samma Azure AD-klientorganisation.

### <a name="valid-configuration"></a>Giltig konfiguration

I följande exempeldistributionsscenario aktiveras den Hanterade Contoso Azure AD DS-domänen i Contoso Azure AD-klienten. Den hanterade domänen distribueras i ett virtuellt nätverk som tillhör en Azure-prenumeration som ägs av Contoso Azure AD-klienten. Både den hanterade domänen och det virtuella nätverket tillhör samma Azure AD-klientorganisation. Den här exempelkonfigurationen är giltig och stöds fullt ut.

![Giltig Azure AD DS-klientkonfiguration med den hanterade domänen och den virtuella nätverksdelen av samma Azure AD-klientorganisation](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Felaktig klientkonfiguration

I det här exempeldistributionsscenariot aktiveras den Hanterade Contoso Azure AD DS-domänen i Contoso Azure AD-klienten. Den hanterade domänen distribueras dock i ett virtuellt nätverk som tillhör en Azure-prenumeration som ägs av Fabrikam Azure AD-klienten. Den hanterade domänen och det virtuella nätverket tillhör två olika Azure AD-klienter. Den här exempelkonfigurationen är en inkompatibel klient och stöds inte. Det virtuella nätverket måste flyttas till samma Azure AD-klient som den hanterade domänen.

![Felaktig klientkonfiguration](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Lösa felmatchade klientfel

Följande två alternativ löser det felmatchade katalogfelet:

* [Ta bort den Hanterade Azure AD DS-domänen](delete-aadds.md) från din befintliga Azure AD-katalog. [Skapa en ersättnings-Azure AD DS-hanterad domän](tutorial-create-instance.md) i samma Azure AD-katalog som det virtuella nätverk som du vill använda. När du är klar ansluter du alla datorer som tidigare har anslutits till den borttagna domänen till den återskapade hanterade domänen.
* [Flytta Azure-prenumerationen](../cost-management-billing/manage/billing-subscription-transfer.md) som innehåller det virtuella nätverket till samma Azure AD-katalog som azure AD DS-hanterad domän.

## <a name="next-steps"></a>Nästa steg

Mer information om felsökningsproblem med Azure AD DS finns i [felsökningsguiden](troubleshoot.md).
