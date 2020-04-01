---
title: Kerberos begränsad delegering för Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du aktiverar resursbaserad Kerberos-begränsad delegering (KCD) i en hanterad Azure Active Directory Domain Services-domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 0d7c9319097d81f4db2e2c5ecfc692269d27a4db
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476091"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Konfigurera Kerberos-begränsad delegering (KCD) i Azure Active Directory Domain Services

När du kör program kan det finnas ett behov av att dessa program får åtkomst till resurser i samband med en annan användare. Ad DS (Active Directory Domain Services) stöder en mekanism som kallas *Kerberos-delegering* som aktiverar det här användningsfallet. Kerberos *begränsad delegering* (KCD) bygger sedan på den här mekanismen för att definiera specifika resurser som kan nås i kontexten för användaren. Azure Active Directory Domain Services (Azure AD DS) hanterade domäner är säkrare låsta än traditionella lokala AD DS-miljöer, så använd en säkrare *resursbaserad* KCD.

Den här artikeln visar hur du konfigurerar resursbaserad Kerberos-begränsad delegering i en Azure AD DS-hanterad domän.

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här artikeln behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs [kan du skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* En virtuell windows serverhantering som är ansluten till den Hanterade Azure AD DS-domänen.
    * Om det behövs slutför du självstudien för att [skapa en virtuell Windows Server-dator och ansluter den till en hanterad domän][create-join-windows-vm] och installerar sedan AD [DS-hanteringsverktygen][tutorial-create-management-vm].
* Ett användarkonto som är medlem i azure *AD DC-administratörsgruppen* i din Azure AD-klientorganisation.

## <a name="kerberos-constrained-delegation-overview"></a>Översikt över begränsad delegering av Kerberos

Med Kerberos-delegering kan ett konto personifiera ett annat konto för åtkomst till resurser. Ett webbprogram som har åtkomst till en backend-webbkomponent kan till exempel utge sig själv som ett annat användarkonto när den upprättar backend-anslutningen. Kerberos-delegeringen är osäker eftersom den inte begränsar vilka resurser som personifierar kontot kan komma åt.

Kerberos begränsad delegering (KCD) begränsar de tjänster eller resurser som en angiven server eller ett angivet program kan ansluta när de personifierar en annan identitet. Traditionell KCD kräver domänadministratörsbehörighet för att konfigurera ett domänkonto för en tjänst, och det begränsar kontot till att köras på en enda domän.

Traditionella KCD har också några frågor. I tidigare operativsystem hade serviceadministratören till exempel inget användbart sätt att veta vilka frontend-tjänster som delegerats till de resurstjänster de ägde. Alla frontend-tjänster som kunde delegera till en resurstjänst var en potentiell attackpunkt. Om en server som var värd för en frontend-tjänst som konfigurerats för att delegera till resurstjänster komprometterades, kan resurstjänsterna också komprometteras.

I en Azure AD DS-hanterad domän har du inte domänadministratörsbehörighet. Därför kan traditionella kontobaserade KCD inte konfigureras i en Azure AD DS en hanterad domän. Resursbaserad KCD kan i stället användas, vilket också är säkrare.

### <a name="resource-based-kcd"></a>Resursbaserad KCD

Windows Server 2012 och senare ger tjänstadministratörer möjlighet att konfigurera begränsad delegering för sin tjänst. Den här modellen kallas resursbaserad KCD. Med den här metoden kan backend-tjänstadministratören tillåta eller neka specifika frontend-tjänster från att använda KCD.

Resursbaserad KCD är konfigurerad med PowerShell. Du använder cmdleterna [Set-ADComputer][Set-ADComputer] eller [Set-ADUser,][Set-ADUser] beroende på om personifieringskontot är ett datorkonto eller ett användarkonto/tjänstkonto.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Konfigurera resursbaserad KCD för ett datorkonto

I det här fallet anta vi att du har en webbapp som körs på datorn som heter *contoso-webapp.aaddscontoso.com*. Webbappen måste komma åt ett webb-API som körs på datorn som heter *contoso-api.aaddscontoso.com* i samband med domänanvändare. Slutför följande steg för att konfigurera det här scenariot:

1. [Skapa en anpassad organisationsenhet](create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade organisationsenheten till användare inom azure AD DS-hanterade domänen.
1. [Domän ansluta till de virtuella datorerna][create-join-windows-vm], både den som kör webbappen och den som kör webb-API: et, till Azure AD DS-hanterad domän. Skapa dessa datorkonton i den anpassade organisationsenheten från föregående steg.

    > [!NOTE]
    > Datorkontona för webbappen och webb-API:et måste finnas i en anpassad organisationsenhet där du har behörighet att konfigurera resursbaserad KCD. Du kan inte konfigurera resursbaserad KCD för ett datorkonto i behållaren för inbyggda *AAD DC-datorer.*

1. Konfigurera slutligen resursbaserad KCD med hjälp av Cmdleten [Set-ADComputer][Set-ADComputer] PowerShell. Från din domänledda hanterings-VM och inloggad som användarkonto som är medlem i Azure *AD DC-administratörsgruppen* kör du följande cmdlets. Ange dina egna datornamn efter behov:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Konfigurera resursbaserad KCD för ett användarkonto

I det här fallet anta vi att du har en webbapp som körs som ett tjänstkonto med namnet *appsvc*. Webbappen måste komma åt ett webb-API som körs som ett tjänstkonto med namnet *backendsvc* i samband med domänanvändare. Slutför följande steg för att konfigurera det här scenariot:

1. [Skapa en anpassad organisationsenhet](create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade organisationsenheten till användare inom azure AD DS-hanterade domänen.
1. [Domän ansluta till de virtuella datorer][create-join-windows-vm] som kör serverdelen webb-API /resurs till Azure AD DS hanterad domän. Skapa dess datorkonto i den anpassade organisationsenheten.
1. Skapa tjänstkontot (till exempel "appsvc") som används för att köra webbappen i den anpassade organisationsenheten.

    > [!NOTE]
    > Återigen måste datorkontot för den virtuella datorn och tjänstkontot för webbappen finnas i en anpassad organisationsenhet där du har behörighet att konfigurera resursbaserad KCD. Du kan inte konfigurera resursbaserad KCD för konton i de inbyggda *AAD DC-datorerna* eller *AAD DC-användare.* Det innebär också att du inte kan använda användarkonton som synkroniserats från Azure AD för att konfigurera resursbaserad KCD. Du måste skapa och använda tjänstkonton som skapats specifikt i Azure AD DS.

1. Konfigurera slutligen resursbaserad KCD med hjälp av cmdleten [Set-ADUser][Set-ADUser] PowerShell. Från din domänledda hanterings-VM och inloggad som användarkonto som är medlem i Azure *AD DC-administratörsgruppen* kör du följande cmdlets. Ange dina egna tjänstnamn efter behov:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Nästa steg

Mer information om hur delegering fungerar i Active Directory Domain Services finns i [Kerberos Constrained Delegation Overview][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
