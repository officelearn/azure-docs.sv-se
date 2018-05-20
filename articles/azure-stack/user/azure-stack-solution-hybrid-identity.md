---
title: Konfigurera hybrididentitet moln med Azure och Azure-stacken program | Microsoft Docs
description: Lär dig hur du konfigurerar hybrididentitet moln med Azure och Azure-stacken program.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>Självstudier: Konfigurera hybrididentitet moln med Azure och Azure Stack-program

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du har två alternativ för att bevilja åtkomst till dina program i både globala Azure och Azure-stacken. Om Azure-stacken är en kontinuerlig anslutning till Internet, kan du använda Azure Active Directory (AD Azure). När Azure-stacken är bortkopplad från Internet, kan du använda Azure Directory Federation Services (AD FS). Du kan använda tjänstens huvudnamn för att bevilja åtkomst till dina program i Azure-stacken för distribution eller konfigurationen med hjälp av Azure Resource Manager i Azure-stacken. 

I den här självstudiekursen skapar du en exempel-miljö till:

> [!div class="checklist"]
> * Upprätta en hybrididentitet i globala Azure och Azure-stacken
> * Hämtar en token för att komma åt Azure Stack-API.

Dessa steg kräver att du har behörighet för Azure Stack-operator.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Skapa ett huvudnamn för tjänsten för Azure AD i portalen

Om du har distribuerat Azure stacken använder Azure AD som Identitetslagret kan skapa du tjänstens huvudnamn precis som för Azure. [Det här dokumentet](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) visar hur du utför steg via portalen. Kontrollera att du har den [nödvändiga behörigheter för Azure AD](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) innan du börjar.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Skapa ett huvudnamn för tjänsten för AD FS med hjälp av PowerShell

Om du har distribuerat Azure stacken med AD FS kan använda du PowerShell för att skapa ett huvudnamn för tjänsten, tilldela en roll för åtkomst och logga in från PowerShell den identiteten. [Det här dokumentet](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) visar hur du utför steg med hjälp av PowerShell.

## <a name="using-the-azure-stack-api"></a>Med hjälp av Azure API-stacken

[Den här självstudiekursen](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) vägleder dig genom processen att hämta en token för att komma åt Azure Stack-API.

## <a name="connect-to-azure-stack-using-powershell"></a>Ansluta till Azure-stacken med hjälp av Powershell

Här är ett exempelskript med hjälp av de begrepp som lärs ut under det här dokumentet för att ansluta till Azure-stacken.

### <a name="prerequisites"></a>Förutsättningar

En installation av Azure-stacken ansluten till Azure Active Directory med en prenumeration som du kan komma åt. Om du inte har en Azure-Stack-installation du kan använda dessa instruktioner för att ställa in en [Azure Stack Development Kit](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy).

[Den här självstudiekursen](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) tar dig igenom de steg som krävs för att installera Azure PowerShell och ansluta till Azure Stack-installationen.

#### <a name="connect-to-azure-stack-using-code"></a>Ansluta till Azure-stacken med kod

Använda slutpunkter för Azure Resource Manager API för att ansluta till Azure-stacken använder kod att hämta autentiserings- och diagram slutpunkter för din Azure Stack-installation och sedan autentisera med hjälp av REST-begäranden. Du hittar ett exempelprogram [här](https://github.com/shriramnat/HybridARMApplication).

> [!Note]  
Om Azure SDK för din valda språket inte stöder Azure API-profiler, kanske SDK inte fungerar med Azure-stacken. Mer information om Azure API-profiler, [här](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>Nästa steg

 - Läs mer om hur identitet hanteras i Azure-stacken i [identitet arkitektur för Azure-Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).  
 - Läs mer om Azure Cloud mönster i [designmönster](https://docs.microsoft.com/azure/architecture/patterns).
