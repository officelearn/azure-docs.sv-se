---
title: Grupphanterade tjänstkonton för Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du skapar ett grupphanterade tjänstkonto (gMSA) för användning med hanterade domäner för Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 58749e4518f6fa73c8641ce38483c101576047aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614083"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Skapa ett grupphanterad tjänstkonto (gMSA) i Azure AD Domain Services

Program och tjänster behöver ofta en identitet för att autentisera sig med andra resurser. En webbtjänst kan till exempel behöva autentiseras med en databastjänst. Om ett program eller en tjänst har flera instanser, till exempel en webbservergrupp, blir det tidskrävande att manuellt skapa och konfigurera identiteterna för dessa resurser.

I stället kan ett grupphanterade tjänstkonto (gMSA) skapas i azure Active Directory Domain Services (Azure AD DS) hanterad domän. Windows OS hanterar automatiskt autentiseringsuppgifterna för en gMSA, vilket förenklar hanteringen av stora grupper av resurser.

Den här artikeln visar hur du skapar en gMSA i en Azure AD DS-hanterad domän med Azure PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs slutför du självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* En virtuell windows serverhantering som är ansluten till den Hanterade Azure AD DS-domänen.
    * Om det behövs slutför du självstudien för att [skapa en vm-hantering][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Översikt över hanterade tjänstkonton

Ett fristående hanterat tjänstkonto (sMSA) är ett domänkonto vars lösenord hanteras automatiskt. Den här metoden förenklar SPN-hantering (Service Principal Name) och möjliggör delegerad hantering till andra administratörer. Du behöver inte manuellt skapa och rotera autentiseringsuppgifter för kontot.

Ett grupphanterad tjänstkonto (gMSA) ger samma hanteringsförenkling, men för flera servrar i domänen. Med en gMSA kan alla instanser av en tjänst som finns på en servergrupp använda samma tjänsthuvudnamn för att protokoll för ömsesidig autentisering ska fungera. När en gMSA används som huvudnamn för tjänsten hanterar Operativsystemet Windows igen kontots lösenord i stället för att förlita sig på administratören.

Mer information finns i [översikt över grupphanterade tjänstkonton (gMSA).][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Använda tjänstkonton i Azure AD DS

Eftersom Azure AD DS-hanterade domäner är låsta och hanterade av Microsoft finns det vissa överväganden när du använder tjänstkonton:

* Skapa tjänstkonton i anpassade organisationsenheter (OU) på den hanterade domänen.
    * Du kan inte skapa ett tjänstkonto i de inbyggda *AADDC-användare* eller *AADDC-datorerna.*
    * Skapa i stället [en anpassad organisationsenhet][create-custom-ou] i azure AD DS-hanterad domän och skapa sedan tjänstkonton i den anpassade organisationsenheten.
* ROTNYCKELN För distributionstjänster (KDS) är förskapad.
    * KDS-rotnyckeln används för att generera och hämta lösenord för gMSAs. I Azure AD DS skapas KDS-roten åt dig.
    * Du har inte behörighet att skapa en annan, eller visa standard-KDS-rotnyckeln.

## <a name="create-a-gmsa"></a>Skapa en gMSA

Skapa först en anpassad organisationsenhet med cmdleten [New-ADOrganizationalUnit.][New-AdOrganizationalUnit] Mer information om hur du skapar och hanterar anpassade företagsenheter finns [i Anpassade företagsenheter i Azure AD DS][create-custom-ou].

> [!TIP]
> Om du vill utföra de här stegen för att skapa en gMSA [använder du den virtuella hanteringen.][tutorial-create-management-vm] Den här hanterings-vm:n bör redan ha de AD PowerShell-cmdlets och anslutningen till den hanterade domänen.

I följande exempel skapas en anpassad organisationsenhet med namnet *myNewOU* i den Azure AD DS-hanterade domänen med namnet *aaddscontoso.com*. Använd din egen organisationsenhet och hanterade domännamn:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Skapa nu en gMSA med cmdleten [New-ADServiceAccount.][New-ADServiceAccount] Följande exempelparametrar definieras:

* **-Namnet** är inställt på *WebFarmSvc*
* **-Path** parameter anger den anpassade organisationsenheten för gMSA som skapats i föregående steg.
* DNS-poster och tjänsthuvudnamn anges för *WebFarmSvc.aaddscontoso.com*
* Huvudnamn i *AADDSCONTOSO-SERVER$* tillåts hämta lösenordet med hjälp av identiteten.

Ange egna namn och domännamn.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Program och tjänster kan nu konfigureras för att använda gMSA efter behov.

## <a name="next-steps"></a>Nästa steg

Mer information om gMSAs finns i [Komma igång med grupphanterade tjänstkonton][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
