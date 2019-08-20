---
title: 'Azure Active Directory Domain Services: Skapa ett grupphanterat tjänst konto | Microsoft Docs'
description: Lär dig hur du skapar ett grupphanterat tjänst konto (gMSA) för användning med Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: 3742aed7ff39e0a2f6bdf353fb9f261176027422
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612956"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Skapa ett grupphanterat tjänst konto (gMSA) på en Azure AD Domain Services hanterad domän
Den här artikeln visar hur du skapar hanterade tjänst konton på en Azure AD Domain Services hanterad domän.

## <a name="managed-service-accounts"></a>Hanterade tjänst konton
Ett fristående-hanterat tjänst konto (sMSA) är ett hanterat domän konto vars lösen ord hanteras automatiskt. Det fören klar hanteringen av tjänstens huvud namn (SPN) och möjliggör delegerad hantering till andra administratörer. Den här typen av hanterade tjänst konton (MSA) introducerades i Windows Server 2008 R2 och Windows 7.

Grupphanterade tjänst konton (gMSA) ger samma fördelar för många servrar i domänen. Alla instanser av en tjänst som finns i en Server grupp måste använda samma tjänst huvud namn för att ömsesidiga autentiseringsprotokoll ska fungera. När en gMSA används som tjänstens huvud namn, hanterar Windows-operativsystemet kontots lösen ord i stället för att förlita dig på administratören.

**Mer information:**
- [Översikt över grupphanterade tjänst konton](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Komma igång med grupphanterade tjänst konton](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Använda tjänst konton i Azure AD Domain Services
Azure AD Domain Services hanterade domäner är låsta och hanteras av Microsoft. Det finns ett par viktiga överväganden när du använder tjänst konton med Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Skapa tjänst konton inom anpassade organisationsenheter (OU) på den hanterade domänen
Det går inte att skapa ett tjänst konto i de inbyggda organisationsenheterna "AADDC Users" eller "AADDC Computers". [Skapa en anpassad Organisationsenhet](create-ou.md) på din hanterade domän och skapa sedan tjänst konton inom den anpassade organisationsenheten.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Rot nyckeln Key Distribution Services (KDS) har redan skapats i förväg
Rot nyckeln Key Distribution Services (KDS) skapas i förväg på en Azure AD Domain Services hanterad domän. Du behöver inte skapa en KDS-rot nyckel och har inte behörighet att göra det. Du kan inte Visa rot nyckeln KDS på den hanterade domänen.

## <a name="sample---create-a-gmsa-using-powershell"></a>Exempel – skapa en gMSA med hjälp av PowerShell
I följande exempel visas hur du skapar en anpassad ORGANISATIONSENHET med hjälp av PowerShell. Du kan sedan skapa en gMSA i den organisationsenheten med hjälp ```-Path``` av parametern för att ange organisationsenheten.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=contoso,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso.com  `
-Path "OU=MYNEWOU,DC=contoso,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
http/WebFarmSvc.contoso.com/contoso,  `
http/WebFarmSvc/contoso.com, http/WebFarmSvc/contoso  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**PowerShell-cmdlet-dokumentation:**
- [New-ADOrganizationalUnit-cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [New-ADServiceAccount-cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Nästa steg
- [Skapa en anpassad ORGANISATIONSENHET på en hanterad domän](create-ou.md)
- [Översikt över grupphanterade tjänst konton](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Komma igång med grupphanterade tjänst konton](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
