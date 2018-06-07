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
ms.date: 05/22/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: a57afb4a90da5877879afddc35545e0bfef622a7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808170"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Självstudier: Konfigurera hybrididentitet moln för Azure och Azure Stack-program

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Lär dig hur du konfigurerar en hybrid cloud identitet för dina Azure och Azure Stack-program.

Har du två alternativ för att bevilja åtkomst till dina program i både globala Azure och Azure-stacken.

 * Om Azure-stacken är en kontinuerlig anslutning till Internet, kan du använda Azure Active Directory (AD Azure).
 * När Azure-stacken är bortkopplad från Internet, kan du använda Azure Directory Federation Services (AD FS).

Du kan använda tjänstens huvudnamn för att bevilja åtkomst till dina Azure Stack-program för distribution eller konfigurationen med hjälp av Azure Resource Manager i Azure-stacken.

I den här självstudiekursen skapar du en exempel-miljö till:

> [!div class="checklist"]
> * Upprätta en hybrididentitet i globala Azure och Azure-stacken
> * Hämta en token för att komma åt Azure Stack-API.

Du måste ha Azure Stack operatorn behörigheter för stegen i den här självstudiekursen.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Skapa ett huvudnamn för tjänsten för Azure AD i portalen

Om du har distribuerat Azure stacken använder Azure AD som Identitetslagret kan skapa du tjänstens huvudnamn precis som för Azure. Den [skapa tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) artikeln visar hur du utför steg via portalen. Se till att du har den [nödvändiga behörigheter för Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) innan du börjar.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Skapa ett huvudnamn för tjänsten för AD FS med hjälp av PowerShell

Om du har distribuerat Azure stacken med AD FS kan använda du PowerShell för att skapa ett huvudnamn för tjänsten, tilldela en roll för åtkomst och logga in från PowerShell den identiteten. [Skapa tjänstens huvudnamn för AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) visar hur du utför nödvändiga steg med hjälp av PowerShell.

## <a name="using-the-azure-stack-api"></a>Med hjälp av Azure API-stacken

Den [Azure Stack API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) självstudier vägleder dig genom processen att hämta en token för att komma åt Azure Stack-API.

## <a name="connect-to-azure-stack-using-powershell"></a>Ansluta till Azure-stacken med hjälp av Powershell

Snabbstart [att komma igång med PowerShell i Azure-stacken](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) vägleder dig genom stegen som behövs för att installera Azure PowerShell och ansluta till Azure Stack-installationen.

### <a name="prerequisites"></a>Förutsättningar

En installation av Azure-stacken ansluten till Azure Active Directory med en prenumeration som du kan komma åt. Om du inte har en Azure-Stack-installation du kan använda dessa instruktioner för att ställa in en [Azure Stack Development Kit](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Ansluta till Azure-stacken med kod

Använda slutpunkter för Azure Resource Manager API för att ansluta till Azure-stacken använder kod att hämta autentiserings- och diagram slutpunkter för din Azure Stack-installation och sedan autentisera med hjälp av REST-begäranden. Du hittar ett exempelprogram för klienten på [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Om Azure SDK för din valda språket inte stöder Azure API-profiler, kanske SDK inte fungerar med Azure-stacken. Mer information om Azure API-profiler finns i [hantera profiler för API-version](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles) artikel.

## <a name="next-steps"></a>Nästa steg

 - Läs mer om hur identitet hanteras i Azure-stacken i [identitet arkitektur för Azure-Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Läs mer om Azure Cloud mönster i [designmönster](https://docs.microsoft.com/azure/architecture/patterns).
