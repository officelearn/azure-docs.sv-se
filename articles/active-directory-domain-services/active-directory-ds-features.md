---
title: 'Azure Active Directory Domain Services: Funktioner | Microsoft Docs'
description: Funktioner i Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e0f38ed8367f076c9f9c4da77440bb3e9603d514
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234200"
---
# <a name="features-provided-by-azure-active-directory-domain-services"></a>Funktioner som tillhandahålls av Azure Active Directory Domain Services

Följande funktioner är tillgängliga i Azure AD Domain Services hanterade domäner.

* **Enkel distributions upplevelse:** Du kan aktivera Azure AD Domain Services för din Azure AD-katalog med bara några få klick. Din hanterade domän innehåller endast molnbaserade användar konton och användar konton som synkroniseras från en lokal katalog.
* **Stöd för domän anslutning:** Du kan enkelt domän ansluta datorer i det virtuella Azure-nätverket som din hanterade domän är tillgänglig i. Domän kopplings upplevelsen på Windows-klient-och serveroperativ system fungerar sömlöst mot domäner som servas av Azure AD Domain Services. Du kan också använda automatiserade verktyg för domän anslutning mot sådana domäner.
* **En domän instans per Azure AD-katalog:** Du kan skapa en enda Active Directory domän för varje Azure AD-katalog.
* **Skapa domäner med anpassade namn:** Du kan skapa domäner med anpassade namn (till exempel "contoso100.com") med hjälp av Azure AD Domain Services. Du kan använda antingen verifierade eller overifierade domän namn. Du kan också skapa en domän med det inbyggda domänsuffix (det vill säga *. onmicrosoft.com) som erbjuds av Azure AD-katalogen.
* **Integrerat med Azure AD:** Du behöver inte konfigurera eller hantera replikering till Azure AD Domain Services. Användar konton, grupp medlemskap och användarautentiseringsuppgifter (lösen ord) från Azure AD-katalogen är automatiskt tillgängliga i Azure AD Domain Services. Nya användare, grupper eller ändringar av attribut från din Azure AD-klient eller din lokala katalog synkroniseras automatiskt till Azure AD Domain Services.
* **NTLM-och Kerberos-autentisering:** Med stöd för NTLM-och Kerberos-autentisering kan du distribuera program som förlitar sig på Windows-integrerad autentisering.
* **Använd företagets autentiseringsuppgifter/lösen ord:** Lösen ord för användare i Azure AD-klienten fungerar med Azure AD Domain Services. Användarna kan använda sina företags uppgifter för att ansluta till domän datorer, logga in interaktivt eller via fjärr skrivbord och autentisera mot den hanterade domänen.
* **LDAP-bindning & LDAP Read-support:** Du kan använda program som förlitar sig på LDAP-bindningar för att autentisera användare i domäner som servas av Azure AD Domain Services. Dessutom kan program som använder LDAP Läs åtgärder för att fråga användare/dator-attribut från katalogen också arbeta med Azure AD Domain Services.
* **Säkert LDAP (LDAPs):** Du kan aktivera åtkomst till katalogen via säker LDAP (LDAPs). Säkert LDAP åtkomst är tillgängligt i det virtuella nätverket som standard. Du kan dock också aktivera säker LDAP-åtkomst via Internet.
* **Grupprincip:** Du kan använda ett enda inbyggt grup princip objekt för användare och dator behållare för att genomdriva efterlevnad med nödvändiga säkerhets principer för användar konton och domänanslutna datorer. Du kan också skapa egna anpassade grup princip objekt och tilldela dem till anpassade organisationsenheter för att [Hantera grup principer](manage-group-policy.md).
* **Hantera DNS:** Medlemmar i gruppen "AAD DC-administratörer" kan hantera DNS för din hanterade domän med hjälp av välbekanta administrations verktyg för DNS, till exempel administration av MMC-snapin-modulen för DNS.
* **Skapa anpassade organisationsenheter (OU):** Medlemmar i gruppen "AAD DC-administratörer" kan skapa anpassade organisationsenheter i den hanterade domänen. De här användarna beviljas fullständig administratörs behörighet för anpassade organisationsenheter, så att de kan lägga till/ta bort tjänst konton, datorer, grupper osv. i dessa anpassade organisationsenheter.
* **Tillgängligt i många globala Azure-regioner:** På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.
* **Hög tillgänglighet:** Azure AD Domain Services erbjuder hög tillgänglighet för din domän. Den här funktionen ger garanti för högre service drift tid och återhämtning till problem. Inbyggd hälso övervakning erbjuder automatiserad reparation av fel genom att snurra nya instanser för att ersätta misslyckade instanser och tillhandahålla fortsatt tjänst för din domän.
* **Skydd mot AD-konto utelåsning:** Användar konton är låsta i 30 minuter om fem ogiltiga lösen ord används inom 2 minuter. Konton låses automatiskt upp efter 30 minuter.
* **Använd välkända hanterings verktyg:** Du kan använda välbekanta Windows Server Active Directory hanterings verktyg som Active Directory Administrationscenter eller Active Directory PowerShell för att administrera hanterade domäner.
