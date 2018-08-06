---
title: Hanterad tjänstidentitet (MSI) för Azure-resurser
description: En översikt över funktionen Hanterad tjänstidentitet (MSI) för Azure-resurser.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: 1edd6f846d539b1f263877a5e0af107148513c6e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398393"
---
# <a name="what-is-managed-service-identity-for-azure-resources"></a>Vad är Hanterad tjänstidentitet för Azure-resurser?

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Det är viktigt att dessa autentiseringsuppgifter skyddas. Helst bör autentiseringsuppgifterna aldrig visas på utvecklarnas arbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem. 

Funktionen Hanterad tjänstidentitet (MSI) i Azure Active Directory (Azure AD) löser detta problem. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod.

Funktionen Hanterad tjänstidentitet är kostnadsfri med Azure AD för Azure-prenumerationer. Ingen extra kostnad utgår.

## Hur fungerar funktionen?<a name="how-does-it-work"></a>

Det finns två typer av hanterade identiteter:

- En **systemtilldelad identitet** aktiveras direkt på en instans av Azure-tjänsten. När identiteten har aktiverats skapar Azure en identitet för instansen i den Azure AD-klientorganisation som är betrodd av prenumerationen för instansen. När identiteten har skapats etableras autentiseringsuppgifterna till instansen. Livscykeln för en systemtilldelad identitet är direkt knuten till den tjänstinstans i Azure som den är aktiverad på. Om instansen tas bort rensar Azure automatiskt autentiseringsuppgifterna och identiteten i Azure AD.
- En **användartilldelad identitet** skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure. Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för de Azure-tjänstinstanser som den är tilldelad till.

Din kod kan använda en hanterad tjänstidentitet för att begära åtkomsttoken för tjänster som stöder Azure AD-autentisering. Azure tar hand om de autentiseringsuppgifter som används av tjänstinstansen.

Följande diagram visar hur hanterade tjänstidentiteter fungerar med virtuella datorer i Azure (VM):

![Hanterade tjänstidentiteter och virtuella datorer i Azure](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-identity-works-with-an-azure-vm"></a>Så här fungerar en systemtilldelad identitet med en virtuell dator i Azure

1. Azure Resource Manager tar emot en begäran om att aktivera den systemtilldelade identiteten på en virtuell dator.
2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD som representerar den virtuella datorns identitet. Tjänstens huvudnamn skapas i den Azure AD-klientorganisation som är betrodd av prenumerationen.
3. Azure Resource Manager konfigurerar identiteten på den virtuella datorn:
    1. Azure Instance Metadata Service-identitetens slutpunkt uppdateras med klient-ID och certifikat för tjänstens huvudnamn.
    1. Etablerar tillägget för den virtuella datorn och lägger till klient-ID:t och certifikatet för tjänstens huvudnamn. (Det här steget kommer att fasas ut.)
4. När den virtuella datorn har fått en identitet använder du informationen om tjänstens huvudnamn för att ge den virtuella datorn åtkomst till Azure-resurser. Använd rollbaserad åtkomstkontroll (RBAC) i Azure AD när du anropar Azure Resource Manager för att tilldela lämplig roll till tjänstens huvudnamn för den virtuella datorn. Ge din kod åtkomst till den specifika hemligheten eller nyckeln i Key Vault när du anropar Key Vault.
5. Din kod som körs på den virtuella datorn kan begära en token från två slutpunkter som endast är tillgängliga inifrån den virtuella datorn:

    - Azure Instance Metadata Service-identitetsslutpunkt (rekommenderas): `http://169.254.169.254/metadata/identity/oauth2/token`
        - Resursparametern anger vilken tjänst som token ska skickas till. Använd `resource=https://management.azure.com/` för att autentisera mot Azure Resource Manager.
        - API-versionsparametern anger IMDS-versionen, använd api-version=2018-02-01 eller högre.
    - Slutpunkt för VM-tillägg (kommer att fasas ut): `http://localhost:50342/oauth2/token` 
        - Resursparametern anger vilken tjänst som token ska skickas till. Använd `resource=https://management.azure.com/` för att autentisera mot Azure Resource Manager.

6. Ett anrop görs till Azure AD för att begära en åtkomsttoken (se steg 5) med klient-ID:t och certifikatet som konfigurerades i steg 3. Azure AD returnerar en åtkomsttoken för JSON Web Token (JWT).
7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.

### <a name="how-a-user-assigned-identity-works-with-an-azure-vm"></a>Så här fungerar en användartilldelad identitet med en virtuell dator i Azure

1. Azure Resource Manager tar emot en begäran om att skapa en användartilldelad identitet.
2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD som representerar den användartilldelade identiteten. Tjänstens huvudnamn skapas i den Azure AD-klientorganisation som är betrodd av prenumerationen.
3. Azure Resource Manager tar emot en begäran om att konfigurera den användartilldelade identiteten på en virtuell dator:
    1. Azure Instance Metadata Service-identitetsslutpunkten uppdateras med klient-ID:t och certifikatet för tjänstens huvudnamn i den användartilldelade identiteten.
    1. Etablerar VM-tillägget och lägger till klient-ID:t och certifikatet för tjänstens huvudnamn för den användartilldelade identiteten. (Det här steget kommer att fasas ut.)
4. När den användartilldelade identiteten har skapats använder du informationen om tjänstens huvudnamn för att ge identiteten åtkomst till Azure-resurser. Använd rollbaserad åtkomstkontroll (RBAC) i Azure AD när du anropar Azure Resource Manager för att tilldela lämplig roll till tjänstens huvudnamn för den användartilldelade identiteten. Ge din kod åtkomst till den specifika hemligheten eller nyckeln i Key Vault när du anropar Key Vault.

   > [!Note]
   > Du kan även utföra det här steget före steg 3.

5. Din kod som körs på den virtuella datorn kan begära en token från två slutpunkter som endast är tillgängliga inifrån den virtuella datorn:

    - Azure Instance Metadata Service-identitetsslutpunkt (rekommenderas): `http://169.254.169.254/metadata/identity/oauth2/token`
        - Resursparametern anger vilken tjänst som token ska skickas till. Använd `resource=https://management.azure.com/` för att autentisera mot Azure Resource Manager.
        - Parametern för klient-ID anger den identitet som token har begärts för. Det här värdet krävs för att lösa tvetydigheter om mer än en användartilldelad identitet finns på samma virtuella dator.
        - Parametern för API-version anger Azure Instance Metadata Service-versionen. Använd `api-version=2018-02-01` eller senare.

    - Slutpunkt för VM-tillägg (kommer att fasas ut): `http://localhost:50342/oauth2/token`
        - Resursparametern anger vilken tjänst som token ska skickas till. Använd `resource=https://management.azure.com/` för att autentisera mot Azure Resource Manager.
        - Parametern för klient-ID anger den identitet som token har begärts för. Det här värdet krävs för att lösa tvetydigheter om mer än en användartilldelad identitet finns på samma virtuella dator.
6. Ett anrop görs till Azure AD för att begära en åtkomsttoken (se steg 5) med klient-ID:t och certifikatet som konfigurerades i steg 3. Azure AD returnerar en åtkomsttoken för JSON Web Token (JWT).
7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.

## <a name="how-can-i-use-managed-service-identities"></a>Hur kan jag använda hanterade tjänstidentiteter?

Gå självstudiekurserna nedan om du vill veta hur du använder hanterade tjänstidentiteter för att få åtkomst till olika Azure-resurser.

Lär dig hur du använder en hanterad tjänstidentitet med en virtuell Windows-dator:

* [Få åtkomst till Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Få åtkomst till Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Få åtkomst till Azure SQL](tutorial-windows-vm-access-sql.md)
* [Få åtkomst till Azure Storage med en åtkomstnyckel](tutorial-windows-vm-access-storage.md)
* [Få åtkomst till Azure Storage med signaturer för delad åtkomst](tutorial-windows-vm-access-storage-sas.md)
* [Få åtkomst till en resurs utanför Azure AD med Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Lär dig hur du använder en hanterad tjänstidentitet med en virtuell Linux-dator:

* [Få åtkomst till Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Få åtkomst till Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Få åtkomst till Azure Storage med en åtkomstnyckel](tutorial-linux-vm-access-storage.md)
* [Få åtkomst till Azure Storage med signaturer för delad åtkomst](tutorial-linux-vm-access-storage-sas.md)
* [Få åtkomst till en resurs utanför Azure AD med Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Lär dig hur du använder en hanterad tjänstidentitet med andra Azure-tjänster:

* [Azure App Service](/azure/app-service/app-service-managed-service-identity)
* [Azure Functions](/azure/app-service/app-service-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)

## Vilka Azure-tjänster stöder funktionen?<a name="which-azure-services-support-managed-service-identity"></a>

Hanterade tjänstidentiteter kan användas för att autentisera mot tjänster som stöder Azure AD-autentisering. En lista över Azure-tjänster som stöder funktionen Hanterad tjänstidentitet finns i [Tjänster som stöder hanterad tjänstidentitet](services-support-msi.md).

## <a name="next-steps"></a>Nästa steg

Kom igång med funktionen Hanterad tjänstidentitet med hjälp av följande snabbstarter:

* [Använda en hanterad tjänstidentitet på en virtuell Windows-dator för att få åtkomst till Resource Manager](tutorial-windows-vm-access-arm.md)
* [Använda en hanterad tjänstidentitet på en virtuell Linux-dator för att få åtkomst till Resource Manager](tutorial-linux-vm-access-arm.md)
