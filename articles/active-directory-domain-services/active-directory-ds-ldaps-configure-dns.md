---
title: Konfigurera DNS för att få åtkomst till en hanterad domän som använder LDAPS via internet | Microsoft Docs
description: Konfigurera DNS för att få åtkomst till en Azure AD Domain Services-hanterad domän med hjälp av LDAPS via internet
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 122282d168246e34aaa4a6369f7433b167355887
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416746"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Konfigurera DNS för att få åtkomst till en Azure AD Domain Services-hanterad domän med hjälp av säker LDAP (LDAPS)

## <a name="before-you-begin"></a>Innan du börjar
Fullständig [uppgift 3: aktivera säkert LDAP för den hanterade domänen med Azure-portalen](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Uppgift 4: Konfigurera DNS att komma åt den hanterade domänen från Internet
> [!TIP]
> **Valfri uppgift** – om du inte planerar att få åtkomst till den hanterade domänen med LDAPS via internet, hoppa över det här konfiguration.
>
>

Innan du utför den här åtgärden slutförts stegen som beskrivs i [uppgift 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

När du aktiverar åtkomst med säkert LDAP via internet, måste du uppdatera DNS-server så att klientdatorerna kan hitta den här hanterade domänen. Du ser en extern IP-adress på det **egenskaper** fliken **extern IP-adress för LDAPS åtkomst**.

Konfigurera din externa DNS-leverantör så att DNS-namnet på den hanterade domänen (till exempel ldaps.contoso100.com) pekar på den här externa IP-adressen. Till exempel skapa följande DNS-posten:

    ldaps.contoso100.com  -> 52.165.38.113

Klart! Nu är du redo att ansluta till den hanterade domänen med säkert LDAP via internet.

> [!WARNING]
> Kom ihåg att LDAPS-certifikatets utfärdare för att kunna ansluta till den hanterade domänen med LDAPS måste lita på klientdatorer. Om du använder en offentligt betrodd certifikatutfärdare behöver du inte göra något eftersom dessa certifikatutfärdare litar på klientdatorer. Om du använder ett självsignerat certifikat, installerar du den offentliga delen av det självsignerade certifikatet i det betrodda certifikatarkivet på klientdatorn.
>
>

## <a name="next-step"></a>Nästa steg
[Uppgift 5: Bind till den hanterade domänen och lås åtkomst med säkert LDAP](active-directory-ds-ldaps-bind-lockdown.md)
