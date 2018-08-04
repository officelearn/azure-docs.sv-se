---
title: 'Azure Active Directory Domain Services: Skapa en grupphanterade tjänstkontot | Microsoft Docs'
description: Administrera Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: maheshu
ms.openlocfilehash: 9bfd38b2eba3cab5012e5715ad283b9cb7b84a9b
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505900"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Skapa en grupp Hanterat tjänstkonto (gMSA) på en Azure AD Domain Services-hanterad domän
Den här artikeln visar hur du skapar hanterade tjänstkonton på en Azure AD Domain Services-hanterad domän.

## <a name="managed-service-accounts"></a>Hanterade tjänstkonton
Ett fristående Hanterat tjänstkonto (sMSA) är ett hanterat domänkonto vars lösenord hanteras automatiskt. Det förenklar hanteringen för tjänstens huvudnamn (SPN) och möjliggör delegerad förvaltningen till andra administratörer. Den här typen av Hanterat tjänstkonto (MSA) introducerades i Windows Server 2008 R2 och Windows 7.

Grupp-Hanterat tjänstkonto (gMSA) ger samma fördelar för många servrar i domänen. Alla instanser av en tjänst som finns i en servergrupp måste du använda samma tjänstens huvudnamn för protokoll ömsesidig autentisering ska fungera. När ett gMSA används som tjänstens huvudnamn, hanterar Windows-operativsystemet lösenordet för det kontot i stället för en administratör.

**Mer information:**
- [Översikt över Grupphanterade tjänstkonton](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Komma igång med Grupphanterade tjänstkonton](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Med hjälp av service-konton i Azure AD Domain services
Azure AD Domain Services-hanterade domäner är låsta och hanteras av Microsoft. Det finns några viktiga överväganden när du använder service-konton med Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Skapa tjänstkonton i anpassade organisationsenheter (OU) i den hanterade domänen
Du kan inte skapa ett tjänstkonto i de inbyggda ”AADDC-användare” eller ”AADDC-datorer, organisationsenheterna. [Skapa en anpassad OU](active-directory-ds-admin-guide-create-ou.md) på din hanterade domän och sedan skapa tjänstkonton i den anpassade Organisationsenheten.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Key Distribution Services (KDS) rotnyckeln färdiga redan
Key Distribution Services (KDS) rotnyckeln är förinställda på en Azure AD Domain Services-hanterad domän. Du behöver inte skapa en KDS-rot-nyckeln och inte har behörighet att göra så något. Du kan inte visa KDS-rotnyckel i den hanterade domänen antingen.

## <a name="sample---create-a-gmsa-using-powershell"></a>– Skapa ett gMSA med hjälp av PowerShell
I följande exempel visas hur du skapar en anpassad Organisationsenhet med hjälp av PowerShell. Du kan sedan skapa ett gMSA i denna Organisationsenhet med hjälp av den ```-Path``` parametern för att ange Organisationsenheten.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**PowerShell-cmdlet-dokumentationen:**
- [Ny ADOrganizationalUnit cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [En ny-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Nästa steg
- [Skapa en anpassad OU på en hanterad domän](active-directory-ds-admin-guide-create-ou.md)
- [Översikt över Grupphanterade tjänstkonton](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Komma igång med Grupphanterade tjänstkonton](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
