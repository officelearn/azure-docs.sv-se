---
title: Hanterade identiteter för Azure-resurser
description: En översikt över hanterade identiteter för Azure-resurser.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f5446e836a65c6d40c2cc6703757670988593bd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896599"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Vad är hanterade identiteter för Azure-resurser?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Det är viktigt att dessa autentiseringsuppgifter skyddas. Helst bör autentiseringsuppgifterna aldrig visas på utvecklarnas arbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem.

Funktionen Hanterade identiteter för Azure-resurser i Azure Active Directory (Azure AD) löser det här problemet. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod.

Funktionen Hanterade identiteter för Azure-resurser är kostnadsfri med Azure AD för Azure-prenumerationer. Ingen extra kostnad utgår.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="terminology"></a>Terminologi

Följande termer används i de hanterade identiteterna för Azure-resursernas dokumentationsuppsättning:

- **Klient-ID** – En unik identifierare som genereras av Azure AD och som kopplas till ett program och tjänstens huvudnamn under den första etableringen.
- **Ägar-ID** – Objekt-ID för tjänstens huvudnamnsobjekt för din hanterade identitet som används för att bevilja rollbaserad åtkomst till en Azure-resurs.
- **Azure Instance Metadata Service (IMDS)** – En REST-slutpunkt som är tillgänglig för alla virtuella IaaS-datorer som skapas via Azure Resource Manager. Slutpunkten är tillgänglig på en välkänd icke-dirigerbar IP-adress (169.254.169.254), som endast kan nås från den virtuella datorn.

## <a name="how-does-the-managed-identities-for-azure-resources-work"></a>Hur fungerar de hanterade identiteterna för Azure-resurser?

Det finns två typer av hanterade identiteter:

- En **systemtilldelad hanterad identitet** aktiveras direkt på en instans av Azure-tjänsten. När identiteten har aktiverats skapar Azure en identitet för instansen i den Azure AD-klientorganisation som är betrodd av prenumerationen för instansen. När identiteten har skapats etableras autentiseringsuppgifterna till instansen. Livscykeln för en systemtilldelad identitet är direkt knuten till den tjänstinstans i Azure som den är aktiverad på. Om instansen tas bort rensar Azure automatiskt autentiseringsuppgifterna och identiteten i Azure AD.
- En **användartilldelad hanterad identitet** skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure. Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för de Azure-tjänstinstanser som den är tilldelad till.

Internt är hanterade identiteter tjänstens huvud namn av en särskild typ, som är låsta för att endast användas med Azure-resurser. När den hanterade identiteten tas bort tas motsvarande tjänst objekt bort automatiskt.

Din kod kan använda en hanterad identitet för att begära åtkomsttoken för tjänster som stöder Azure AD-autentisering. Azure tar hand om de autentiseringsuppgifter som används av tjänstinstansen.

Följande diagram visar hur hanterade tjänstidentiteter fungerar med virtuella datorer i Azure (VM):

![Hanterade tjänstidentiteter och virtuella datorer i Azure](media/overview/msi-vm-vmextension-imds-example.png)

|  Egenskap    | Systemtilldelad hanterad identitet | Användartilldelad hanterad identitet |
|------|----------------------------------|--------------------------------|
| Flikar |  Skapas som en del av en Azure-resurs (till exempel en virtuell Azure-dator eller Azure App Service) | Skapas som fristående Azure-resurs |
| Livscykel | Delad livs cykel med den Azure-resurs som den hanterade identiteten skapas med. <br/> När den överordnade resursen tas bort, tas även den hanterade identiteten bort. | Oberoende livs cykel. <br/> Måste tas bort explicit. |
| Dela mellan Azure-resurser | Kan inte delas. <br/> Den kan bara kopplas till en enda Azure-resurs. | Kan delas <br/> Samma användare-tilldelade hanterade identitet kan associeras med fler än en Azure-resurs. |
| Vanliga användarsituationer | Arbets belastningar som finns i en enda Azure-resurs <br/> Arbets belastningar för vilka du behöver oberoende identiteter. <br/> Till exempel ett program som körs på en enskild virtuell dator | Arbets belastningar som körs på flera resurser och som kan dela en enda identitet. <br/> Arbets belastningar som behöver förautentisering till en säker resurs som en del av ett etablerings flöde. <br/> Arbets belastningar där resurser återvinns ofta, men behörigheter bör vara konsekventa. <br/> Till exempel en arbets belastning där flera virtuella datorer behöver åtkomst till samma resurs |

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Så här fungerar en systemtilldelad hanterad identitet med en virtuell dator i Azure

1. Azure Resource Manager tar emot en begäran om att aktivera den systemtilldelade hanterade identiteten på en virtuell dator.

2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD som representerar den virtuella datorns identitet. Tjänstens huvudnamn skapas i den Azure AD-klientorganisation som är betrodd av prenumerationen.

3. Azure Resource Manager konfigurerar identiteten på den virtuella datorn genom att uppdatera Azure Instance Metadata Service Identity-slutpunkten med klient-ID och certifikat för tjänstens huvud namn.

4. När den virtuella datorn har fått en identitet använder du informationen om tjänstens huvudnamn för att ge den virtuella datorn åtkomst till Azure-resurser. Använd rollbaserad åtkomstkontroll (RBAC) i Azure AD när du anropar Azure Resource Manager för att tilldela lämplig roll till tjänstens huvudnamn för den virtuella datorn. Ge din kod åtkomst till den specifika hemligheten eller nyckeln i Key Vault när du anropar Key Vault.

5. Din kod som körs på den virtuella datorn kan begära en token från Azure instance metadata service-slutpunkt, som endast kan nås från den virtuella datorn: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Resursparametern anger vilken tjänst som token ska skickas till. Använd `resource=https://management.azure.com/` för att autentisera mot Azure Resource Manager.
    - API-versionsparametern anger IMDS-versionen, använd api-version=2018-02-01 eller högre.

6. Ett anrop görs till Azure AD för att begära en åtkomsttoken (se steg 5) med klient-ID:t och certifikatet som konfigurerades i steg 3. Azure AD returnerar en åtkomsttoken för JSON Web Token (JWT).

7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Så här fungerar en användartilldelad hanterad identitet med en virtuell dator i Azure

1. Azure Resource Manager tar emot en begäran om att skapa en användartilldelad hanterad identitet.

2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD som representerar den användartilldelade hanterade identiteten. Tjänstens huvudnamn skapas i den Azure AD-klientorganisation som är betrodd av prenumerationen.

3. Azure Resource Manager tar emot en begäran om att konfigurera den tilldelade hanterade identiteten på en virtuell dator och uppdaterar slut punkten för Azure Instance Metadata Service-identiteten med klient-ID och certifikat för den användardefinierade hanterade identitets tjänstens huvud namn.

4. När den användartilldelade hanterade identiteten har skapats använder du informationen om tjänstens huvudnamn för att ge identiteten åtkomst till Azure-resurser. Använd rollbaserad åtkomstkontroll (RBAC) i Azure AD när du anropar Azure Resource Manager för att tilldela lämplig roll till tjänstens huvudnamn för den användartilldelade identiteten. Ge din kod åtkomst till den specifika hemligheten eller nyckeln i Key Vault när du anropar Key Vault.

   > [!Note]
   > Du kan även utföra det här steget före steg 3.

5. Din kod som körs på den virtuella datorn kan begära en token från Azure-Instance Metadata Service identitetens slut punkt, endast tillgänglig från den virtuella datorn: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Resursparametern anger vilken tjänst som token ska skickas till. Använd `resource=https://management.azure.com/` för att autentisera mot Azure Resource Manager.
    - Parametern för klient-ID anger den identitet som token har begärts för. Det här värdet krävs för att lösa tvetydigheter om mer än en användartilldelad identitet finns på samma virtuella dator.
    - Parametern för API-version anger Azure Instance Metadata Service-versionen. Använd `api-version=2018-02-01` eller senare.

6. Ett anrop görs till Azure AD för att begära en åtkomsttoken (se steg 5) med klient-ID:t och certifikatet som konfigurerades i steg 3. Azure AD returnerar en åtkomsttoken för JSON Web Token (JWT).
7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Hur använder jag hanterade identiteter för Azure-resurser?

Gå självstudiekurserna nedan om du vill veta hur du använder hanterade identiteter för att få åtkomst till olika Azure-resurser.

> [!NOTE]
> Se kursen om att [implementera hanterade identiteter för Microsoft Azure-resurser](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) för mer information om hanterade identiteter, däribland detaljerade videogenomgångar av flera scenarier som stöds.

Lär dig hur du använder en hanterad identitet med en virtuell Windows-dator:

* [Få åtkomst till Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Få åtkomst till Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Få åtkomst till Azure SQL](tutorial-windows-vm-access-sql.md)
* [Få åtkomst till Azure Storage med en åtkomstnyckel](tutorial-vm-windows-access-storage.md)
* [Få åtkomst till Azure Storage med signaturer för delad åtkomst](tutorial-windows-vm-access-storage-sas.md)
* [Få åtkomst till en resurs utanför Azure AD med Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Lär dig hur du använder en hanterad identitet med en virtuell Linux-dator:

* [Åtkomst Azure Container Registry](../../container-registry/container-registry-authentication-managed-identity.md)
* [Få åtkomst till Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Få åtkomst till Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Få åtkomst till Azure Storage med en åtkomstnyckel](tutorial-linux-vm-access-storage.md)
* [Få åtkomst till Azure Storage med signaturer för delad åtkomst](tutorial-linux-vm-access-storage-sas.md)
* [Få åtkomst till en resurs utanför Azure AD med Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Lär dig hur du använder en hanterad identitet med andra Azure-tjänster:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Azure Container Registry uppgifter](../../container-registry/container-registry-tasks-authentication-managed-identity.md)
* [Azure Event Hubs](../../event-hubs/authenticate-managed-identity.md)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Kubernetes Service](/azure/aks/use-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)


## Vilka Azure-tjänster stöder funktionen?<a name="which-azure-services-support-managed-identity"></a>

Hanterade identiteter för Azure-resurser kan användas för att autentisera till tjänster som stöder Azure AD-autentisering. En lista över Azure-tjänster som stöder funktionen Hanterade identiteter för Azure-resurser finns i [Tjänster som stöder hanterade identiteter för Azure-resurser](services-support-msi.md).

## <a name="next-steps"></a>Nästa steg

Kom igång med funktionen Hanterade identiteter för Azure-resurser med följande snabbstarter:

* [Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Resource Manager](tutorial-windows-vm-access-arm.md)
* [Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Resource Manager](tutorial-linux-vm-access-arm.md)
